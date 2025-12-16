---
title: JSON
layout: default
parent: JavaScript and AJAX
nav_order: 4
description: "JSON"
date: 2013-03-12
tags: [Web,ASPNET,JavaScript and AJAX]
postid: "3339053015603481232"
---
JSON （JavaScript Object Notation）是一種以純文字為基礎的資料格式。  因為它的結構簡單，很適合用來做為程式溝通或交換資料時使用。  也由它的輕量化和易於閱讀的特性，目前已廣泛應用於各種技術領域，例如：AJAX, WCF, jQuery。  

# JSON 格式概念

#### 單個 JSON 對象
[
{empid:'001',name:'vito'},
{empid:'002',name:'shao'}
]    
#### 多個 JSON 對象
{
"emp":[{empid:'001',name:'vito'},{empid:'002',name:'shao'}],
"dpt":[{dptid:'001',name:'IT'},{dptid:'002',name:'FIN'}]
}    
#### 巢狀 JSON 對象
[
{
"empid":"1",
"name":"vito",
"familys":[
{"id":"001","name":"family1"},
{"id":"002","name":"family2"},
{"id":"003","name":"family3"}
]
},
{
"empid":"2",
"name":"shao",
"familys":[{"id":"004","name":"family4"}]
}
]

# JSON 序列化和還原序列化

檢視 JSON 資料

[Online JSON Viewer](http://jsonviewer.stack.hu/) 是一個線上工具，可用來檢視 JSON 資料。

建立 JSON 資料的物件類別

[json2csharp](http://json2csharp.com/) 是一個線上工具，可幫你自動產生類別程式碼。

PS.這個功能在 Visual Studio 2012 Ultimate 已內建類似功能

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgmjAOrOK4kZU0Ze-25s19Dwe-2kSurOJhZSgXw2VcwuVhxP5KJke_-VAG7VjvQuxHb1bMpXYJn12VnseZLVWaoPuPu03CDaEWLTqq66H78s6Nbb5EVH8v5pc35RtBPoHaV3uhPvDNE0ao/s541/gen-json-class.png)

序列化類別

在 .NET Framework 中，用來序列化 JSON 資料的類別，除了內建的 [JavaScriptSerializer](http://msdn.microsoft.com/zh-tw/library/system.web.script.serialization.javascriptserializer.aspx) 外，還有 [DataContractJsonSerializer](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.json.datacontractjsonserializer.aspx) 類別。  此外，還有免費的 [Json.NET](http://json.codeplex.com/) 類別可以使用。

# 讀取 JSON

# 寫入 JSON