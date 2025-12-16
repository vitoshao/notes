---
title: Common Language Runtime
layout: default
parent: Foundation
nav_order: 7
description: "Common Language Runtime"
date: 2012-03-10
tags: [DotNetFramework,Foundation]
postid: "3778878223197065007"
---
**通用語言執行平台**（Common Language Runtime，簡稱CLR）是微軟為.NET的虛擬機器所選用的名稱。  這是通用語言架構（簡稱CLI）的微軟實作版本，它定義了一個程式碼執行的環境。  CLR執行一種稱為通用中間語言的位元組碼，就是 Microsoft Intermediate Language (MSIL)。這個是微軟的通用中間語言實作版本。   

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhPH2yGo3BpBjnK3uAwMWwjbpt0JtwNrrtoa3BA_ZWApcm4ge-Y-6Fw14q3eUUaOu81bMEYQxoIUjqVJ4_MFc_B4yfHdaiHQBzIpMSZjO4sVzbiNzdw4np0ahxZdswPD8tMx-bZ8mK4Uk0/s600/Common_Language_Runtime_diagram_svg.png)

開發人員使用高階程式語言撰寫程式。接下來編譯器將程式碼編譯成微軟的中繼語言(MSIL)。執行的時候CLR會將MSIL碼轉換為作業系統的原生碼（Native code）。CLR內建有(即時編譯）編譯器。  

CLR的主要功能如下

- 1.基礎類別庫支援 Base Class Libray Support
- 2.記憶體管理 Memory Management
- 3.執行緒管理 Thread Management
- 4.記憶體自動回收 Garbage Collection
- 5.安全性 Security
- 6.型別檢查 Type Checker
- 7.例外管理 Exception Manager
- 8.除錯管理 Debug Engine
- 9.中間碼(MSIL)到機器碼(Native)編譯
- 10.類別裝載 Class Loader