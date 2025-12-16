---
title: 排除安全性問題
layout: default
parent: Security
nav_order: 4
description: "排除安全性問題"
date: 2013-09-23
tags: [SQL,Security]
postid: "4936137713331919857"
---
資料庫狀況百百種，該如何對症下藥：  
若有人在固定伺服器角色中加入或刪除成員，你要使用什麼方法記錄到Application Log？Server Audit Specification  
若資料庫發生無法挽回的錯誤，你要使用什麼方法立即得到通知？Alert  
若資料庫發生死結，你要用什麼方法去錄製這個過程以便可以在另一台測試機重現？SQL Profiler  
你要使用什麼方法防止有人將稽核停掉？Policy  

# 帳號驗證問題

## 啟用強制執行密碼原則

當 SQL Login 的「強制執行密碼原則」 (Enforce password policy) 被設定成啟用時，  該 SQL Server 帳戶就會依據「本機安全性原則」中的「帳戶鎖定原則」去限制帳戶**登入的次數(Lockout Threshold)**與**鎖定的時間(Lockout Duration)**，如下圖所示：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjjYmT0_inUASlKNX30a4s4OFf5fZRfxgxohY2tvMUJWExdDPyiXdiuG2eZixHE29ceTOCIvxu9eBhGy7KZYDwvdZ2AglXwWvsn-Afn8NAz2cLkO5KeIC34egsRcE6FXV6vMq3ZBWQYBT8/s0/sql-enforce-pw-policy.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEikJRq5EIc3-KR_Hy_Wp_Xo0qTyVzoI6ikfle5VL6pb15Yx0_LzK6mDKq0IPVQTcMPLbIQOQ_0O22v8Ay183f6oGSxlHp5Sn-mw-0mNFbBQ0K0E-GuwaLOBtxVovAkundewIJMVqdZgero/s0/sql-security-ploicy.png)

如果登入失敗次數超過設定值，帳號就會被鎖定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi1rYlMzB3FNMaDLK-jdzR-_fOfnfMxZmtFgfjw-D8fS_pWOI3fOL6CsuskrUkNj75WlTHRBr1EUd8TZq-3Svso9U03ytWBjqL7NxqndnB-O3EdDcg4uPvtHwumCyc-jv2oJtKUzzym78g/s0/sql-account-locked.png)

## 查看帳號狀態

管理員可使用 SSMS 查看帳號狀態：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiGxnAARAbqJyqc5zA87haerxeJigV-KQQn9ZbGHxJGnJwJ-5USjQ8Vul1-i_aFRLSVqg9am8ppOhlslFwP4cd1GpsfzrymS73nHj1PnCigVqhcnn6HGtuPEplgzo1ikzqsCpz4GCZSOUE/s0/sql-account-status.png)

也可使用 sys.server\_principals 系統檢視查看帳號狀態：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEieCxlgy0dUnOfGPmNZA67VTbJCpOLFf4oEJMMwu1H_QV18i9SPOKz-hzqWpleymYbdCTNvmAPxYh6i2nGTnzT0KRd9rfWnrzGeH0nEMu7sKI6XB49XFVFIdItkPw17U6eXJYEr7D3R_pU/s0/sql-server_principals.png)

## 解除帳號鎖定

如果只是使用者不小心的行為，只要在鎖定時間過期之後，即可再次嘗試登入。

如果是忘了密碼，就必須由管理員重設密碼，才可解除鎖定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhP9LbTYjNYtYXkeFC3DDgzaDBViVOl8s7ZH0FXesqxHwxj3o3CrBNtdErCjKlqWoB8uKK9miEa_PSAf8D0B-n6i4FtAki1SzIa8VEN6q6KvZvaWHyF5_PQn-6o1Hk75kWCTw4ti8KRQmU/s0/sql-reset-pwd.png)

或者你也可以使用 [ALTER LOGIN](http://msdn.microsoft.com/zh-tw/library/ms189828.aspx) 指令來重設帳號，以下這個設定為使用者必須先使用 abc123 登入，同時變更密碼設定。  而且這個帳戶將會繼續使用強制執行密碼原則。  
```sql
ALTER LOGIN [user1] WITH
DEFAULT_DATABASE = [master],
PASSWORD = 'abc123' MUST_CHANGE Unlock,
CHECK_POLICY = ON,
CHECK_EXPIRATION  = ON
```

# 連線問題

有時候客戶端在帳號密碼都正確情況下，一直無法連線成功，這時候可以檢查一下 Browse Service 是否有正常啟動。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiejeSUysdXhhhB4wuAzkfkMUtZsDZNNSOHucMDaw3plVUb9JNrCnpw-WyZtcG5EDiFEfd6eRocx0Zcx6lBMy9sbg1BlkIS9hfR-XUMSUSnHgphWyz9I4nCkm9eKOlx2F7_Klg91S8iofM/s0/sql-browse-service.png)

SQL Server Browser 是一支 Windows 服務程式。它會接聽客戶端對 SQL Server 的內送要求，並回應相關的 SQL Server 執行個體的資訊給客戶端。  

更多詳情可參考 MSDN：[SQL Server Browser 服務](http://technet.microsoft.com/zh-tw/library/ms181087%28v=sql.105%29.aspx)

# 憑證與金鑰問題（Certificates and Keys）

當使用憑證與金鑰時，有時會遇到突然無法登入的問題，通常這大都是憑證的有效期限問題。  

你可以使用以下系統檢視，來查詢相關資訊：  

- [sys.asymmetric_keys](http://msdn.microsoft.com/zh-tw/library/ms188399.aspx) ：傳回「非對稱金鑰」資訊
- [sys.certificates](http://msdn.microsoft.com/zh-tw/library/ms189774.aspx) ：傳回「憑證」資訊
- [sys.key_encryptions](http://msdn.microsoft.com/zh-tw/library/ms188334.aspx) ：傳回使用 [CREATE SYMMETRIC KEY](http://msdn.microsoft.com/zh-tw/library/ms188357.aspx) 建立「對稱金鑰」時，在 ENCRYPTION BY 子句使用的加密憑證資訊。
- [sys.symmetric_keys](http://msdn.microsoft.com/zh-tw/library/ms189446.aspx) ：傳回「對稱金鑰」資訊

更多詳情可參考 MSDN：[SQL Server 憑證與非對稱金鑰](http://technet.microsoft.com/zh-tw/library/bb895327.aspx)

# 端點問題（Endpoints）

- [sys.endpoints](http://technet.microsoft.com/zh-tw/library/ms189746.aspx) ：回傳所有端點資訊。
- [sys.http_endpoints](http://technet.microsoft.com/zh-tw/library/ms174371.aspx) ：回傳使用 HTTP 協定的端點資訊。
- [sys.tcp_endpoints](http://technet.microsoft.com/zh-tw/library/ms177572.aspx) ：回傳使用 TCP 協定的端點資訊。
- [sys.database_mirroring_endpoints](http://technet.microsoft.com/zh-tw/library/ms190278.aspx) ：回傳 SQL Server 2012 執行個體的資料庫鏡像端點。
- [sys.service_broker_endpoints](http://technet.microsoft.com/zh-tw/library/ms182747.aspx) ：回傳 Service Broker 端點資訊。

更多詳情可參考 MSDN：[端點目錄檢視 (Transact-SQL)](http://msdn.microsoft.com/zh-tw/library/ms180076.aspx)

# 與安全性相關的系統檢視

server-level

- [sys.server_permissions](http://msdn.microsoft.com/zh-tw/library/ms186260.aspx) ：
- [sys.server_principals](http://msdn.microsoft.com/zh-tw/library/ms188786.aspx) ：
- [sys.server_role_members](http://msdn.microsoft.com/zh-tw/library/ms190331.aspx) ：
- [sys.sql_logins](http://msdn.microsoft.com/zh-tw/library/ms174355.aspx) ：
- [sys.system_components_surface_area_configuration](http://msdn.microsoft.com/zh-tw/library/ms189471.aspx) ：

database-level

- [sys.database_permissions](http://msdn.microsoft.com/zh-tw/library/ms188367.aspx) ：
- [sys.database_principals](http://msdn.microsoft.com/zh-tw/library/ms187328.aspx) ：
- [sys.database_role_members](http://msdn.microsoft.com/zh-tw/library/ms189780.aspx) ：
- [sys.master_key_passwords](http://msdn.microsoft.com/zh-tw/library/ms188373.aspx) ：

# 啟動問題

## 以最低組態啟動 SQL Server

如果有組態上的問題，而無法啟動伺服器，您可以使用**最低組態啟動(minimal configuration mode)**選項來啟動 SQL Server 的執行個體。  此為啟動選項 -f。 以最低組態啟動 SQL Server 的執行個體，會自動將伺服器設定為單一使用者模式。

例如當 tempdb 所在的磁碟損毀導至系統無法啟動，則可以加入 /f  /T3608 的參數進入 minimal configuration mode 後，再使用 ALTER DATABASE 變更 tempdb 的存放路徑。

詳細做法可參考下面文章：

- [以最低組態啟動 SQL Server](http://msdn.microsoft.com/zh-tw/library/ms186400.aspx)
- [搬移系統資料庫](http://msdn.microsoft.com/zh-tw/library/ms345408.aspx)
- [Recovering TempDB](http://sql.richarddouglas.co.uk/archive/2010/08/recovering-tempdb.html#axzz2CH2IObU8)
## 參考資料  

- [安全性目錄檢視 (Transact-SQL)](http://technet.microsoft.com/zh-tw/library/ms178542.aspx)
- 
-