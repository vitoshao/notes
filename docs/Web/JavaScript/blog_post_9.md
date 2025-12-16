---
title: jQuery 效果
layout: default
parent: JavaScript
nav_order: 9
description: "jQuery 效果"
date: 2013-02-05
tags: [Web,JavaScript]
postid: "5842890048119451485"
---
JavaScript 應用在客戶端網頁上最強悍的用途之一就是動態效果了，往往利用純 JavaScript 作一個效果你可能要寫好幾行的 Code，而 jQuery Effects 幫你把許多常會用到的特效包起來，現在你只需要寫個幾行 Code 來作事。([更多請看 jQuery UI](http://ui.jquery.com/))   

# 基本效果 (Basics)

####  [.show()](http://api.jquery.com/show/) ：顯示效果

####  [.hide()](http://api.jquery.com/hide/) ：隱藏效果

####  [.toggle()](http://api.jquery.com/toggle/) ：輪流切換顯示/隱藏狀態
```javascript
.show()
.show( [speed ] [, callback  ] )
.show( options )
.show( [speed ] [, easing ] [, callback  ] )
```

- speed ：A string or number determining how long the animation will run. (default: 400)
- easing ：A string indicating which easing function to use for the transition. (default: swing)
- callback ：A function to call once the animation is complete.
```javascript
$("#hideshow").click(
function () {
var isvisible = $("#Img1").is(":visible");
if (isvisible)
$("#Img1").hide();
else
$("#Img1").show();
}
); 

$("#toggle").click(
function () {
$("#Img2").toggle('slow');
}
); 
```

執行特效時常用的方法

####  [.delay()](http://api.jquery.com/delay/) ：delay to the method

####  [jQuery.fx.off](http://api.jquery.com/jQuery.fx.off/) ：Turns off all jQuery effects for the page.

####  [.stop()](http://api.jquery.com/stop/) ：Stop the currently-running animation on the matched elements.

# 滑動 (Sliding)

相對於 show、hide 方法，slideDown、slideUp 則是以滑動的效果來顯示/隱藏元素。 

####  [.slideDown()](http://api.jquery.com/slideDown/) ：以滑下的特效來顯示元素，參數同 show。 

####  [.slideUp()](http://api.jquery.com/slideUp/) ：以滑上的特效來隱藏元素，參數同 show。 

####  [.slideToggle()](http://api.jquery.com/slideToggle/) ：輪流切換顯示/隱藏狀態。 
```javascript
$("#sliding").click(
function () {
var isvisible = $("#Img3").is(":visible");
if (isvisible)
$("#Img3").slideUp();
else
$("#Img3").slideDown();
}
); 

$("#slideToggle").click(
function () {
$("#Img4").slideToggle('slow');
}
); 
```

# 淡入淡出 (Fading)

相對於 show、hide 方法，fadeIn、fadeOut 則是以淡入淡出的效果來顯示/隱藏元素。

####  [.fadeIn()](http://api.jquery.com/fadeIn/) ：以淡入的特效來顯示元素，參數同 show。

####  [.fadeOut()](http://api.jquery.com/fadeOut/) ：以淡出的特效來隱藏元素，參數同 show。

####  [.fadeTo()](http://api.jquery.com/fadeTo/) ：動態漸變調整元素透明度，且可在開關完畢後執行一函式。(寬高不變) 
```javascript
$("#fadeInOut").click(
function () {
var isvisible = $("#Img5").is(":visible");
if (isvisible)
$("#Img5").fadeOut(1000);   //隱藏
else
$("#Img5").fadeIn(1000);    //顯示
}
); 

$("#Img6").css("display", "none");
$("#fadeTo").click(
function () {
$("#Img6").fadeTo("slow", 0.33);
}
); 
```

# 自己設定 (Custom)

####  [.animate()](http://api.jquery.com/animate/) ：
```javascript
.animate( properties, options )
.animate( properties [, duration ] [, easing ] [, callback ] )
```

animate 這個函式可以讓你自行定義動態效果，其參數分別為： 

- properties ：一組包含最終CSS樣式的{屬性:值}
- duration ：三種預定的速度("slow", "normal", "fast")，或動畫間隔的毫秒數值(如：1000)
- easing ：緩和方式 { linear | swing }
- callback ：每個元素在完成動畫後要執行的函數
```javascript
$("#go").click(function(){
$("#block").animate(
{ width: "70%", opacity: 0.4, marginLeft: "0.6in", fontSize: "3em", borderWidth: "10px" }, 
1500
);
});
```

#### animate只支援「可數字化」的屬性，如height、width、left、top、opacity等。

#### 你可以在屬性值前面指定「+=」或「-=」來做相對運動。
```javascript
$("#go").click(function(){
$(".block").animate({left: '+=100px'}, 2000);
});
```