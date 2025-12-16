---
title: 效能調校(3)-讓索引變的有用
layout: default
parent: Tuning Performance
nav_order: 15
description: "效能調校(3)-讓索引變的有用"
date: 2014-03-28
tags: [SQL,Tuning Performance]
postid: "9184749521111140707"
---
一般資料庫系統都會針對查詢進行最佳化設計，負責這功能的引擎就稱為[查詢最佳化器（Query Optimization）](http://en.wikipedia.org/wiki/Query_optimization)  而 Query Optimizer 就是 SQL Server 的查詢最佳化器，  它會根據查詢條件進行工作計劃（execution plans ）評估，並找到最小成本的那個評估計劃來執行。  所以，我們必須調校我們的資料庫，以便讓 Query Optimizer 可以正確使用我們所建立的索引，以取得最佳的執行計畫。  

# 讓查詢善用索引

當查詢的目標資料已經建立了索引，但是當實際進行查詢時，資料庫也不見得會使用索引，會有這種結果，簡單講，就是與**查詢參數**使用不當有關。  因為只有在查詢參數具有足夠的**選擇性**（Selectivity）時，才會正確使用到索引。  所謂的「選擇性」就是指 WHERE 子句中，其所指定的篩選條件，其合格資料列與總資料列的比率。  如果比率低，表示索引具有高度選擇性，可以排除大部分的資料列，大幅減少結果集的大小。  因此，這是一項值得建立的有效索引。反之，非選擇性的索引則沒有用處。  

底下將會探討幾種案例，用來說明在什麼狀況下查詢不會用到索引。不過要分析查詢，我們就必須知道每個查詢對索引的使用狀況。  

## 如何得知索引的使用狀況

### 使用圖型執行計畫

索引的使用狀況，可以使用 SSMS 提供的「[圖型執行計畫](http://technet.microsoft.com/zh-tw/library/ms178071.aspx)」，它為成「估計執行計畫」和「實際執行計畫」。  

- 估計執行計畫：剖析該指令碼，並產生估計的執行計畫。
- 實際執行計畫：在產生執行計畫之前，先執行指令碼。當指令碼結束之後，再將實際的執行計畫顯示在 [執行計畫] 索引標籤中。

![](http://127.0.0.1:8080/_images/cs/sql-toolbar-workplan.png)

### 使用 sys.dm\_db\_index\_usage\_stats

## 如何讓查詢使用索引

一道查詢指令，到底會不會使用到索引，大部份的原因取決於 WHERE 子句。底下藉由例子來說明這個因素：  

### 例１：加入 WHERE 條件

這個例子，因為沒有 WHERE 條件，所以使用「Clustered Index Scan」。
```sql
SELECT  CustomerID, CompanyName, Region
FROM Customers
```

![](http://127.0.0.1:8080/_images/cs/sql-index-usage-02.png)

### 例２：建立合適索引

這個例子，因為 AccountNumber 不是索引鍵，所以沒有任何索引適用於 WHERE 條件，所以還是使用「Clustered Index Scan」。
```sql
SELECT  CustomerID, CompanyName, Region
FROM Customers
WHERE Country='USA'  ```

![](http://127.0.0.1:8080/_images/cs/sql-index-usage-03.png)

### 例３：選擇性要夠

下面這個例子中， title 本是一個**非叢集索引**，所以有合適的索引滿足 WHERE 條件。  但是，若條件不夠「選擇性」，也就是查詢結果返回太多列的話，還是會使用「Clustered Index Scan」或「Table Scan」。  
```sql
SELECT  customerid, firstname, title
FROM Customer
WHERE title='Ms'

SELECT  customerid, firstname, title
FROM Customer 
WHERE title='Mr.'
```

![](http://127.0.0.1:8080/_images/cs/sql-index-usage-04.png)

上例中的第一個狀況， 因為足夠「選擇性」，所以使用「Index Seek」。  但是由於 IX\_Title 只包含 Title 資料，所以它還必須搭配「索引鍵查閱（Key Lookup）」，以取得其他欄位的資訊。  這也是為什麼，若返回太多列的話，直接使用「Clustered Index Scan」比「Index Seek + Key Lookup」還要來得有效率。

### 例４：使用彙總

若查詢中包含 GROUP BY 子句，你也應該考慮為它建立索引。  
SQL Server 執行彙總時，會使用 hash match 或 stream aggregate 來進行資料分組。  
stream aggregate 效能較佳，但只能用在已排序的資料。  

底下例子中，因為 Region 為索引鍵，所以使用 stream aggregate 進行資料分組；  而 Country 不是索引鍵，所以使用 hash match 進行資料分組。  
```sql
SELECT  Region, Count(\*)
FROM Customers
GROUP BY Region

SELECT  Country, Count(\*)
FROM Customers
GROUP BY Country
```

![](http://127.0.0.1:8080/_images/cs/sql-index-usage-05.png)

上面例子中的第一個狀況，因為不夠選擇性，所以使用「Index Scan」。若夠選擇性，則會使用「Index Seek」。  

![](http://127.0.0.1:8080/_images/cs/sql-index-usage-06.png)

即使沒有用到 GROUP BY 子句，只要查詢中包含彙總函式，都可以由索引得到明顯的效能改善。  例如 MIN() 函式，對於排序過的資料，它只須要由索引中找到第一筆資料值，而不用整個資料表掃過。

### 例５：使用排序

如果你要使用 ORDER BY 子句排序輸出結果，你也必須考慮為排序欄位建立索引。  像下列中，因為 Country 不是索引，所以 SQL Server 在返回資料前，還必須額外進行資料排序的工作。  
```sql
SELECT  CustomerID, CompanyName, Region
FROM Customers
WHERE Region = 'ID'  ORDER BY Region  SELECT  CustomerID, CompanyName, Region
FROM Customers
WHERE Region = 'ID'  ORDER BY Country  ```

![](http://127.0.0.1:8080/_images/cs/sql-index-usage-07.png)

## 查詢參數（Search Arguments, SARGs）

上面例３中提到，必須 WHERE 條件子句的選擇性要足夠才會正確使用到索引，  其實這樣還不一定能夠保證 Query Optimizer 就一定會使用索引，真正要讓 Query Optimizer 使用索引的條件是：  WHERE 子句的撰寫格式要符合「查詢參數（Search Arguments, SARGs）」規則。  

### SARGs 格式

#### SARGs 的格式：

SARGs 的格式如下，也就是欄位只能出現在運算子的單一邊，而比較值在另一邊。如果欄位名稱同時出現在運算子的兩邊，就不算 SARGs 。
欄位 operator ＜值＞
或
＜值＞ operator 欄位  
#### SARGs 的運算子：

SARGs 包含以下運算子： =、&gt;、&lt;、=&gt;、&lt;=、BETWEEN、LIKE。  其中 LIKE 必須滿足萬用字元「% 或 \_ 」不在條件值開頭的狀況才算符合規則。  例如：LIKE 'A%' 符合 SARGs ，但是 LIKE '%A' 就不符合。  因為以萬用字頭的條件，將無法限制 SQL Server 查詢的記錄數目。  

下面列出幾點在使用查詢參數時，要注意的事項。

### 使用 AND 與 OR 的差異

### 不要對資料欄位做運算

### 不要對資料欄位使用函式

### 不要使用負向查詢