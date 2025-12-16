---
title: MSSQL中與 schema 相關的語法
layout: default
parent: SQL
nav_order: 1
description: "MSSQL中與 schema 相關的語法"
date: 2012-03-08
tags: [SQL,Query Advance]
postid: "1217865925086636589"
---
下表整理一些在SQL Server 中，和 schema 相關的一些系統資料表或檢視表。

| IDX | 系統表格 | 功能簡述 | 備註說明 |
| --- | --- | --- | --- |
| T | sys.tables | 記錄table name | ```sql
SELECT <br>T.object_id, T.name TableName, <br>C.column_id ColID, C.name AS ColName, C.system_type_id AS ColType,<br>IS_C.CHARACTER_MAXIMUM_LENGTH AS ColLen, <br>TY.name AS ColTypeName,<br>CASE WHEN C.is_nullable=1 THEN 'Y' ELSE '' END AS AllowNull, <br>Convert(nvarchar(500),D.definition) AS DefaultValue,<br>Convert(nvarchar(500),IsNull(E.value,'')) AS Description,<br>CASE WHEN IS_KEY.COLUMN_NAME IS NOT NULL THEN 'Y' ELSE '' END AS PK <br>FROM         <br>sys.tables T<br>INNER JOIN sys.columns C ON C.object_id = T.object_id<br>INNER JOIN sys.types TY ON TY.user_type_id = C.user_type_id<br>INNER JOIN INFORMATION_SCHEMA.COLUMNS IS_C ON IS_C.TABLE_Name=T.name AND IS_C.COLUMN_NAME = C.name<br>LEFT JOIN sys.default_constraints D ON D.parent_object_id=T.object_id AND D.parent_column_id = C.column_id  <br>LEFT JOIN sys.extended_properties E ON E.major_id = T.object_id AND E.minor_id=C.column_id<br>LEFT JOIN INFORMATION_SCHEMA.TABLE_CONSTRAINTS IS_TC ON IS_TC.TABLE_NAME=T.name AND IS_TC.CONSTRAINT_TYPE='PRIMARY KEY'<br>LEFT JOIN INFORMATION_SCHEMA.KEY_COLUMN_USAGE IS_KEY ON IS_TC.CONSTRAINT_NAME=IS_KEY.CONSTRAINT_NAME AND IS_KEY.COLUMN_NAME=C.name<br>WHERE 1=1<br>AND T.name='tblUser'<br>``` |
| C | sys.columns | 記錄column name |
| IC | INFORMATION\_SCHEMA.  &lt;br&gt;          COLUMNS | 記錄column types |
| EP | sys.extended\_properties | 記錄table/column description |
| DC | sys.default\_constraints | 記錄column預設值 |
| TP | sys.types | 系統欄位型態的代碼分類 |
| IS\_TC | INFORMATION\_SCHEMA.  &lt;br&gt;          TABLE\_CONSTRAINTS | 索引鍵主檔(PK,FK) | ```sql
SELECT CONSTRAINT_TYPE, IS_TC.CONSTRAINT_NAME, IS_KEY.COLUMN_NAME<br>FROM       INFORMATION_SCHEMA.TABLE_CONSTRAINTS IS_TC<br>INNER JOIN INFORMATION_SCHEMA.KEY_COLUMN_USAGE IS_KEY ON IS_TC.CONSTRAINT_NAME=IS_KEY.CONSTRAINT_NAME<br>WHERE IS_TC.TABLE_NAME='tblBook'<br>``` |
| IS\_KEY | INFORMATION\_SCHEMA.  &lt;br&gt;          KEY\_COLUMN\_USAGE | 索引鍵明細檔 |
| ID | sys.index | 索引主檔 | ```sql
SELECT ID.NAME index_name,ID.type_desc index_type,C.name index_column_name<br>FROM sys.tables T<br>INNER JOIN sys.indexes ID   ON ID.object_id = T.object_id<br>LEFT JOIN sys.index_columns ID_C ON ID_C.object_id = ID.object_id AND ID.index_id=ID_C.index_id<br>LEFT JOIN sys.columns C    ON C.object_id = T.object_id AND C.column_id = ID_C.column_id<br>WHERE T.name='tblUser'<br>ORDER BY ID.type,ID_C.index_id,ID_C.index_column_id<br>``` |
| ID\_C | sys.index\_columns | 索引明細檔 |

### 查詢某個 Table 的 Primary Key
```sql
Declare @Table varchar(100)
SET @Table = 'CodeB'

SELECT DISTINCT C1.CONSTRAINT_NAME [PK_NAME], 
STUFF((
SELECT ', ' + KCU.COLUMN_NAME
FROM INFORMATION_SCHEMA.TABLE_Constraints C2
INNER JOIN INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU ON C2.CONSTRAINT_NAME=KCU.CONSTRAINT_NAME
WHERE C2.CONSTRAINT_TYPE = 'PRIMARY KEY' AND C2.TABLE_NAME=C1.TABLE_NAME
FOR XML PATH('') 
), 1, 1, '') AS [PK_COLUMNs]
FROM INFORMATION_SCHEMA.TABLE_Constraints C1
WHERE C1.CONSTRAINT_TYPE = 'PRIMARY KEY' AND C1.TABLE_NAME=@Table

--PK_NAME               PK_COLUMNs
----------------------- -----------------
--PK_tblDocuments       DID
--PK_CodeB              CodeA, CodeB--
```

### 查詢某個 Table 的 Foreign Key
```sql
--查詢某個 Table 的 Foreign Key

Declare @Table varchar(100)
SET @Table = 'tblDocuments'

SELECT C.CONSTRAINT_TYPE [ConstraintType], C.CONSTRAINT_NAME [ConstraintName], 
C.TABLE_NAME [FKTable], KCU.COLUMN_NAME [FKColumn], PKT.TABLE_NAME [PKTable], PKT.COLUMN_NAME [PKColumn], R.UNIQUE_CONSTRAINT_NAME [PK_Name]
FROM INFORMATION_SCHEMA.TABLE_Constraints C
INNER JOIN INFORMATION_SCHEMA.Key_Column_Usage KCU ON C.CONSTRAINT_NAME=KCU.CONSTRAINT_NAME
INNER JOIN INFORMATION_SCHEMA.Referential_CONSTRAINTS R ON C.CONSTRAINT_NAME=R.CONSTRAINT_NAME
INNER JOIN (
SELECT C.CONSTRAINT_NAME, C.TABLE_NAME, KCU.COLUMN_NAME
FROM INFORMATION_SCHEMA.TABLE_Constraints C
INNER JOIN INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU ON C.CONSTRAINT_NAME=KCU.CONSTRAINT_NAME
WHERE C.CONSTRAINT_TYPE = 'PRIMARY KEY'
) PKT ON PKT.CONSTRAINT_NAME = R.UNIQUE_CONSTRAINT_NAME
WHERE C.TABLE_NAME=@Table
ORDER BY CONSTRAINT_TYPE DESC
```