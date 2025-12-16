---
title: 如何利用使用 trigger 記錄 Log
layout: default
parent: HowTo
nav_order: 5
description: "如何利用使用 trigger 記錄 Log"
date: 2013-03-12
tags: [SQL,HowTo]
postid: "7543574196218727140"
---
# 記錄 Log

## 1. 建立一個 log 資料表，用來儲存所有資料表的異動
```sql
CREATE TABLE [dbo].[Log](
	[Id] [int] IDENTITY(1,1) NOT NULL,
	[TableName] [varchar](50) NOT NULL,
	[Operation] [char](1) NOT NULL,
	[OriginalData] [xml] NOT NULL,
	[UpdatedTime] [datetime] NOT NULL,
CONSTRAINT [PK_Log] PRIMARY KEY CLUSTERED 
(
	[Id] ASC
)WITH (PAD_INDEX  = OFF, STATISTICS_NORECOMPUTE  = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS  = ON, ALLOW_PAGE_LOCKS  = ON) ON [PRIMARY]
) ON [PRIMARY]

GO

SET ANSI_PADDING ON
GO

ALTER TABLE [dbo].[Log] ADD  CONSTRAINT [DF_Log_UpdatedTime]  DEFAULT (getdate()) FOR [UpdatedTime]
GO
```

## 2. 替每個資料表加入以下 Trigger 
```sql
CREATE TRIGGER [dbo].[trUser_UPDATE_INSERT_DELETE] ON [dbo].tblUser AFTER UPDATE,INSERT,DELETE   
AS   
BEGIN       
	DECLARE @OriginalData XML      
	DECLARE @Operation char(1)   
	SET @Operation=''   

	IF EXISTS(SELECT 1 FROM inserted) AND NOT EXISTS(SELECT 1 FROM deleted)   
		SET @Operation = 'I'		--Insert   

	IF EXISTS(SELECT 1 FROM inserted) AND EXISTS(SELECT 1 FROM deleted)  
		SET @Operation = 'U'		--Update  

	IF NOT EXISTS(SELECT 1 FROM inserted) AND EXISTS(SELECT 1 FROM deleted)  
		SET @Operation = 'D'		--Delete  

	--UPDATE 事件只記錄更新前的舊資料  
	IF (@Operation = 'I')      
		SET @OriginalData=(SELECT * FROM inserted FOR XML RAW('tblUser'), ELEMENTS, ROOT)  
	ELSE  
		SET @OriginalData=(SELECT * FROM deleted FOR XML RAW('tblUser'), ELEMENTS, ROOT)  

	IF (@Operation <>'')  
	BEGIN  
		INSERT INTO Log(TableName, Operation, OriginalData) VALUES ('tblUser', @Operation, @OriginalData)   
	END  
END
```

# 讀取 Log

## 拆解 XML 欄位
```sql
DECLARE @OriginalData XML
SELECT @OriginalData = OriginalData FROM Log WHERE Id=1

DECLARE @hDoc AS INT
EXEC sp_xml_preparedocument @hDoc OUTPUT, @OriginalData

SELECT * FROM OPENXML(@hDoc, '/root/tblUser', 2)
WITH (
[UserID] [varchar](20) ,
[UserName] [nvarchar](50) ,
[Password] [nvarchar](50) ,
[Sex] [varchar](10) ,
[Birthday] [datetime] ,
[Photo] [nvarchar](255) ,
[CreatedTime] [datetime] ,
[CreatedUser] [varchar](20) ,
[RevisedTime] [datetime] ,
[RevisedUser] [varchar](20))

EXEC sp_xml_removedocument @hDoc
```

## 查詢 XML 欄位
```sql
SELECT *
FROM log 
WHERE OriginalData.value('(/root/tblUser/UserName)[1]', 'varchar(50)') = 'test1'

SELECT id, OriginalData.value('(/root/tblUser/UserName)[1]', 'varchar(50)') FROM log 
```
## 參考資料  

- [使用 Trigger 紀錄資料表的新增、修改、刪除的行為](http://www.dotblogs.com.tw/jameswu/archive/2009/07/23/9643.aspx)
- 
-