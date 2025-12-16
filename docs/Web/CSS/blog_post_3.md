---
title: CSS繼承
layout: default
parent: CSS
nav_order: 3
description: "CSS繼承"
date: 2012-03-08
tags: [Web,CSS]
postid: "1615419208014413809"
---
- 什麼是CSS繼承
- CSS的繼承規則和權重
- 少用CSS的繼承，多用組合

# 什麼是CSS繼承

在一個 HTML 文件中，標籤之間有所謂的親子關係。  舉例來說，title 這個標籤一定都是在 head 這個標籤之內，所以我們就說 title 是 head 的子標籤。  CSS 繼承的意思就是說子標籤的樣式會跟父標籤一樣，除非子標籤本身另有自訂的樣式。   舉例來說，如果我們有以下的樣式：  
p { color: #00FF00; }
strong { font-size:22px; }  
下面這行 HTML 碼，
```xml
<p>這是一個 <strong>繼承的例子。</strong></p>
```

其顯現結果為：

這是一個**繼承**的例子。

在以上的例子中，雖然我們並沒有對 &lt;strong&gt; 設定顏色，可是我們可以看到，整個句子的顏色都是綠色的。   這是因為， &lt;strong&gt; 是 &lt;p&gt; 的子標籤，所以 &lt;strong&gt; 繼承了 &lt;p&gt; 的樣式。   因為 &lt;p&gt; 的樣式是設定為綠色文字，所以 &lt;strong&gt; 也是綠色文字。

# CSS 的繼承規則和權重

當一個 tag 同時有多個 CSS 樣式可套用時，那該套用哪一個呢？  
例如下面這個例子，瀏覽器要如何決定該使用哪一個 &lt;a&gt; 的顏色?  
a{color:#000;}
p a{color:#f00;}  
底下幾個規則，是瀏覽器用來決定權重的原則：

#### 1. 只有元素是最弱的規則
h1 p { font-size: 1em; }    
#### 2. 在只有元素的規則內，多元素代表高權力

例如下面例子：5個元素勝3個，所以 1.5em 會被選上套用。
body div div h1 p { font-size: 1.5em; }  div h1 p { font-size: 1em; }    
#### 3. 如果有兩個相同權力的規則，後出現的會勝出

#### 4. 一個 class 規則會勝過任何多個元素規則
p.introduction{color:red}  
勝
html body p{color:blue}    
#### 5. 多 class 規則勝過少 class 規則
div.content div.summary p.intro  
勝
div.content div p.intro    
#### 6. 有 id 規則的 CSS 勝過任何 class
#content img  
勝
body div.wrapper div.summary p.intro img    
#### 7. id、class 混用時

同時使用 id, class 勝過只用他們其中之一
div#content.summary li  
勝
div#content ul li
div.content div.secondary li
div ul li    
#### 8. "!important" 規則表示強迫使用，忽略其他規則

但這規則不太建議使用的，因為很容易會令整個 css 檔都是 "!important"。  
div.source_code pre.syntax { 
font-size: 1em !important; 
}    
#### 結論

寫CSS 都有一套策略，可以令整個 CSS 檔案較直觀，較容易修改。

- 1.先放一般性的規則，例如body，a，以定義一個基本的頁面風格
- 2.再用不同的class，id 定義一些地方(.description，.error)
- 3.最後是特別的區域，例如 #primary-links a 避免一開始便定義高級的規則

例如
div#pagewrapper div#contentwrapper a

# 少用CSS的繼承，多用組合

繼承是 css 中經常要用到的技術，好處是可以盡量讓頁面的代碼減少重複利用。  但是隨時項目越來越大，需求的不斷變化，css 代碼就會變得越來越臃腫，越來越難以控制和維護。  其實，css 代碼和普通程式代碼在編寫的時候有很多的相似之處，下面我們就用試試用組合的方式，是不是能更好的解決這個問題。  下面是一段普通的代碼：  
.box
{ 
border:1px solid #ccc; 
font-size:12px; 
background:#f1f1f1; 
}  ```xml
<div class="box">this is  a gray box</div>
```

若需求增加，在頁面中不僅要有一個灰色的盒子可能還有綠色的盒子，這時候就可以將共用部分獨立出來，做如下更改：
.box-gray, .box-green
{ 
border:1px solid #ccc; 
font-size:12px; 
} 
.box-gray{background:#f1f1f1} 
.box-green{background:#66ff66}  ```xml
<div class="box-gray">this is  a gray box</div> 
<div class="box-green">this is a green box</div>
```

但是這個時候如果需求又有變化了，盒子中有些要用到12號字，有些要用到14號字，有些字要10px有些要20px，如果要用用繼承css代碼就會變得異常的複雜，那我們就來試驗一下用組合的方式看能不能解決。
.fs-12{font-size:12px} 
.fs-14{font-size:14px} 
.pd-10{padding:10px} 
.pd-20{padding:20px} 
.box{border:1px solid #ccc;} 
.box.gray{background:#f1f1f1} 
.box.green{background:#66ff66}  ```xml
<div class="box gray fs-12 pd-20">this a gray fontsize12px padding20px box</div> 
<div class="box green fs-14 pd-10">this a gray fontsize14px padding10px box</div> 
```

從上面可以看到「組合」的方式是不言而喻的，但是也不是十全十美的，再拆分組合的時候一定不要過度，不然效果可能適得其反。  只有把組合+繼承運用的恰到好處才能讓我們的代碼更加優雅和藝術。
## 參考資料  

- [CSS 的規則繼承和權重](http://www.joetsuihk.com/css_specificity_for_poker_players)
- [CSS 語法教學](http://css.1keydata.com/tw/)