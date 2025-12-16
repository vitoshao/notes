---
title: SP、VIEW、FUNC 範本
layout: default
parent: StoredProc、Triggers、Views
nav_order: 8
description: "SP、VIEW、FUNC 範本"
date: 2013-06-27
tags: [SQL,StoredProc、Triggers、Views]
postid: "920282745960286104"
---
| Stored Procedure | Function | View |
| --- | --- | --- |
| ```sql
--=================================<br>-- 使用 RETURN 回傳一個純量值<br>--=================================<br><br>CREATE PROCEDURE uspGetProductPrice<br>    @ProductID as int<br>AS<br>[BEGIN]
DECLARE @Price money;<br>
SELECT @Price=Price
FROM Product 
WHERE ProductID= @ProductID<br>
RETURN @Price;  --必要<br>[END]<br>```  ```sql
declare @Price1 int<br>exec @Price1 = uspGetProductPrice 2<br>select @Price1<br>``` | ```sql
--=================================<br>-- 回傳純量值<br>--=================================<br><br>CREATE FUNCTION funGetProductPrice ( 
@ProductID as int <br>)<br>RETURNS money<br>[AS]<br>BEGIN
DECLARE @Price money;<br>
SELECT @Price=Price
FROM Products
WHERE ProductID= @ProductID<br>
RETURN @Price<br>END<br>```  ```sql
select dbo.funGetProductPrice (4)<br><br>declare @Price1 int<br>set @Price1 = dbo.funGetProductPrice (4)<br>select @Price1<br>``` |  |
| ```sql
--=================================<br>-- 使用 OUTPUT 參數回傳<br>--=================================<br><br>CREATE PROCEDURE uspGetProductPrice<br>    @ProductID as int,<br>    @Price int OUTPUT<br>AS<br>[BEGIN]
SELECT @Price=Price
FROM Product 
WHERE ProductID= @ProductID<br>
RETURN --可省略<br>[END]<br>```  ```sql
declare @Price int;<br>exec uspGetProductPrice 2, @Price OUTPUT<br>select @Price<br>``` | ```sql
--=================================<br>-- 回傳資料表 (使用multi-statement)<br>--=================================<br><br>CREATE FUNCTION funGetProduct_M ( 
@ProductID as int <br>)<br>RETURNS @TmpTable TABLE(
ProductID int,
ProductName nvarchar(255),
Price int<br>)<br>[AS]<br>BEGIN
INSERT @TmpTable
SELECT ProductID, ProductName, Price
FROM Product 
WHERE ProductID= @ProductID<br>
RETURN --必要 <br>END<br>```  ```sql
select \* from dbo.funGetProduct_M(4)<br>``` |  |
| ```sql
--=================================<br>-- 使用 SELECT 回傳一個資料表<br>--=================================<br><br>CREATE PROCEDURE uspGetProduct<br>    @ProductID int   <br>AS<br>[BEGIN] 
SELECT ProductID, ProductName, Price
FROM Products
WHERE ProductID= @ProductID<br>
RETURN; --可省略<br>[END]<br>```  ```sql
exec uspGetProduct 4<br>``` | ```sql
--=================================<br>-- 回傳資料表<br>--=================================<br><br>CREATE FUNCTION funGetProduct_S ( 
@ProductID as int <br>)<br>RETURNS TABLE<br>[AS]<br>RETURN <br>(
SELECT ProductID, ProductName, Price
FROM Products
WHERE ProductID= @ProductID<br>)<br>```  ```sql
select \* from dbo.funGetProduct_S(4)<br>``` | ```sql
CREATE VIEW dbo.vwCheapProduct<br>AS
SELECT ProductID,ProductName,Price
FROM dbo.Products
WHERE Price < 10<br>```  ```sql
select \* from vwCheapProduct<br>``` |