---
title: 精確或模糊搜尋
layout: default
parent: Database Design
nav_order: 5
description: "全文檢索查詢-精確或模糊搜尋"
date: 2013-06-14
tags: [SQL,FullTextSerach、ServiceBroker]
postid: "5335859655789397"
---

# CONTAINS

這個 [CONTAINS](http://msdn.microsoft.com/zh-tw/library/ms187787.aspx) 述詞必須用於 WHERE 子句中，可用來對全文檢索進行精確或模糊搜尋。它可以搜尋以下項目：  

- 精確比對字詞
- 比對同義字(synonym)，例如：teacher, educator, professor.
- 比對字形變化(Inflectional Form )，例如：(foot, feet), (run, ran, running)
- 接近另一個單字的單字。
- 單字或片語的前置詞。
```sql
CONTAINS ( <IncludedColumns>, <SearchCondition> [,language_term] )
```

## IncludedColumns

這個參數是用來指明比對的欄位

### 比對單一欄位
```sql
SELECT * FROM Documents WHERE CONTAINS(Content, '台北')
```

### 比對多個欄位
```sql
SELECT * FROM Documents WHERE CONTAINS((Title, Content), '台北')
```

### 比對全部欄位
```sql
SELECT * FROM Documents WHERE CONTAINS(*, '台北')
```

### SearchCondition

這個參數是用來指明**搜尋條件**。搜尋條件可以是字(word)或詞(phrase)。

- 字：不含空白
- 詞：一個或多個字，各字間有空格。

亞洲某些地區的語言，可能會有不含空格的詞。

搜尋條件若是變數，最好使用 nvarchar ，否則系統必須隱含轉換，引響效能。。

## 簡單項目（Simple Term）

- Term是一個明確的單字或片語。例如："blue berry"、blueberry 和 "Microsoft SQL Server" 都是有效的簡單詞彙範例。
- 片語應該用雙引號 ("") 括住。
- 在單字或片語中搜尋字元並不區分大小寫。
- 片語中的單字順序，必須同索引中的順序才找的到。
- 全文檢索索引資料行中的**非搜尋字** (或**停用字詞**) (如 a、and 或 the) 不會儲存在全文檢索索引中。
- 在每個 SQL Server 執行個體的 \Mssql\Binn\FTERef 目錄中，都有存放一份**非搜尋字清單**。
- 如果在 Simple Term 中使用非搜尋字，SQL Server 會傳回一則錯誤訊息，指出查詢只包含非搜尋字。
```sql
--搜尋條件是一個「字」
SELECT * FROM Documents WHERE CONTAINS(Content, '市長')

--搜尋條件是一個「片語」，要用雙引號括住
SELECT * FROM Documents WHERE CONTAINS(Content, '"台北 市長"')

--片語中的單字順序，必須同索引中的順序才找的到
SELECT * FROM Documents WHERE CONTAINS(Content, '"市長 台北"')  --這會找不到。
```

## 前置項目（Prefix Term）

- 可用來搜尋指定文字開頭的單字或片語。
- 應該用雙引號括住
- 在後面引號前要加上*號
```sql
--這會找不到，因為索引中不含"市"這個token.
SELECT * FROM Documents WHERE CONTAINS(Content, '市')

--但因為含"市長"這個token，所以可以用"市*"找到
SELECT * FROM Documents WHERE CONTAINS(Content, '"市*"')
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjHw9ZKYA-uLEEZuldwIfteBZfh_tqxkItenvd0VmaibJPugsnQfGT0EAQHGpgwkwko-PsqnS_Hg37b69HRJUlucfAgfnkcf3FA-OBj1Rq3ZdtP-bYT8zH0JfZuJgSxqCMgesVgb7Aw75Y/s0/ft-prefix-term.png)

## 衍生項目（Generation Term）

用來查詢指定 Term 的各種變化形，可分成以下二類：

- INFLECTIONAL：透過字幹分析器(stemmer)，依據指定語系，查詢所有符合該語系詞態變化的字
- THESAURUS：使用指定語系的同義字定義檔以查詢同義字。
```sql
--return 市長 or 巿長
SELECT * FROM Documents WHERE CONTAINS(Content, 'FORMSOF(THESAURUS,"市長")')  
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9AQarTKnPCLYYv7pm-Sr5c3SRlDD3yqmAL1zwybyIm5y4HBOPqjaO5nfB0N77HVeVjnbqZwhOai1TkB1tdC7u_Kn6tgJuctOmRtdhcji2ekoD9jAKFLLMhd2CVx_j7U-DvaTFWqGeQAY/s0/ft-using-thesaurus.png)
```sql
--only return ride
SELECT * FROM Documents WHERE CONTAINS(Content, 'FORMSOF(INFLECTIONAL,"ride")') 

--return ride, riding, rode
SELECT * FROM Documents WHERE CONTAINS(Content, 'FORMSOF(INFLECTIONAL,"ride")', LANGUAGE N'English')  
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgxUIXiLsg84Xv3Sr0z6jka68Fi8cquw7onKCsOb8Ve5zXyBWkrf5mkzhvoYIwMRfb_LymHUQvZyeMg3gX57BjyC_4mqU12e3kBCfEuUsxIIirxmCsjOrTOeQzTxgmgzwhmHzkvjb1_AwM/s0/ft-using-inflection.png)

## 鄰近項目（Proximity Term）

用來查詢特定 Term 附近指定的 Term
```sql
<proximity_term> ::= 
{ <simple_term> | <prefix_term> } 
{ { NEAR | ~ }
{ <simple_term> | <prefix_term> } 
} [ ...n ] 
```
```sql
SELECT * FROM Documents WHERE CONTAINS(Content,'市長 NEAR 琨')
```

## 加權項目（Weighted Term）

針對不同的 Term 指定不同的的查詢權重

- 使用 **ISABOUT** 關鍵字來指定 Term 的權重
- 加權值（WEIGHT），由 0.0 至 1.0 的數字。
- WEIGHT 不會影響 [CONTAINS](http://msdn.microsoft.com/zh-tw/library/ms187787.aspx) 查詢的結果，但 WEIGHT 會影響 [CONTAINSTABLE](http://msdn.microsoft.com/zh-tw/library/ms189760.aspx) 查詢中的等級。

加權值若只用在 [CONTAINS](http://msdn.microsoft.com/zh-tw/library/ms187787.aspx) ，不會影響查詢的結果
```sql
SELECT * FROM Documents
WHERE CONTAINS(Content, 
'ISABOUT ( 花蓮 weight (.8), 市長 weight (.4), 傅 weight (.2) )' 
);
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5fci0n_VRkrdZZcyg8_uZ9IXvFOxfqPrW2xBiEtMZX6ndoXxknAREBSKxWeEfOnKoGOwwving_sgjw1jbhJNfgD4wi4mA_myY0V4EDF_QFOnHtr_xYuc_YHE6DaHb-EJuW4h53epX-lM/s0/ft-weight-term-1.png)

加權值若用在 [CONTAINSTABLE](http://msdn.microsoft.com/zh-tw/library/ms189760.aspx) ，則會產生不同的 Ranking 值
```sql
SELECT A.*,B.* FROM Documents as A
INNER join CONTAINSTABLE(Documents, Content, 
'ISABOUT ( 花蓮 weight (.8), 市長 weight (.4), 傅 weight (.2) )' 
) as B ON A.DID=B.[Key]
ORDER BY B.RANK DESC
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjgxUGUH-RUC_0iGNkd-1GFkYUqdHZMCSJMIaB-y47-4K_ddrlCgS3I5vLp2kUReUVLhl76zJ3TNgsiLCQZ9dBDJBUSziFEkeORJHbP3DUmrcQnB5e-Swv9e66RXG14Q7bIBv2JgwTa1Mk/s0/ft-weight-term-2.png)

## 邏輯運算（Logical Operators）

指定兩個包含搜尋條件之間的邏輯運算。共有三種運算式 { AND | AND NOT | OR }，也可以用符號表示 { & | &! | | }
```sql
SELECT * FROM Documents WHERE CONTAINS(Content,'市長 AND 高雄')
SELECT * FROM Documents WHERE CONTAINS(Content,'市長 OR 高雄')
SELECT * FROM Documents WHERE CONTAINS(Content,'市長 &! 高雄')
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiFLVo1UXfEL1b0sNoneKsiGuCko0MCItp-RkjSYCbmySvjbdj2Efoxbob3lFrlX3opJpxa4ThqjHfougSTBVagRiYr7Oj6EokcmSmStAoBiLYMldNoC-BCCP7XWAAHOGF5m5kOqNrI3Rg/s0/ft-logical-operation.png)

# CONTAINSTABLE

這個 [CONTAINSTABLE](http://msdn.microsoft.com/zh-tw/library/ms189760.aspx) 述詞必須使用於 FROM 子句中。   它和 [CONTAINS](http://msdn.microsoft.com/zh-tw/library/ms187787.aspx) 述詞一樣適用於相同的比對類型，並且使用和 [CONTAINS](http://msdn.microsoft.com/zh-tw/library/ms187787.aspx) 一樣的搜尋條件。   不同的是，使用 [CONTAINSTABLE](http://msdn.microsoft.com/zh-tw/library/ms189760.aspx) 的查詢會傳回每個資料列的相關次序值 (RANK) 和全文檢索索引鍵 (KEY)。   

也就是回傳的資料表中會多了二個欄位。  一個是名稱為 KEY 且含有索引鍵值的資料行；  一個是名稱為 RANK 的資料行，數值由 0 至 1000 ，用來表示比對的 match 程度。  
```sql
CONTAINSTABLE  ( table , <InludedColumns>, <SearchCondition> [,language_term] [,top_n_by_rank]  )
```

由定義看的出來，它就只比 [CONTAINS](http://msdn.microsoft.com/zh-tw/library/ms187787.aspx) 多了 table 和 top\_n\_by\_rank 二個參數。

- table：Specifies the name of a table for which full-text search is enabled.
- top\_n\_by\_rank：Specifies that only the specified number of rows with the highest rankings should be returned.

所以，若透過這個選項可以用來限制回傳的資料量，也算是提升效率的一種方法。  

#### 例一：傳回等級值
```sql
SELECT * FROM Documents as A
INNER join CONTAINSTABLE(
Documents, 
Content, 
'ISABOUT ( 花蓮 weight (.8), 市長 weight (.4), 傅 weight (.2) )' 
) as B ON A.DID=B.[Key]
ORDER BY B.RANK DESC
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgDTwBftkQU0XsM03_faLG5wRC59aAh4w8TKahv1JKnTCbocnvaoa7F8amIf_0NZUWXXSvk__MVgP6uiNlIsb_MQZBVfJ9-F6RCyrWtkCnvzqm6tCW6FhpnEtPXqZvcC7B37v6vgxFMwr0/s0/ft-containstable-1.png)

#### 例二：傳回大於指定值的等級值
```sql
SELECT * FROM Documents as A
INNER join CONTAINSTABLE(
Documents, 
Content, 
'ISABOUT ( 花蓮 weight (.8), 市長 weight (.4), 傅 weight (.2) )' 
) as B ON A.DID=B.[Key]
WHERE B.RANK > 30
ORDER BY B.RANK DESC
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgBT-J0ld2Ou04jrGt2_zhvikO6dM01rmD8eFEaZ4o_aWDYGPRinyFaewZSn02TjP-btj8Kw0yoVWpRnfQatBh6ipwLppLNOVi-YfT5WHbcseWEoICiTeSs0y8a0Du1Rxzn1So5sZFr96A/s0/ft-containstable-2.png)

#### 例三：利用 top\_n\_by\_rank 傳回前 2 個等級的結果
```sql
SELECT * FROM Documents as A
INNER join CONTAINSTABLE(
Documents, 
Content, 
'ISABOUT ( 花蓮 weight (.8), 市長 weight (.4), 傅 weight (.2) )',
2
) as B ON A.DID=B.[Key]
ORDER BY B.RANK DESC
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgIXoqZ9Fr9SlnnRQM5TsKTO1Iu2Thxt2RrARVpb_DOc_cXLh87BBsrkAcsRui5efz1Mlbtz_iNxut1B78NpqnjILDZDSbF8dcmsiK-B8BYrkNMdGGgfZ9Gop9b-1C15XmVvZ6xbSeeCeA/s0/ft-containstable-3.png)

# FREETEXT

這個 [FREETEXT](http://msdn.microsoft.com/zh-tw/library/ms176078.aspx) 述詞是用於 WHERE 子句中。  同 [CONTAINS](http://msdn.microsoft.com/zh-tw/library/ms187787.aspx) 一樣，是用來指定查詢條件。  然後，它並不是要搜尋與輸入條件完全相同的資料，它會搜尋意義與條件中的文字符合但用字不一定完全相同的資料。  
```sql
FREETEXT ( <InludedColumns>, 'freetext_string' [,language_term] )
```

使用 [FREETEXT](http://msdn.microsoft.com/zh-tw/library/ms176078.aspx) 時，全文檢索查詢引擎內部會對 freetext\_string 執行下列動作、為每個詞彙指派加權，然後找出相符項目： 

- 斷詞：將字串分隔成單字。
- 詞幹分析：產生字組的字形變化
- 同義字分析：依據同義字中的相符項目識別詞彙的展開或取代清單。

下面這個例子，查詢引擎會執行下列動作  

- 將＂花蓮市長＂拆成＂花蓮＂和＂市長＂
- 尋找＂花蓮＂和＂市長＂的其他詞態字
- 再依據同義字定義檔中的定義，展開或取代＂花蓮＂和＂市長＂。
```sql
SELECT * FROM Documents 
WHERE FREETEXT(Content, '花蓮市長')
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjxnO5VDSpEdvmJgguYcOJZkgHLnoA7wBAnw3wRSdAVSpq-68acA4AuvwLKlivI5CNg6mUyFg16lRafunr06Hxxg3XyJJZquVaAogiyPsn0ZeA4wN1WSK-qFKYFspqyIiqC0wvHLC6LGAE/s0/ft-freetext-1.png)

# FREETEXTTABLE

這個 [FREETEXTTABLE](http://msdn.microsoft.com/zh-tw/library/ms177652.aspx) 述詞結合了 [FREETEXT](http://msdn.microsoft.com/zh-tw/library/ms176078.aspx) 和 [CONTAINSTABLE](http://msdn.microsoft.com/zh-tw/library/ms189760.aspx) 二者的參數。   

[FREETEXTTABLE](http://msdn.microsoft.com/zh-tw/library/ms177652.aspx) 使用和 [FREETEXT](http://msdn.microsoft.com/zh-tw/library/ms176078.aspx) 述詞相同的搜尋條件，然後自動斷詞，並自動給定每個 token 權重後再查詢。  
```sql
FREETEXTTABLE  ( table, <InludedColumns>, 'freetext_string' [,language_term] [,top_n_by_rank]  )
```
```sql
SELECT * FROM Documents as A
INNER join FREETEXTTABLE(
Documents, 
Content, 
'台北市長傅琨城',
5
) as B ON A.DID=B.[Key]
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjC_1oZe5dTDZUnx_v74Olw1FDxDSZmW9sHCq4sQoypODeeynpEY7zlDLpGUokFf5n2D4Ch9pXYhiMDm7Kdigax7OMBFmhRfVTdwyVl6b6pWYUdAVXJDEBRa_5rkDkL1gruoMio7MhXVzI/s0/ft-freetexttable.png)

首先查詢條件＂台北市長傅琨城＂被支解成＂台北＂、＂市長＂、＂傅＂、＂琨＂、＂城＂  
所以＂花蓮市長傅琨城＂比對到的token有：＂市長＂、＂傅＂、＂琨＂、＂城＂  
所以＂台北巿長郝龍斌＂比對到的token有：＂台北＂、＂巿＂、＂長＂  
所以＂台北市長郝龍斌＂比對到的token有：＂台北＂、＂市長＂  
所以＂高雄市長陳菊花＂比對到的token有：＂市長＂