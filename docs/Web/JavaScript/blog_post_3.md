---
title: jQuery 屬性與樣式
layout: default
parent: JavaScript
nav_order: 3
description: "jQuery 屬性與樣式"
date: 2013-02-05
tags: [Web,JavaScript]
postid: "2551037669952081479"
---
我想一般對於 JavaScript 大部分的操作都用在變動 HTML DOM 元素的屬性跟樣式，如果你有這個經驗，就會知道在設定時會有許多複雜及麻煩的問題，比如說 IE 它 setAttribute 不吃 name 屬性；又像在指定 class 屬性時，名稱不能用 class 而要用 className 等問題。令人開心的，jQuery 已經幫你處理掉中間這些繁雜過程可能遇到的問題了！你只需記得原本的 HTML 和 CSS 是怎樣寫就行。   

# 修改標籤屬性 (Attributes)

## 變更 attribute

####  [.attr()](http://api.jquery.com/attr/) ：取得匹配元素的屬性值

####  [.removeAttr()](http://api.jquery.com/removeAttr/) ：移除元素屬性
```javascript
attr(name)          //取得第一個匹配到的元素的屬性值
attr(key, value)    //設定匹配到的元素的屬性值： 
attr(properties)    //使用 key/value object 的方式來替所有匹配到的元素設定多個屬性值

removeAttr(name)    //移除元素屬性
```
```javascript
//取得第一個連結的 title 值：
$("a").attr("title");

//取得第一個 img 的 src 值：
$("img").attr("src");

//替所有連結的 title 設為 Enjoy jQuery： 
$("a").attr("title","Enjoy jQuery");

//替所有的圖片設定 src、title 及 alt 屬性：
$("img").attr({ 
src: "logo.gif",
title: "jQuery",
alt: "jQuery Logo"
});

//移除所有連結的 title
$("a").removeAttr("title");
```

example 2:
```xml
<a href="http://abc">How </a>
<a href="http://def">are </a>
<a>you. </a> 
```
```javascript
<script type="text/javascript">
$("a").first().attr("title", "click me");

$("a").last().attr({
href: "http://xyz",
title: "click me"
});
</script>
//=> 
```

輸出結果
```xml
<a href="http://abc" title="click me">How </a>
<a href="http://def">are </a>
<a href="http://xyz" title="click me">you. </a>
```

## 變更 class

對於 class，jQuery 另外提供個別的函式來作 class 增刪的動作，這是為了可以方便疊加與移除特定的 class。

####  [.addClass()](http://api.jquery.com/addClass/) ：增加 class

####  [.removeClass()](http://api.jquery.com/removeClass/) ：移除 class
```xml
<p>Hello</p>  
<p>and</p>  
<p>Goodbye</p>
```
```javascript
//幫最後一個段落加入 selected 和 highlight 類別：

$("p").last().addClass("selected highlight");

// 結果＝＞ <p class="selected highlight">Goodbye</p>
```

## 變更 value

####  [.val()](http://api.jquery.com/val/) ：取得或設定輸入欄位的 value 值。

####  [.text()](http://api.jquery.com/text/) ：取得或設定 innerText  的內容

####  [.html()](http://api.jquery.com/html/) ：取得或設定 innerHTML 的內容

### 存取 input 元素的值

[.val()](http://api.jquery.com/val/) 是一個很常用到的是方法，可以用來取得與設定表單元素的 Value 值。
```javascript
val()           //get
val(value)      //set
```

依不同型態的 input 元素，其設定方法或回傳值略有不同：

- **textbox**  
可以直接使用 val() 及 val( val ) 讀取及設定欄位內容
- **select**  
若為單選，val() 傳回的是字串，而 val( val ) 要傳入的也是字串。  
當為多選時 (&lt;select multiple="multiple"&gt; 時，則 val() 會傳回字串陣列，由所有選取的選項內容組成，同理此時要設定時也要傳入陣列。
- **radio**  
實務上多半有多個 &lt;input type="radio"&gt; 指定同樣的 name，但指定不同的 value，在使用時選取同一群 radio(可透過 #name) 以 val() 取得被選取 radio 的值。要設定時，則是選取該群 radio，再傳入單一字串的陣列，例如: val([ str ])，就可選取 value 與 str 相同的 radio。
- **checkbox**  
checkbox 常用於多選選項，所以若要一口氣找出所有被勾選的項目，通常可以善用:checked 這個虛擬類別 (Pseudo Class) 一次將被勾選的 checkbox 找出來，再逐一用 val() 取值。而要設定時則與 radio 相同，先選取所有可能選項的 checkbox，傳入字串陣列，jQuery 會逐一比對，將吻合者勾選起來。

如果是 checkbox, select, radio 元素，則參數 value 為一個 Array&lt;string&gt;
```xml
<input type="text" /> 
```
```javascript
$("input").val("Hello World!"); 

//output => <input type="text" value="Hello World!" />
```

### 存取 Html 元素的內容

jQuery 提供了 html()、text() 可取得 innerHTML 及 innerText 內容
```xml
<div class="demo-container">      
<div>Demo Div</div>  
<span>DemoSpan</span>
<a href="http://abc">abc</a>
</div>
```
```javascript
$('div.demo-container').text();   //Demo DivDemoSpan abc

$('div.demo-container').html();   //<DIV>Demo Div</DIV><SPAN>DemoSpan</SPAN> <A href="http://abc">abc</A>
```

# 修改標籤樣式 (CSS)

傳統 JavaScript 在處理 css 的問題上簡直頭大，設定一個 background-color ，  你得先知道必須改用 elem.style.**backgroundColor**來設定；對透明度 opacity 設定上你必須了解 IE 是用 **filter** 屬性名稱來指定透明度。  也就是 javascript 用來設定樣式的屬性名稱和 CSS 中的名稱不一定相同，你必須事先知道二者的差異。  jQuery 的寫法就簡單多了，可以直接用 key/value 成對的一組屬性來設定，而且只要知道 CSS 的表達式就沒問題。  

## 變更 CSS

####  [.css()](http://api.jquery.com/css/) ：
```javascript
css(name)           //取得第一個匹配到的元素之樣式：
css(name,value)     //替所有匹配到元素設定一種樣式： 
css(properties)     //替所有匹配到元素設定多種樣式，使用逗號分隔多個 key/value 樣式給所有匹配的元素： 
```

例
```javascript
//取得第一個段落的字體顏色：
$("p").css("color"); 

//設定一個樣式    
$("p").css("color","red");       

//同時設定多個樣式：字體設為紅色＋背景設為藍色：
$("p").css({
"color":"red",
"background-color":"blue"
});

//==> <p style="BACKGROUND-COLOR: blue; COLOR: red">Hello</p>
```

使用 jQuery 設定樣式，不僅不用擔心屬性名稱不同問題，而且它還會幫我們處理好跨瀏覽器的問題。如下面範例：
```javascript
//替所有段落的透明度設為半透明：       
$("p").css("opacity","0.5");           
```

這段範例是用來設定透明度，jQuery會自動處理好跨瀏覽器問題，當遇到不同瀏覽器時，其輸出會不同：
```xml
<p style="FILTER:  alpha(opacity=50); ZOOM: 1">Hello</p>
```
```xml
<p style="opacity : 0.5;">Hello</p>
```

## 變更元素的寬高和位置

####  [.height()](http://api.jquery.com/height/) ：取得或設定元素的高度

####  [.width()](http://api.jquery.com/width/) ：取得或設定元素的寬度
```javascript
$("p").height(); //取得第一個段落的高度

$("p").height(20); //將所有段落的高度設作20px
```

####  [.offset()](http://api.jquery.com/offset/) ：取得或設定元素左上角的座置
```xml
<p>Hello</p>
<input id="Button1" type="button" value="Button5" />
```
```javascript
<script type="text/javascript">
$(document).ready(function () {

$("#Button5").click(
function () {
var p = $("p:last");
var coordinates = p.offset();
coordinates.left += 10;
p.offset(coordinates);
}
);
});
</script>
```