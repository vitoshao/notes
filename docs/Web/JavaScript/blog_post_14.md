---
title: 函式
layout: default
parent: JavaScript
nav_order: 14
description: "函式"
date: 2014-09-01
tags: [Web,JavaScript]
postid: "910649094512789881"
---
# function 基本用法

## 函式的定義

在 JavaScript 中要定義函式，有以下幾種方法：  

##### 使用 function 關鍵字宣告函式：
```javascript
function add1(x, y) {
return x + y;
}
myOutput(add1(3, 5));
```

##### 使用匿名函式表示式的宣告方式來定義函式：（named function expressions, NFE）
```javascript
var add2 = function (x, y) {
return x + y;
};
myOutput(add2(4, 3));
```

在例一中，這種函式的宣告方式是一般語言中常見的方式，其中 add1 就是它的函式名稱，但其實它就是一個 variable ，當要叫用該函式時，我們都很直覺就會使用 add1() 來叫用。  以這樣的觀點來看例二，那就容易理解了，add2 是一個變數，它表示一個函式，要叫用它，就使用 add2() 即可。  

##### 使用具名函式表示式來定義函式：
```javascript
var add3 = function MyAdd(x, y) {
return x + y;
};
myOutput(add3(4, 3));
```

對具名函式來說，這個名稱的存取範圍僅限於函式內部，在函數之外是看不到的，所以你不可以這麼叫用：
```javascript
MyAdd(4, 3)
```

在 JavaScript 的除錯環境的 stack traces、call stacks、breakpoints 列表中，  若是使用匿名函數，大都只會顯示 anonymous 這樣的名稱，  如果是具名函數的話，就會清楚標示該函數的名稱，這對於除錯而言是很好用功能。  

注意事項：

在 JavaScript 中定義 function 主要分成二種方式：(1) function expression (2) function declaration 。  在上面幾種函式定義方法中，有出現等號(=)的方式，表示它是一個「表示式」(expression)，所以就必須以「；」結尾。  如果是使用「函式宣告」(function declaration)，那麼就用「{}」包起來就可以。  

使用表示式來定義函式，可以更突顯出函式就是物件，它就像其他物件一樣，不是特殊的語言結構。

## 函式的提升（Hoisting）行為

上面函式的定義分別使用了「表示式」與「宣告」二種方式，雖然他們作用是一樣的，但是它們卻有不一樣的 hoisting 行為。  

使用「宣告」方式，你可以這麼叫用，因為 JavaScript 會把「宣告」提升到其所在區域內的頂端，  所以在程式碼中看起來，就變成在函式宣告前就可以叫用該函式。  
```javascript
test1();
function test1() {
myOutput('test1') ;
}
```

但是如果使用「表示式」方式，則不允許這麼叫用。  因為 JavaScript 只會把變數宣告部分提升到 scope 頂端，不會提升指定部分。  
```javascript
test2();               // test2 is not a function
var test2 = function () {
myOutput('test1');
};
```

# function 也是物件

在許多程式設計語言中，函式和物件通常被視為兩種不同的東西。但是在 JavaScript 中，「函式」其實就是包含可執行程式碼的 Function 物件。  這意味 Function 可以被動態建立，儲存在變數、陣列和物件中，也能做為函式的「傳入參數」或「傳回結果」。  當你使用 function 關鍵字宣告一個函式，其實就是在定義 Function 物件。  因此，要特別記得，只要使用 new 運算子呼叫函式，就會傳回完全初始化的 Object 物件。  

Javascript 中，函式除了可以被呼叫外，它還有以下功能，是一般程式語言比較少見的：

- function 是一個物件
- function 可以有自已的屬性和方法
- function 可以指定給變數
- function 可以當做參數
- function 可以在執行時才動態建立
- function 可以被擴充，也可以被刪除
```javascript
function Person(_name) {
this.name = _name;
this.sayHello = function () {
myOutput("hello " + this.name);
}
myOutput(this.name);
}

var person = new Person("vito");    //vito  
person.sayHello();                  //hello vito
```

你也可以使用 **Function 建構函式**來建立函數物件。
```javascript
var add4 = new Function("x", "y", "return x + y;");
myOutput(add4(2, 3));
```

# 回呼模式（call back）

## 什麼是回呼模式

下面這個例子，f 是一個函式參數，當 Say() 這個函式被呼叫時，  在它在定義中，僅知道要去執行某些事情，但是並不知道要去執行哪個事情，必須等到執行階段才能由參數來決定。  這種情況， hello() 或 hi() 這二個函式就被稱為回呼函式（callback function）。  
```javascript
function Say(f) {
// 要做些事情....
f(); 
//...
}

function hello() {
myOutput('HELLO');
}

function hi() {
myOutput('HI');
}

Say(hi);
Say(hello);
```

回呼可以是一個現有的函式，也可以是個匿名函式，例如你也可以直接這樣叫用：
```javascript
Say(
function () {
myOutput('Bye Bye');
}
);
```

### 傳遞參數給回呼函式

若要傳遞參數給回呼函式，做好有數種方式，底下這個範例直接使用 arguments 變數來表示傳進來的參數值。  
```javascript
function Cacular(callback) {
return callback(arguments[1], arguments[2]);
}

function Add(a, b) {
return a + b;
}

function Sub(a, b) {
return a - b;
}

myOutput(Cacular(Add, 10, 5));   // 15
myOutput(Cacular(Sub, 10, 5));   // 5 
```

## 非同步事件監聽器

在網頁中使用「非同步事件監聽器（event listener）」就是回呼模式的一種應用。  它可以協助你針對網頁中的特定事件，設定要執行的對應函式。  例如底下這個例子中的 setTimeout() 這個方法，當指定的時間到時，就會進行回呼。  
```javascript
var now = new Date()

var doSomething = function () {
//...
//Date().fo
myOutput('2...' + (new Date()).format("hh:mm:ss.ms"));
};

myOutput('1...' + (new Date()).format("hh:mm:ss.ms"));
setTimeout(doSomething, 1000);
myOutput('3...' + (new Date()).format("hh:mm:ss.ms"));

//1...15:22:39.42
//3...15:22:39.42
//2...15:22:40.49
```

下面這個簡單範例示範，如何在網頁中執行 click 事件的監聽。  
```javascript
document.addEventListener("click", callback, false);

var i = 0;
function callback() {
i++;
myOutput(i);
}
```

# 回傳一個函式（return a function）

既然函式本身是一個物件，所以它也可以當作某個函式的回傳值。  也就是函式並不一定只能回傳某些資料，它也可以回傳另一個函式。  
```javascript
var FA = function () {
myOutput('FA');
return function FB() {
myOutput('FB');
}
};

//呼叫FA(), 取得一個函式
var fa = FA();   //FA
fa();            //FB
fa();            //FB
fa();            //FB
```

## 閉包（Closure）

在認識「閉包」之前，先看看底下這個例子：
```javascript
var setup = function () {
var counter = 0;
myOutput(counter);
return function () {
counter += 1;
myOutput(counter);
}
};
var next = setup();   // counter = 0
next();     // 1
next();     // 2
next();     // 3
```

在上面這個例子中，next 經由呼叫 setup 函式，取得一個函式，像這樣子的 next 就稱為「閉包（Closure）」。  

一般函式在執行完畢時，內部的區域變數會被釋放掉，但如果在執行時產生了閉包。那閉包會保留其所在巢狀函式內的變數，讓他不至於被系統釋放掉。  所以閉包除了回傳的函式本身，它還擁有一組特殊的環境變數，這些環境變數就是閉包在建立時可存取的作用域中的所有變數。

## 自我定義函式（self-defining function）

JavaScritp 可以將函式指派給變數，若是你又建立了一個新函式，且指派給相同的變數，那麼舊的函式將被取代，而這個變數將指向新的函式。  如下列程式碼：  
```javascript
var hello = function () {
myOutput("hello");
hello = function () {
myOutput("hello world");
}
}

hello();    //hello
hello();    //hello world
```

上面例子中，第一次呼叫 hello 時，函式會用一份新的實作重新定義並覆蓋掉自已，所以第二次呼叫 hello 時，執行的內容就與第一次不同。  這種例子的使用情境，大都是該函式第一次叫用時，必須執行一些初始他工作，而之後的叫用再也不須要這些工作，就可以使用這種自我定義函式來取代掉原先的函式。  這種函式因為之後做的事比原先的少，所以又稱為**懶惰函式**（lazy function definition）。  

想想看，底下這個例子的結果，為什麼執行結果和想像中的不太一樣。  
```javascript
var hello = function () {
myOutput("hello");
hello = function () {
myOutput("hello world");
}
}

var hi = hello;

hi();       //hello
hi();       //hello

hello();    //hello world
hello();    //hello world
```

這是因為，當第一次叫用 hi() 時，它只會重新定義 hello 這個變數的指向，但自已仍等同舊的 hello() 定義。  所以不管 hi() 叫用幾次，輸出都是 hello ，而叫用 hello() ，輸出都是 hello world 。

# 立即函式（immediate function）

「立即函式」只是一種表示法，它可以讓函式在定義時，立刻執行，所以也稱為自我調用（self-invoking）或自我執行（self-executing）。例如：
```javascript
var counter = 0;

//註１
var count = function Count() {
counter++;
myOutput(counter); //1
}();

//註２
(function Count() {
counter++;
myOutput(counter); //2
}());

//註３
(function Count() {
counter++;
myOutput(counter); //2
})();
```

- 註１：在函式後面加上一組括號 () ，這樣就會讓函式立即執行。
- 註２：如果沒有將函式 assign 給變數，那麼就要再用一組括號包起整個函式。
- 註３：這是同上面的替代語法，不過 JSLint 只支援上面那種。

## 立即函式的參數

你也可以傳遞參數給立即函式，例如：
```javascript
(function (day) {
var week = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六'];
myOutput('今天' + week[day]);  //今天星期二
} (2));
```

上面這類例子，其參數值通常來自 global variable 。另外，如果你要同時傳遞很多個全域變數，你也可以直接傳遞全域物件 this ，以避免傳遞太多變數。  然後就可以在函式內部，藉由 this 物件與外部的環境互動。  
```javascript
var day = (new Date()).getDay();

(function (global) {
var week = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六'];
myOutput('今天' + week[global.day]);  //今天星期二
} (this));
```

## 立即函式的回傳值

你也可以在立即函式使用 return ，並將結果 assing 給一個變數。  下面例子中的二種寫法都可以，因為，立即函式若要 assign 給變數，可以去掉外層括號。  
```javascript
var hello = (function () {
return  "hello";
} ());
myOutput(hello);

var hello = function () {
return "hello";
} ();
myOutput(hello);
```

但是要注意下面的程式碼，這個例子中的 hello 表示一個函式，與上面完全是不一樣的意義。
```javascript
var hello = function () {
return "hello";
} ;
```

##### 立即函式也可以回傳函式。
```javascript
var hello = (function () {
var msg = "hello";
return function () {
return msg
}
} ());
myOutput(hello());
```

##### 使用立即函式來計算物件的屬性值。

如果你要定義一個物件的屬性，該屬性值在生命週期內都不會變動，但是在定義時必須先經由計算取得，這時就可以利用立即函式來包裝這些運算。  
```javascript
var o = {
area: (function () {
var pi = 3.14;
var radio = 5;
return pi \* radio \* radio;
} ()),
getArea: function () {
return this.area;
}
};

myOutput(o.area);
myOutput(o.getArea());
```

## 立即函式的使用時機

先看看這段程式碼，我們宣告一個 week 陣列和一個 day 變數，以協助取得要輸出的資訊。
```javascript
var week = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六'];
var day = (new Date()).getDay();
myOutput('今天' + week[day]);  //今天星期二
```

上例中，week 和 day 都是全域變數，主要用以協助計算輸出的資訊，如果後續都沒有其他作用，那麼就可以使用「立即函式」來避免這個全域變數。  或許你會說，把它們定義在一個函式裡，再叫用這個函式來，也可以減少全域變數，但最簡便的方法還是使用「立即函式」。  
```javascript
(function () {
var week = ['星期日', '星期一','星期二','星期三','星期四','星期五','星期六'];
var day = (new Date()).getDay();
myOutput('今天' + week[day]);  //今天星期二
}());
```