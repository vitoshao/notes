---
title: jQuery 篩選元素
layout: default
parent: JavaScript
nav_order: 6
description: "jQuery 篩選元素"
date: 2013-02-05
tags: [Web,JavaScript]
postid: "6709333918413119407"
---
jQuery 的選取元素 (selectors) 是直接從根元素去尋找其下的後代子元素，而我們這裡要探討如何去進一步「篩選」。  jQuery 的 Traversing 能幫我們做這些過濾、查訪元素的動作。  

# 巡覽元素 (Traversing)

The result returned by a jQuery selector is a set of elements that match the selection criteria.  Even if you only get a single element back in your selection, jQuery gives you a set of elements.  This allows you to work with a single item or a collection of items by using the same syntax.  

####  [.each()](http://api.jquery.com/each/) ：Iterate over a jQuery object, executing a function for each matched element. 
```xml
<div class="xyz">1</div>
<div class="xyz">2</div>
<div class="xyz">3</div>
<div class="xyz">4</div>
<input id="Button2" type="button" value="Button2" />
```
```javascript
<script type="text/javascript">
$(function () {
$("#Button2").click(function () {
$("div.xyz").each(function (index) {
this.innerHTML = "Section " + index;
});
});
});
</script>
```

####  [.add()](http://api.jquery.com/add/) ：Add elements to the set of matched elements.

這個方法可以用來加入新的 selector 
```javascript
$set1 = $("div.xyz");           //selectors 中原本只有符合 div.xyz 的元素

$set2 = $set1.add("div.abc");   //將符合 div.abc 的元素也加入到 selectors 中
</script>
```

# 過濾元素 (Filtering)

jQuery 有提供一些函數幫助我們方便的「濾出」我們要的目標元素：

## 透過 index 取得特定物件

選取器取回的是一個jQuery物件陣列，我們可以透過索引，以取得特定的 jQuery 物件。  這個 index 是從 0 開始。  

####  [.eq(n)](http://api.jquery.com/eq/) ：取出集合中第 N 個 jQuery 物件
```javascript
//取得匹配的第3個元素
$("p").eq(2);

$("img", this).eq(1).attr("src", "head2_r_16.png");
```

####  [.get(n)](http://api.jquery.com/get/) ：取出集合中第 N 個 DOM 物件

相較於 eq(index) 得到的是 jQuery 物件，而 get(index) 得到的則是 DOM 物件。  

$("span").get(0) = $("span")[0]

## 篩選與刪除

####  [.filter()](http://api.jquery.com/filter/) ：篩選出與表達式匹配的元素（篩選自身集合）

####  [.not()](http://api.jquery.com/not/) ：刪除與表達式匹配的元素

這二個方法的參數表達式，都可以使用逗號分開，表示多個 expr。
```javascript
//例：取得類別為highlight的所有段落元素
$("p").filter(".highlight");

例：刪除掉類別為green的及id為blueone的元素
$("div").not(".green, #blueone");
```

####  [.slice()](http://api.jquery.com/slice/) ：只保留指定索引區間的元素。

使用 slice(start[, end]) 方法，它會去除索引 start 之前的元素，或 end 開始後面元素。（索引由 0 開始）
```javascript
$set1 = $("div.xyz, div.abc");
$set2 = $set1.slice(3);
```

假設 $set1 有5個元素，(0,1,2,3,4)：

- slice(3) 則會去除前面3個(0~2)，保留後面2個(3~4)
- slice(1,3) 則會去除前面1個(0~0)，去除後面2個(3~4)，所以剩2個(1~2)

####  [.nextUntil()](http://api.jquery.com/nextUntil/) ：

The nextUntil() function finds all elements returned from a previous search until it finds a specific element that tells it to stop.  

### filter 的進階用法

使用 [.filter()](http://api.jquery.com/filter/) 時，除了可以傳入一個表達式當做參數外，它另外還有個多載方法，可以傳進一個 function 。  你可以用這個 function 來逐一判斷集合中的元素是否要篩選出來。  

下面這個例子，篩選集合中的每個元素，取得元素內只含一個&lt;b&gt;的元素。
```xml
<div class="vito">
<b>div</b> <b>1</b>
</div>
<div class="vito">
div 2
</div>
<div class="vito">
<b>div 3</b>
</div>
```
```javascript
$(function () {
$("div.vito").filter(function (index) {
return $("b", this).length == 1;
}).css("color", "red");
});
```

# 元素 (節點) 間位置的相互關係

在談如何查訪元素之前，我先用一張簡單的示意圖來說明元素節點間相互的空間關係：

![](http://127.0.0.1:8080/_images/cs/jq-node-relation.png)

以下方法可以用來查詢特定選取集合的相關元素：

### 依縱向關係來查訪

####  [.parent()](http://api.jquery.com/parent/) ：取得上一階層的父元素

####  [.parents()](http://api.jquery.com/parents/) ：取得全部的父元素集合(祖先元素)

####  [.children()](http://api.jquery.com/children/) ：取得(僅)下一階層的所有子元素之集合 (不含text nodes)

####  [.contents()](http://api.jquery.com/contents/) ：取得全部的子元素 (含text nodes)，若selectors是個iframe，則contents會取得其document物件

### 依橫向關係來查訪

####  [.next()](http://api.jquery.com/next/) ：取得其後緊鄰的兄弟元素 (同輩元素) 

####  [.nextAll()](http://api.jquery.com/nextAll/) ：取得從下一個直到最後一個同輩元素

####  [.prev()](http://api.jquery.com/prev/) ：前一個同輩元素

####  [.prevAll()](http://api.jquery.com/prevAll/) ：從前一個直到最開頭的同輩元素

####  [.siblings()](http://api.jquery.com/siblings/) ：取得其所有同輩元素的集合

# 依表達式條件來查訪元素 (Finding)

####  [.find()](http://api.jquery.com/find/) ：匹配目前集合中的子元素，並篩選出與表達式符合的元素

例如我們想取得段落下的span元素：
```xml
<p><span>Hello</span> World</p>
```
```javascript
$("p").find("span");        // 得到的結果 : <span>Hello</span> 
```

## example 1

這個例子主要用來說明 find 與 filter 的差別：

- find ：篩選出選擇元素中符合條件的子元素。
- filter ：篩選集合中符合條件的元素。
```xml
<div id="demo1" class="aaa" >demo 1
<div class="aaa">test1</div>
<div class="bbb">test2</div>
<div class="ccc">test3</div>
</div>
<div id="demo2" class="bbb">demo 2
<div class="aaa">test4</div>
<div class="bbb">test5</div>
<div class="ccc">test6</div>
</div>
<div id="demo3" class="ccc">demo 3
<div class="aaa">test7</div>
<div class="bbb">test8</div>
<div class="ccc">test9</div>
</div>
<input id="Button1" type="button" value="Button1" />
```
```javascript
$("#Button1").click(
function () {
var $r1 = $("div");                                 //12 :
var $r2 = $r1.find(".aaa");                         //3 : T1, T4, T7
var $r3 = $r1.filter(".aaa");                       //4 : D1, T1, T4, T7
var $r4 = $r1.filter(".aaa, .bbb");                 //8 : D1, T1, T2, D2, T4, T5, T7, T8
var $r5 = $r1.filter(".aaa, .bbb").not(".ccc");     //8 : D1, T1, T2, D2, T4, T5, T7, T8
}
);
```

## example 2

這個例子也是用來說明 find 與 filter 的差別：

由於 img 沒有子元素，所以用 find 找不到任何符合條件的元素。
```xml
<img alt="" class="aaa" src="http://127.0.0.1:8080/_images/web/head2_b_16.png" />
<img alt="" class="bbb" src="http://127.0.0.1:8080/_images/web/head2_b_16.png" />

<input id="Button2" type="button" value="Button2 " />
<input id="Button3" type="button" value="Button3" />
```
```javascript
$("#Button2").click(
function () {
$("img").find(".aaa").attr("src", "http://127.0.0.1:8080/_images/web/head2_r_16.png");
// no match
}
);

$("#Button3").click(
function () {
$("img").filter(".aaa").attr("src", "http://127.0.0.1:8080/_images/web/head2_r_16.png");
}
);
```

## example 3
```xml
<div id="Div1" class="aaa">Div1
<img alt="" class="aaa" src="http://127.0.0.1:8080/_images/web/head2_b_16.png" />Img1
<img alt="" class="bbb" src="http://127.0.0.1:8080/_images/web/head2_b_16.png" />Img2
<div id="Div2" class="bbb">Div2
<img alt="" class="aaa" src="http://127.0.0.1:8080/_images/web/head2_b_16.png" />Img3
<img alt="" class="bbb" src="http://127.0.0.1:8080/_images/web/head2_b_16.png" />Img4
</div>
</div>
```
```javascript
$("#Div1").click(
function () {
var $r1 = $(this);                                  //1 :
var $r2 = $r1.find("img");                          //4 : 
var $r3 = $("img", this);                           //4 : 
var $r4 = $r1.filter("img");                        //0 : 
}
);
```
## 參考資料  
![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [jQuery Tutorial](http://www.tutorialspoint.com/jquery/index.htm)
- [jQuery API](http://api.jquery.com/)
- [jQuery教學](http://webdesign.kerthis.com/jquery/)
- [jQuery 邊做邊學系列](http://msdn.microsoft.com/zh-tw/dd310332.aspx#jQuery)