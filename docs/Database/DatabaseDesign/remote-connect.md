---
title: 遠端連線
layout: default
parent: Install and Configure
nav_order: 5
description: "遠端連線"
date: 2013-11-15
tags: [SQL,Install and Configure]
postid: "1172178670007622627"
---
這篇文章包含三個主題：  

- 如何啟用 TCP/IP 協定，提供遠端連線功能( remote access )。
- 如何設定防火牆，允許遠端連線通過。
- 如何設定遠端管理員連接( remote admin connections )。

# 設定允許遠端連線功能（remote connection）

SQL Server 的遠端連線功能，必須使用 TCP/IP 通訊協定，因此要允許遠端連線，除了啟用遠端連線功能，還得啟用 TCP/IP 通訊協定。  

允許遠端連線，這個功能預設是啟用的，但是 TCP/IP 通訊協定，不同版本的 SQL Server ，其預設狀態不盡相同

## 啟用允許遠端連線

你可以在伺服器屬性視窗中，找到啟用遠端連線功能。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiOldVCp93YKJnQzPQwM8uwEhzxydhs8FW3Iwt66EJqC39hWJOR_aZ18bdNcGL26oXy2zrRLds0k_X1BjSpqdeO2f5DLi2TmnXudPc83I74H0kY_o4433AaQfGQu4Jjx16UGDNcAyhmir4/s0/sql-enable-remote-1.png)

## 啟用 TCP/IP 通訊協定

1. 執行【SQL Server 組態管理員】，並針對 SQL Server 執行個體啟用 TCP/IP 通訊協定

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhVStXHUaX_pS3fDjYhDjY98pqWQOKwTzwpgoG5H6pxJpNbap-ZbviblLcqS9lk5ASYboyxgT7_NBO5XPJr530HBvHex2NvSAdF9tP7eGaAClwWyiUdNh8yY4Fl5w2nH8pyop2fcXBb714/s0/sql-enable-tcpip-1.png)

2. 設定 TCP/IP 通訊協定內容：

一部主機可能有多組IP，例如內部IP、外部IP、本機IP等等，如果你要允許這些IP都開放遠端連線，就設定為「是」，否則就設定為「否」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjnyGiNk73R__BmW3kXzszwJ8z2n_xj7SvsaDVytpPCftWCBaa_T4snXiqwyKeHCkZcAoEWmQXGmn4IswgeiIXBR-Pv_z9ZNS5mHE1sHC8RQei6PKSRy8ttUnl3sS4DtPo_lLNUhjjuP-E/s0/sql-enable-tcpip-2.png)

3. 設定 TCP/IP 通訊埠：輸入你要使用的 port number 。

上一步驟中，若全部接聽設定為「是」，此處你只要指定「IPAll」這個選項即可；否擇，則找到你要使用的那個IP選項。

- TCP 動態通訊埠：若不使用動態通訊埠，請保留空白；若要使用動態通訊埠，請輸入0。
- TCP 動態通：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgLNQk-OpLFcxR020UqPaP2OyCvVmLuYwELpP9TUgrGgO5leLvPzohsO-r3vyfjRlhILvECeakZGzg1T7N2TKOohk0bxn4r0FqVo8OxR9bJP9CdybsljVOMcrnVNrRwEgogr-apuQFlmmw/s0/sql-enable-tcpip-3.png)

4. 啟用 SQL Server Broswer

如果只有一台預設執行個體，那麼即使不啟用 SQL Server Broswer ，也可以找的到該台執行個體。  SQL Server Broswer 是用來管理與識別電腦上的所有 SQL Server 執行個體，當它啟動時，會要求 UDP 通訊埠 1434，並讀取 SQL Server 的登錄項目，以回應用戶端的要求。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi_0mHzir_OqcrHpnbq4tHvzXVZdPEEkQFaafGC5MGCBWOgBj4cWYmJ_85joKAwIQtUdOgZzQheX8R9q-_RY7gaxSu6d2CVXkMRvsJRK-xvTzhhR4QJesGpZzk34XghdxvfJjYbONh59w0/s0/sql-enable-sqlbrowser.png)

5. 完成以上步驟後，你必須重新啟動 SQL Server 讓設定值生效。之後就可以使用 TCP/IP 通訊協定連線。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhGGAQI-dj9giNHZKnwK15IUxpPyjTHkSXlA6vQJBzmTrRchFvAEzrupI_A0xVH7rnuYUG0NSrabEh2XdnXkwHKBEIlKdrLpbcipGSEt34JbzEalI1FzcqGISG52yDxGaWk0mHNkgnf23E/s0/sql-enable-tcpip-4.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg1bAXNfCwpGeFKkRtckRKlNrcm3qWCCCCwAroCgUpT_9U9DutnfcJzYZRVxFKEPU42CWWT6HJAU2GJ0T5y8XZdZp77pWxeZ-p3ZiJRxSNHl0-aC26bnN7t0_gMKdX5H6BNbzjvypoqyLw/s0/sql-enable-tcpip-5.png)

# 設定防火牆，允許遠端連線通過

SQL Server is a Winsock application that communicates over TCP/IP   SQL Server listens for incoming connections on a particular port. The default port for SQL Server is 1433.   The port doesn't need to be 1433, but 1433 is the official Internet Assigned Number Authority (IANA) socket number for SQL Server.  

A client application communicates to SQL Server using the client-side network library Dbmssocn.dll   When the client establishes a TCP/IP connection, a three-way handshake takes place.   

- The client opens a source port ( random value between 1024 and 5000 ) and sends traffic to a destination port ( which is 1433 by default ).
- The SQL Server then communicates to the client by sending traffic from 1433 back to the port that the client established.

如果伺服器啟用了防火牆功能，若要允許遠端連線，就必須開啟 SQL 對外的通訊埠。

1.執行【控制台】-&gt; 【系統及安全】-&gt;【Windows 防火牆】，點選進階設定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg99Wnd531_hbrKt6RNKElob_FZGcCAdfgHDnVQRJ1maHAYPrRnwXLCtSVrLIHw67xKEXeuNhFp32FHrV9qFZVtEnbabWAV5CUiJgQbXotcHkgoebuV_57zSSMcyst0iBltVsrtv5ERV8s/s0/sql-firewall-1.png)

2.新增規則

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh1UeWwfAXTODc-_A7ucBBWJIw2nkw9UIVPp8IL17LBcL9hcRnOGnglfnHzJ6IaN_s-WWdYzJO-7q2rXnPObj8lB9cidtMaaImsYwdk04y5X7hxc5_YAphevBhbcOONp5hXYc-Jl-Puh_0/s850/sql-firewall-2.png)

3.規則類別，選擇「通訊埠」。你也可以選擇「自訂」，可以更詳細的設定防火牆規則，例如限制允許連線的IP等等。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgrj_M6fUmSyumr4ldqCqbUHfjpyhndr3ada0WCTm5WlJTrVJkODEAUZgTTAiiqwgQKKNffHw08fLG0i_17Vwaopv0Pe7vKVpFz9LLQnOz1t4L3ktJ_WE2fIBD_yCu3t6dQHoMfzTvM5DI/s0/sql-firewall-3.png)

4.設定通訊埠

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhRsz0jDfgqAuAHQOLzE6pDVKW6Hv_A5igKIndPghcfnfb8CKFbPfxf9bnenICXX5An-Na8SirDQLZPU5zLd15VOr6dk5dAW7U-v02-C_2rZ6n6lgtwPgGwmY8uF5dnXj0u8U9McZaXIo4/s0/sql-firewall-4.png)

5.設定執行動作

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiZUabFqkC4nJRbnqwW3nOLoA-iYr9BaFj2S0mb5EVGNRj7gaM3STsm1LRB88DaMg8G7dbRK-MkKu5PpXMLsLjEkoTP2Jc_nEpllnekRuYTowgfNY0oytAKdna9oo-ibkpiytFZzei59AY/s0/sql-firewall-5.png)

6.設定套用規則

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgr1yL_kzymi9mWj99eguoc_ohGvK8q-JqjQNS6_MqWn-wiEW4GT9AoLVU4DtXgoi9xo9COZmgzgttGrPUy6_NZXe4DnbFR0ezwYYZFC-rgEO6FeWFAvAWWW57xN9WczcmOKsHvcNncDNc/s0/sql-firewall-6.png)

7.設定規則名稱

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEillfzl8GAbZI4S0lzAcwG4uEe5pYzZkedl5sI7q0WTdhehDK-iK9gsPuh0G77wXFefqvEqASY9g_c9CWy9rxXCgHmzpwIf8pXCEINZg0PKTXUWYCa5Ik6AJI0YeY_aQrfiLl7eRGfdeD4/s0/sql-firewall-7.png)

# 設定遠端管理員連接（remote admin connections）

[專用管理員連接（dedicated administrator connection, DAC）](http://technet.microsoft.com/zh-tw/library/ms190468.aspx)

**專用管理員連接**可以提供管理員在伺服器遭到鎖定或在異常狀態下，仍然可以存取執行中的伺服器，進而執行診斷功能或 Transact-SQL 陳述式，以針對伺服器上的問題進行疑難排解。

根據預設，DAC 只會接聽回送 IP 位址 (127.0.0.1)、通訊埠 1434。  如果無法使用 TCP 通訊埠 1434，當 Database Engine 啟動時，會自動指派 TCP 通訊埠。 在電腦上安裝多個 SQL Server 執行個體時，請檢查錯誤記錄檔以取得 TCP 通訊埠編號。 

#### [remote admin connections 選項](http://technet.microsoft.com/zh-tw/library/ms190468.aspx)

- ０：只允許使用 DAC 的本機連接。(default)
- １：允許使用 DAC 的遠端連接。
```sql
sp_configure 'remote admin connections', 1;
GO　
RECONFIGURE;
GO
```

# 設定遠端存取（remote access）

[遠端存取（remote access）](http://technet.microsoft.com/zh-tw/library/ms190468.aspx)

**遠端存取**選項是用來控制本機或遠端伺服器上執行的預存程序。

#### [remote access 選項](http://technet.microsoft.com/zh-tw/library/ms190468.aspx)

- ０：只允許本機伺服器執行本機預存程序。
- １：允許從遠端伺服器執行本機預存程序，或從本機伺服器執行遠端預存程序。(default)
```sql
EXEC sp_configure 'remote access', 0 ;
GO
RECONFIGURE ;
GO
```
## 參考資料  

- [啟用 SQL Server 2008 遠端連線(Enable Remote Connection)](http://sharedderrick.blogspot.tw/2009/05/sql-server-2008-express-enable-remote.html)
- 
- [遠端管理員連接伺服器組態選項( remote admin connections )](http://technet.microsoft.com/zh-tw/library/ms190468.aspx)
- [設定 remote access 伺服器組態選項](http://technet.microsoft.com/zh-tw/library/ms191464.aspx)