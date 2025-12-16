---
title: Json 序列化
layout: default
parent: 序列化
nav_order: 4
description: "Json 序列化"
date: 2013-01-25
tags: [DotNetFramework,序列化]
postid: "5026206195478422452"
---
[JavaScriptSerializer](http://msdn.microsoft.com/zh-tw/library/system.web.script.serialization.javascriptserializer.aspx) 是 .Net3.5 才加進來的類別，  可以用來將指定的 JSON 字串轉換成物件圖形。  
```c#
public string Serialize(object obj);           
public object Deserialize(string input, Type targetType);
public T Deserialize<T>(string input);
public object DeserializeObject(string input);
```

- [Serialize](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter.serialize.aspx) ：將物件或連接之物件的圖形序列化至指定資料流。
- [Deserialize](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter.deserialize.aspx) ：將資料流還原序列化至物件圖形。
- [DeserializeObject](http://msdn.microsoft.com/zh-tw/library/system.web.script.serialization.javascriptserializer.deserializeobject.aspx) ：將指定的 JSON 字串轉換成物件圖形。

## Json 的序列化和還原序列化
```c#
private void btnJavaScriptSerializer_Click(object sender, EventArgs e)
{
//initial data
var employee = new Employee
{
ID = 1,
Name = "vito",
Birthday = new DateTime(1977, 7, 7),
Departmnet = new Department
{
ID = 2,
Name = "IT Dept"
}
};

//Serialize an entity to Json

JavaScriptSerializer jSer = new JavaScriptSerializer();
string data = jSer.Serialize(employee);

//Deserialize Json string to entity

var emp = jSer.Deserialize<Employee>(data);

Console.WriteLine("ID={0}", emp.ID);
Console.WriteLine("Name={0}", emp.Name);
Console.WriteLine("Birthday={0}", emp.Birthday.ToString());
Console.WriteLine("DeptID={0}", emp.Departmnet.ID);
Console.WriteLine("DeptName={0}", emp.Departmnet.Name);

//ID=1
//Name=vito
//Birthday=1977/7/6 下午 04:00:00
//DeptID=2
//DeptName=IT Dept
}
```
```c#
public class Employee
{
public int ID { get; set; }
public string Name { get; set; }
public DateTime Birthday { get; set; }
public Department Departmnet { get; set; }
}
public class Department
{
public int ID { get; set; }
public string Name { get; set; }
}
```

底下是序列化後的資料內容
{"ID":1,"Name":"vito","Birthday":"/Date(237052800000)/","Departmnet":{"ID":2,"Name":"IT Dept"}}    
註１：

JSON 格式的第一層不可以是陣列，否則無法還原序列化。
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
]  {
"emps":
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
"familys":[
{"id":"004","name":"family4"}
]
}
]
}  
註２：

在 .NET Framework 中，用來序列化 JSON 資料的類別，除了內建的 [JavaScriptSerializer](http://msdn.microsoft.com/zh-tw/library/system.web.script.serialization.javascriptserializer.aspx) 外，還有 [DataContractJsonSerializer](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.json.datacontractjsonserializer.aspx) 類別。  此外，還有免費的 [Json.NET](http://json.codeplex.com/) 類別可以使用。

## 將 Json 轉成 Dictionary 
```c#
private void button2_Click(object sender, EventArgs e)
{
string data = "{\"ID\":1,\"Name\":\"vito\",\"Birthday\":\"1977/7/7\",\"Departmnet\":{\"ID\":2,\"Name\":\"IT Dept\"}}";

JavaScriptSerializer jSer = new JavaScriptSerializer();
var obj = jSer.DeserializeObject(data);

Dictionary<string, object> emp = jSer.DeserializeObject(data) as Dictionary<string, object>;
Dictionary<string, object> dep = emp["Departmnet"] as Dictionary<string, object>;

Console.WriteLine("ID={0}", emp["ID"].ToString());
Console.WriteLine("Name={0}", emp["Name"].ToString());
Console.WriteLine("Birthday={0}", emp["Birthday"].ToString());
Console.WriteLine("DeptID={0}", dep["ID"].ToString());
Console.WriteLine("DeptName={0}", dep["Name"].ToString());

//ID=1
//Name=vito
//Birthday=1977/7/7
//DeptID=2
//DeptName=IT Dept
}
```
## 參考資料  

- [C# 的 Json 序列化和反序列化(JSON.NET)](http://www.allenj.net/archives/2645)
- 
-