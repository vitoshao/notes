---
title: Log Shipping
layout: default
parent: Redundant
nav_order: 7
description: "Log Shipping"
date: 2013-11-12
tags: [SQL,Redundant]
postid: "7150928086099162551"
---
Log Shipping 是早從 SQL 7 就有的功能，它與 SQL 2005 中的 Mirroring 是相似的作用。  二者都有一台「主要伺服器」當作營運使用，另外還有一台「次要伺服器」當作 StandBy ，並隨時與「主要伺服器」保持資料同步。  

# 記錄傳送（Log Shipping ）

## 記錄傳送概觀

在記錄傳送的架構中，成員有以下幾種：  

- 主要伺服器 (primary database) ：做為實際執行 SQL Server 的主要執行個體。
- 次要伺服器 (secondary server)：用來保留主要資料庫的暖待命副本(warm standby copy)的執行個體。
- 監視伺服器 (monitor server) ：用來以追蹤記錄傳送的所有詳細資料

實作**SQL Server 記錄傳送**最少需要兩個 SQL Server 執行個體，一個是「主要伺服器」，另一個則為「次要伺服器」，而「監視伺服器」則是可選擇性的。  

在**SQL Server 記錄傳送作業**中，  送出紀錄的稱為**主要伺服器**(primary database)，  目的端我們稱為**次要伺服器**(secondary database)，一個主要伺服器可以設定傳送給多個次要伺服器。  

主要伺服器會進行**交易紀錄備份**，然後傳送到次要伺服器；  次要伺服器將交易紀錄進行還原，次要伺服器的還原模式可設定的選項有 **NORECOVERY** 或是 **STANDBY** 。  而在紀錄傳送作業中，您可以設定監控伺服器來監視記錄傳送活動並保存記錄傳送記錄，下圖就是紀錄傳送的作業示意圖。  

記錄傳送由三項作業組成： 

1. 在**主要伺服器**執行個體上**備份交易記錄檔**。
2. 將交易記錄檔**複製**到次要伺服器執行個體上。
3. 在**次要伺服器**執行個體上**還原記錄備份**。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjEBJpYKbMLlyu9g2qaHO23cm-oG0pXfzBOb4jIo7SvmXMlBZbUU3F8AryBAJpF5JPE7Vlso9RGWB-LcTmLho1xJZVfpg9Cd_5j_kBlhUNZvxRvS2KNT3SE6VfzagUnkQqqTAiYT0Ta3WQ/s0/sql-log-shipping.gif)

以上的紀錄傳送作業，都是透過 SQL Server Agent 來執行作業

記錄傳送組態沒有自動從主要伺服器容錯移轉到次要伺服器的功能，若主要資料庫無法使用，您可以手動將任何次要資料庫連上線。

您可以將次要資料庫做為報表用途。

## 權限設定

#### 備份作業 (backup job) 

主要伺服器執行個體的 SQL Server 服務帳戶與執行備份作業的帳戶(預設是主要伺服器執行個體的 SQL Server Agent 服務帳戶)，必須擁有備份目錄的讀取/寫入權限。  

#### 複製作業 (copy job) 

執行複製作業的帳戶(預設是次要伺服器執行個體的 SQL Server Agent 服務帳戶)必須擁有備份目錄的讀取權限和複製目錄(這個目錄通常在次要伺服器)的寫入權限。  

#### 還原作業 (restore job) 

次要伺服器執行個體的 SQL Server 服務帳戶與執行還原作業的帳戶(預設是次要伺服器執行個體的 SQL Server Agent 服務帳戶)，必須擁有複製目錄的讀取/寫入權限。  

#### 警示作業 (alert job) 

SQL Server Agent 作業，若備份或還原作業未在指定臨界值內順利完成，此作業就會為主要及次要資料庫發出警示。  若資料庫啟用了記錄傳送，監視伺服器執行個體上就會產生「記錄傳送警示」目錄（Log Shipping Alert）。

## 互通性

記錄傳送也能與 SQL Server 的下列功能或元件搭配使用：

- [將記錄傳送主要資料庫和次要資料庫加入至現有的可用性群組](http://technet.microsoft.com/zh-tw/library/hh758463.aspx)
- [記錄傳送搭配複寫](http://technet.microsoft.com/zh-tw/library/ms151224.aspx)
- [記錄傳送搭配資料庫鏡像](http://technet.microsoft.com/zh-tw/library/ms187016.aspx)

# 實作記錄傳送

下面範例，使用二個執行個體。  假設第一台執行個體中有個 TestDB1 ，第一台執行個體中則完全沒有資料庫。  我們要將這個TestDB1資料庫,利用 Log Shipping 方式，同步到第二台執行個體。  

## 準備工作

### 1. 設定主資料庫的復原模式

在設定交易記錄傳送前，必須先確認主資料庫的復原模式，必須選擇「完整」或「大量記錄」，不支援「簡單」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhxM-5eCzsE2YY3M0Vs4wKaqOrQbF4F4uDsSf2eJQvzOKXjrVVdiPcAt723g_nmEQkW8hK07y7wvHyDdG1_9ntsMkCxXfqYXrzha9eohteoD87QyFYHCyj3Esuwrnd0uqOeQQ2M60o7AYg/s0/sql-log-shipping-01.png)

### 2. 建立備份資料夾和複製資料夾

在作業前，我們可以先建立二個資料夾，存放資料夾和複製資料夾。(本範例使用 LogShipping\_Backup 和 LogShipping\_Copy)  

備份資料夾：  
SQL Server Agent 會將主資料庫的備份存放於此。  所以必須開放主要伺服器 SQL Server Agent 服務帳號的讀取/寫入權限；  也必須開放次要伺服器 SQL Server Agent 服務帳號的讀取權限。  

複製資料夾：  
SQL Server Agent 會將主資料庫的備份複製一份到次要伺服器。  所以必須開放次要伺服器 SQL Server Agent 服務帳號的讀取/寫入權限。

### 3. 啟用 SQL Server Agent

由於 Log Shipping 都是由 SQL Server Agent 負責執行，所以請先確認二端的 SQL Server Agent 是否都已啟動。

## 設定主要伺服器

### 4. 啟用交易記錄傳送

開啟主資料庫資料庫屬性頁面，然後勾選啟用交易記錄傳送。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjWl3kHPm0zkXtCr_almX5heiGc0isGJiGoAeZgs0MU-_cYEnpFHJDk24mCQCOTmoNUOGVtlDXLCbhEnJy2S_jCYMa8eQEJNpsKgtIreJMFs8XxbxmBxXaYgvwdzubdzUwLocMvEVgSG90/s0/sql-log-shipping-02.png)

### 5. 備份設定

1. 設定備份資料夾：  
將步驟2建立的備份資料夾填入。  

2. 設定壓縮：  
可設定啟用或不啟用備份壓縮。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhifnROkDiXHiIa9305o5sImKgVC7bcKyc9YNMfp6OFJ0lpKbxEk7dWFtQ3aDMQahQti7A1dlMurdM39r7EjaTEDMHgv3NFVmmreT5HynQ7nOU6LchGcmNy9dz-VCIGUgDeOR6OXDYAxdA/s0/sql-log-shipping-03.png)

## 設定次要伺服器

### 6. 加入次要資料庫

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiY3upB8L9liuo4aWfrpliS0slgqWF-4zBRG0QQh-1yKw_LLvZUvOvRSJeI7qM5f2ugT7SJuSPPyoDc6yveYcAK3ntXQisWvmS21kl5RfIbTG0s_94XABzT2HgOzeISoBPC2IG7LQdxmPo/s0/sql-log-shipping-05.png)

### 7. 連線次要伺服器

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiwwRlGKhsH-Y-mlr6Z2REKW4kIQp3uMmMjU3ubhtgzaa7tGFo5sgWQ1gOttkA8C-SRIZMAGzHk972JYAFJZ-9ERPyukLV3lQh9wlKfKQazucKO2-6bbS9FYobt4trKChMnhblYB__Jdes/s0/sql-log-shipping-04.png)

### 8. 設定還原選項

因為次要伺服器中，原先並沒有資料庫，所以這裡要設定一個資料庫存放的路徑。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh3ASnocEsh0YZWs5xMo0lrG9Gph5EeXc2Xn2tReMcyHlEzLYJEI9DNBTPJC47CL5ZO5vlz96za73m07ayrLM0EoR7amGijUoHbkTSrFzq5WBaTK9iSIw5ZEC33xcwREJ6KKmRkj3oDi-g/s0/sql-log-shipping-06.png)

### 9. 指定複製檔案用的目的資料夾

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhp-4f7kY2YWsmUyTAzCDzFAFTB-hmbbe_pXrrznS9Iy3nfBGAhtndUI046ThGiweG3YYabyO9_7951Jkdey2SDlRYYgrfG2btl2iTpa3IMHWZmatSDcC5HWjpJssHN7YIvIMzzv5VC_cM/s0/sql-log-shipping-07.png)

### 10. 指定還原模式

指定次要伺服器的還原模式，支援：不復原模式(NoRecover)或待命模式(Standby)。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEid0pNvtTmqx8OYT6jomgsDwvQUomwW4pDGXBvyuw8AD5UugVkV9_WqXmPlRNyQXx0tqZRgnEDZfdnlbbT-fIB36NlhKg2opKB102Vi81UlWSx7k6xTtXnafsP0_dufEV-ZpU2FlES3zqw/s0/sql-log-shipping-08.png)

## 設定監視伺服器

監視伺服器為選用設備，若有使用，只要依操作設定即可。

## 完成工作

回顧一下，剛剛設定做了哪些設定。沒錯，就上面文章介紹的：備份、複製、還原。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg4h4XS5CYSTsbdi7zKL-7NJosLYgyhneHl5wuvGr0oAPelxbN_KGIZ3S8F1OOaDTbkRBsIi04PZFHtqzuqSPwVJ7-II2Uip3ZWl4Nw71PtjV9jy1i1thNJzuaSWc7uWaU6aNiYn1N8dZI/s0/sql-log-shipping-10.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj-6vI3qTH0MLd8dr-dn9FBENSbz8gj-zG4rk8xc_Kzm2J26pH6s9oO0KBS8-ny4BW119xImQ5xTTdzYRG73yQIE2TIexKgpe2xXr_eVznsCIgoonHHx6OcYCnspj51-Z_7qY2wQJk4_tQ/s0/sql-log-shipping-11.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjs5ORrWws4XvjZc7jrfZ1dxKCGxDM7hJVUfHf75wf1aHhrsbREzjNC7AuVtf_DUtntVWTx4MU2JEh2REtvLlIkAWLNmQkfgCc4ge0YK9E8lytaESrFo7a5ZbeMqJMatXeR-adwqf52XQg/s0/sql-log-shipping-12.png)

按下[確定]，若設定無誤，可以下列視窗

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi0PEOja00GtlA41DXjIS66eZm0gu_c0QGMmxVEycQB-S10lGOzYmrWKHUwhHSrI3N4B01RhRMxtywe0-kDysduJpShiclKrx59L2_aDFwcdaB1h-qhPXTDQ29-cwXoDK06HaaiNEw-hJ0/s0/sql-log-shipping-09.png)

此時 Log Shipping 已設定完成，可正常執行，你可以透過 [Instance] -&gt; [報表]   -&gt; [標準報表] -&gt; [交易記錄傳案狀態] 查看目前的執行狀況，

主要伺服器  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj7tuV1q_Vq8QkPxUOOvlaUfY-L_QSb9PXLGfgohIBTuSriywUmNSoghjq6NsL8efKFzp0gKCyb2N-duRmixjF6fZoovavxwwM06RdKb7egnnCllj3SjRxp65p-erANvwSmsPotEybvgPo/s850/sql-log-shipping-13.png)

次要伺服器  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEigE1tByAA6S_HusgMYtKzrn2kOY47xETeORVqym6ULBT6m2v_EEIgdz7YykpPSLxhvD034n9PYNvXYd7nUJ8HD3_N371W5wxWuIpfZydf5yi5uaZ8-ayiMuGB4ciZxp6ywqTkWaQ-_gKk/s850/sql-log-shipping-14.png)

最後你可以在次要伺服器上，看到資料庫已經新增完成。  同時因為我們選用 NORECOVERY 模式，所以狀態會一直處於還原中，所以無法存取。  若選用 STANDBY 模式，狀態就會是待命/唯讀。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhovPcAUHtfqtkuZk9i3QZrYNSvN5UThTDWvG49yfbw7QCCvaFQf9w8Yiz2te5QuTQhrfb6Wx79IqHWvah-BvO4XjmuB0Q9_OwxPUKJzHqIaTvhRMpEog_o9Jz0m8bMPcSTXp-PgkeEuB4/s0/sql-log-shipping-15.png)

# 變更主要與次要記錄傳送伺服器間的角色

請參考[變更主要與次要記錄傳送伺服器間的角色 (SQL Server)](http://technet.microsoft.com/zh-tw/library/ms178117.aspx)
## 參考資料  

- [關於記錄傳送 (SQL Server)](http://technet.microsoft.com/zh-tw/library/ms187103.aspx)
- [SQL Server 無敵手冊第二十篇-實作SQL Server的記錄傳送(Log Shipping)](http://ithelp.ithome.com.tw/question/10030916)
- [如何建立 Log Shipping](http://caryhsu.blogspot.tw/2012/02/log-shipping.html)
- [容錯移轉至記錄傳送次要伺服器 (SQL Server)](http://caryhsu.blogspot.tw/2012/02/log-shipping.html)