---
title: LINQ 表示式(5) - Concat、Union、Intersect、Except
layout: default
parent: LINQ
nav_order: 13
description: "LINQ 表示式(5) - Concat、Union、Intersect、Except"
date: 2013-04-11
tags: [DotNetFramework,LINQ]
postid: "3094770746650523861"
---
關鍵字： Concat、Union、Intersect、Except

這些關鍵字適用於對二個集合做比對處理，例如，合併，交集，差集。  

# Concat

合併兩個序列，不會自動過濾相同項
```sql
(
from c in Customers
where c.ContactName.Contains("ann")
select new { c.CustomerID, c.ContactName }
)
.Concat
(
from c in Customers
where c.ContactName.Contains("von")
select new { c.CustomerID, c.ContactName }
)
```
```c#
Customers
.Where (c => c.ContactName.Contains ("ann"))
.Select ( c => new { c.CustomerID, c.ContactName } )
.Concat (
Customers
.Where (c => c.ContactName.Contains ("von"))
.Select ( c => new { c.CustomerID, c.ContactName } )
)
```
```sql
SELECT [t0].[CustomerID], [t0].[ContactName] FROM [Customers] AS [t0] WHERE [t0].[ContactName] LIKE '%ann%'
UNION ALL
SELECT [t0].[CustomerID], [t0].[ContactName] FROM [Customers] AS [t0] WHERE [t0].[ContactName] LIKE '%von%'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhy3gS-n9WIbZ-XbDfFmbM0eNeXFYFzDi-QuxVChRIxSpHm8YwuLnrEDbukZ0fS7OtTi0i5cuU7-kTmNdGTAqpJbT3NGCsfClSr3wvbDfnRvkXKM_eWTZ65WZr_SKYWIR1P9A5Gspb3BLI/s387/linq-concat.png)

# Union

產生兩個序列的聯集(∪)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhxYGroXGpKmzWjUEIRoa6bbZIRyJ7zd1z7lel6Tk9hv6SQCWF-J399K3BeqCIez1yNtq9VpVDHf0J_-6ykl5snhxThX20ISFqHIgDjUKUd0gJTuhIIju0c22_uOhTGE2A8S9q0wD2GVTU/s293/set-union.png)
```sql
(
from c in Customers
where c.ContactName.Contains("ann")
select new { c.CustomerID, c.ContactName }
)
.Union
(
from c in Customers
where c.ContactName.Contains("von")
select new { c.CustomerID, c.ContactName }
)
```
```c#
Customers
.Where (c => c.ContactName.Contains ("ann"))
.Select ( c => new { c.CustomerID, c.ContactName } )
.Union (
Customers
.Where (c => c.ContactName.Contains ("von"))
.Select ( c => new { c.CustomerID, c.ContactName } )
)
```
```sql
SELECT [t0].[CustomerID], [t0].[ContactName] FROM [Customers] AS [t0] WHERE [t0].[ContactName] LIKE '%ann%'
UNION
SELECT [t0].[CustomerID], [t0].[ContactName] FROM [Customers] AS [t0] WHERE [t0].[ContactName] LIKE '%von%'
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg3-unj91QdWLtQPkDyTNLmmqJuk8uvwtkK_rcXsrSyWxg_LEHdVCo6k9LmScNbSxPWkprtsXwc-gDiGUsJ7BsrP1PC_ptlsqbCd2QbLR8f0bLib3w46uphyphenhyphenHQOs4a4LQFrHzw4hPUx6Pg/s264/linq-Union.png)

# Intersect

產生兩個序列的交集(∩)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhBXwcnuMlMDN2bgQPsKMBb6PkG2S1b63K8wGVPEMdLODckMvle1kKWpDySzocBft5uBWATYw8lxqIW4zuO4cA2eQXXx86hiqmdOcvfMoTnM248m0s7cxt9Q-dlIDxPRkHjekgjoYVqQLs/s296/set-intersect.png)
```sql
(
from c in Customers
where c.ContactName.Contains("ann")
select new { c.CustomerID, c.ContactName }
)
.Intersect
(
from c in Customers
where c.ContactName.Contains("von")
select new { c.CustomerID, c.ContactName }
)
```
```c#
Customers
.Where (c => c.ContactName.Contains ("ann"))
.Select ( c => new { c.CustomerID, c.ContactName } )
.Intersect (
Customers
.Where (c => c.ContactName.Contains ("von"))
.Select ( c => new { c.CustomerID, c.ContactName } )
)
```
```sql
SELECT DISTINCT [t0].[CustomerID], [t0].[ContactName]
FROM [Customers] AS [t0]
WHERE ([t0].[ContactName] LIKE '%ann%')
And
EXISTS(
SELECT NULL FROM [Customers] AS [t1]
WHERE ([t0].[CustomerID] = [t1].[CustomerID]) And ([t1].[ContactName] LIKE '%von%')
)
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgBxpKBc-wp6H3o0SKpsAZkRWBhKqz4Q-grX3_4fZq0FVqVaVday4u8rU8YMKOmZaVwcuSFC7g9ws-_6WriiNjYZrd4LcaLmVbGvFm1nz2nQAVpA1zZbksin5y9QK4mbM3N1uXp4uFg80U/s257/linq-Intersect.png)

# Except

產生兩個序列的差集

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj78jUWuXoTWU2WCeWzSSSQd_4ZuLFiMFcYItp-FrWgluD3isaIGpRcL5x3WP75ZKpESTNy1FVB3c1CG3EtvoWRHHmxKb5X7hqN8V3JXgnf2pQrQeTpucxt26sZek2TXTKmURbxbWTip_E/s292/set-except.png)
```sql
(
from c in Customers
where c.ContactName.Contains("ann")
select new { c.CustomerID, c.ContactName }
)
.Except
(
from c in Customers
where c.ContactName.Contains("von")
select new { c.CustomerID, c.ContactName }
)
```
```c#
Customers
.Where (c => c.ContactName.Contains ("ann"))
.Select ( c => new { c.CustomerID, c.ContactName } )
.Except (
Customers
.Where (c => c.ContactName.Contains ("von"))
.Select ( c => new { c.CustomerID, c.ContactName } )
)
```
```sql
SELECT DISTINCT [t0].[CustomerID], [t0].[ContactName]
FROM [Customers] AS [t0]
WHERE ([t0].[ContactName] LIKE '%ann%')
And
NOT EXISTS(
SELECT NULL FROM [Customers] AS [t1]
WHERE ([t0].[CustomerID] = [t1].[CustomerID]) And ([t1].[ContactName] LIKE '%von%')
)
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgfvhLIIvGTAJM9dqokaibuvGARprNRawKen82FJh6WuK6obtLPzV3cIuSPz2WPGqD97T8LQq5jj_jibv0xTUAxX8rmbbrnUZNdRqLDaFkhz-BXJ0ZS3HrPHAq5O1kYDfGEwVi7LzL54ec/s265/linq-except.png)