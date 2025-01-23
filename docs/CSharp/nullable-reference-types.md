---
title: nullable reference types
layout: default
parent: C Sharp
nav_order: 3
description: "「可為 NULL 參考型別」是 C# 8 新導入的功能，它用來明確定義：一個參考型別是否允許 null。這個功能可以幫助開發者在設計階段就發現潛在的 NullReferenceException 問題，提高程式碼的品質。"
date: 2025-01-23
tags:
  - nullable reference types
  - C# 8
---

「可為 NULL 參考型別」是 C# 8 新導入的功能，它用來明確定義：一個參考型別是否允許 null。這個功能可以幫助開發者在設計階段就發現潛在的 NullReferenceException 問題，提高程式碼的品質。

早先，參考型別（reference types）的變數，預設允許 null ？也就是如果沒有給值，其預設值就是 null。
這樣的設計，當一個參考型別變數值為 null 時，若直接使用就會導致 NullReferenceException。
為了解決這個問題，C# 8 引入了「可為 NULL 參考型別」（nullable reference types）的概念，幫助開發者在設計階段就發現潛在的 NullReferenceException 問題。

## 未啟用可為 NULL 參考型別

下面這段程式碼，`Office` 屬性是一個參考型別，但沒有初始化，所以預設值是 null。
因為未啟用「可為 NULL 參考型別」，所以在設計階段，編譯器不會檢查是否有可能產生 NullReferenceException。

![Null Disable](images/null-disable.png)

## 啟用可為 NULL 參考型別

在 C# 8 中，可以透過在檔案的最上方或需要啟用新語法的地方加上 `#nullable enable` 來啟用「可為 NULL 參考型別」。
下面這段程式碼，因為已經啟用「可為 NULL 參考型別」，所以在宣告屬性時，就必須明確標註是否允許 NULL。
這樣的改變，讓編譯器在設計階段，就可以檢查出是否有可能產生 NullReferenceException。

![Null Enable](images/null-enable.png)



