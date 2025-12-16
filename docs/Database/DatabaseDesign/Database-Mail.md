---
title: Database Mail
layout: default
parent: 
nav_order: 3
description: "Database Mail"
date: 2013-06-22
tags: [SQL, Database Mail, Maintenance Plan, SQL Agent]
postid: "1327948016020444605"
---
Databasebase Mail 是 SQL Server Database Engine 用來發送電子郵件的服務，這個服務由外部處理序 DatabaseMail.exe 負責。  使用 Databasebase Mail 最主要的原因在於可以將直接發送資料庫的查詢結果給使用者。  

另外關於 Databasebase Mail 架構以及使用 DatabasebaseMail 優點請直接看 [MSDN](http://technet.microsoft.com/zh-tw/library/ms189635%28SQL.110%29.aspx)。  本文僅記錄如何使用 Databasebase Mail 。  

# 簡介 Databasebase Mail

在 SQL 2012 中的 Databasebase Mail 有以下特性：

- 是獨立的服務，與資料庫引擎分開，若該服務停罷，郵件訊息將被 queue 住，直到服務正常為止。
- Databasebase Mail 可同時設定多組 SMTP 伺服器。若其中一個 SMTP 伺服器停罷，會自動使用其他 SMTP 伺服器。
- Database Mail is cluster aware and can be installed on failover clusters.
- 一個資料庫執行個體，可以同時支援設定多組郵件設定檔（Mail profiles）

## 郵件主機資料庫（Mail Host Database）

**msdb** 資料庫是 Database Mail 主機資料庫。這個資料庫包含 Database Mail 的預存程序和訊息物件。  你可以使用 SSMS 中的 「Database Mail 組態精靈」，來啟用 Database Mail、建立和管理設定檔和帳戶，以及設定 Database Mail 選項。

依預設，Database Mail 不在使用中狀態。若要使用 Database Mail，您必須使用 Database Mail 組態精靈或 [sp_configure](http://msdn.microsoft.com/en-us/library/ms188787.aspx) 系統預存程序的 Database Mail XP 選項 來明確啟用 Database Mail。

## 檢查 Databasebase Mail 使用狀態

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEisdHa58hssnYbf91CSiLCi_1X4N1SuoKQGKQr291H-UiSGnp3xg2kZqwlh4B3UXmKVWHBMt8dQDvG2EoTbgkZoQFCHNSqv1KEAsA4IECxLgaqIDnUGB2oUnpftFyRnyvmq8C0M21gCXc8/s0/sql-dbmail-09.png)

# 設定 Databasebase Mail

在 Databasebase Mail 設定中，主要有二個項目：Account、Profile。

- **帳戶（Account）**：帳戶表示一組SMTP的相關設定，包含smpt server, email, username, password, port, security 等設定。
- **設定檔（Profile）**：設定檔表示Account的集合，一個設定檔可以設定多個Account。

## 透過精靈啟用 Databasebase Mail

啟動 Databasebase Mail 最簡單的方式就是透過精靈，步驟如下：

1.建立Account and Profile

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgEtpEj9nbiTZNheS06CldyIUy60AYjuF_YUqnOp6vioOueBdDz9715oOCg-9vNm4msjI8NT-wydos0nzDM2SnT993YrRGgdOrfunL4dNEnXxyOFiPUIFnws-_E57tuLEhHLVMGwg7Kz8A/s0/sql-dbmail-01.png)

2.由於設定前Database Mail尚未啟用，故下一步會出現詢問訊息。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiGIo5_p_VgE9I3E8XmUJGynX0nmLGY0UK5Mk2ZUOjiH3HzGr_D3s5y5-XRuOKD5QvSu0_Af1kTwkoStk2H-Hh3pmaiPLuvePSY4_9k98XUrXTbfdGUevQNYqiz5SvXCEMSlfMRC4idh4Q/s0/sql-dbmail-02.png)

3.接著新增Database Mail帳戶

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjf9_KDYCSbtLmOhbMV5tqYvDogb7NDUsQqZvIUZ9Uiz6gYV5sIvqS9C3X3Aui6j-MQWKtA6PSUBYLadT9RTWHcA5yO3t6BAN1ac0_wcjuX9gbJCURoIaQjfZxOfG3XoDodYVXnMD6H3D4/s0/sql-dbmail-03.png)

4.再來是設定檔安全性及參數的設定。

安全性設定可選擇是不是要將設定檔開放共用以及是否要變更為預設，參數設定請自行依需求修改設定值。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhMPnb0wDJ0NG8uJQXDmLPPVyVJsSfyMgkGeSgH6WEfwOtw7qGG9r4LLxJls8nGzLNxRsQ0UZ3GMKEioDGc93T8ekGvHKiGEV2cwjpVTUaCZvIIpc3Mmk1DGo1CIxmf0IEMZf1Ye-oW710/s0/sql-dbmail-04.png)

5.完成畫面。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh3b43-NvHKkjRtHnHlQkKcGq3pBQGf4zn9g3k5oGOGoYh99oZ-dvjL0AOi-oHaEWq4tic6S57dJSbDwyeqKKcBmJawGkJP74HJOWgrxsKsUW2LtTlek5hmGzrTtId9D0EmRM2dhUKuDUw/s0/sql-dbmail-07.png)

## 透過 TSQL 啟用 Databasebase Mail

### Database Mail XP 伺服器組態選項

除了在精靈中，設定啟用 DatabaseMail 外，也可以透過 DatabaseMail XP 選項，來啟用 Database Mail。可能的值為： 

- 0 表示無法使用 Database Mail (預設值)。
- 1 表示可使用 Database Mail

以下步驟示範如何使用 TSQL 啟用 Database Mail.
```sql
USE master
GO

--開啟進階選項
EXEC sp_configure 'show advanced option', '1';
RECONFIGURE
GO

--設定啟用Database Mail
EXEC sp_configure 'Database Mail XPs','1'
RECONFIGURE
GO

--檢視設定是否成功
EXEC sp_configure
GO
```

### 使用 TSQL 建立 Database Mail 帳戶

除了透過精靈設定 Database Mail 帳戶外，也可以使用系統預存程序來建立 Database Mail 設定。相關預存程序如下：

- [sysmail_add_account_sp](http://technet.microsoft.com/zh-tw/library/ms182804.aspx) ：建立新的 Database Mail 帳戶來保留 SMTP 帳戶的相關資訊。
- [sysmail_add_profile_sp](http://technet.microsoft.com/zh-tw/library/ms188058.aspx) ：建立新的 Database Mail 設定檔。
- [sysmail_add_profileaccount_sp](http://technet.microsoft.com/zh-tw/library/ms186258.aspx) ：將 Database Mail 帳戶加入 Database Mail 設定檔中。
- [sysmail_help_profileaccount_sp](http://technet.microsoft.com/zh-tw/library/ms177575.aspx) ：列出與 Database Mail 設定檔相關聯的帳戶。
- [sysmail_add_principalprofile_sp](http://technet.microsoft.com/zh-tw/library/ms187911.aspx) ：授予資料庫使用者或角色使用 Database Mail 設定檔的權限。

#### 建立帳號
```sql
EXECUTE msdb.dbo.sysmail_add_account_sp
@account_name = 'My DBMail Account 1',
@description = 'Desciption of My DBMail Account 1',
@email_address = 'vxxxxxxo@gmail.com',
@mailserver_name = 'smtp.gmail.com',
@port = '25',
@username = 'vxxxxxxo@gmail.com',
@password = 'XXXXXXXX',
@enable_ssl = '1'
;
```

#### 建立設定檔
```sql
EXECUTE  msdb.dbo.sysmail_add_profile_sp 
@profile_name = 'My DBMail Profile 1',
@description = 'Desciption' 
;
```

#### 指定設定檔的帳號
```sql
EXECUTE  msdb.dbo.sysmail_add_profileaccount_sp   
@profile_name = 'My DBMail Profile 1',
@account_name = 'My DBMail Account 1',
@sequence_number = 1
; 
```

#### 授予設定檔的存取權限
```sql
EXECUTE msdb.dbo.sysmail_add_principalprofile_sp
@profile_name = 'My DBMail Profile 1',
@principal_name = 'DBMail Users',   --指定 DBMail Users 角色才可以使用 My DBMail Profile 1 這個設定檔
@is_default = 1 
;
```

#### 查詢設定檔
```sql
EXECUTE msdb.dbo.sysmail_help_profileaccount_sp                     --回傳全部設定檔
EXECUTE msdb.dbo.sysmail_help_profileaccount_sp @profile_id = 4 ;   --回傳指定設定檔
```

## 傳送測試電子郵件

你可以使用「傳送測試電子郵件」來檢驗 Databasebase Mail 功能

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhn4kkL115ishA-Lp1DBcd-OOrwnEEnn37YW1XeYZh8Vmldyx5Cna9FiopIYD15fODSg7iuRetrnhplGu8oEha4l7ppwPRimv1G2s1BBJ6cn6K3gQuwartz1GzBCbXn0x31Ns9yiFJezvY/s0/sql-dbmail-08.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiBQ2RVpOZifXC43If3PsrFpx-2sGqsWvPGWpCPoiua8PqzsY4_wQya56_BpaAsEekYs5h7AqnnQgbktBWOMFXshyphenhyphend_OUcj3A8NZftua5nYJlDUYkSXIj0Az151h-c5YwySc9j6KqG_rzI/s0/sql-dbmail-10.png)

# Databasebase Mail 安全性

## sp\_send\_dbmail

要發送電子郵件，必須使用系統預存程序 [sp_send_dbmail](http://technet.microsoft.com/zh-tw/library/ms190307.aspx) 。  它可以用來發送電子郵件訊息，此郵件內容也可以包含附件檔案或者查詢結果。  執行後，郵件會被放在 Database Mail 佇列，並傳回一個 mailitem\_id 。  

在發送郵件時，有一些安全性的議題要知道：

- Databasebase Mail 功能必須啟用後才可以使用，預設是不啟用的。
- 若要傳送 Database Mail，使用者必須是 msdb 資料庫中 **DatabaseMailUserRole** 資料庫角色的成員。也就是要執行 [sp_send_dbmail](http://technet.microsoft.com/zh-tw/library/ms190307.aspx) 必須是 **DatabaseMailUserRole** 成員才行。
- **使用附件**：若要寄送含附件的郵件，則 SQL Server 引擎帳戶應具備存取包含該檔案之資料夾的權限。
- **附件大小**：寄送附件郵件，有預設的大小限制，你可以使用 sysmail\_configure\_sp 預存程序來變更這項限制。
- **禁止的副檔名**：你可以使用 sysmail\_configure\_sp 維護一個禁止的副檔名清單，那麼符合清單中的檔案，就無法附加在郵件中。
- 要執行 [sp_send_dbmail](http://technet.microsoft.com/zh-tw/library/ms190307.aspx) 預存程序

## 設定檔

使用 Database Mail 傳送電子郵件時必須指定**設定檔**，而不是直接使用**帳戶**。  一個**設定檔**可以包含多個**帳戶**，設定檔會提供自動容錯移轉功能。  所以如果第一部 SMTP 沒有回應，Database Mail 就會自動將郵件傳送至另一部 SMTP。  

這個**設定檔**是有安全性的，它可設定成公用或私人的。

- 若是**公用設定檔**，則 msdb 資料庫中之 **DatabaseMailUserRole** 資料庫角色的所有成員都可以存取。
- 若是**私用設定檔**，則只容許特定的資料庫使用者、角色才可以存取。（這是預設值）

也就是說，若新增一個設定檔，它會是私人設定檔。

# 發送Email範例

## 發送文字Email

使用 SSMS

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgRIjKSFe6YiD0bumSHjEePvE6olAjdUB53k59MRQ3tVTP-LPsjIyIiI9JxzXMOfym_in-UBOFfXZr_7oc0p56kGgg3Gov5efnTPudNeGsHns7GLqP9clVh4AoIYlsQAfprqTlPJ7VM-iM/s0/sql-dbmail-05.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgrS3wwiPaOzvSkOYLCBOGTI2fYaxcOyqeCjxENU8H3KbUJXAYqymSeJR-9aUT9l-AMOYUrUPinj7N24LbnesueRkuZ4zF-1IIxmAik1YaUYLvf3TWVEVjsyQ1-DE4rGhfGZdBUyy3fd6E/s0/sql-dbmail-06.png)

使用 TSQL
```sql
EXEC msdb.dbo.sp_send_dbmail 
@profile_name='My DBMail Profile 1',
@recipients='vxxxxxxo@hotmail.com',
@subject='測試 Database Mail',
@body='DataBase Mail 功能測試 From SQL2008',
@body_format = 'TEXT' ;  
```

[@body\_format]選項可用來指定郵件格式，若不指定，預設值是 TEXT。

## 發送 HTML Email
```sql
EXEC sp_send_dbmail 
@profile_name='My DBMail Profile 1',
@recipients='vxxxxxxo@hotmail.com',
@subject='測試 Database Mail',
@body='<b>DataBase Mail 功能測試 From SQL2008</b>',
@body_format = 'HTML' ;
```

## 發送含附件的Email
```sql
EXEC sp_send_dbmail 
@profile_name='My DBMail Profile 1',
@recipients='vxxxxxxo@hotmail.com',
@subject='測試 Database Mail',
@body='DataBase Mail 功能測試 From SQL2008',
@file_attachments='C:\cloud.jpg'                --指定附件
```

## 發送查詢結果Email

只要給定 @query 參數值，就可以將 SQL 傳送給 Database Mail 去執行，它會將查詢結果以文字形式直接加在郵件後面。
```sql
EXEC sp_send_dbmail 
@profile_name='My DBMail Profile 1',
@recipients='vxxxxxxo@hotmail.com',
@subject='測試 Database Mail',
@body='DataBase Mail 功能測試 From SQL2008',
@query = 'Select Top 10 \* from TestDB2.dbo.Products'
```

## 將查詢結果以附件寄送
```sql
EXEC sp_send_dbmail 
@profile_name='My DBMail Profile 1',
@recipients='vxxxxxxo@hotmail.com',
@subject='測試 Database Mail',
@body='DataBase Mail 功能測試 From SQL2008',
@query = 'Select Top 10 \* from TestDB2.dbo.Products',
@attach_query_result_as_file = 1 ;    
```

這個執行結果，它會將查詢結果以文字檔格式放在附件中寄出。

## 將查詢結果轉 csv 放在附件寄送

上一個例子中，該附件格式為固定欄寬的文字格，如果想將查詢結果以 CSV 格式寄出，可參考以下做法：
```sql
EXEC sp_send_dbmail 
@profile_name='My DBMail Profile 1',
@recipients='vxxxxxxo@hotmail.com',
@subject='測試 Database Mail',
@body='DataBase Mail 功能測試 From SQL2008',
@query = 'Select Top 10 \* from TestDB2.dbo.Products',
@attach_query_result_as_file = 1,  
@query_result_separator = ',',
@query_attachment_filename = 'result.csv'
```

另外一個做法是使用 [bcp](http://technet.microsoft.com/zh-tw/library/ms162802.aspx) 將資料匯出成 csv 檔，將使用附加檔案方式寄出。  詳結做法請參考積沙成塔部落格上的這篇介紹：[Server 2012 RC0 Database Mail簡介](http://vito-note.blogspot.com/2013/10/sql-server-agent.html)。

# Database Mail 記錄

Database Mail 會將執行記錄儲存在 msdb 資料庫與 Microsoft Windows 應用程式事件記錄檔中。  

## Database Mail 服務記錄

由 SSMS 中，你可以執行以下功能，查看 Database Mail 記錄：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgB18WZsiDMqqZRgvxDd5lOLTtG4m7bCcSc2h1hRPxNGEoXnLRQrG45TQi_kzFRISxd3YIo4y8JuT1MIrCwFg9I75HRcK1j7xmRtzjXCm1nsAsPZlnJkcTRS-tZ5TYS7dT0TOJ4N8d41-c/s0/sql-dbmail-log-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgGO2KtQjUFVdj27dRZC0ktTJBXzlL9gtE-3T3HT1gzR2EORrXazUpDnpmdk-qUUwJ7UB3zQimT3YthIwMelqPuGPOScCxENTRzZNejDvf3HzWROWMGE23aeXE93R0AGm3zViIoHkC7hrE/s0/sql-dbmail-log-2.png)

或使用 TSQL 查看 msdb.dbo.sysmail\_log 系統資料表：  
```sql
USE msdb
GO
SELECT \* FROM sysmail_log
```

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh9C8In7567-9IECt5R71iik0UPTBrXw5cCiGbMC55rRSpF5t_CgDZXc4Fr_PynWIWEuhFpUaNs8ZHT19VSSu6iDJmgWPKzqYGIUT3ix_-A36TJedwHTI53g6IfwzZAuSmNDM6OZEOpQg0/s800/sql-dbmail-log-3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh9C8In7567-9IECt5R71iik0UPTBrXw5cCiGbMC55rRSpF5t_CgDZXc4Fr_PynWIWEuhFpUaNs8ZHT19VSSu6iDJmgWPKzqYGIUT3ix_-A36TJedwHTI53g6IfwzZAuSmNDM6OZEOpQg0/s0/sql-dbmail-log-3.png)

其他與 database mail 相關的檢視表：（[Database Mail Views (Transact-SQL)](http://technet.microsoft.com/en-us/library/ms188023.aspx)）  
```sql
SELECT \* FROM msdb.dbo.sysmail_allitems
SELECT \* FROM msdb.dbo.sysmail_sentitems 
SELECT \* FROM msdb.dbo.sysmail_faileditems
SELECT \* FROM msdb.dbo.sysmail_unsentitems
```

## 查看傳送成功記錄

由 msdb 中的系統資料表 [sysmail_sentitems](http://technet.microsoft.com/zh-tw/library/ms174372.aspx) 可以查看傳送成功的訊息。
```sql
select recipients, subject, body, body_format, sent_date, sent_status
from sysmail_sentitems
```

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiD9kLFUUAodompFSpehszlxgzElmd9kVUyFl8gLLYhHJ58ft9BRFoHTLMy_UVVzeFcN6PnKua9TQXL3_BE0O42tNZO7m7Ya783k2ZCjAq2SOh_9ogZIZs_X4IirVTfPdTMo0-vODO1OvY/s800/sql-dbmail-log-4.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiD9kLFUUAodompFSpehszlxgzElmd9kVUyFl8gLLYhHJ58ft9BRFoHTLMy_UVVzeFcN6PnKua9TQXL3_BE0O42tNZO7m7Ya783k2ZCjAq2SOh_9ogZIZs_X4IirVTfPdTMo0-vODO1OvY/s0/sql-dbmail-log-4.png)

# 在 SQL Server Agent 中使用 Database Mail

若希望 SQL Server Agent 能夠使用 Database Mail 來傳送警示通知，則必須先啟用 SQL Server Agent 的郵件設定檔。  警示通知是以[操作員]（Operator）為傳送對象，若要透過電子郵件來傳送通知，就必須先設定好操作員的電子郵件。  

#### 啟用 SQL Server Agent 的郵件設定檔

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhBJYkKTPLaebt2YjYvLsCp30n2l3usF__SEJelas2U7PoRvtePTeeifqXVL9NuCzKVXHqTdoW8kzgR9PF-ZT8E6y3H_PcRf1fD4i_fwsAiBZeg6juRo_l5hTxUyXm2SMOxujYqsCLU0YI/s0/sql-dbmail-8.png)

**注意**，這個設定完成後，必須重啟SQL Sserver Agent，因為 SQL Server Agent 會快取設定檔的資訊，所以只要設定檔有變更就得重啟 SQL Server Agent 才能生效。  

#### 新增操作員

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEikh4qDPyplE79j9I2l12RI9bH10B0vlFYaj_zAa03sABhKRllcF9nivS0FiPe7lnI1pMalRxAHjv1CP8mmEdYO4azFa2PFKMuW3CIQADpgjAkisGu-8CVj1EDtr4hBa670RPYBaVtep-E/s0/sql-dbmail-11.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhs6bnshDfUtAYjHOgw6ki8c9u5iW2nLx-sXNpwYYE1oGEX2cDfd_C1D_iXSm36_azqhn69WF52DK8M2uKdoce_g_0ZG7dt41b73rhXnMlrE-B_LNNEWwJa_6vNfPSySvO2nzsJxGfyqaM/s0/sql-dbmail-12.png)

#### 設定作業的通知對象

開啟作業的[屬性]設定視窗，並在[通知]頁籤中，指定作業完成時，要使用電子郵件執行通知。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiB9JOwzJ9_5JZHhkNqr4B0ZfnIyDMWW7AGJQsFuAJtoUnskKMY5yDLYmF9K4Mpn-fYGK8GwS_0v1SutkcIeSTfsQPFD60xoMccXXRRFqUUIecFPcfsbdOFbHKnUa4l0bsyDX3Nsipyr4s/s0/sql-dbmail-13.png)

完成以上設定，當作業完成時，指定的作業員就會收到電子郵件通知。
## 參考資料  

- [Database Mail](http://technet.microsoft.com/zh-tw/library/ms189635%28SQL.110%29.aspx)
- [Database Mail 設定檔](http://technet.microsoft.com/zh-tw/library/ms189879%28v=sql.105%29.aspx)
- [設定 SQL Server Agent Mail 使用 Database Mail](http://technet.microsoft.com/zh-tw/library/ms186358.aspx)
- [Database Mail 架構](http://technet.microsoft.com/zh-tw/library/ms189635%28v=sql.105%29.aspx)