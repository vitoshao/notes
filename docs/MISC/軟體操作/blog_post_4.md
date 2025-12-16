---
title: SyntaxHighlighter 在 Blogspot 上的設定
layout: default
parent: 軟體操作
nav_order: 4
description: "SyntaxHighlighter 在 Blogspot 上的設定"
date: 2012-06-15
tags: [MISC,軟體操作]
postid: "2742291617769812699"
---
# 如何在 blogspot 中，使用 syntax highlighter 3.x

執行修改範本［ 範本 -&gt; 修改HTML -&gt; 繼續］，進入後勾選［展開小裝置範本］：

在 &lt;head&gt;&lt;/head&gt; 之間，加入以下 script 宣告
&lt;!-- 設定需要載入的 script   --&gt;
&lt;script src="https://sites.google.com/site/sharedvito/syntaxhighlighter3x/shCore.js" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="https://sites.google.com/site/sharedvito/syntaxhighlighter3x/shBrushXml.js" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="https://sites.google.com/site/sharedvito/syntaxhighlighter3x/shBrushCss.js" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="https://sites.google.com/site/sharedvito/syntaxhighlighter3x/shBrushJScript.js" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="https://sites.google.com/site/sharedvito/syntaxhighlighter3x/shBrushCSharp.js" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="https://sites.google.com/site/sharedvito/syntaxhighlighter3x/shBrushVb.js" type="text/javascript"&gt;&lt;/script&gt;
&lt;script src="https://sites.google.com/site/sharedvito/syntaxhighlighter3x/shBrushSql.js" type="text/javascript"&gt;&lt;/script&gt;
&lt;link  href="https://sites.google.com/site/sharedvito/syntaxhighlighter3x/shCore.css" type="text/css" rel="stylesheet" /&gt;
&lt;link  href="https://sites.google.com/site/sharedvito/syntaxhighlighter3x/shThemeDefault.css" type="text/css" rel="stylesheet" /&gt;

&lt;script src="https://sites.google.com/site/sharedvito/blogfile/blog.js" type="text/javascript"&gt;&lt;/script&gt;

&lt;!-- 設定啟用 highlighter --&gt;
&lt;script type="text/javascript"&gt;
SyntaxHighlighter.config.bloggerMode = true;
SyntaxHighlighter.defaults['toolbar'] = false; //隱藏問號按鈕
SyntaxHighlighter.all()
&lt;/script&gt;    ## 參考資料  

- [SyntaxHighlighter3.0.83](http://alexgorbatchev.com/SyntaxHighlighter/donate.html)
- [Brushes alias](http://alexgorbatchev.com/SyntaxHighlighter/manual/brushes/)

# 在 blogspot 在標籤頁或搜尋頁中，如何只顯部分內容

首先在修改範本中，找到下行資料
```xml
<b:include data='post' name='post'/>
```

將上行資料，以下面資料取代
```c#
<b:if cond='data:blog.homepageUrl != data:blog.url'>    <b:if cond='data:blog.pageType != "item"'>      <!-- Show article title only -->      <div class='post-title'>      <ul><li><a expr:href='data:post.url'><span style='color: #40007f; font-size: 18px; font-family: 微軟正黑體;'><data:post.title/></span></a></li></ul>      </div>      <div class='item-snippet'><data:post.snippet/></div><br/>    <b:else/>    <b:include data='post' name='post'/>
</b:if>  <b:else/>  <b:include data='post' name='post'/>
</b:if>  ```