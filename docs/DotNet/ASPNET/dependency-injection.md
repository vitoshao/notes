---
title: Dependency Injection
layout: default
parent: Dot Net
description: "依賴注入是一種軟體設計模式，也是實現控制反轉的其中一種技術，此模式確保了任何想要使用給定服務的物件不需要知道如何建立這些服務。"
date: 2025-01-15
tags:
  - Dependency Injection
---

{: .highlight }
>依賴注入是一種軟體設計模式，也是實現控制反轉的其中一種技術，此模式確保了任何想要使用給定服務的物件不需要知道如何建立這些服務。


|  Transient   | Scoped   |Singleton   |
|  ----  | ----  | ----  |
| 每次請求時都會產生新的 Instance  |  每個 http Request 都會產生一份 Instance | 整個 Application 只會有一份 Instance | 