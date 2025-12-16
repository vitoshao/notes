---
title: 物件
layout: default
parent: JavaScript
nav_order: 13
description: "物件"
date: 2014-09-01
tags: [Web,JavaScript]
postid: "4564955652677440010"
---
雖然 JavaScript 沒有提供 Class 和 Inheritance ，但是它還是可以透過別的方式來實現這些 OOP 的功能。  要實現這些 OO 功能，只要回歸到 JavaScript 物件本身即可，只是 JavaScript 物件與 C# 或 C++ 語言中的物件有不一樣的概念。  這篇文章，主要就是介紹在 JavaScript 語言中的物件概念。  

# 建立物件

**JavaScript物件**  
在 JavaScript 中，除了原始資料型態 ( Boolean, Number, String ) 和 undefined 外，其餘的所有東西都是物件。  所以，null, array, function 都是物件。  

要使用 JavaScript 實現 OO 功能必須回歸到 JavaScript 的物件本身，但是 JavaScript 的物件與 C# 或 C++ 語言中的物件是不一樣的概念。  在一般物件導向程式語言中，物件是 class 的執行個體，具有不同的屬性和方法，要建立物件，通常要先建立類別，再使用該類別建立執行個體。  但是對 JavaScript 程式語言，這些都是不需要的，JavaScript 物件就只是由數個 Name-Value Pair 所組成的集合。  你可以很簡單的由下面的範例語法建立物件：  
```javascript
//建立物件
var person = new Object();

//設定物件屬性
person.name = "vito";
person.age = 10;

//設定物件方法
person.sayHello = function () {
return "I am " + this.name;
}

alert(person.sayHello());
```

上面這個範例，透過 new 建立一個新的 Object 物件，然後才指派屬性及方法給物件變數，而所謂的方法，其實也只是參考到 function 物件的屬性而已。  同時由這個範例也可以看的出來，JavaScript 物件的屬性不一定要事先宣告，你可以在任何時候加入額外的屬性。  

若要建立 javascript 物件，有三種模式：實字、建構式、自訂建構式函式。

## 使用實字（Literal）方式建立物件

「實字模式」是 JavaScript 1.2 版本支援的物件實體語法，是 JavaScript 的另一種定義方式，你可以使用這個方法來宣告及初始化物件。  實字語法語法除了較簡潔以外，在使用上也較不容易出錯。  

### 將屬性加入物件

前面題過，JavaScript 物件就是由數個 Name-Value Pair 所構成的集合，而使用實字模式這個方式建立物件，就是利用「 { } 」將物件的屬性包起來即可。  
```javascript
// 首先建立一個空物件
var person = {};

// 加入屬性
person.name = "vito";
person.age = 20;

// 加入方法
person.sayHello = function () {
return "I am " + this.name;
}

myOutput(person.sayHello());
```

另外，你也可以使用以下寫法，將**屬性**與**方法**都包在 「{ } 」裡頭，最後別忘了要在最後面的「}」加上「分號」。  你也可以把方法當做屬性來看，此時它的值就一個 function 物件。  

- 使用「{ }」包住。
- 使用「：」分隔 name 跟 value 。
- 使用「，」分隔所有屬性或方法。
- 使用「；」結束整個 expression 。
```javascript
// 宣告及初始化物件
var person = {
name: "vito",
age: 20,
sayHello: function () {
return "I am " + this.name;
}
};
```

簡單講，實字語法就是使用「{ }」包住一堆 name/value 對的屬性或方法。

### 存取物件的屬性

若要存取物件的屬性，有二種方式：使用「.」或者使用「[""]」來存取。  
```javascript
person.tel = "12345";
person["tel"] = "54321";

alert(person.tel);
alert(person["tel"]);

//也可以刪除物件屬性
delete person.tel;
delete person["tel"];
```

## 使用建構式（Constructor）方式建立物件

雖然 JavaScript 沒有class，但他卻有一個很相似class 的語法，叫做建構式函式（constructor functions），  使用建構式模式來建立物件，你必須透過 new 關鍵字來呼叫建構式函式。  建構式函式可以是內建物件的建構式（如：Object()、Date()、String()），也可以是自訂的建構式函式。  

下面這個例子，Object()可以看成是沒有參數列的constuctor，Object這個物件也可以使用帶參數的constuctor.  
```javascript
//一個空物件
var person = new Object();
person.name = "vito";

//一個數值物件
var number = new Object(5);
number.double = number\*number;
```

上面這種使用方法，稱為「反模式」。書本建議，這個方式在使用上，若不注意有可能導至非預期的結果，所以建議還是使用實字建立物件比較好。

## 自訂建構式函式

綜合以上的介紹，我們就可以來建立自訂的建構函式。  要在 JavaScript 中建立「自訂建構函式」，不必像其他語言還得建立類別，JavaScript 的「自訂建構函式」就是一個函式。  只是它有二種寫法，其建立語法如下：  
```javascript
var Person = function (_name) {
// property
this.name = _name;

// public method
this.sayHello = function () {
return "hello " + this.name;
}

// private method
function internalMethod(x) {
return x;
}
}
```
```javascript
function Person(_name) {
// property
this.name = _name;

// public method
this.sayHello = function () {
return "hello " + this.name;
}

// private method
function internalMethod(x) {
return x;
}
}
```

要叫用建構式函式，必須使用 new 關鍵字。
```javascript
var p = new Person("vito");
myOutput(p.sayHello());
```

上面例子，Person 明明只是一個 function ，之所以會有類別的效果，實際上是因為這個 function 中會在背後先建立一個 this 參考，指向一個新的 Object 物件，然後將各個屬性加進去，最後再傳出這個 this 參考，如下所示：  
```javascript
// 定義建構函式
function Person(_name) {
//var this = {};
this.name = _name;
this.sayHello = function () {
return "I am " + this.name;
}
//return this;
}

// 實體化物件
var person = new Person("vito");
myOutput(person.sayHello());
```

## 比較［物件］與［自訂建構式函式］

先看看底下這二段 Code 。
```javascript
var ObjA = {
name: "--ObjA--",
show: function () {
myOutput(this.name + ' show');
}
};

var ObjB = function () {
this.name = "--ObjB--";
this.show = function () {
myOutput(this.name + ' show');
};
};

//ObjA 是一個物件，無須使用 new 建立物件，使用時直接叫用物件方法即可。

ObjA.show();

//ObjB 是一個 constructor，無法直接叫用 show() 方法
//必須先使用 new 建立物件，才可以叫用方法

var objB = new ObjB();
objB.show();
```

# 原型繼承

原型物件（Prototype）是 JavaScript 用來模擬類別階層（Class Hierarchy）的中心概念物件。每個函式物件（fucntion 型別的物件）都有一個 prototype 屬性（該物件所擁有的原型物件），它可以用來加入自訂屬性及方法。所謂的原型物件，其實也是使用 Object 建構函式所建立的物件。另外，所有物件實體內部還隱含一個名為 \_\_proto\_\_ 的屬性，它會指向其建構函式的 prototype 屬性。當然，建構函式的 prototype 屬性參照的原型物件也隱含有 \_\_proto\_\_ 屬性，指向它的建構函式的原型物件，以此類推，最後追朔到最終基底原型 Object.prototype 為止。每一個物件都會繼承一整鏈的原型，這樣的鏈結關係稱之為「原型鍊（Prototype Chain）」。  

我們都知道 Object 的原型物件擁有以下的屬性成員：

- constructor
- toString()
- toLocaleString()
- valueOf()
- hasOwnProperty(propertyName)
- isPrototypeOf(objectRef)
- isPropertyEnumerable(propertyName)

當你建立自訂物件時，將繼承 Object 物件原型的所有屬性和方法。
```javascript
function MyObject() {

}
var myObject = new MyObject();
alert(myObject.toString()); // Displays "[object Object]"
```

上例中，我們透過自訂的物件參照呼叫 toString() 方法。事實上，這個方法是來自 Object 的原型物件。那麼 JavaScrpt 是如何解析呢？當你試圖要存取物件的屬性/方法時，沒有定義在物件中，那麼 JavaScript 就會檢查該物件的原型。如果還是沒有，就會循我們前面所提到的原型鏈往上尋找，直到 Object.prototype 為止。  

## 透過 prototype 建立階層關係

現在，你已暸解 JavaScript 是如何以原型鏈來模擬類別階層關係。接下來，我們就可以利用原型物件來實作衍生類別：  
```javascript
function Shape(x, y) {
this.x = x;
this.y = y;      
}
Shape.prototype = {
getCoordinates : function() {
return "(" + this.x + " ," + this.y + ")";
}
};
function Rectangle(x, y, width, height) {      
Shape.call(this, x, y);
this.width = width;
this.height = height;  
}
Rectangle.prototype = new Shape();
Rectangle.prototype.getArea = function() {
return this.width \* this.height;
};
var objRect = new Rectangle(100, 200, 5, 10);
alert(objRect.getCoordinates()); // Displays "(100, 200)"
alert(objRect.getArea()); // Displays "50"
```

在上例中，Shape 可視為基底類別（Base Class），而 Rectangle 是衍生類別（Derived Class）。其中，繼承的關鍵在於設定 Rectangle.prototype 屬性，將 Shape 物件加入 Rectangle 的原型鏈。另外，在 Rectangle 建構函式中，透過函數物件的 call() 方法，呼叫 Shape 建構式來進行初始化。

## 使用 prototype 節省記憶體

下面這個建構函式物件，當每次使用 new 呼叫建構式時，都會整個重新建立一次。
```javascript
function Person(name, age) {
this.name = name;
this.age = age;
this.toString = function () {
return '[' + this.name + ', ' + this.age + ']';
};
}
var p1 = new Person('vito', 35);
var p2 = new Person('vito', 30);
myOutput(p1.toString() == p2.toString());
```

這程式碼中的 toString 其實沒有什麼特別的，每個物件都建立一份只是浪費，所以為了節省記憶體，你可以這麼改寫：
```javascript
function ToString(name,age) {
return '[' + name + ', ' + age + ']';
}

function Person(name, age) {
this.name = name;
this.age = age;
this.toString = ToString(name,age);
}
var p1 = new Person('vito', 35);
var p2 = new Person('vito', 30);
myOutput(p1.toString == p2.toString);
```

上面做法的確解決了重複建立函式實例的問題，但在全域範圍（物件）上多加一個 ToString 名稱。  其實這問題就可以直接透過物件的 prototype 特性來解決，如下：  
```javascript
function Person(name, age) {
this.name = name;
this.age = age;
}
Person.prototype.toString = function () {
return '[' + this.name + ', ' + this.age + ']';
}
var p1 = new Person('vito', 30);
var p2 = new Person('vito', 30);
myOutput(p1.toString() == p2.toString());
```