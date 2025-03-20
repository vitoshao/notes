---
title: EF Loading Policy
layout: default
parent: Entity Framework
description: "EF Core 提供三種 Loading Policy，讓您在模型中使用導覽屬性來載入相關實體，分別是 Lazy Loading、Eager Loading 與 Explicit Loading。"
date: 2025-01-21
tags:
  - Lazy Loading
  - Eager Loading
  - Explicit Loading
---


使用 Entity Framework，最方便的事情就是可以直接透過 `導覽屬性` 來存取相關聯的資料，但使用上，若沒弄清楚做法，反而可能導致資料庫查詢的效能問題。

EF Core 提供三種 Loading Policy，讓您在模型中使用導覽屬性來載入相關實體，分別是 `Lazy Loading`、`Eager Loading` 與 `Explicit Loading`。

在 EF6 以前，預設的 Loading Policy 是 Lazy Loading，也就是說，當導覽屬性第一次被存取時，才自動載入關聯資料。
但從 EF Core 開始，預設的 Loading Policy 是 Eager Loading，所以，若要透過導覽屬性存取關聯的資料，這些關聯資料都必需事先明確指定載入。


## 積極載入 (Eager Loading)

積極載入是 EF Core 預設的 Loading Policy，查詢時，若要同時載入相關聯的資料，必需使用 <a target="_blank" href="https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include?view=efcore-9.0">Include</a> 方法明確指定，這樣就可以一次查詢出所有相關聯的資料，避免多次查詢資料庫。

使用 `include` 方法，可以在單一查詢戴入一個或多個關聯資料
```csharp
    var instructor1 = dbContext.Instructors
        .First(x => x.Id == 1);

    //因為沒有明確指定要載入 Experiences, 所以 instructor.Experiences 不會有資料
    foreach (var experience in instructor1.Experiences)
    {        
        Debug.WriteLine(experience.Description);
    }

    var instructor2 = dbContext.Instructors
        .Include(ins => ins.Experiences)
        .Include(ins => ins.Office)
        .First(x => x.Id == 1);

    Debug.WriteLine(instructor2.Office?.Location);
    foreach (var experience in instructor2.Experiences)
    {
        Debug.WriteLine(experience.Description);
    }
```

使用 `ThenInclude` 方法，可以在單一查詢向下切入關聯性以包含多個層級的相關資料。
```csharp
    var instructor3 = dbContext.Instructors
        .Include(ins => ins.Experiences)
        .Include(ins => ins.Office)
        .Include(ins => ins.Courses)
        .ThenInclude(cus => cus.Enrollments)
        .First(x => x.Id == 1);

    foreach (var experience in instructor3.Experiences)
    {
        Debug.WriteLine(experience.Description);
    }

    foreach (var course in instructor3.Courses)
    {
        foreach (var enrollment in course.Enrollments)
        {
            Debug.WriteLine($"{enrollment.StudentId} {enrollment.Grade}");
        }
    }
```


## 明確載入 (Explicit Loading)	

明確載入是指，在查詢取得實體物件後，再透過 <a target="_blank" href="https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.dbcontext.entry?view=efcore-9.0">Entry</a> 的 <a href="https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.changetracking.entityentry-1.collection?view=efcore-9.0" target="_blank">Collection</a> 或 <a href="https://learn.microsoft.com/en-us/dotnet/api/microsoft.entityframeworkcore.changetracking.entityentry-1.reference?view=efcore-9.0" target="_blank">Reference</a> 方法，明確指定要載入的關聯資料。
```csharp
    var instructor = dbContext.Instructors.First(x => x.Id == 1);

    //因為沒有明確指定要載入 Experiences, 所以 instructor.Experiences 不會有資料
    foreach (var experience in instructor.Experiences)
    {
        Debug.WriteLine(experience.Description);
    }

    //載入集合導覽屬性 Experiences
    dbContext.Entry(instructor)
        .Collection(x => x.Experiences)
        .Load();

    foreach (var experience in instructor.Experiences)
    {
        Debug.WriteLine(experience.Description);
    }

    //載入參考導覽屬性 Office
    dbContext.Entry(instructor)
        .Reference(x => x.Office)
        .Load();
```

## 消極載入 (Lazy Loading)

消極載入是指當導覽屬性被存取時，才自動載入關聯資料，這樣可以避免一次查詢出所有相關聯的資料。但是若使用不當，可能會導致 N+1 問題。

```csharp
var companies = _companyRepository.FetchAll()
    .Where(x => x.Id > 100)
    .Take(5);

foreach (var company in companies)
{
    foreach (var branch in company.Branches)
    {
        Debug.WriteLine($"Branch: {branch.CName}");
    }
}
```
這個範例會載入 Company 資料，然後在需要時才載入 Branch 資料。

### 設定使用 Lazy Policy

在 EF Core 中，如果要使用 Lazy Loading，心須先安裝 `Microsoft.EntityFrameworkCore.Proxies` 套件，
同時在註冊 DbContext 時，啟用 Lazy Loading　這個載入機制。
```csharp
builder.Services.AddDbContext<YourDbContext>(options => options
    .UseLazyLoadingProxies()
    .UseSqlServer(builder.Configuration.GetConnectionString("YourDbContextConnection")));
```

## 參考資料
- <a target="_blank" href="https://learn.microsoft.com/en-us/ef/core/querying/related-data/">Loading Related Data</a>
