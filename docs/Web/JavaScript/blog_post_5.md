---
title: jQuery DOM操作
layout: default
parent: JavaScript
nav_order: 5
description: "jQuery DOM操作"
date: 2013-02-05
tags: [Web,JavaScript]
postid: "4446579796208793435"
---
jQuery Manipulation 主要是用來操作 DOM 元素的新增、刪除與修改等。

# 變更元素內容 (Changing Contents)

####  [.html()](http://api.jquery.com/html/) ：取得或變更元素內容，這個功能類似 JavaScript 中的 innerHTML

####  [.text()](http://api.jquery.com/text/) ：取得或變更元素內容（純文字）
```xml
<p>改變<b>元素</b>內容</p>
```
```javascript
<script type="text/javascript">
var p = $("p:last");

//取得物件的 innerHTML
var html = p.html()     //改變<b>元素</b>內容

//變更物件的 innerHTML
p.html("<b style='color:red'>元素內容已變</b>");

//取得物件的 innerText
var html = p.text();    //元素內容已變

//變更物件的 innerText
p.text("內容<b style='color:blue'>已變</b>");   //內容<b style='color:blue'>已變</b>
</script>
```

![](http://127.0.0.1:8080/_images/cs/jq-text-result.png)

####  [.replaceWith()](http://api.jquery.com/replaceWith/) ：removes content from the DOM and replaces it with something
```javascript
$("h2").replaceWith("<h1>Page Title</h1>");  //使用 <h1> 標籤取式 <h2> 標籤
```

# 插入元素 (Inserting)

####  [.append()](http://api.jquery.com/append/) ：將指定的元素插入到集合中的元素(內部插入)

####  [.prepend()](http://api.jquery.com/prepend/) ：在每個匹配的元素內部最前面加入(內部插入)

####  [.before()](http://api.jquery.com/before/) ：在每個匹配的元素前面加入(外部插入)

####  [.after()](http://api.jquery.com/after/) ：在每個匹配的元素後面加入(外部插入)
```xml
<p class="insert">插入1</p>
<p class="insert">插入2</p>
```
```javascript
<script type="text/javascript">
var p = $(".insert");
p.append("元素");           //<p>插入1元素</p>
//<p>插入2元素</p>
p.prepend("在前面");        //<p>在前面插入1元素</p>
//<p>在前面插入2元素</p>
p.before("(1)");            //(1)<p>在前面插入1元素</p>     
//(1)<p>在前面插入2元素</p>    
p.after("(...)");           //(1)<p>在前面插入1元素</p>(...)  
//(1)<p>在前面插入2元素</p>(...)  
</script>
```

####  [.appendTo()](http://api.jquery.com/appendTo/) ：將集合中的元素插入指定的元素中，做為最後的子元素

####  [.prependTo()](http://api.jquery.com/prependTo/) ：將集合中的元素插入指定的元素中，當做最前的子元素

####  [.insertBefore()](http://api.jquery.com/insertBefore/) ：將集合中的元素插入到指定元素之前

####  [.insertAfter()](http://api.jquery.com/insertAfter/) ：將集合中的元素插入到指定元素之後
```xml
<p class="appendTo">appendTo插入</p>
<p class="prependTo">prependTo插入</p>
```
```javascript
<script type="text/javascript">
var obj1 = $("<a href='http://abc'>xyz</a>");
obj1.appendTo($(".appendTo"));

var obj2 = $("<a href='http://abc'>xyz</a>");
obj2.prependTo($(".prependTo"));
</script>
```

![](http://127.0.0.1:8080/_images/cs/jq-appendto-prependto.png)

# 移動元素 (moving)

同樣使用上面的函式，若參數值是一個 「jQuery」或「DOM」物件，則代表移動它們：
```xml
<span id='hello'> Hello </span>
<span id='hi' style="color:Red"> Hi </span>
```

![](http://127.0.0.1:8080/_images/cs/jq-moving.png)

#### 將 Hello 移到 Hi 裡面的後頭
```javascript
$("#hi").append($("#hello"));
```

![](http://127.0.0.1:8080/_images/cs/jq-moving-append.png)

#### 將 Hello 移到 Hi 裡面的前頭
```javascript
$("#hi").prepend($("#hello"));
```

![](http://127.0.0.1:8080/_images/cs/jq-moving-prepend.png)

#### 將 Hello 移到 Hi 前面
```javascript
$("#hi").before($("#hello"));
```

![](http://127.0.0.1:8080/_images/cs/jq-moving-before.png)

#### 將 Hello 移到 Hi 後面
```javascript
$("#hi").after($("#hello"));
```

![](http://127.0.0.1:8080/_images/cs/jq-moving-after.png)

# 包裹元素 (wraping)

####  [.wrap()](http://api.jquery.com/wrap/) ：Wrap an HTML structure around each element in the set of matched elements.
```xml
<span id='Span3'> Hello </span>
<span id='Span4'> Hello </span>
```
```javascript
$("#Span1, #Span2").wrap("<span style='color:Red'></span>");
```
```xml
<span style="COLOR: red"><span id="Span1">Hello </span></span>
<span style="COLOR: red"><span id="Span2">Hello </span></span>
```

其他相關的方法還有：

####  [$(X).wrapAll(Y)](http://api.jquery.com/wrapAll/) ：將所有 X 元素集中在一起並用一個Y包起來

####  [$(X).wrapInner(Y)](http://api.jquery.com/wrapInner/) ：將 X 的子元素用 Y 包起來

####  [$(X).replaceWith(Y)](http://api.jquery.com/replaceWith/) ：將 X 置換成 Y

####  [$(X).replaceAll(Y)](http://api.jquery.com/replaceAll/) ：以 X 去置換 Y

# 刪除元素 (Removing)

####  [.empty()](http://api.jquery.com/empty/) ：Remove all child nodes of the set of matched elements from the DOM.
```xml
<p>Hello, <span>Person</span> <a href="#">and person</a></p>
```
```javascript
$("p").empty(); //結果：<p></p>
```

####  [.remove()](http://api.jquery.com/remove/) ：Remove the set of matched elements from the DOM.
```xml
<p>Hello</p> how are <p>you ?</p>
```
```javascript
$("p").remove(); //結果：how are
```

####  [.detach()](http://api.jquery.com/detach/) ：removes the found elements from the DOM.

# 複製元素 (Copying)

####  [.clone()](http://api.jquery.com/clone/) ：Create a deep copy of the set of matched elements.

這個方法預設不會複製資料和事件，如果須要，必須將 withDataAndEvents 參數設成 true 。
```xml
<b id='cp1'>☆</b>
<span id='cp2'> Hello </span>
```
```javascript
$("#copy").click(
function () {
$("#cp2").after($("#cp1").clone());  //結果： ☆ Hello ☆
}
);
```

# 暫建物件

以上的說明都是使用頁面中現有的元素，以進行插入、移動、刪除等操作。  jQuery 也可以利用暫建物件的做法，讓我們動態的建立元素。  
```xml
<div id="Div1">Test Div </div>
```
```javascript
<script type="text/javascript">
var obj = $("<a href='http://abc'>xyz</a>");

$("#dvDisp").append(obj);
</script>
```
```xml
<div id="dvDisp">Test Div <a href="http://abc/">xyz</a></div>
```

# jQuery 共用方法

以下是幾個 jQuery 所提供的好用函式：

####  [jQuery.browser](http://api.jquery.com/jQuery.browser/) ：偵測瀏覽器版本

用以偵測瀏覽器版本，它是一個物件，會有 safari、opera、msie、mozilla 及 version 五個屬性，前四個屬性依瀏覽器不同，只有一個屬性會傳回 true，其餘都傳回 false，而 version 則會傳回版號。

####  [jQuery.support](http://api.jquery.com/jQuery.support/) ：判斷瀏覽器的支援度

由於使用 [jQuery.browser](http://api.jquery.com/jQuery.browser/) 的做法意味著我們要寫死何種瀏覽器支援哪些功能，一旦瀏覽器改版或特性改變時就要修校，因此從 jQuery 1.3 起，jQuery 核心不再用 [jQuery.browser](http://api.jquery.com/jQuery.browser/) 判斷決定不同的做法，而是一律由 jQuery.support.\* 傳回 true/flase 判斷瀏覽器的支援度，目前可測試的瀏覽器特性包含了： boxModel、cssFloat、hrefNormalized、htmlSerialize、leadingWhitespace、noCloneEvent、objectAll、opacity、scriptEval、style、tbody。但基於向前相容，jQuery.browser 仍會持續存在。  

####  [jQuery.each()](http://api.jquery.com/jQuery.each/) ：逐一處理 array 中的各元素

####  [jQuery.extend()](http://api.jquery.com/jQuery.extend/) ：將物件內容合併。

## 列舉集合物件

首先要知道的是， [.each()](http://api.jquery.com/each/) 和 [jQuery.each()](http://api.jquery.com/jQuery.each/) 是不同的。  前者是 jQuery 物件的列舉，用來列舉某個 jQuery 物件中的元素。  後者是用來列舉集合或陣列中的元素。  

[jQuery.each()](http://api.jquery.com/jQuery.each/) 方法，可以讓我們逐一處理 array 或 obj 集合中的各元素。  若在 each 中 return false 時，若尚未處理到的其他陣列元素/物件屬性則略過不處理  

- **jQuery.each(array, function() { ... })**  
將 array 的各元素逐一送給 callback 函數處理，函數中以 **this** 取得陣列元素內容。
- **jQuery.each(object, function(i, val) { ... })**  
逐一傳入集合物件中各屬性的名稱 (i) 以及值 (val) 給 callback 函數處理。

#### ［例一］

下面範例，我們在一個 div 中，加入４個不同顏色的小 div 。
```javascript
<script type="text/javascript">
var colors = ["red", "green", "blue", "yellow"];

//利用$.each跑迴圈，在callback中，透過 this 取得陣列中的元素
$.each(colors, function () {
$("<div class='Square' style='background-color:" + this + "'></div>").appendTo("#targetDiv");
});
</script>
```

#### ［例二］
```javascript
<script type="text/javascript">
$.each(colors, function (index, value) {
alert(index + ': ' + value);
});
// 0: red
// 1: green
// 2: blue
// 3: yellow
</script>
```

## 合併物件內容

####  [jQuery.extend()](http://api.jquery.com/jQuery.extend/) ：將物件內容合併。

**jQuery.extend(obj1, obj2[, obj3])**：  
這個方法會將後面的物件 (obj2, obj3, ...) 的屬性合併到 obj1 中(若同名屬性存在則覆寫之)，很常用於指定參數的預設值，例如:  
```javascript
<script type="text/javascript">
var finalSetting = {};
var defaultSetting = { limit: 256, maxRows: 16, maxCols: 16 };
var userOption = { maxRows: 10 };

//第一個參數物件會被修改，為了保持 defaultSetting 不被更動，放在第二個參數   
$.extend(finalSetting, defaultSetting, userOption);
$.each(finalSetting, function (i, val) {
alert(i + "->" + val);
});
</script>
```