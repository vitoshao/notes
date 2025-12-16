---
title: 資料庫快照集
layout: default
parent: Redundant
nav_order: 6
description: "資料庫快照集"
date: 2013-11-08
tags: [SQL,Redundant]
postid: "2637487017181765511"
---
#### 資料庫快照集（Snapshot)

- 資料庫快照集是來源資料庫的一個靜態檢視，並且唯讀。
- 必需與來源資料庫位在同一個SQL執行個體。
- Snapshot 會與來源資料庫維持交易的一致性。
- 當來源資料庫更新時，Snapshot 也會更新。
- 一個來源資料庫可以建立多個 Snapshot 。

# 概念

若對資料庫建立 Snapshot ， Snapshot 就相當於該資料庫的複本。  但是當建立 Snapshot 時，它只是一個空白檔案，沒有任何資料。  若來源資料庫的某個資料頁發生第一次修改，這個資料頁才會被複製到 Snapshot ，檔案大小也才會跟著成長。  Snapshot 的運作都是以**資料頁**為單位，若來源資料庫的資料頁，都沒任何異動，就不會被複製到 Snapshot 。  若有資料頁第一次被異動，才會將其原始資料複製到 Snapshot 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjxDMn23uzLV4sA1yQLxpbLft3-QLTZsXjjGpS94rKsJD4qbuUgUCEdOIiXTROlEXFKxWbT6gIfOoXSbdvFoGTcP6ddeAn-_5FGvaSXZpULzNbfUbrETMENTJbWlkde6YNOJ_WfLFiYTzM/s0/IC10718.gif)

這樣的機制有幾個特殊的優點，例如：  

例如，你可以在每個月最後一天結束時，建立一個快照集，之後再對快照集執行期末報表，你就可以取得不同的歷程資料。  

另外，像財務部門常常要進行月結作業，她們可能必須統計大量的資料，在統計過程中，可能會回存或修改部份的資料。  可是若最後財務部門最後發覺資料統計有誤，就必須復原重算，這時候用 Snapshot 就是最好的方法。  因為 Snapshot 保有被更改過的資料頁的原始資料，所以復原就只會復原這些資料頁。

# 如何建立資料庫快照集

下面範例示範，如何建立資料庫快照集，同時如何

建立資料庫快照集
```sql
--NAME：SQL Server 中所使用的邏輯名稱
--FILENAME：路徑和檔案名稱

CREATE DATABASE TestDB1_snapshot ON ( 
NAME = TestDB1, 
FILENAME ='D:\Database\testdb\TestDB1.mdf' 
) AS SNAPSHOT OF TestDB1;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjaSpemoFL6JFT5yuMEf6KkN8DLHk26OI6eAccakYqblgXPPf5mKcA_xZQ3s47KUkTTvXMf7sKZregK08NMqvxNZhw9mlhfOQYHqRAkZyGw-aDsaVSQJDbpWVy9ne8JWdkBEuQsjRGOvUQ/s0/sql-snapshot-create.png)

PS.安全性

- 能夠建立資料庫的任何使用者都可以建立資料庫快照集
- 不過若要建立鏡像資料庫的快照集，您必須是 sysadmin 固定伺服器角色的成員。

# 將資料庫還原成資料庫快照集

在建立資料庫快照集之後，若原始資料庫經由不當操作，造成資料遺失，此時你可以用以下方法還原

1.若某資料表整個被刪除了，你可以直接將資料由 snapshot 複製回來即可
```sql
insert Products([ProductID], [ProductName], [Color], [SupplierID], [CategoryID], [QuantityPerUnit], [UnitPrice], 
[UnitsInStock], [UnitsOnOrder], [ReorderLevel], [Discontinued]) 
select \* from TestDb1_snapshot.dbo.Products

--以下用法為：
--若資料表有 identity 欄位，若想在 insert 資料時，對該 identity 欄位指定特定的值，
--就必須啟用 IDENTITY_INSERT 屬性，且 insert 後面必須明確指定欄位名稱
SET IDENTITY_INSERT Products ON

insert Products([ProductID], [ProductName], [Color], [SupplierID], [CategoryID], [QuantityPerUnit], [UnitPrice], 
[UnitsInStock], [UnitsOnOrder], [ReorderLevel], [Discontinued]) 
select \* from TestDb1_snapshot.dbo.Products

SET IDENTITY_INSERT Products OFF
```

2.直接使用 Restore Database 指定
```sql
use master
RESTORE DATABASE TestDB1 FROM DATABASE_SNAPSHOT = 'TestDB1_snapshot'
```

# 檢視與刪除資料庫快照集

## 檢視資料庫快照集

若要查詢資料庫的快照集，可以查詢 sys.databases 這個系統檢視表，如果 source\_database\_id 欄位不是 NULL 則為資料庫快照集。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh_VPdz6Bz9JYdXDj1kTBN-3xVmKHIOGnKkYrUcKqlfHkpzaiMkXL0LKUnhLHWBo9Uk0XD7VvFWv5iqRuwHz2bRp7cwg4rGhyphenhyphenGivFkGE7sCg1cE4brW_FslzL_dCzBTvBTClzwMH3PF8vs/s0/sql-snapshot-view.png)

## 刪除資料庫快照集

對一個已建立 snapshot 的資料庫是不允許被刪除的，所以若要刪除一個含有 snapshot 的資料庫，就必須先刪除該資料庫的 snapshot 。  你可以利用上面檢視資料庫快照集的方法找到 snapshot ，再透過 drop database 指令刪除即可。  
```sql
drop database TestDB1_snapshot
```
## 參考資料  

- [資料庫快照集 (SQL Server)](http://technet.microsoft.com/zh-tw/library/ms175158.aspx)
- [SQL 2008 Snapshot 資料庫快照集 - 救援篇](http://www.dotblogs.com.tw/jerrymow/archive/2010/10/26/18587.aspx)
- [SQL 2008 Snapshot 資料庫快照集 - 原理篇](http://www.dotblogs.com.tw/jerrymow/archive/2010/10/26/18586.aspx)