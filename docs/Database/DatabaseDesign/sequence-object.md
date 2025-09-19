---
title: 順序物件
layout: default
parent: Database Design
nav_order: 3
description: "順序物件"
date: 2013-06-21
tags: [SQL,NewFeature in SQL2012]
postid: "4979987942084436372"
---
[CREATE SEQUENCE](http://msdn.microsoft.com/zh-tw/library/ff878091.aspx) 可以用來建立順序物件（sequence object ）。  順序物件是一個由使用者定義的物件，它會根據定義規格產生數值序列。  不同於新增資料時使用的**識別資料行**（identity），應用程式可以藉由呼叫 [NEXT VALUE FOR](http://msdn.microsoft.com/zh-tw/library/ff878370.aspx) 函數取得下一個序號，而不需要插入資料列。  

**語法**

```sql
CREATE SEQUENCE [schema_name . ] sequence_name
[ AS [ built_in_integer_type | user-defined_integer_type ] ]
[ START WITH <constant> ]
[ INCREMENT BY <constant> ]
[ { MINVALUE [ <constant> ] } | { NO MINVALUE } ]
[ { MAXVALUE [ <constant> ] } | { NO MAXVALUE } ]
[ CYCLE | { NO CYCLE } ]
[ { CACHE [ <constant> ] } | { NO CACHE } ]
[ ; ]
```

## 建立順序物件

### 例一：使用語法建立
```sql
CREATE SEQUENCE MySeqNo
AS int
START WITH 1
INCREMENT BY 1
MINVALUE 1
MAXVALUE 100
NO CYCLE
CACHE;
```

### 例二：使用介面建立

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiAMtqauBxjmldZY_fe1i-fhrnpn_yQZblxAAk6W5cvjqyeP169NJs9jNn4DrIpNJYOM_cCc0c6ttO-dQ3HWQKO1Nw6NwWk8w8ErYUTHLJwwAQ4ZNfVw-wbMmhM_md0FdGoIcbPVf9O2ns/s0/sql-sequence-1.jpg)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgAduJiwg9CA-5D3wyRTTGQ2ytr5Pe2HIsVl-pO_AkeQM4ugdRDdSlz87tc4XvlkRi0ArrYUIQ82ZUcEzT5qsCZnw4Z8HbBDy0j8VWm4pRnDLxfAuHdW9z6yZ44OY3ZrU62P-iHrCL6heM/s0/sql-sequence-2.jpg)

## 使用順序物件

順序物件可以使用在以下幾種狀況：

### 1. 重設序號

下列指令，可以用來取得序號、重置序號、設定自動循環
```sql
--重設 MySeqNo ,由10開始

ALTER SEQUENCE MySeqNo   
RESTART WITH 10

--取得流水號，預計應該是會得到 10

SELECT NEXT VALUE FOR MySeqNo

--因為設定為不循環，所以，若號碼超過100將發生錯誤

--設定為自動循環

ALTER SEQUENCE MySeqNo  
CYCLE

--因為設定為自動循環，取得流水號，預計應該是會得到 1

SELECT NEXT VALUE FOR MySeqNo

--在 INSERT 命令中直接取得流水號

INSERT tmpTable (ID,Name) VALUES ( NEXT VALUE FOR MySeqNo ,'test name' );
```

### 2. 在 INSERT 指令中使用順序物件

若要在新增陳述式中，直接取得流水號，你可以這麼做
```sql
INSERT tmpTable (ID,Name) VALUES ( NEXT VALUE FOR MySeqNo ,'test name' );
```

### 3. 將 SEQUENCE 當做欄位預設值

流水號物件也可以當做欄位的預設值，你只要加入一個預設值的條作約束即可。
```sql
ALTER TABLE [dbo].[tmpTable] 
ADD CONSTRAINT [DF_tmpTable_ID] DEFAULT (NEXT VALUE FOR [dbo].[MySeqNo]) FOR [ID]
```

### 4. 在排序的結果集中，加入順序值

你也可以透過 NEXT VALUE FOR 的 OVER 子句，設定 sequence object 在回傳的結果集中，依指定的特定欄位排序後再給值。  
```sql
--在回傳結果集中套用順序值
SELECT NEXT VALUE FOR MySeqNum as SeqNum, ProductID, ProductName 
FROM Products
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiei1MKBr-OzY02cPImjVgupEoh-XB4Ec9_B1N0pI1Lxe-uD9623bPOM9LoenXfqKEu9aHGnlGwrnD37juMIy6DKHj8xAQz6FfXKwjOORLquO2cuvG4N3sF3NoJQINr2-wd38Ujqly6EPM/s0/sql-next-value-for.png)
```sql
--在回傳結果集中，先排序再套用順序值
SELECT NEXT VALUE FOR MySeqNum OVER ( ORDER BY ProductID ) as SeqNum, ProductID, ProductName 
FROM Products
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhIrrzvGjGCUQnpd9IfocXAA8M7debiL7gWg7OZuV_g8GAHKq3UtQnv4ewa_DrXu06AkhykYNRsBpaEt9G89YqWOJByUFurOxiVVZz6zluwM4fZtHp3eG6J2_ZdKvh68meyQ-DL2g3nAiw/s0/sql-next-value-for-over.png)

## 使用順序的時機

雖然識別資料行（identity）也有自動建立順序數值的能力，但是下面幾種狀況下，應該使用 sequences ，而不是 識別資料行：  

- 你須要在 INSERT 前就取得下一號編號。
- 你須要在多個資料表中共用一個序列號。
- 你須要一次就取得多個序列號。
- 當序列號使用到達特定數字後，你須要重新開始序列號。
- 你需要依特定欄位排序再產生序列號。
- 你需要可以變更序列號的規格時，例如將規格由每次加１號改成每次加２號。