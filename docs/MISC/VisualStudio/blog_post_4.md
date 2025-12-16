---
title: Visual Studio 尋找和取代
layout: default
parent: VisualStudio
nav_order: 4
description: "Visual Studio 尋找和取代"
date: 2013-04-16
tags: [MISC,VisualStudio]
postid: "4381669165635571118"
---
這篇文章要介紹，如何善用 Visual Studio 「尋找和取代」這個工具

下表是常用的運算式語法，其他語法請參考 [MSDN](http://msdn.microsoft.com/en-us/library/vstudio/2k3te2cs.aspx) 

![](http://127.0.0.1:8080/_images/cs/vs-regular-exp.png)

### 範例一：插入斷行符號

若想將特定的二個文字中間插入斷行，例如：在所有的「&gt;」和「&lt;」這二個文字中間插入斷行，可以這麼做：

- 尋找選項：　使用規則運算式
- 尋找目標：　\&gt;\&lt;
- 取代字串：　\&gt;\n\&lt;

### 範例二：取代跳行

若文件因為跳行很亂，如下文，你可以這麼做

假設你有如下的資料：

![](http://127.0.0.1:8080/_images/cs/vs-find-replace-1.png)

你希望調整如下：

![](http://127.0.0.1:8080/_images/cs/vs-find-replace-2.png)

- 尋找選項：　使用規則運算式
- 尋找目標：　&lt;p&gt;:b\*\n:b\*&lt;     　→　     \&lt;p\&gt;:b\*\n:b+\&lt;
- 取代字串：　&lt;p&gt;&lt;　　　       　→　     \&lt;p\&gt;\&lt;

\n：斷行  
:b：空白或Tab  
\*：表零或多個  
+：表一或多個

### 範例三：越短越好

下面這個範例，你想將 span 中的屬性都刪除，你可以尋找以下目標：「( style).\*\&gt;」，不過，你會發覺比對到的結果如下：

![](http://127.0.0.1:8080/_images/cs/vs-find-replace-3.png)

若你希望比對到的字串，以較短為原則，你可以更改尋找目標如下：「( style).＠\&gt;」

![](http://127.0.0.1:8080/_images/cs/vs-find-replace-4.png)
## 參考資料  

- [尋找和取代視窗、檔案中尋找](http://msdn.microsoft.com/zh-tw/library/dechx2tz%28v=vs.110%29.aspx)
- [Using Regular Expressions in Visual Studio](http://msdn.microsoft.com/en-us/library/vstudio/2k3te2cs.aspx)
- [Using Regular Expressions in Visual Studio](http://msdn.microsoft.com/en-us/library/2k3te2cs.aspx)