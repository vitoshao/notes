---
title: Validation
layout: default
parent: JavaScript
nav_order: 11
description: "Validation"
date: 2013-03-21
tags: [Web,JavaScript]
postid: "5707974863583395551"
---
jQuery Validation 是 jQuery 的一個既強又有彈性的 plugin 套件，主要功能就是用來進行表單驗證。  

# Using jQuery Validation

#### jQuery.validate

**外掛名稱：**jQuery.validate  
**外掛版本：**1.6  
**官網下載：**[http://jquery.bassistance.de/](http://bassistance.de/jquery-plugins/jquery-plugin-validation/)

## 簡單範例
```xml
<head>
<title></title>
<script type="text/javascript" src="./Scripts/jquery-1.5.1.js"></script>
<script type="text/javascript" src="./Scripts/jquery.validate.js"></script> 
<script type="text/javascript">
$(function () {
$("#myForm").validate();
});
</script>
</head>
<body>
<form id="myForm" method="post" action="javascript:alert('完成');">
<p>   
UserName(\*) :<input class="required" minlength="5" />   
</p> 
<p>   
Email(\*) :<input name="email" class="required email"/>   
</p> 
<p>   
<input class="submit" type="submit" value="Submit"/>  
</p> 
</form>
</body>
```

![](http://127.0.0.1:8080/_images/cs/jquery-validate-ex1.png)

**語法**說明

若要驗證欄位，只要將下列驗證規則參數加入 class 屬性中即可，也可以混合使用。

#### 驗證規則參數：([查看完整列表](http://docs.jquery.com/Plugins/Validation#List_of_built-in_Validation_methods))

1. required：必填欄位
2. email：格式要符合E-Mail格式
3. url：格式要符合網址格式，如：http://www.minwt.com
4. number：數字格包含小數點
5. digits：數字為正整數
6. date：日期格式
7. dataISO：日期格式，格式必須為YYYY/MM/DD、YYYY-MM-DD、YYYYMMDD
8. equalTo：與某一欄位值相同
9. minValue：最小字元長度
10. maxValue：最大字元長度
11. rangeValue：字元長度區間長度
12. minLength：最小字元長度(中文字算一個字符)
13. maxLength：最大字元長度(中文字算一個字符)
14. rangeLength：字元長度區間長度(中文字算一個字符)

## 進階範例
```xml
<form id="myForm" method="post" action="javascript:alert('完成');">
<p>   
UserName :<input type="text" name="username"/>   
</p> 
<p>   
Email :   <input type="text" name="email"/>   
</p> 
<p>   
Policy :  <input type="checkbox" name="agree" id="agree"/>
</p> 
<p>   
Favorite :<input type="checkbox" name="favorite" value="apple"/>apple
<input type="checkbox" name="favorite" value="htc"/>htc
<input type="checkbox" name="favorite" value="samsung"/>samsung
<label for="favorite" class="error">請至少選擇二項</label>
</p> 
<p>   
<input type="submit" value="Submit"/>  
</p> 
</form>
```
```javascript
<script type="text/javascript">
$(function () {
$("#myForm").validate({
rules: {
username: {
required: true,
minlength: 3
},
email: {
required: true,
email: true
},
agree: "required",
favorite: {
minlength: 2
}
},
messages: {
username: {
required: "此為必填欄位",
minlength: "UserName 至少需要 {0} 個字"
},
email: "請正確輸入Email",
agree: "你必須勾選同意"
},
event: "keyup",
submitHandler: function () {
if ($("#agree").attr("checked"))
$("#agree").attr("checked", true);
}
});
});
</script>
```

![](http://127.0.0.1:8080/_images/cs/jquery-validate-ex2.png)

**語法**說明

1. rules ：定義驗證規則。
2. messages ：定義訊息文字。
3. event ：設定觸發驗證的事件，可選用的事件值有 [keyup|blur]，若不使用這個參數時就只會在 submit 事件時觸發驗證。
4. submitHandler ：定義當驗證通過，但尚未送出前所要執行的程式碼。
5. debug ：定義當驗證失敗時所要執行的程式碼。

# Customized validation rule

有的時候，我們需要稍微複雜一點的檢查邏輯，讓 A 欄位的檢查邏輯可以因為其他的輸入欄位而調整。例如：如果在線上購物的時候，使用者有匯款、線上刷卡兩種付款的方式，我們希望如果使用者選擇了線上刷卡，匯款帳號後五碼變成選填，信用卡號變成必填；如果使用者選匯款，檢查的邏輯就顛倒過來，像這樣的功能要怎麼做呢？  
```xml
<input name="payment" id="atm" type="radio" value="ATM" />
<label for="atm">ATM 匯款 : 請填入匯款帳號末五碼 </label>
<input name="pay_account" type="text" class="atm_required" />
```
```javascript
$.validator.addMethod("atm_required",
function( value, element){
return ( $('#payment_atm').is(':checked') ) ?
( value != '' && value.length == 5) : true;
}, 'ATM 匯款必填'
);
```

# Customized error message

若要修改驗證結果的訊息文字，有以下做法

## 修改 jquery.validate.js

這方法可以修改預設的訊息內容，只要開啟 jquery.validate.js ，找到以下區塊，變更文字內容即可。

![](http://127.0.0.1:8080/_images/cs/jquery-validate-message.png)

## 使用 localization messages

jQuery validation 預設的 message 是英文，如果你想要用其他的語言，可以在 include jquery validation plugin 之後，再多 include locale 的 javascript, 如：  
```javascript
<script type="text/javascript" src="/PATH/TO/jquery-validation/jquery.validate.min.js"></script>
<script type="text/javascript" src="/PATH/TO/jquery-validation/localization/messages_tw.js"></script>
```

## 在 messages 中加入 icon

jQuery validation 預設會將 error message 裝在 &lt;label class="error"&gt;&lt;/label&gt; 裡，並放在輸入欄位的後面。  所以你也可以針對這個 class 設定屬於你的 error message 樣式。  
label.error{
background:url("images/unchecked.gif") no-repeat 0px 0px;
padding-left: 16px;
}

label.success{
background:url("images/checked.gif") no-repeat 0px 0px;
padding-left: 16px;
}  ```javascript
$(document).ready( function(){
$('#commentForm').validate({
success: function(label) {
label.addClass("success").text("Ok!");
}
});
});
```
## 參考資料  
![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [jQuery plugin: Validation (官網)](http://bassistance.de/jquery-plugins/jquery-plugin-validation/)
- [jQuery教學－jQuery.validate萬用表單驗證元件直接套用](http://www.minwt.com/js/1800.html)
- [jQuery plugin Validation的7個Callback介紹](http://www.dotblogs.com.tw/wadehuang36/archive/2010/08/22/17353.aspx)
- [好用的表單驗證工具 – jQuery validation plugin](http://brooky.cc/2011/08/04/using-jquery-validation-plugin-for-form-validation/)
- [10 Useful jQuery Form Validation Techniques and Tutorials](http://speckyboy.com/2009/12/17/10-useful-jquery-form-validation-techniques-and-tutorials-2/)