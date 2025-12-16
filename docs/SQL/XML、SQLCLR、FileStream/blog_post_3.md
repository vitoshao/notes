---
title: 使用 FileStream
layout: default
parent: XML、SQLCLR、FileStream
nav_order: 3
description: "使用 FileStream"
date: 2013-06-07
tags: [SQL,XML、SQLCLR、FileStream]
postid: "3030443522180229731"
---
有許多資料都是**未結構化**的，如文字檔、圖檔、影音檔。這些資料一般統稱**二進位大型物件**(BLOB)。  若將這些未結構化的資料儲存在資料庫外面，管理上往往造成許多困擾，所以在 SQL 裡，也提供了 **varbinary(max)** 型別來存放 BLOG 資料。  

在 SQL Server 2008 新增了 **FILESTREAM** 功能，可以讓 SQL Server 直接存取位於檔案系統上的非結構化資料。  它的方式是使用 **varbinary(max)** 型別，並整合 NTFS 檔案系統，當你存取 BLOB 資料時，實際上 SQL Server 是存取在檔案系統上資料，而不是存取位於資料庫中的資料。  

至於該使用哪一種來作業，來存取 BLOG 資料呢？MSDN 提到：  若儲存的物件平均大於 1 MB，請使用 **FILESTREAM varbinary(max)**；  如果是較小的物件，直接使用 **varbinary(max)** 將物件資料儲存在資料庫之中通常會提供較好的資料流處理效能。  

# 使用 CLR 存取檔案

以往要由資料庫存取檔案系統，必須透透 CLR 才可以，所以在認識 FILESTREAM varbinary(max) 之前，我們先來看看，如何在預存程序中，透過 CLR 去存取檔案系統。  

## 1. 撰寫 CLR 組件

建立儲存資料表
```sql
--
CREATE TABLE [dbo].[Documents](
[SerNum] [int] IDENTITY(1,1) NOT NULL,
[DocId] [uniqueidentifier] NOT NULL,
[FileData] [varbinary](max) NULL,
[FileName] [nvarchar](100) NULL,
[CreatedTime] [datetime] NULL,
)
```

撰寫組件
```c#
using System;
using System.Data;
using System.Data.SqlClient;
using System.Data.SqlTypes;
using Microsoft.SqlServer.Server;
using System.Collections;
using System.Collections.Generic;
using System.IO;

public partial class StoredProcedures
{
/// <summary>
/// 將指定的檔案存入資料庫
/// </summary>
/// <param name="filepath">要存入的檔案</param>
[Microsoft.SqlServer.Server.SqlProcedure()]
public static void WriteToDB_FromFile(string filepath)
{
FileInfo file = new FileInfo(filepath);
string filename = file.Name;
byte[] filedata = ReadFile(file.FullName);

string connstr = "context connection = true";
using (SqlConnection conn = new SqlConnection(connstr))
{
SqlCommand oCmd = conn.CreateCommand();
oCmd.CommandType = CommandType.Text;

oCmd.CommandText = "INSERT Documents(DocId, FileName, FileData) values (NEWID(), @FileName, @FileData)";
oCmd.Parameters.Add("@FileName", SqlDbType.NVarChar).Value = filename;
oCmd.Parameters.Add("@FileData", SqlDbType.VarBinary).Value = filedata;

try
{
conn.Open();
oCmd.ExecuteNonQuery();
}
catch (Exception ex)
{
throw new Exception(ex.Message);
}
}
}

/// <summary>
/// 將資料庫中的資料轉出到指定的資料夾
/// </summary>
/// <param name="DocId">要轉出的資料</param>
/// <param name="OutputFolder">指定的資料夾</param>
[Microsoft.SqlServer.Server.SqlProcedure()]
public static void WriteToFile_FromDB(string DocId, string OutputFolder)
{
if (!Directory.Exists(OutputFolder))
{
Directory.CreateDirectory(OutputFolder);     //若建立資料夾
}

string connstr = "context connection = true";
using (SqlConnection conn = new SqlConnection(connstr))
{
SqlCommand oCmd = conn.CreateCommand();
oCmd.CommandType = CommandType.Text;
oCmd.CommandText = "Select \* from Documents where DocId=@DocId";
oCmd.Parameters.Add("@DocId", SqlDbType.UniqueIdentifier).Value = new Guid(DocId);

try
{
conn.Open();
SqlDataReader reader = oCmd.ExecuteReader();

if (reader.Read())
{
byte[] filedata = reader["FileData"] as byte[];

string sFilePath = OutputFolder + @"\\" + reader["FileName"].ToString();
FileStream fs = new FileStream(sFilePath, FileMode.OpenOrCreate, FileAccess.Write);
fs.Write(filedata, 0, filedata.Length);
fs.Close();
}
}
catch (Exception ex)
{
Console.WriteLine(ex.Message);
}
}
}

public static byte[] ReadFile(string filePath)
{
FileStream stream = new FileStream(filePath, FileMode.Open, FileAccess.Read); 
BinaryReader reader = new BinaryReader(stream); 
byte[] data = reader.ReadBytes((int)stream.Length); 
reader.Close(); 
stream.Close();
return data;
}

};
```

## 2. 載入組件

### 2.1 啟用 CLR 功能

在 SQL Server 中， CLR 功能預設是停用的，若要啟用 CLR 整合，請使用 [sp_configure](http://msdn.microsoft.com/en-us/library/ms188787.aspx) 預存程序的 **clr enabled** 選項
```sql
sp_configure 'clr enabled', 1;
GO
RECONFIGURE;
GO
```

### 2.2 設定 TRUSTWORTHY 屬性

TRUSTWORTHY 屬性是用來指定 SQL Server 執行個體是否信任資料庫及其中的內容。
```sql
ALTER DATABASE Northwind SET TRUSTWORTHY ON;
```

### 2.3 設定安全性原則

使用 CLR 執行的程式，都得遵守它的安全模型，稱為程式碼存取安全性(Code Access Security, CAS)。  詳細介紹請參考 MSDN : [CLR 整合程式碼存取安全性](http://msdn.microsoft.com/zh-tw/library/ms345101.aspx)  

我們必須在建立組件時指定組件的權限集合。  有三個權限集合：SAFE、EXTERNAL\_ACCESS 、 UNSAFE ，預設值為 **SAFE** 。  因為我們要讀寫檔案，所以必須將權限集合設定成 **UNSAFE** 。  
```sql
CREATE ASSEMBLY MySqlLib
FROM 'D:\MySqlLib\bin\Debug\MySqlLib.dll'
WITH PERMISSION_SET = UNSAFE
```

## 3. 建立預存程序
```sql
--將檔案寫入DB的預存程序
CREATE PROCEDURE usp_WriteToDB_CLR
@FilePath nvarchar(255)
AS
EXTERNAL NAME MySqlLib.StoredProcedures.WriteToDB_FromFile;

--讀取DB資料再寫入檔案的預存程序
CREATE PROCEDURE usp_WriteToFile_CLR
@DocId nvarchar(100),
@DirPath nvarchar(255)
AS
EXTERNAL NAME MySqlLib.StoredProcedures.WriteToFile_FromDB;
```

## 4. 執行預存程序
```sql
--將圖檔寫入資料庫
EXEC usp_WriteToDB_CLR 'D:\CapturePic\sql-clr-udf-1.png';
EXEC usp_WriteToDB_CLR 'D:\CapturePic\sql-clr-udf-3.png';
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgEcGEarJnwUz7GP6B_spXDyLw1cugyJu-F-8Q7-yfm45bUdSJAQxNRcs2ML0MdqHFAwTmjOtUY4ZQnUi9gc7vcsLW5qXIL5yMqZ4MQyf3MSxpkycUSORla0FRyadvF-tUTJC1LozS7UUk/s850/sql-clr-sp-file.png)
```sql
--將資料庫中的資料轉出至指定目錄
EXEC usp_WriteToFile_CLR '54124D7E-8E45-4AB3-8853-B935B233F5D3','D:\Images'
```

# 使用 Filestream

前面提過，Filestream 是 varbinary(max) 的另一種型式，它會將資料直接使用檔案系統儲存，而不是放在資料庫之中。  

## 啟用 FileStream

要啟用 FileStream 功能，共必須完成以下二個步驟：

### 1. 啟用 FILESTREAM 的檔案 I/O 資料流存取

在 SQL 的組態管理員(SQL Server Configuration Manager)工具中，點選你的 SQL 執行個體。  並按一下 **[啟用 FILESTREAM 的檔案 I/O 資料流存取]** 和 **[允許遠端用戶端具有 FILESTREAM 資料的資料流存取權]**。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEghajMsK3qu8ooZQT2mR550Mm1EwN3VEL3IoPHU0Xq_ziy4maPBqhA8D5jpllaqsP9FRnPVoQsw2jTOx5-DpI6xPZezIe0tsfwbNq-pFxV8hMFM8CFoO3T1jiNnrLnUoAMOmr3nAyjz_9c/s0/sql-enable-filestream.png)

### 2. 設定 filestream\_access\_level
```sql
/\* filestream_access_level 選項值之說明： \*/ 
-- 0 ： 針對這個執行個體停用 FILESTREAM 支援。 
-- 1 ： 針對 Transact-SQL 存取啟用 FILESTREAM。 
-- 2 ： 針對 Transact-SQL 和 Win32 資料流存取啟用 FILESTREAM。 

EXEC sp_configure filestream_access_level, 2
RECONFIGURE
```

執行完以上步驟，再重新啟動 SQL Server 服務即可。

## 建立 FileGroup

要使用 FileStream ，除了必須啟用 FileStream 功能外，還得建立 FileGroup 。  

FileGroup 就是 FileStream 真正要存放資料的資料庫檔案(database file)，然而它實際上並不是一個檔案，而是一個目錄，用來存放 FileStream 的資料檔。  一個 FileGroup 只能有一個存放資料的資料庫檔案。  

下列程式碼示範如何建立 FileGroup  
```sql
ALTER DATABASE Northwind
ADD FILEGROUP fg_Documents
CONTAINS FILESTREAM;

ALTER DATABASE Northwind
ADD FILE
(
NAME = 'f_Documents'
,FILENAME = 'D:\Documents'
)
TO FILEGROUP fg_Documents;
```

建立完成後，你可以在資料庫的屬性視窗中看到其對於 FileSteram 的支援。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjphLdezLER5F-PbX3QYTIesxMfMFnU48-j43CxZQozRdd5Mh-bpPrlCscIPyhD8lzELlpWSgB2wSgSzQpYlzRxCJHtpy2brdTjneZn2ewmUOkHRHttUrF7xN8vDfY7sHk9J3IBxr5eqZ8/s850/sql-enable-filestream-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5JIrfnc1p4-cccz5DzpF7g_rcnTKhvOT2uywADOEYwrTvfCAgvlVHVOM_gdjtmHRH2uZlj6gn8mChGfRBJIPF9alkI8Z8ItXycxIlxQ7kxH9cw-500e_dMj_MZxfWBnpaU0M7GpIzCqs/s0/sql-enable-filestream-2.png)

同時你也可以檔案系統中看到系統為你建立的目錄。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhI5clbbdISeG-csUah3hyphenhyphenHwrEr8MToz0VmUuUmVaUSDfcJykvMxVDxmo649rclHnCJF9nVP24Y_RU8buRmKhsM1uLg73kj9YzB5CJ1uc7UdE7nnhmy_2A8KEZex4TEQvWne9FtRouEa0w/s0/sql-enable-filestream-4.png)

- The **$FSLOG** directory acts as the Filestream data's transaction log.
- The **Filestream.hdr** file stores metadata about the Filestream filegroup.
- All other directories with GUID names, such as 09A42544-450A-4932-B25F-5E33F117C179, are the directories that store the actual data.

若要移除 FILEGROUP ，請參考以下步驟
```sql
ALTER DATABASE [Northwind] REMOVE FILE [f_Documents]
GO

ALTER DATABASE [Northwind] REMOVE FILEGROUP [fg_Documents]
GO
```

## 建立 Table

When the filegroup has been added, varbinary(max) Filestream columns can be created.  For a table to contain Filestream columns, it must have a **uniqueidentifier** column marked with the property **ROWGUIDCOL** and having a **unique** constraint defined on it.   The following code sample shows how to add a ROWGUIDCOL to the FileDocuments table:  

要使用 FILESTREAM 資料行，該資料表必須要有一個欄位，符合以下屬性：  

- 非 NULL
- 唯一
- 使用 ROWGUID 型別
- 具有 ROWGUIDCOL 屬性
```sql
CREATE TABLE [dbo].[Documents](
[SerNum] [int] IDENTITY(1,1) NOT NULL,
[DocId] [uniqueidentifier] ROWGUIDCOL NOT NULL UNIQUE,
[FileData] [varbinary](max) FILESTREAM NOT NULL,
[OriFileName] [nvarchar](100) NULL,
[CreatedTime] [datetime] NULL,
)
```
## 參考資料  

- [啟用 CLR 整合](http://msdn.microsoft.com/zh-tw/library/ms131048.aspx)
- [FILESTREAM (SQL Server)](http://msdn.microsoft.com/zh-tw/library/gg471497.aspx)
- [CLR 整合程式碼存取安全性](http://msdn.microsoft.com/zh-tw/library/ms345101.aspx)
- [啟用及設定 FILESTREAM](http://technet.microsoft.com/zh-tw/library/cc645923.aspx)