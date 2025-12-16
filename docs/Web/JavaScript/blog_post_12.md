---
title: 基礎語法
layout: default
parent: JavaScript
nav_order: 12
description: "基礎語法"
date: 2014-09-01
tags: [Web,JavaScript]
postid: "4016418624254179893"
---
# 變數

JavaScript 變數可分成 local 和 global 兩類。  local scope 變數會跟隨著 function 的生命週期，而 global 變數則會一直存在。  由於使用全域變數，一個不小心就容易發生覆蓋問題，所以要盡量減少全域變數的使用。  

## 使用 var 宣告變數

在 JavaScript 中要宣告變數，可以使用 var 來宣告。  使用 var 宣告的變數，如果在函式外則為全域變數，如果在函式內則為區域變數。  
```javascript
var a = 3;

function func() {
var x = 4;
y = 5;
myOutput(a);        //3
myOutput(x+y);      //9
}
func();

myOutput(typeof x); //undefined
myOutput(typeof y); //number     , 沒有使用 var 宣告的變數,隱含為全域
```

其實在 JavaScript 中，並沒有強制一定要使用 var 來宣告變數，若不使用 var 宣告變數，則隱含為全域變數，而且與 var 宣告的變數最大的差別在於它可以被刪除。  這是因為不使用 var 宣告的變數並不是真正的變數，而是全域物件的屬性，所以才可以透過 delete 運算子刪除。  
```javascript
delete a;
delete y;
myOutput(typeof a); //number    , 透過 var 宣告的變數無法刪除
myOutput(typeof y); //undefined , 因為 y 被 delete 了
```

## 變數的 hoisting 行為

在 JavaScript 中有一個特性，稱為 Hoisting ，也就是程式會將 function 中全部需要宣告的 Local Variable ，不管 var 的敘述在什麼位置，都會被提升到 function 的第一行來執行。  如下面這個例子，程式碼中的的第５行，一般我們預期的結果應該是 vito ，但是，就是因為 hoisting 的關係，區域變數覆蓋了全域變數，所以結果才會變成 undefined。  
```javascript
var myname = "vito";
alert(myname);              //vito

function func() {
alert(myname);          //undefined
var myname = "shao";
alert(myname);          //shao, 區域的 myname 變數 
}
func();

alert(myname);              //vito, 全域的 myname 變數
```

上面例子實際上等同下面例子，這樣就可以理解為什麼了。  
```javascript
var myname = "vito";
alert(myname);              //vito

function func() {
var myname;
alert(myname);          //undefined
myname = "shao";
alert(myname);          //shao, 區域的 myname 變數 
}
func();

alert(myname);              //vito, 全域的 myname 變數
```

# 資料型態

在 JavaScript 中，只提供 string, number, boolean, null, undefined 五種基本型別，其餘的資料型態都屬於 Object 。  

## 字串

## 布林

## 數值

## 陣列

## 日期

## 正規表示式物件 RegExp

JavaScript 的正規表示式也是一個物件，用來描述字元的樣式。  依據指定的樣式，你可以對一個字串進行樣式比對，或者取代的操作。  要建立 JavaScript 正規表示式有二種建立方法：使用實字，或者使用 new RegExp 建構式。  

### 樣式與修飾詞

RegExp 由二個元素組成：

#### 樣式（Pattern）

樣式是用來設定比對的原則，你可以使用以下規則組成比對樣式：  

使用小括弧用於子樣式，中括弧可指定特定範圍的字元

| (a | e | i | o | u) | a 、 e 、 i 、 o 、 u 中任一字元 |
| --- | --- |
| 0-9] | 0 到 9 的數字 |
| [a-z] | 所有小寫英文字母 |
| [A-Z] | 所有大寫英文字母 |
| A-z] | 所有英文字母，無論大小寫 |

中介字元都是單一字元，除了小數點符號外，均以反斜線開始，如下

| . | 任何字元，除了新行或結束符號 |
| --- | --- |
| \w | 任何文字字元 |
| \W | 任何非文字字元 |
| \d | 任何數字 |
| \D | 任何非數字 |
| \s | 任何空白字元 |
| \S | 任何非空白字元 |
| \b | 英文單字開頭 |
| \B | 非英文單字開頭 |
| \0 | NULL 字元 |
| \n | 新行符號 |
| \f | 換頁符號 |
| \r | 輸入鍵符號 |
| \t | 水平 |
| \v | 垂直 tab 符號 |
| \000 | 八進位數字 |
| \x00 | 十六進位數字 |
| \u0000 | Unicode 字元 |

量詞標記在字元之後，表示數量

| + | 表示 1 到無數個 |
| --- | --- |
| \* | 表示 0 到無數個 |
| ? | 表示 1 或 1 個 |
| {1} | 表示 1 個 |
| {1, 2} | 表示 1 到 2 個 |
| {1,} | 表示 1 到無數個 |

開頭與結尾的指定符號

| $ | 若出現在字串結尾，表示其字元必須是字串結尾的字元 |
| --- | --- |
| ^ | 若出現在字串開頭，表示其字元必須是字串開頭的字元 |

#### 修飾詞（Modifiers）

修飾詞屬於額外的比對原則，包括底下幾種標記符號：

| 名稱 | 說明 |
| --- | --- |
| i | 不區分大小寫 |
| g | 傳回所有符合的結果 |
| m | 多行筆對 |

### 使用 new RegExp 建構式

#**語法**
```sql
var patt = new RegExp(pattern,modifiers);
```
```javascript
var rege = new RegExp("[a-z]+", "g");
var data = "AbC 302 c12";
var result = data.match(rege);  // b, c
```

### 使用實字

#**語法**
```sql
var patt = /pattern/modifiers;
```
```javascript
var rege = /[a-z]+/ig;
var data = "AbC 302 c12";
var result = data.match(rege);  // AbC, c
```

# 運算子

JavaScript 運算子有以下幾種類：

## 1.算術運算子 (Arithmetic Operators)

## 2.字串運算子 (String Operators)

## 3.邏輯運元 (Logical Operators)

## 4.位元運算子 (Bitwise Operators)

## 5.指定運算子 (Assignment Operators)

## 6.比較運算子 (Comparison Operators)

## 7.特別運算子 (Special Operators)
## 參考資料  

- [JavaScript 教學](http://taiwantc.com/js/)
- [\[IT邦幫忙\](Ted) JavaScript 教學系列整理](http://ithelp.ithome.com.tw/question/10098361)
- [\[維克的煩惱 \]基礎資料型態(字串-數值-布林)](http://www.victsao.com/blog/81-javascript/84-javascript-basic-type)
- [認識 JavaScript 的物件導向技術](http://renjin.blogspot.tw/2008/08/object-oriented-javascript.html)
- [使用物件導向技術來建立進階 Web 應用程式](http://msdn.microsoft.com/zh-tw/magazine/cc163419.aspx)
- [\[JavaScript\] 談物件, 實字與建構式](http://blog.rx836.tw/blog/javascript-patterns-1/)
- [\[w3schools\] JavaScript Tutorial](http://www.w3schools.com/js/default.asp)