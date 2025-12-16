---
title: 使用 Div 做排版
layout: default
parent: CSS
nav_order: 1
description: "使用 Div 做排版"
date: 2012-03-08
tags: [Web,CSS]
postid: "5144367257665552286"
---
# 使用 &lt;div&gt; 做排版

##  display 屬性

- 區塊模式（display:block）：元素會以區塊方式呈現，除非設定 position 或 float。
- 行內模式（display:inline）：所有文字或圖片均不換行，也就是全部都會是同一行的意思。

## 例一：若沒有任何設定，以 block 為預設
```xml
<div style="background-color:Yellow">div 1</div>
<div style="background-color:Silver">div 2</div>
<div>
<div style="background-color:Yellow">div 3</div>
<div style="background-color:Silver">div 4</div>
</div>
```

#### 顯示結果

div 1

div 2

div 3

div 4

## 例二：inline 與 block 的區別
```xml
<div>
<div style="display:inline; background-color:Yellow">div 1</div>
<div style="display:inline; background-color:Silver">div 2</div>
</div>
<div>
<div style="display:block; background-color:Yellow">div 3</div>
<div style="display:block; background-color:Silver">div 4</div>
</div>
```

#### 顯示結果

div 1

div 2

div 3

div 4

## 例三：block 搭配 float 的使用
```xml
<div>
<div style="display:block; float:left; background-color:Yellow">div 1</div>
<div style="display:block; float:right; background-color:Silver">div 2</div>
</div>
<p style="background-color:Gray"> p 3 </p>
```

#### 顯示結果

div 1

div 2

p 3 

使用 float 後，若有剩餘空間，會被後面的內容填補。若想保留這個空間，可放一個空白字元「&nbsp;」假裝。如下：
```xml
<div>
<div style="display:block; background-color:Yellow; float:left; width:50%;">div 1</div>
<div style="display:block; background-color:Lime; float:right; width:50%; ">div 2</div>
</div>
<div style="background-color:Orange">&nbsp;</div>
<div style="background-color:Orange">div 3</div>
```

#### 顯示結果

div 1

div 2

p 3 

#### 調整調度後的顯示結果

div 1

span 1          span 2

p 3

## Display v.s. Visibility

要隱藏一個 DIV 區塊，可以這麼做：  
&lt;div style="display:none"&gt;區塊中的內容&lt;/div&gt;  &lt;div style="visibility:hidden"&gt;區塊中的內容&lt;/div&gt;  
這二者不同處在於：  
display:none =&gt; 隱藏包含 &lt;div&gt; 標籤整個元素，  
visibility:hidden  =&gt; 只會隱藏區塊中的內容，換句話說 &lt;div&gt; 標籤所佔的區塊還是存在，只是沒有顯示內容而已。

#  div vs span

這二個標籤都是將資料分為不同區塊

- 在 &lt;/div&gt; 標籤後會換行
- 在 &lt;/span&gt; 標籤後不會換行

#### 盒子模式 (box model)

![](http://127.0.0.1:8080/_images/cs/box-model-tw.gif)

#### Sample

![](http://127.0.0.1:8080/_images/cs/div_style.jpg)