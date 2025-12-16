---
title: Sample
layout: default
parent: Entity Framework
nav_order: 1
description: "...。"
date: 2025-01-15
tags: [Update, Bulk Update, Batch Update]
nav_exclude: true
---

使用 Entity Framework 進行資料更新, 基本上有以下幾種方法：

## 使用 EF Core 的變更追蹤功能

這種方法是透過查詢取得需要更新的實體，然後逐一修改每個實體的屬性，最後呼叫 `SaveChanges()` 方法來將變更保存到資料庫。這種方法適用於少量資料的更新，但在大量資料更新時效能較差，因為每次更新都會產生多次的 SQL 命令。

```csharp
using (var context = new MyDbContext())
{
    var employees = context.Employees.Where(e => e.Department == "IT").ToList();

    foreach (var employee in employees)
    {
        employee.Salary *= 1.1m; // Increase salary by 10%
    }

    context.SaveChanges();
}
```
### 優點:
- 簡單易懂，符合物件導向的設計理念。
- 善用 EF Core 的追蹤功能

### 缺點:
- 將所有實體載入記憶體。
- 效能較差，特別是在大量資料更新時，因為會產生多次的 SQL 命令。

## 使用 ExecuteUpdate
從 EF Core 7.0 開始，提供了 `ExecuteUpdate` 方法，可以直接在查詢上進行批次更新，而不需要將實體載入記憶體。這種方法適用於大量資料的更新，效能較好。
```csharp
using (var context = new MyDbContext())
{
    var employees = context
        .Employees
        .Where(e => e.Department == "IT")
        .ExecuteUpdate(e => e.SetProperty(emp => emp.Salary, emp => emp.Salary * 1.1m));

}
```
### 優點:
- 不需要將實體載入記憶體，效能較好。

### 缺點:
- 需要 EF Core 7.0 或更新版本支援。


## 使用原始 ExecuteSqlRaw

這種方法是使用第三方套件（如 Entity Framework Extensions）來進行批次更新，這樣可以在單一的 SQL 命令中更新多筆資料，提升效能。這種方法適用於大量資料的更新，但需要額外的套件支援。

```csharp
using (var context = new MyDbContext())
{
    var department = "IT";

    string sql = "UPDATE Employees SET Salary = Salary * 1.1 WHERE Department = @Department";

    context.Database.ExecuteSqlRaw(sql, new SqlParameter("@Department", department));
}
```
### 優點:
- 只執行一個 UPDATE 陳述式，效能較高，適用於大量資料更新。
- 避免將實體載入記憶體。

### 缺點:
- 需要額外的套件支援（如 Entity Framework Extensions）。
- 需要手動確保 SQL 語法正確。

{: .note-title}
> ExecuteSqlRaw vs ExecuteUpdate
>
> - 優先用 ExecuteUpdate：當你想批次更新符合 LINQ 條件的資料時.
> - 使用 ExecuteSqlRaw：當你需要更複雜的 SQL 操作，或是你的 EF Core 版本不支援 ExecuteUpdate 時。


## 使用EFCore.BulkExtensions
另一種高效能的批次更新方法是使用 EFCore.BulkExtensions 套件。這個套件提供了簡單的 API 來進行批次更新操作，並且能夠自動生成高效的 SQL 命令。
```csharp
using (var context = new MyDbContext())
{
    var employees = context.Employees.Where(e => e.Department == "IT").ToList();

    foreach (var employee in employees)
    {
        employee.Salary *= 1.1m;
    }

    context.BulkUpdate(employees);
}
```
### 優點:
- 維持 EF Core 的整合。
- 高效能，適用於大量資料更新。
- 減少資料庫往返次數。

### 缺點:
- 需要額外安裝 EFCore.BulkExtensions 套件。



## 參考資料
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>