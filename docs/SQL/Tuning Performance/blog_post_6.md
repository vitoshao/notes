---
title: 效能調校-心得分享
layout: default
parent: Tuning Performance
nav_order: 6
description: "效能調校-心得分享"
date: 2013-05-27
tags: [SQL,Tuning Performance]
postid: "8713460214861920069"
---
有些程式員在撰寫前端的應用程式時，會透過各種 OOP 語言將存取資料庫的 SQL 陳述式串接起來，卻忽略了 SQL 語法的效能問題。版工曾聽過某半導體大廠的新進程式員，所兜出來的一段 PL/SQL 跑了好幾分鐘還跑不完；想當然爾，即使他前端的 AJAX 用得再漂亮，程式效能頂多也只是差強人意而已。以下是版工整理出的一些簡單心得，讓長年鑽究 ASP.NET / JSP / AJAX 等前端應用程式，卻無暇研究 SQL 語法的程式員，避免踩到一些 SQL 的效能地雷。

1、資料庫設計與規劃

‧ Primary Key 欄位的長度儘量小，能用 small integer 就不要用 integer。例如員工資料表，若能用員工編號當主鍵，就不要用身分證字號。  
‧ 一般欄位亦同。若該資料表要存放的資料不會超過 3 萬筆，用 small integer 即可，不必用 integer。  
‧ 文字資料欄位若長度固定，如：身分證字號，就不要用 varchar 或 nvarchar，應該用 char 或 nchar。  
‧ 文字資料欄位若長度不固定，如：地址，則應該用 varchar 或 nvarchar。除了可節省儲存空間外，存取磁碟時也會較有效率。  
‧ 設計欄位時，若其值可有可無，最好也給一個預設值，並設成「不允許 NULL」(一般欄位預設為「允許 NULL」)。因為 SQL Server 在存放和查詢有 NULL 的資料表時，會花費額外的運算動作 [2]。  
‧ 若一個資料表的欄位過多，應垂直切割成兩個以上的資料表，並用同名的 Primary Key 一對多連結起來，如：Northwind 的 Orders、Order Details 資料表。以避免在存取資料時，以叢集索引掃描時會載入過多的資料，或修改資料時造成互相鎖定或鎖定過久。

2、適當地建立索引

‧ 記得自行幫 Foreign Key 欄位建立索引，即使是很少被 JOIN 的資料表亦然。  
‧ 替常被查詢或排序的欄位建立索引，如：常被當作 WHERE 子句條件的欄位。  
‧ 用來建立索引的欄位，長度不宜過長，不要用超過 20 個位元組的欄位，如：地址。  
‧ 不要替內容重複性高的欄位建立索引，如：性別；反之，若重複性低的欄位則適合建立索引，如：姓名。  
‧ 不要替使用率低的欄位建立索引。  
‧ 不宜替過多欄位建立索引，否則反而會影響到新增、修改、刪除的效能，尤其是以線上交易 (OLTP) 為主的網站資料庫。  
‧ 若資料表存放的資料很少，就不必刻意建立索引。否則可能資料庫沿著索引樹狀結構去搜尋索引中的資料，反而比掃描整個資料表還慢。  
‧ 若查詢時符合條件的資料很多，則透過「非叢集索引」搜尋的效能，可能反而不如整個資料表逐筆掃描。  
‧ 建立「叢集索引」的欄位選擇至為重要，會影響到整個索引結構的效能。要用來建立「叢集索引」的欄位，務必選擇「整數」型別 (鍵值會較小)、唯一、不可為 NULL。

3、適當地使用索引

‧ 有些書籍會提到，使用 LIKE、% 做模糊查詢時，即使您已替某個欄位建立索引 (如下方例子的 CustomerID)，但以常數字元開頭才會使用到索引，若以萬用字元 (%) 開頭則不會使用索引，如下所示：

USE Northwind;  
GO  
[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) \* FROM Orders WHERE CustomerID LIKE 'D%'; --使用索引  
[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) \* FROM Orders WHERE CustomerID LIKE '%D'; --不使用索引

執行完成後按 Ctrl+L，可檢閱如下圖的「執行計畫」。  
圖 1 可看出「查詢最佳化程式」有使用到索引做搜尋  
圖 2 在此的叢集索引掃描，並未直接使用索引，效能上幾乎只等於掃描整個資料表

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhD7Q9ZB-mrV8MHJcIbJxHLC2dt4hTFEzOVOMp6FZnY75szTMn8HQ1lGo1xIZXltQjsB49jEyFLfn-Tinxd_I9BbRfOBqEp5CPai1pU5Vk5rxt6hHMILw_Il3wLT0I9wjbJig7gsFs7Zr0/s735/sql_performance_like.png)

但經版工反覆測試，這種語法是否會使用到索引，抑或會逐筆掃描，並非絕對的。仍要看所下的查詢關鍵字，以及欄位內儲存的資料內容而定。但對於儲存資料筆數龐大的資料表，最好還是少用 LIKE 做模糊查詢。

‧ 以下的運算子會造成「負向查詢」，常會讓「查詢最佳化程式」無法有效地使用索引，最好能用其他運算子和語法改寫 (經版工測試，並非有負向運算子，就絕對無法使用索引)：  
NOT 、 != 、 &lt;&gt; 、 !&gt; 、 !&lt; 、 NOT EXISTS 、 NOT IN 、 NOT LIKE

‧ 避免讓 WHERE 子句中的欄位，去做字串串接或數字運算，否則可能導致「查詢最佳化程式」無法直接使用索引，而改採叢集索引掃描 (經版工測試並非絕對)。

‧ 資料表中的資料，會依照「叢集索引」欄位的順序存放，因此當您下 BETWEEN、GROUP BY、ORDER BY 時若有包含「叢集索引」欄位，由於資料已在資料表中排序好，因此可提升查詢速度。

‧ 若使用「複合索引」，要注意索引順序上的第一個欄位，才適合當作過濾條件。

4、避免在 WHERE 子句中對欄位使用函數

對欄位使用函數，也等於對欄位做運算或串接的動作，一樣可能會讓「查詢最佳化程式」無法有效地使用索引。但真正對效能影響最重大的，是當您的資料表內若有 10 萬筆資料，則在查詢時就需要呼叫函數 10 萬次，這點才是真正的效能殺手。程式員應注意，在系統開發初期可能感覺不出差異，但當系統上線且資料持續累積後，這些語法細節所造成的效能問題就會逐步浮現。

[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) \* FROM Orders WHERE DATEPART(yyyy, OrderDate) = 1996 AND DATEPART(mm, OrderDate)=7  
可改成  
[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) \* FROM Orders WHERE OrderDate BETWEEN '19960701' AND '19960731'

[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) \* FROM Orders WHERE SUBSTRING(CustomerID, 1, 1) = 'D'  
可改成  
[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) \* FROM Orders WHERE CustomerID LIKE 'D%'

注意當您在下 UPDATE、DELETE 陳述式時，若有採用 WHERE 子句，也應符合上述原則。

5、AND 與 OR 的使用

在 AND 運算中，「只要有一個」條件有用到索引 (如下方的 CustomerID)，即可大幅提升查詢速度，如下圖 3 所示：

[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) \* FROM Orders WHERE CustomerID='VINET' AND Freight=32.3800 --使用索引，會出現下圖 3 的畫面  
[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) \* FROM Orders WHERE Freight=32.3800 --不使用索引，會出現上圖 2 的畫面

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjg6gBjLD1emvHa1tBAqQgMAdjhDBBwnpX1TNy_Nf9z8EGvxeHBCeLoeaj9NiO3y9eO_iJQ1zyhpHLFKqVHPfRpHU38pmIL3mtCO4tmF5vZeod6yCi_VkEyq4TzIDUCMFEvdi2b6-n2DF8/s752/sql_performance_and.png)

但在 OR 運算中，則要「所有的」條件都有可用的索引，才能使用索引來提升查詢速度。因此 OR 運算子的使用必須特別小心。

若您將上方 AND 的範例，邏輯運算子改成 OR 的話，如下所示：

[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) \* FROM Orders WHERE CustomerID='VINET' OR Freight=32.3800

由於無法有效地使用索引，也會出現圖 2 的畫面。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhJX63xJljPSY-EXLkM6b0XkBxQNjSBZ7fONTok-1Asu84t-MxLGPpnrkB5bxbdXOlntmWKrq22VhKQca3eQi7wd_1ZKhRUykfJjsrNKrvgVxcxwC-IMeFTOLs7vf3NEIiX0zEUrPwr11Y/s686/sql_performance_or.png)

在使用 OR 運算子時，只要有一個條件 (欄位) 沒有可用的索引，則其他所有的條件 (欄位) 都有索引也沒用，只能如圖 2 般，把整個資料表或整個叢集索引都掃描過，以逐筆比對是否有符合條件的資料。

據網路上文件的說法 [1]，上述的 OR 運算陳述式，我們還可用 [UNION](http://msdn.microsoft.com/zh-tw/library/ms180026.aspx) 聯集適當地改善，如下：

[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) \* FROM Orders WHERE CustomerID='VINET'  
UNION  
[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) \* FROM Orders WHERE Freight=32.3800

此時您再按 Ctrl+L 檢閱「執行計畫」，會發現上半段的查詢會使用索引，但下半段仍用叢集索引掃描，對效能不無小補。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhz7Pw7FWtrZSTNzOMpmMOQlDpW-EvwdKAIorv7bafjo1cSTtpiO3ow9bED0FpesD6ErOJ8en5vsolzHu5Vobp7zzQhytUyEB2GYz2dgCPRs3hH9dgApscIGhD_WbWTM9dxQxSD7lFoF4c/s686/sql_performance_union.png)

6、適當地使用子查詢

相較於「子查詢 (Subquery)」，若能用 JOIN 完成的查詢，一般會比較建議使用後者。原因除了 JOIN 的語法較容易理解外，在多數的情況下，JOIN 的效能也會比子查詢較佳；但這並非絕對，也有的情況可能剛好相反。

我們知道子查詢可分為「獨立子查詢」和「關聯子查詢」兩種，前者指子查詢的內容可單獨執行，後者則無法單獨執行，亦即外層查詢的「每一次」查詢動作都需要引用內層查詢的資料，或內層查詢的「每一次」查詢動作都需要參考外層查詢的資料。

以下我們看一個比較極端的例子 [2]。若我們希望所有查詢出來的資料，都能另外給一個自動編號，版工我在之前的文章「用 SQL Server 2005 新增的 ROW\_NUMBER 函數撰寫 GridView 分頁」中有介紹過，可用 SQL Server 2005 中新增的 ROW\_NUMBER 函數輕易地達成，且 ROW\_NUMBER 函數還能再加上「分群 (PARTITION BY)」等功能，而且執行效能極佳。

圖 4 將 Orders 資料表的 830 筆資料都撈出來，並在右側給一組自動編號  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi_5anvvzSgWAndCt4SPJfo0vgmBXfFPmmuRE8ecxVJg1oYqKMUHHT2zLscI85pzeI24Dth4RyzbEXqi1S6GUIP5n1xKcyn2-LNgjQi-QlL1cFznft4Sei_owQZ0Pklcj-2c0aTHyne0No/s850/sql_performance_subquery.png)

現在我們要如上圖 4 般，將 Northwind 中 Orders 資料表的 830 筆資料都撈出來，並自動給一組編號，若用 ROW\_NUMBER 函數的寫法如下所示，而且效能極佳，只要 2 ms (毫秒)，亦即千分之二秒。

[SET STATISTICS TIME](http://msdn.microsoft.com/zh-tw/library/ms190287.aspx) ON  
[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) OrderID, ROW\_NUMBER() OVER(ORDER BY OrderID) AS 編號  
FROM dbo.Orders

但如果是在舊版的 SQL Server 2000 中，我們可能得用以下的「子查詢」寫法：

[SET STATISTICS TIME](http://msdn.microsoft.com/zh-tw/library/ms190287.aspx) ON  
[SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) OrderID,  
(SELECT COUNT(\*) FROM dbo.Orders AS 內圈  
WHERE 內圈.OrderID &lt;= 外圈.OrderID) AS 編號  
FROM dbo.Orders AS 外圈  
ORDER BY 編號  

但這種舊寫法，會像先前所提到的，外層查詢的「每一次」查詢動作都需要引用內層查詢的資料。以上方例子而言，外層查詢的每一筆資料，都要等內層查詢「掃描整個資料表」並作比對和計數，因此 830 筆資料每一筆都要重複掃描整個資料表 830 次，所耗用的時間也因此爆增至 170 ms。

若您用相同的寫法，去查詢 AdventureWorks 資料庫中，有 31,465 筆資料的 Sales.SalesOrderHeader 資料表，用 ROW\_NUMBER 函數要 677 ms，還不到 1 秒鐘；但用子查詢的話，居然要高達 225,735 ms，將近快 4 分鐘的時間。

雖然這是較極端的範例，但由此可知子查詢的撰寫，在使用上不可不慎，尤其是「關聯子查詢」。程式員在程式開發初期、資料量還很少時感受不到此種 SQL 語法的重大陷阱；但等到系統上線幾個月或一兩年後，可能就會有反應遲緩的現象。

7、其他查詢技巧

‧ DISTINCT、ORDER BY 語法，會讓資料庫做額外的計算。此外聯集的使用，若沒有要剔除重複資料的需求，使用 [UNION](http://msdn.microsoft.com/zh-tw/library/ms180026.aspx) ALL 會比 [UNION](http://msdn.microsoft.com/zh-tw/library/ms180026.aspx) 更佳，因為後者會加入類似 DISTINCT 的演算法。

‧ 在 SQL Server 2005 版本中，存取資料庫物件時，最好明確指定該物件的「結構描述 (Schema)」，也就是使用兩節式名稱。否則若呼叫者的預設 Schema 不是 dbo，則 SQL Server 在執行時，會先尋找該使用者預設 Schema 所搭配的物件，找不到的話才會轉而使用預設的 dbo，會多耗費尋找的時間。例如若要執行一個叫做 dbo.mySP1 的 Stored Procedure，應使用以下的兩節式名稱：

EXEC dbo.mySP1

8、儘可能用 Stored Procedure 取代前端應用程式直接存取資料表

Stored Procedure 除了經過事先編譯、效能較好以外，亦可節省 SQL 陳述式傳遞的頻寬，也方便商業邏輯的重複使用。再搭配自訂函數和 View 的使用，將來若要修改資料表結構、重新切割或反正規化時亦較方便。

9、儘可能在資料來源層，就先過濾資料

使用 [SELECT](http://technet.microsoft.com/zh-tw/library/ms189499.aspx) 語法時，儘量避免傳回所有的資料至前端而不設定 WHERE 等過濾條件。雖然 ASP.NET 中 SqlDataSource、ObjectDataSource 控制項的 FilterExpression 可再做篩選，GridView 控制項的 SortExpression 可再做排序，但會多消耗掉資料庫的系統資源、Web server 的記憶體和網路頻寬。最好還是在資料庫和資料來源層，就先用 SQL 條件式篩選出所要的資料。

結論：

本文的觀念，不管是寫 SQL statement、Stored Procedure、自訂函數或 View 皆然。本文只是挑出程式員較容易犯的 SQL 語法效能問題，以期能在短時間瀏覽過本文後，在寫 ADO.NET 程式時能修正以往隨興的 SQL 撰寫習慣。文中提到的幾點，只不過是 SQL 語法效能議題的入門篇。後續有時間的話，版工會再補充在本帖的回應留言，或另開新主題。