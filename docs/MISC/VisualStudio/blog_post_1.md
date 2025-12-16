---
title: FileTracker Error
layout: default
parent: VisualStudio
nav_order: 1
description: "FileTracker Error"
date: 2012-03-08
tags: [MISC,VisualStudio]
postid: "5933135556095884558"
---
#### FileTracker : error FTK1011

當舊版程式轉換成新版VS2010時,可能會遇到這個問題。

有兩種方法可以處理這問題。

- 1、修改Framewotk=4.0。
- 2、新增環境變數：我的電腦=&gt;右鍵=&gt;內容=&gt;進階系統設定=&gt;環境變數=&gt;新增系統變數=&gt;名稱：TRACKFILEACCESS，值：False