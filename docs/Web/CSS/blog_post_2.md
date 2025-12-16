---
title: CSS語法
layout: default
parent: CSS
nav_order: 2
description: "CSS語法"
date: 2012-03-08
tags: [Web,CSS]
postid: "2426680271894158567"
---
- 一、CSS語法概念
- 二、CSS套用方式
- 三、CSS串接(cascade)
- 四、CSS串接方法

# 一、CSS語法概念

#### 宣告 CSS 樣式的語法如下：
```c#
選擇器 { 
屬性：設定值； 
... 
}
```

#### CSS 的選擇器 (Selector) 可以由以下三種構成：

- element 選擇器：系統內部的選擇器，例如 div、h1。
- class 選擇器：使用者自訂的選擇器，以「.」符號開頭，例如 .sidenote 。
- id 選擇器：使用者自訂的選擇器，以「#」符號開頭，例如 #navigation 。

在一個選擇器中，可以設定的屬性數目沒有限制的。 
div
{
padding:0px 5px 5px 5px;
}

.RedBold
{
color:Red;
font-weight:bold;
}

#blog_content
{
display: block;
color: #444;
padding-left: 6px;
margin: 10px 10px 20px 8px;
line-height: 1.8em;
font-size:1em;
font-family:微軟正黑體;
}    
#### grouping

如果有數個選擇器享有同樣的樣式，它們可以同時被宣告。這叫做 "grouping"。  
h1 h2 h3 { font-size:12pt }    
#### Descendant Selectors (後代選擇器)

我們可以設定說，只有當甲元素在乙元素之內時，甲元素才會用某個樣式。  若甲元素不在乙元素內的話，那甲元素就可以有其他的樣式。  要達到這個目標，我們就要利用後代選擇器的方式。   

後代選擇器宣告的語法是：
```c#
【父選擇器】【子選擇器】{ 
屬性：設定值； 
... 
}
```

舉例來說，以下的宣告，代表在 &lt;li&gt; 內部的 &lt;b&gt; 標籤中的文字須套會黃色樣式。不是在 &lt;li&gt; 之內的 &lt;b&gt; 中的文字，就不會套用黃色字體這個樣式。
li b { 
color:yellow; 
}    
#### Class 跟 ID 的比較

這兩者最大的不同，是在於 ID 選擇器在一個 HTML 文件中只能被使用一次，而 Class 選擇器在一個 HTML 文件中可以被使用多次。  第二個不同的地方，是 ID 選擇器可以被 Javascript 中的 GetElementByID 函數所運用，而 Class 選擇器無法被 Javascript 運用到。  

並沒有什麼固定的規則，來決定什麼時候要用 ID 及什麼時候要用 Class。  我們的建議是盡量用 Class，因為這樣子最靈活 (同一個 HTML 文件可以利用這類的選擇器多次)。  唯一的例外，是當你要用 Javascript 的 GetElementByID 函數時。在這個情況下，你就應該要用 ID。  

Class 名稱及 ID 名稱都是對大小寫敏感的。舉例來說，.classone 及 .ClassOne 是代表兩個不同的 Class 選擇器。

# 二、CSS套用方式

#### 1.行內套用

將CSS樣式，直接套用在 tag 上面。
```xml
<h1 style="color:red">標題</h1>
```

#### 2.嵌入套用

將CSS樣式表，獨立設定在文件中的&lt;head&gt;&lt;/head&gt;區塊中間。
&lt;style type="text/css"&gt;   h1 { color:red; }h1 h2 h3 { font-size:12pt }
&lt;/style&gt;    
#### 3.外部連接套用

將CSS樣式表，獨立在一個檔案中，再使用連結方式套用。
&lt;link rel=stylesheet type="text/css" href="external-stylesheet.css"&gt;    
#### 4.匯入套用
&lt;STYLE TYPE="text/css"&gt; 
&lt;!-- 
@import url(http://www.mysite.com/style.css); 
--&gt; 
&lt;/STYLE&gt;  
**import** 指令最初的用意，是為了能夠針對不同的瀏覽器而運用不同的樣式。  不過，現在已經沒有這個必要。現在用 @import 的目的，最常是要加入多個 CSS 樣式。  當多個 CSS 樣式被 @import 的方式加入，而不同 CSS 樣式互相有衝突時，後被加入的 CSS 樣式有優先的順位

# 三、CSS串接(cascade) 

『串接』(cascade) 是指當不同樣式表中對相同屬性有不同定義時，應該要用哪一個樣式表中的定義的規則。  如果只有一個樣式表，那串接這個部分就不重要。  若一個 HTML 文件中包含有多個樣式表，那串接這個概念就非常重要了。  

最基本的規則是，**愈接近 HTML 本身的樣式優先權愈大**。因此：  

1. **行內套用**的樣式通常會有最高的優先權，因為它最接近 HTML 的元素。
2. 接下來的是**嵌入套用**的樣式表。這一類的樣式表通常是在&lt;head&gt;&lt;/head&gt;內宣告的。
3. 再下來是**匯入套用**的樣式表，若有多個樣式表被匯入，愈後面被匯入的愈有優先權。
4. 優先權最低的是**外部連接套用**的樣式表，若有多個外部樣式表被連接，愈後面被連接的愈有優先權。

另外，每一個瀏覽器也都有自己的樣式表 (這就是為什麼超連接文字通常會是藍色，而且下面有一條線)。  這一類的樣式表優先權比以上的幾種都低。  

#### 樣式表套用優先順序：（最高-&gt;最低）

- 1. 內行套用的樣式表 (Inline stylesheet)
- 2. 嵌入套用的樣式表 (Embedded stylesheet)
- 3. 匯入套用的樣式表 (Imported stylesheet)
- 4. 外部連接套用的樣式表 (Linked stylesheet)
- 5. 瀏覽器本身的樣式表 (Browser's own stylesheet)

# 四、CSS串接方法

#### 1. 直接定義 element 的樣式

#### 使用行內套用
```xml
<h1 style="color:red">XXX</h1>
```

#### 使用嵌入套用
h1 { color:red; }h1 h2 h3 { font-size:12pt }    
#### 2. 利用 Class

舉例來說：
.navbar  {color:#0000FF;}  ```xml
<span class="navbar">這是用 Class 選擇器的例子。</span>
```

#### ※ 補充1：可以針對不同 tag 設定相同 class 名稱

這樣同一個 class 名稱，在不用 tag 下，就有不同表現。例如：
h1.navbar  {font-size:12pt}
h2.navbar  {font-size:10pt}  ```xml
<h1 class='navbar'>head1</h1>
<h2 class='navbar'>head2</h2>
```

#### ※ 補充2：多重套用class
.navbarA  {font-size:12pt}
.navbarB  {color:blue}  ```xml
<h1 class='navbarA navbarB'>head1</h1>
```

#### ※ 補充3：共同屬性可先獨立出來
.navbarA .navbarB {color:red}
.navbarA {font-size:12pt}
.navbarB {font-size:10pt}              
#### 3. 利用 ID 宣告法，前面放一個井字號 (#)
#footer { color:#FF00FF; }  ```xml
<div id="footer">這是用 ID 選擇器的例子。</div>
```
## 參考資料  

- [CSS 的規則繼承和權重](http://www.joetsuihk.com/css_specificity_for_poker_players)
- [CSS 語法教學](http://css.1keydata.com/tw/)