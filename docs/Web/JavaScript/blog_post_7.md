---
title: jQuery 事件處理
layout: default
parent: JavaScript
nav_order: 7
description: "jQuery 事件處理"
date: 2013-02-05
tags: [Web,JavaScript]
postid: "1044208622279427357"
---
jQuery 可以將寫好的 script 繫結到用戶端的事件，如：button click, mouse move, ...  這些 script 內容，可以寫在網頁裡，也可以獨立在 .js 檔中，再透過 [.bind()](http://api.jquery.com/bind/) 方法與事件綁在一塊。  

# jQuery 事件處理

## 使用 Javascript 處理事件

底下是一個使用 javascript 來處理事件的一般寫法：  
```xml
<div id='div3' onclick="DivClick();"> Hello 3</div>
```
```javascript
<script type="text/javascript">
function DivClick() {
alert("Clicked!");
}   
</script>
```

另外，你也可能使用以下方法，註冊 click 事件：
```javascript
document.getElementById("div3").onclick = DivClick;
```

上面 onclick 方法有個大問題，它會將 click 事件佔為已用，也就是不管原先上面是否已經掛載了其他的事件函數。  如果要避免這個問題，在 IE 中，必須利用 attachEvent() ，在 Firefox 則必須使用 attchEventListener() ，就可以在一個元素事件上重複掛載多個事件函數。  看到不同瀏覽器要用不同寫法先別煩惱，jQuery 當然已經替我們解決了跨瀏覽器問題，在 jQuery 中，我們要掛上事件函數，可以透過統一的寫法:

## 使用 jQuery 處理事件

####  [.bind()](http://api.jquery.com/bind/) ：Attach a handler to an event for the elements.
```javascript
$(...).bind( type[, data], function(){ ... } );
```

- type ：用來指名要掛載的事件名稱字串，如 blur, focus, click, ...
- data ：用來傳遞額外的參數，這在多個事件共用函數時特別有用
- function ：繫結的處理方法
```javascript
<script type="text/javascript">
function ClickHandler() {
alert("。。。");
}

$(document).ready(function () {
$("#div5").bind("click", ClickHandler);
});
</script>
```

上面這個例子，我們使用 [.bind()](http://api.jquery.com/bind/) 方法將 &lt;div5&gt; 的 click 事件，繫結到 ClickHandler 這個腳本程式。  不過，一向以簡潔見長的 jQuery，在事件宣告上有更簡便的寫法，上面例子可以直接改成以下寫法：  
```javascript
function ClickHandler() {
alert("。。。");
}

$(document).ready(function () {
$("#div2").click(ClickHandler);
});
```

更進一步，若使用匿名函式，可以更精簡些。  
```javascript
$(document).ready(function () {
$("#div3").click( function(){
alert("。。。");
});
});
```

# jQuery 支援的事件方法

jQuery 的事件處理函式大都提供兩種用途，一種是呼叫帶有參數的函式 － 綁定事件處理函式；另一種則是呼叫不帶有參數的函式 － 觸發該事件。  

#### 帶有參數：
```javascript
$("p").click(function() {
$(this).css("background-color","blue");
});
```

#### 不帶有參數：
```javascript
$("p").click();
```

事件處理函數中的「this」為被觸發的「DOM元素」。(非jQuery物件)

## 與事件相關的方法

上述的程式碼，我們用到 jQuery 定義好的 click 函式來處理 DOM 中的 click event，jQuery 也同樣對 DOM 中的其他 event 都有相關的函式，  底下是 jQuery 提供的函式，用來處理 DOM 模型中的相對應的事件。  

#### Keyboard Events

- [.keydown()](http://api.jquery.com/keydown/) ：
- [.keypress()](http://api.jquery.com/keypress/) ：
- [.keyup()](http://api.jquery.com/keyup/) ：

#### Mouse Events

- [.click()](http://api.jquery.com/click/) ：
- [.dblclick()](http://api.jquery.com/dblclick/) ：
- [.mousedown()](http://api.jquery.com/mousedown/) ：
- [.mouseup()](http://api.jquery.com/mouseup/) ：
- [.mouseover()](http://api.jquery.com/mouseover/) ：when the mouse **into** an element.
- [.mouseout()](http://api.jquery.com/mouseout/) ：when the mouse **out** an element.
- [.mouseenter()](http://api.jquery.com/mouseenter/) ：when the mouse **enters** an element.
- [.mouseleave()](http://api.jquery.com/mouseleave/) ：when the mouse **leaves** an element.
- [.mousemove()](http://api.jquery.com/mousemove/) ：Bind an event handler to the "mousemove" JavaScript event。
- [.hover()](http://api.jquery.com/hover/) ：when the mouse pointer **enters** and **leaves** the elements.
- [.toggle()](http://api.jquery.com/toggle/) ：Display or hide the matched elements..

#### Form Events

- [.blur()](http://api.jquery.com/blur/) ：when an element **loses focuses**.
- [.change()](http://api.jquery.com/change/) ：
- [.focus()](http://api.jquery.com/focus/) ：當物件被點擊或取得焦點時
- [.focusin()](http://api.jquery.com/focusin/) ：this event will be triggered for child elements when a parent element receives focus.
- [.focusout()](http://api.jquery.com/focusout/) ：this event triggers when focus is lost by an element or its parent.
- [.select()](http://api.jquery.com/select/) ：當文字被選取時
- [.submit()](http://api.jquery.com/submit/) ：當按下送出按紐時
- [.val()](http://api.jquery.com/val/) ：Get the current value of the first element in the set of matched elements or set the value of every matched element.

#### Browser Events

- [.error()](http://api.jquery.com/error/) ：當圖片或文件下載產生錯誤時
- [.resize()](http://api.jquery.com/resize/) ：Bind to this event to trap when the browser changes sizes.
- [.scroll()](http://api.jquery.com/scroll/) ：when a user scrolls a specified element

#### Document Loading Events

- [.unload()](http://api.jquery.com/unload/) ：
- [.load()](http://api.jquery.com/load/) ：
- [.ready()](http://api.jquery.com/ready/) ：Specify a function to execute when the DOM is fully loaded.

# 存取事件資訊

所有的 jQuery event handler，都有一個接受一個 event 參數的同名方法，  如果要取得滑鼠座標、按鍵內容等資訊，就可以在函數中接收 event 參數取得事件資訊物件。  例如：  
```javascript
$("#div6").click(
function (e) {
alert("(X,Y)=(" + e.pageX + "," + e.pageY + ")");
}
);
```

上面這個例子是用來取得滑鼠游標相對於頁面的位置，重點是！這段程式碼在IE上也能執行 (註：原本的IE瀏覽器事件無pageX屬性)。WHY？因為 jQuery 又事先幫你解決掉事件處理中跨瀏覽器的問題了 － 「jQuery 修改了 event object 以使其符合 W3C 的標準」，所以從此你不用再重複 if(window.event)，你只需了解並使用標準的事件屬性！

# 幾個特殊方法介紹

####  [.hover()](http://api.jquery.com/hover/) 
```javascript
.hover( handlerIn(eventObject), handlerOut(eventObject) )
.hover( handlerOut(eventObject) )
```

hover = mouseover + mouseout。當滑鼠移動到一個匹配的元素上面時，會觸發第一個函數 (fn1)；當滑鼠移出該元素時，會觸發第二個函數 (fn2)。  
```xml
<ul>    
<li>Milk</li>    
<li>Bread</li>    
<li class='fade'>Chips</li>     
<li class='fade'>Socks</li>  
</ul>
```
```javascript
$("li").hover(
function () {
$(this).append($("<span> \*\*\*</span>"));
},
function () {
$(this).find("span:last").remove();
}
);

//li with fade class
$("li .fade").hover(
function () {
$(this).fadeOut(100);
$(this).fadeIn(500);
}
); 
```

####  [.toggle()](http://api.jquery.com/toggle/) 

這個方法會交替顯示與隱藏匹配的元素
```javascript
<input id="Button2" type="button" value="button" />
<div id="ToggleButton">ToggleButton</div>
<script type="text/javascript">
$(function () {
$("#ToggleButton").click(function () {
$("#Button2").toggle();  //Display or Hidden the matched element
});
});
</script>
```

#### 最後，但是很重要也很常用的

####  [.ready()](http://api.jquery.com/ready/) 

jQuery 中，大部分的操作都基於 HTML DOM，所以我們必須確定頁面文件已經完全下載好才開始執行你的程式，jQuery 提供下面這個函式來處理 DOM ready 事件 (DOMContentLoaded)：
```javascript
$(document).ready(function() {
// 這裡放你要執行的程式碼
});
```

更精簡的寫法：
```javascript
$(function() {   
// 這裡放你要執行的程式碼
});
```

jQuery的DOM ready event是等HTML DOM準備好就可以開始執行程式，不像一般常用的window.onload要連圖片、外部檔案等全部都下載完畢才會觸發onload事件。

# DOMContentLoaded & 外部樣式表的問題？

如上述，DOMContentLoaded 事件的觸發不必等到圖片檔案等下載完畢，但這裡有個問題就是，那需要等到「外部樣式表 (External CSS Styles)」下載完畢嗎？  關於這一點，各家瀏覽器對於 DOMContentLoaded 事件的觸發也的確不一致，在 Opera 中瀏覽器不會等外部樣式表下載完即觸發 DOM ready；而 Firefox 則會等到外部 CSS 都加載完畢。  因此若你有在 jQuery ready 事件中改變了元素的 CSS 屬性，對於 Opera，你修改的屬性很可能會被接著加載的外部樣式表覆蓋過去，這也就造成跨瀏覽器處理的問題。  但如果你無法避免在頁面初始階段改變 CSS 屬性，這問題我們可以利用 load 事件來避開，以確定事件的觸發會在外部樣式表下載完畢之後：  
```javascript
$(element).load(fn);
```