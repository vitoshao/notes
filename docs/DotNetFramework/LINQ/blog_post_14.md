---
title: LINQ 動態查詢
layout: default
parent: LINQ
nav_order: 14
description: "LINQ 動態查詢"
date: 2014-12-30
tags: [DotNetFramework,LINQ]
postid: "3125133042787093021"
---
在傳統資料庫存取的程式中，若我們要篩選不同條件的資料，或者使用不同欄位的排序條件，我們都可以很簡單的直接使用串接 SQL 的方式來達到目的。  而在 LINQ 語法中，你也可以直接在 Linq 表示式中直接去組成，如下面程式碼片段：  
```c#
var qry = from Ob in dc.Observe
join S in dc.Stocks on Ob.StockID equals S.StockID
where Ob.ObserveID == "A2" && ( S.StockName.StartsWith("台") || S.StockName.EndsWith("台") )
orderby S.StockName
```

不過這樣子的做法，最大的問題就在於沒有辨法動態組成，當你條件變動時就必須更改程式碼。  在 .NET 中，雖然你也可以使用 Lambda 運算式所提供的 [Where](http://msdn.microsoft.com/zh-tw/library/system.linq.queryable.where%28v=vs.100%29.aspx)方法，來動態串接 where 條件。  如下面程式碼，你可以方便的使用 if 判斷式去動態決定想要的篩選條件：  
```c#
var qry = from Ob in dc.Observe
join S in dc.Stocks on Ob.StockID equals S.StockID
select new MyObserve
{
ObserveID = Ob.ObserveID,
OrderNum = Ob.OrderNum,
StockID = Ob.StockID,
StockName = S.StockName
};

qry = qry.Where(X => X.ObserveID.Equals("A2"));
qry = qry.Where(X => X.StockName.StartsWith("台"));
qry = qry.Where(X => X.StockName.EndsWith("電"));
qry = qry.OrderBy(X => X.StockName);
```

但是上面這個做法最大的問題在於，它所轉譯後的 SQL 語言都是使用 And 方式串接所有的條件。下面SQL內容就是轉譯後的結果：  
```sql
SELECT [t0].[StockID], [t0].[OrderNum], [t0].[ObserveID], [t1].[StockName]
FROM [dbo].[Observe] AS [t0]
INNER JOIN [dbo].[Stocks] AS [t1] ON [t0].[StockID] = [t1].[StockID]
WHERE ([t1].[StockName] LIKE '%電') AND ([t1].[StockName] LIKE '台%') AND ([t0].[ObserveID] = 'A2')
ORDER BY [t1].[StockName]
```

為了解決以上問題，你可以使用以下解決方法：  

### 方法一：使用 Dynamic Query library 

要處理上述問題，可以使用 Dynamic Query library ，這個類別庫擴充了許多 LINQ 的功能，而且原始碼就包含在 VS2008 的範例程式碼中（ \LinqSamples\DynamicQuery\ 目錄）。  你可以在自已的專案中參考該專案，或者在網路上找到 System.Linq.Dynamic.dll 組件加入參考即可。  最後就可以將以上程式碼改寫成：  
```c#
var qry = from Ob in dc.Observe
join S in dc.Stocks on Ob.StockID equals S.StockID
select new MyObserve
{
ObserveID = Ob.ObserveID,
OrderNum = Ob.OrderNum,
StockID = Ob.StockID,
StockName = S.StockName
};

qry = qry.Where("ObserveID.Equals(@0) or StockName.StartsWith(@1) or StockName.EndsWith(@2)", "A2", "台", "台");
qry = qry.OrderBy(S => S.StockName);
qry = qry.OrderByDescending(Ob => Ob.ObserveID);

dataGridView.DataSource = qry.ToList();
```

要使用這些擴充功能，記得要先引用 System.Linq.Dynamic 命名空間。  更多關於 Dynamic Query Library 的使用方法，可以參考範例程式碼中的例子，或者參考 [ScottGu](http://weblogs.asp.net/scottgu/dynamic-linq-part-1-using-the-linq-dynamic-query-library) 的部落格介紹。

### 方法二：使用 PredicateBuilder 類別

要解決LINQ動態查詢問題，另一個方法就是使用 **PredicateBuilder** 類別，這是由 C# In a Nutshell 作者寫的一個類別庫，你可以參考他的這篇文章介紹：[Dynamically Composing Expression Predicates](http://www.albahari.com/nutshell/predicatebuilder.aspx)。  

首先你必須將 PredicateBuilder 類別加到你的專案中，程式碼如下：
```c#
public static class PredicateBuilder
{
public static Expression<Func<T, bool>> True<T> ()  { return f => true;  }
public static Expression<Func<T, bool>> False<T> () { return f => false; }

public static Expression<Func<T, bool>> Or<T> (this Expression<Func<T, bool>> expr1,
Expression<Func<T, bool>> expr2)
{
var invokedExpr = Expression.Invoke (expr2, expr1.Parameters.Cast<Expression> ());
return Expression.Lambda<Func<T, bool>>
(Expression.OrElse (expr1.Body, invokedExpr), expr1.Parameters);
}

public static Expression<Func<T, bool>> And<T> (this Expression<Func<T, bool>> expr1,
Expression<Func<T, bool>> expr2)
{
var invokedExpr = Expression.Invoke (expr2, expr1.Parameters.Cast<Expression> ());
return Expression.Lambda<Func<T, bool>>
(Expression.AndAlso (expr1.Body, invokedExpr), expr1.Parameters);
}
}
```

接著你就可以利用這個靜態類別的方法來串接你所需要的 where 條件：
```c#
var predicate = PredicateBuilder.False<MyObserve>();

predicate = predicate.Or(mo => mo.ObserveID.Equals("A2"));
predicate = predicate.Or(mo => mo.StockName.StartsWith("台"));
predicate = predicate.Or(mo => mo.StockName.EndsWith("台"));
```

最後只要建立的 PredicateBuilder 物件，當做 Where 方法的參數即可。
```c#
var qry = (from Ob in dc.Observe
join S in dc.Stocks on Ob.StockID equals S.StockID
select new MyObserve
{
ObserveID = Ob.ObserveID,
OrderNum = Ob.OrderNum,
StockID = Ob.StockID,
StockName = S.StockName
})
.Where(predicate);

dataGridView.DataSource = qry.ToList();
```
## 參考資料  

- [System.Linq.Dynamic 1.0.4](https://www.nuget.org/packages/System.Linq.Dynamic/)
- [Dynamic LINQ (Part 1: Using the LINQ Dynamic Query Library)](http://weblogs.asp.net/scottgu/dynamic-linq-part-1-using-the-linq-dynamic-query-library)
-