---
title: jQuery 選取元素
layout: default
parent: JavaScript
nav_order: 4
description: "jQuery 選取元素"
date: 2013-02-05
tags: [Web,JavaScript]
postid: "7790005006176788569"
---
jQuery 最基本的中心思想就是以「選取 DOM 元素為開始」，接著就是對它們作一些事。  

jQuery 在選取元素方面採用 CSS 選擇器的語法 (CSS1、CSS2、CSS3)，此外透過 plugin 你也可以使用 XPath 語法，我們直接看幾道範例你就會明白怎麼在 jQuery 中用 CSS Selectors 選取元素：  

# 使用 jQuery 選取元素

## selector 的基本用法

jQuery Selector 很重要的精神便在於企圖在 Javascript 裡實踐 CSS3 Selector 的效果，一方面神奇地簡化了取得元素的過程，再者，網頁設計人員多少有點 CSS Selector 的基礎，因此不需額外學習就能在 jQuery 中輕易上手。  

#### jQuery Selector 語法 (Syntax)
```javascript
$(selectors);
```

在 jQuery 裡，Selector 尋找元素可由以下幾個方面著手：  

## Select by element

jQuery 中
```javascript
$("a"); // 取得頁面中所有的<a>標籤元素
```

JS DOM 中
```javascript
document.getElementsByTagName("a");
```

CSS 中 
a {}

## Select by ID

jQuery 中
```javascript
$("#container"); // 取得id為container的元素
```

JS DOM 中
```javascript
document.getElementById("container");
```

CSS 中 
#container {}

## Select by CSS

jQuery 中
```javascript
$(".content"); // 取得class name為content的所有元素
```

JS DOM 中
```javascript
document.getElementsByClassName("content");  //// FF3、或自己實作這個函式
```

CSS 中 
.content {}

## selector 的進階用法

jQuery 的 selector 很彈性，你可以組合以上三種做法，也可以使用類似 CSS 中的串接樣式的寫法，來選取你要的元素。  

### 組合式寫法

#### 取得 id 為 container 之元素其內部的所有連結
```javascript
$('#container a');
```

#### 取得所有 div 和 p 元素
```javascript
$("div, p"); 
```

#### 取得 div 父元素其下所有的 p 子元素
```javascript
$("div p"); 
```

#### 取得 css 為 xyz 的 div 元素
```javascript
$("div.xyz")
```

### 使用關鍵字

你可以用以下關鍵字來加強你的 selector  

- [:first](http://api.jquery.com/first-selector/) ：find the first instance of the matched element.
- [:last](http://api.jquery.com/last-selector/) ：
- [:even](http://api.jquery.com/even-selector/) ：
- [:odd](http://api.jquery.com/odd-selector/) ：
- [:not()](http://api.jquery.com/not-selector/) ：
- [:first-child](http://api.jquery.com/first-child-selector/) ：select the all first child elements inside matched elements
- [:last-child](http://api.jquery.com/last-child-selector/) ：
- [:nth-child()](http://api.jquery.com/nth-child-selector/) ：select the nth child element inside the matched element
- [:contains()](http://api.jquery.com/contains-selector/) ：
- [:enabled](http://api.jquery.com/enabled-selector/) ：
- [:disabled](http://api.jquery.com/disabled-selector/) ：this selector works for select buttons.
- [:checked](http://api.jquery.com/checked-selector/) ：this selector works for checkboxes and radio buttons.
- [:hidden](http://api.jquery.com/hidden-selector/) ：
- [:visible](http://api.jquery.com/visible-selector/) ：
- [:button](http://api.jquery.com/button-selector/) ：

#### ◎取得第一個找到的 tr 標籤元素
```javascript
$("tr:first"); 

$("#myTable tr:odd"); //選取 myTable 的單數列
```

#### ◎選取所有的 radio input 元素
```javascript
$("input:radio") 
```

#### ◎取得所有的 checkbox 被 checked 數量
```xml
<input type="checkbox" name="chk_group" value="value1" checked="checked" />Value 1<br />
<input type="checkbox" name="chk_group" value="value2" />Value 2<br />
<input type="checkbox" name="chk_group" value="value3" />Value 3<br />
<input id="Button1" type="button" value="button" />
```
```javascript
<script type="text/javascript">
$(function () {
$("#Button1").click(function () {
alert($("input:checked").length);
});
});
</script>
```

### 使用搜尋字元

你可以針對特定的屬性內容來設定 selector ，並用以下搜尋字元來加強你的 selector  

- \* ：Searches for the search term in all text in the string
- ^ ：Searches for the search term at the beginning of the string
- $ ：Searches for the search term at the end of the string
- ! ：Searches for elements whose attributes do not match the search string
```javascript
$("a[href\*=MyDomain]")                  //連結中有 MyDomain

("a[href^=Folder/]")                    //連結以 Folder/ 開頭

$(a[href$=.com]")                       //連結以 .com 結束

$(a[href!=http://www.MyDomian.com]")    //連結中沒有 http://www.MyDomian.com
```

#### ◎取得其 name 屬性值為 newsletter 的 input 元素
```javascript
$(function () {
$("#Button1").click(function () {
alert($("input[name='chk_group']:checked").length);
});
});
```

# jQuery 取回的元素型態

## jQuery v.s. DOM

使用 jQuery 物件 ($) 進行匹配，不管結果是單個或多個，回傳的都是一個「**陣列**」型態的「**jQuery 物件**」，  也就是說你可以像下面這樣取得被匹配到元素的個數：  
```javascript
$('a').length;      // 由 length 屬性取得陣列長度
$('a').size();      // 由 size() 方法取得陣列長度
```

所以下面範例是錯誤的，因為，jQuery 物件並沒有 style 屬性，那是 DOM 物件的屬性  
```javascript
$("#container").style.display = "none";
```

jQuery 物件只能使用 jQuery 的方法；而 DOM 物件只能使用 DOM 物件的方法。

## jQuery to DOM

####  [.size()](http://api.jquery.com/size/) ：size() 或 length 傳回群組的物件個數

####  [.eq()](http://api.jquery.com/eq/) ：eq(N) 取出群組中第 N 個 jQuery 物件

####  [.get()](http://api.jquery.com/get/) ：傳回元素的陣列；.get(N) 取出第 N 個元素

如果你想操作實際的 DOM 元素怎麼辦？ jQuery 提供下面的方法來處理這問題：

使用 [.get()](http://api.jquery.com/get/) 取得與 jQuery objct 相對應的 DOM elements
```javascript
/\* 轉換為真實 DOM 元素集合 \*/
var container = $("#container").get();

/\*
取得 $(".containers") 元素集合裡的第X個 DOM 元素
get後面接的是索引值，索引值由0開始
\*/
var containers = $(".containers").get(0);

var container = $("#container")[0];     //或，您也可以這樣
```

所以，我們再來看看，可以正確執行的 Code 應該是這樣的：
```javascript
$("#container").get(0).style.display = "none";
```

## DOM to jQuery

如果你想操作實際的 DOM 元素怎麼辦？ jQuery 提供下面的方法來處理這問題：
```javascript
jQuery(elements);
或
$(elements);
```

如：
```javascript
$(document.getElementById("id"));
```

example 2
```xml
<div>DIV 1</div>
<div>DIV 2</div>
<div>DIV 3</div>
```
```javascript
// 由<a>標籤自訂一個 jQuery 物件, 並設定連結與文字。
var obj = $("<a>").attr("href", "http://abc").text("abc");

// 將 obj 加入第一個 div 之中
$("div").last().append(obj);
```
```xml
<div style="COLOR: red"> DIV 1</div>
<div style="COLOR: red">DIV 2</div>
<div style="COLOR: red">DIV 3<a href="http://abc">abc</a></div>
```
## 參考資料  
![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [jQuery Tutorial](http://www.tutorialspoint.com/jquery/index.htm)
- [jQuery API](http://api.jquery.com/)
- [jQuery教學](http://webdesign.kerthis.com/jquery/)
- [jQuery 邊做邊學系列](http://msdn.microsoft.com/zh-tw/dd310332.aspx#jQuery)