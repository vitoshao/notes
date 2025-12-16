---
title: 如何對資料庫中的資料加密
layout: default
parent: HowTo
nav_order: 3
description: "如何對資料庫中的資料加密"
date: 2012-05-03
tags: [SQL,HowTo]
postid: "6875581849386211374"
---
# 如何加密資料庫中的資料

若想要對SQL中的資料加密，從 SQL 2005版本開始，可以使用內建函式 [HashBytes](http://msdn.microsoft.com/zh-tw/library/ms174415.aspx) 來進行 Hash 的功能操作。其語法如下：

```sql
HashBytes ( '<algorithm>', { @input | 'input' } )
<algorithm>::= MD2 | MD4 | MD5 | SHA | SHA1
```

```
Declare @password nvarchar(50)
Set @password='abc123'
SELECT HashBytes('MD5',@password) AS MD5
--0x6e9b3a7620aaf77f362775150977eeb8
```

不過要注意的是，這個函式的回傳值是一個varbinary型別。  所以，若直接以這個回傳值回寫到資料庫去，將來也就無法直接使用。例如：  
```sql
UPDATE tblUser SET HashedPwd = HashBytes('MD5',Pwd)
```

其結果如下：

| Row | Pwd | HashedPwd |
| --- | --- | --- |
| 1 | 12345678 | 갩春『ま펗⺂흐 |
| 2 | abcd123 | 驴륾㤺ਲ䔯䲼줸꓆ |
| 3 | helloword | 㝪㕡䮱ꚯ䊏༭菵鳻 |

若要取回字串的型態，可以使用 sys.fn_VarBinToHexStr() 進行轉換，例如：

```sql
UPDATE tblUser SET HashedPwd = sys.fn_VarBinToHexStr(HashBytes('MD5',Pwd))
```

這樣子結果就正常了：

| Row | Pwd | HashedPwd |
| --- | --- | --- |
| 1 | 12345678 | 0x29ac25660e3078e87e3097d3822e50d7 |
| 2 | abcd123 | 0x749a7eb93a39320a2f45bc4c38c9c6a4 |
| 3 | helloword | 0x6a376135b14bafa68f422d0ff583fb9c |

若 HashBytes 用在加密使用者密碼，使用上必須注意以下事項：

1. 登入時的查詢語法，要有所變動，例如：  
```sql
declare @password nvarchar(50)
set @password='abcd123'

SELECT * FROM tblUser WHERE HashedPwd = sys.fn_VarBinToHexStr(HashBytes('MD5',@password))
```
2. 針對密碼欄位的變更，可以加一個觸發程序，將明碼的資料，透過 HashBytes 轉成暗碼的資料，這樣就不用變動到程式碼的部份。   ```sql
CREATE TRIGGER [dbo].[trg_EncryptPwd]
ON [dbo].[tblUser]
AFTER INSERT, UPDATE
AS 
BEGIN
	IF(UPDATE(HashedPwd))
	BEGIN
		UPDATE 
			tblUser 
		SET 
			tblUser.HashedPwd = sys.fn_VarBinToHexStr(hashbytes('MD5', cast(inserted.HashedPwd as nvarchar(50))))
		FROM 
			tblUser, inserted 
		WHERE 
			tblUser.UserID=inserted.UserID
	END
END	
```

## HashBytes 用法補充

1. 多個欄位 checksum

```sql
SELECT CustomerKey, 
	HashBytes('MD5', MaritalStatus + CountryRegion ) AS HashBytesValue
From DimCustomers
```

2. 不支援 NULL
```sql
SELECT CustomerKey, 
	HashBytes('MD5', 
	IsNull(MaritalStatus,'') + 
	IsNull(CountryRegion,'') ) AS HashBytesValue 
From DimCustomers
```

3. 只支援字串型別
```sql
SELECT CustomerKey, 
HashBytes('MD5', 
	IsNull(MaritalStatus,'') + 
	IsNull(CountryRegion,'') + 
	Cast(IsNull(BirthDate, '1911/1/1') as varchar) 
) AS SCD_Columns 
From DimCustomers
```