---
title: Markdown 語法
layout: default
parent: MISC
nav_order: 1
description: "常用 Markdown 語法"
date: 2025-01-24
tags:
  - Markdown
---
# 快速在 vs 撰寫 markdown 文件

## 目錄
- [文字與段落](#文字與段落)
- [列表](#列表)
  - [無序列表](#無序列表)
  - [有序列表](#有序列表)
- [連結與圖片](#連結與圖片)
- [引用程式碼](#引用程式碼)
- [表格](#表格)
- [內嵌 html 語法](#內嵌-html-語法)

## 文字與段落

這是一段文字，可以使用 `**` 或 `__` 來加粗，例如：**這是加粗的文字**。__這也是是加粗的文字__	

這是另一段文字，段落之間可以使用 `空行` 來分隔。

也可以使用 `*` 或 `_` 來斜體，例如：*這是斜體的文字*。_這也是是斜體的文字_

刪除線可以使用 `~~` 來標記，例如：~~這是刪除線的文字~~

## 列表

### 無序列表
- 項目 1
- 項目 2
- 項目 3

### 有序列表
1. 項目 1
2. 項目 2
3. 項目 3

## 連結與圖片

- [Loading Related Data](https://learn.microsoft.com/en-us/ef/core/querying/related-data/)
- <a target="_blank" href="https://learn.microsoft.com/en-us/ef/core/querying/related-data/">Loading Related Data</a>

![Sample](images/sample.jpg =x250)

## 引用程式碼## 表格

| Id | CourseId | Name | Description |
|----|----------|------|-------------|
| 1  | 1        | C# 基礎 | C# 基礎課程 |
| 2  | 1        | C# 進階 | C# 進階課程 |
| 3  | 2        | SQL 基礎 | SQL 基礎課程 |
| 4  | 2        | SQL 進階 | SQL 進階課程 |

## 內嵌 html 語法

以下是一段簡單的內嵌 html 語法：

<p style="color: darkgray">
    這是一段帶有深灰色樣式的文字。
</p>
<div style="background-color: red">
    這是一段帶有紅色背景的區塊。
</div>
## 文字與段落

這是一段文字，可以使用 `**` 或 `__` 來加粗，例如：**這是加粗的文字**。__這也是是加粗的文字__	

這是另一段文字，段落之間可以使用 `空行` 來分隔。

也可以使用 `*` 或 `_` 來斜體，例如：*這是斜體的文字*。_這也是是斜體的文字_

刪除線可以使用 `~~` 來標記，例如：~~這是刪除線的文字~~

## 列表

### 無序列表
- 項目 1
- 項目 2
- 項目 3

### 有序列表
1. 項目 1
2. 項目 2
3. 項目 3

## 連結與圖片

- [Loading Related Data](https://learn.microsoft.com/en-us/ef/core/querying/related-data/)
- <a target="_blank" href="https://learn.microsoft.com/en-us/ef/core/querying/related-data/">Loading Related Data</a>

![Sample](images/sample.jpg =x250)

## 引用程式碼
```csharp
public class Teacher
{
    #nullable disable
    public int Id { get; set; }
    public string LastName { get; set; } = null!;
    public Office Office { get; set; }
}
```

## 表格

| Id | CourseId | Name | Description |
|----|----------|------|-------------|
| 1  | 1        | C# 基礎 | C# 基礎課程 |
| 2  | 1        | C# 進階 | C# 進階課程 |
| 3  | 2        | SQL 基礎 | SQL 基礎課程 |
| 4  | 2        | SQL 進階 | SQL 進階課程 |

## 內嵌 html 語法

以下是一段簡單的內嵌 html 語法：

<p style="color: darkgray">
    這是一段帶有深灰色樣式的文字。
</p>
<div style="background-color: red">
    這是一段帶有紅色背景的區塊。
</div>
