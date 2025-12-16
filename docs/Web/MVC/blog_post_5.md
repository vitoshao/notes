---
title: Code First
layout: default
parent: MVC
nav_order: 5
description: "Code First"
date: 2016-03-14
tags: [Web,MVC]
postid: "7852511697044270722"
---
#### 建立 Api 專案使用的 LocalDB 環境

LocalDB 是微軟推出的一種適合在開發環境使用的資料庫，相較於 Sql Server Express，它在安裝上和建立新的執行個體時，速度上都快非常的多，再搭配上 Entity Framework 的 Seed 功能，就可以快速的建立一個開發用的測試環境。  若安裝 VS2012 ，預設會連同 LocalDB 一起安裝，或者在安裝 SQL Server Express 2012 中，也有選擇性安裝 LocalDB 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgHCTW8c9JS7Hq0KeyNm8SzqxnegesN1pcHX4hsTjVTbeMLtYPNc2832_w1mA2aJyah_8-Us96HakplRBVBKFlkEL0YbEm1B3XXmtxSEGlL83MTn5Y_R9YhxHdtXzjFFykMY0yOAZqMGOk/s0/mvc-cf-01.png)

若要單獨安裝，可以到 MS [下載網頁](https://www.microsoft.com/en-us/download/details.aspx?id=29062)中，下載 SqlLocalDB.msi 進行安裝。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgBY_gM7j9GTs4SsWqW64h0ZksGtQpVKUMvVXxI2_B59eAcXn37GtqaaXgRm9xGgV4pMsydlWwaiPAxmLl6du5eQvNJ0WqdidYrXqNRYeA-vvKuVqff1tR6NBmxhn2EP9xVAY6MrIFjjDE/s0/mvc-cf-02.png)

關於 LocalDb 的操作，可以參考

- [SQL Server 2014 Express LocalDB](https://msdn.microsoft.com/zh-tw/library/hh510202%28v=sql.120%29.aspx)
- [命令列管理工具：SqlLocalDB.exe](https://msdn.microsoft.com/zh-tw/library/hh247716%28v=sql.120%29.aspx)

#### 使用 SSMS 檢視資料庫

使用 Code First 建立資料庫時，它會依據 Web.config 中的連線字串的設定，將資料庫建立在指定的 LocalDb 執行個體。  如下面範例中的設定，「v11.0」就是執行個體，「ContosoUniversity1」就是資料庫名稱，如果在建立資料庫，執行個體不存在，那麼就會先自動建立執行個體。  
```xml
<connectionstrings>
<add name=&quot;SchoolContext&quot; connectionString=&quot;Data Source=(LocalDb)\v11.0;Initial Catalog=ContosoUniversity1; Integrated Security=SSPI;&quot; providerName=&quot;System.Data.SqlClient&quot; />
</connectionstrings>
```

#### 使用 Code First 建立的資料庫檔案在哪裡

使用 Code First 建立資料庫，預設會先檢查系統中是否有安裝 SQL Express ，如果有就會將資料庫建立在上面，如果沒有才會建立在 LocalDb 上。  LocalDb 預設的位置在 C:\Users\YourCurrentUserName 。  

#### 使用 SSMS 檢視資料庫

除了透過 SqlLocalDB 指令，你也可以直接透過 SSMS 連至 localdb ，確認資料庫是否成功建立。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEih_IgkNKijck1CWc5_kNBNeWtjqTMVx5w4rC3wH8yzkM-rziyCwkrrD7nrii15UVrsonJda3qXNLHynpjr9OgqgdZqO_gD7RXULqvKZ7xqIcQu0jTAGd5UUMpSzX4F621R_t2zgtdXkKA/s0/mvc-cf-03.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgqNQRjkmLpur3ZhBHYSHDUXVso64-SH9zIC2aej_h_LH2Ak2BABC22P3nhO2VLXDIidC-Zrks5qHU1BYLKuusyg7HSw80T3dSUf9F0Q9LXM4jFZFUfdRfUVASHB8apaqGkTCqmzovfwQA/s0/mvc-cf-04.png)

#### 慣例（Conventions）

- 預設會使用 entity class 的複數(pluralized)當做 table name 。
- entity class 的 property name 當做 column name 。
- 若 property name 為 ID 或 classnameID ，將被當做 primary key 。
- 預設會使用 database name 當做 connection string 的設定名稱。

## 參考資料  

- [Entity Framework Code First 資料庫初始化設定](http://blog.developer.idv.tw/2014/10/entity-framework-code-first_31.html)
- [使用Asp.Net MVC打造Web Api (5) - 用EntityFramework CodeFirst建立資料庫](http://ithelp.ithome.com.tw/question/10133761)
-