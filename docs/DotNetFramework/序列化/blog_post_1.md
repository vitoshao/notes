---
title: 自訂序列化
layout: default
parent: 序列化
nav_order: 1
description: "自訂序列化"
date: 2012-01-26
tags: [DotNetFramework,序列化]
postid: "2572671501362446623"
---
當我們使用 [BinaryFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter) 或 [XmlSerializer](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlserializer) 去序列化一個類別時，系統會依設定幫我們直接產生序列化後的資訊。  但是，如果遇到版本的問題，或者某些類別不支援序列化時，導至還原序列化後的資訊不符合需求，  這時候就是使用自訂序列化的時候，也就是覆寫.NET Framework內建的序列化程式，並自行撰寫程式碼以控制序列化後的資訊。  使用自訂序列化，可以在不破壞該類別的前提下，達到上述問題的解決。  

# 如何實作自訂序列化

若要自訂序列化，就要實作 [ISerializable](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.iserializable) 介面，並且將 [Serializable](http://msdn.microsoft.com/zh-tw/library/bcfsa90a) 屬性套用到該類別即可。  

####   要實作 [ISerializable](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.iserializable) 介面，就必須實作以下二件事：  

1. 實作 [GetObjectData](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.iserializable.getobjectdata) 方法，在這方法中填入序列化所需的資訊到 [SerializationInfo](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.serializationinfo.aspx) 物件之中。
2. 建立用來還原序列化時的特殊建構函式。

這個方法和建構函式的參數都是 [SerializationInfo](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.serializationinfo) 和 [StreamingContext](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.streamingcontext) 型別。  
在 [GetObjectData](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.iserializable.getobjectdata) 方法之中，主要必須撰寫的程式碼，就是使用 [AddValue](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.serializationinfo.addvalue) 方法，將想要序列化的變數，以 name/value 方式加入到 [SerializationInfo](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.serializationinfo) 物件之中。  
使 [SerializationInfo](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.serializationinfo) 這個物件具有足夠的序列化資訊，以便在還原序列化過程中用來重建該物件。  

當執行階段呼叫你的還原序列化建構函式時，它會使用在序列化過程中使用的變數名稱從 [SerializationInfo](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.serializationinfo) 取該變數的值。  
```c#
//宣告成可序列化
[Serializable]
public class Grade : ISerializable
{
public string name;
public int math;
public int english;
[OptionalField] public int chinese; 
[NonSerialized] public int total;  

// The standard, non-serialization constructor
public Grade(string _name, int _math, int _english, int _chinese)
{
name = _name;
math = _math;
english = _english;
chinese = _chinese;
total = _math + _english + _chinese;
}

// 具有 SerializationInfo 和 StreamingContext 型別參數的特殊 constructor
// 這個 constructor 在還原序列化時會使用到

protected Grade(SerializationInfo info, StreamingContext context)
{
name = info.GetString("Name");
math = info.GetInt32("MathGrade");
english = info.GetInt32("EnglishGrade");
chinese = info.GetInt32("ChineseGrade");
total = math + english + chinese;
}

// 實做 GetObjectData 方法，此方法會在叫用 Serialize 方法時自動被呼叫
// GetObjectData 的參數同 constructor

[SecurityPermissionAttribute(SecurityAction.Demand,SerializationFormatter = true)]
public void GetObjectData(SerializationInfo info, StreamingContext context)
{
info.AddValue("Name", name);
info.AddValue("MathGrade", english);
info.AddValue("EnglishGrade", chinese);
info.AddValue("ChineseGrade", total);
}

[OnSerializing]     //截取 OnSerializing
void CalculateTotal(StreamingContext sc)
{
total = math + english + chinese;
}

[OnDeserialized]    //截取 OnDeserialized
void CheckTotal(StreamingContext sc)
{
if (total == 0) { CalculateTotal(sc); }
}
}
```

執行自訂序列化與自訂還原序列化。  
```c#
//Custom Serialize (Using SoapFormatter)
private void btnSerialize_Click(object sender, EventArgs e)
{
FileStream filestream = new FileStream("Custom_Class.SOAP", FileMode.Create);     //建立FileStream
SoapFormatter formatter = new SoapFormatter();                                    //建立BinaryFormatter

Grade grade = new Grade("vito", 80, 90, 30);

formatter.Serialize(filestream, grade);       //透過 SoapFormatter 序列化物件
filestream.Close();
}

//Custom Deserialize (Using SoapFormatter)
private void btnDeserialize_Click(object sender, EventArgs e)
{
FileStream filestream = new FileStream("Custom_Class.SOAP", FileMode.Open);
SoapFormatter formatter = new SoapFormatter();                    //建立SoapFormatter

Grade grade = (Grade)formatter.Deserialize(filestream);
filestream.Close();
}
```

# 回應序列化事件

序列化或還原序列化過程中，會引發下列四種事件。只要將這些屬性套用到回應的方法上，即可在過程中變更物件資訊。

- OnSerializing
- OnSerialized
- OnDeserializing
- OnDeserialized

回應這些事件的方法，必須符合下列要求：

- 在方法上，套用上面想要截取的事件屬性。
- 可接受一個 [StreamingContext](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.streamingcontext) 物件為參數。
- 回傳 void

# XmlDocument
```c#
XmlDocument doc = new XmlDocument();
doc.Load("Log.xml");

//取得文件的根節點

XmlNode root = doc.DocumentElement;

//建立新的節點

XmlElement elem = doc.CreateElement("Log");
elem.SetAttribute("User", "vio");               //設定節點 屬性
elem.InnerText = DateTime.Now.ToString();       //設定節點 InnerText

//附加節點
root.AppendChild(elem); 

//儲存文件
doc.Save("Log.xml");
```
```xml
<?xml version="1.0" encoding="utf-8"?>
<Logs>
<Log User="aaa">2012/2/13 上午 10:08:00</Log>
<Log User="bbb">2012/2/14 上午 11:08:00</Log>
<Log User="vio">2012/9/4 上午 10:32:36</Log>
</Logs>
```