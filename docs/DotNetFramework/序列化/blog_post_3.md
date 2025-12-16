---
title: 序列化物件
layout: default
parent: 序列化
nav_order: 3
description: "序列化物件"
date: 2012-01-26
tags: [DotNetFramework,序列化]
postid: "2347391287090227270"
---
# 什麼是序列化 (Serialization)

當宣告一個字串或數值變數並給定一些資料後，若須要將這些資料儲存起來，我想這沒多大問題，甚至自訂一個格式，日後還可以把它讀入相同型別的變數。  不過，如果這個變數是一個自訂的類別，例如 Employee ，或者一個 .NET 參考型別，如 Font、Hashtable、Datatable 類別等等，那你又會如何儲存呢？  這時，我們需要將物件資料轉成某個特定格式，這樣子才有辨法儲存至檔案、或者傳送給另一個程式、或者透過網路傳輸。  這樣子的一個轉換過程，就稱為 **[序列化 (Serialization)](http://msdn.microsoft.com/zh-tw/library/7ay27kt9)** 。  

序列化可分成二個階段：  

- **序列化 (Serializing)** : 將一個 object 轉換成線性的 byte[] 資料，以方便資料的儲存與傳輸。
- **還原序列化 (Deserializing)** : 將先前序列化的資料轉換成 object。

一個序列化後的物件可以被儲存下來，等到要用的時候，只須要呼叫還原序列化的方法，這個物件就可以被完整的重建。  同樣的，將一個物件序列化成資料流後，就可以透過網路傳送給遠端的另一個程式，然後再由遠端的另一個程式進行還原序列化。  

.Net 的序列化是實作在 [System.Runtime.Serialization](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization) 命名空間裡，下表是三種系統提供的序列化方法，另外也可以自訂序列化方法。

1. [BinaryFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter)：
- 以二進位格式序列化和還原序列化物件。
- 這個序列化物件最有效率的方法。
- 它所序列化的資訊只能讓 .NET Framework 為主的應用程式讀取。
- 命名空間 [System.Runtime.Serialization.Formatters.Binary](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary) 。
2. [SoapFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.soap.soapformatter)：以 SOAP 格式序列化和還原序列化物件，或連接之物件的整個圖形。
- 以 XML 格式為主的序列化物件。
- 以這個方法序列化的物件是最可靠的，可以透過網路傳輸，也可以讓非 .NET 的應用程式讀取。
- [SoapFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.soap.soapformatter) 比 [BinaryFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter) 更可能成功地通過防火牆。
- 命名空間 [System.Runtime.Serialization.Formatters.Soap](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.soap) 。
3. [XmlSerializer](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization.xmlserializer)：
- 將物件序列化成為 XML 文件，以及從 XML 文件將物件還原序列化。
- 是一種公開的標準，以文字為主的格式，和各種平台相容性最高。
- 只能序列化公用(public)的屬性與欄位，不能序列化私用資料 (private)。
- 只能用於序列化物件，不能序列化物件圖形([object graph](http://stackoverflow.com/questions/877157/what-is-an-object-graph-and-how-do-i-serialize-one))。
- 命名空間 [System.Xml.Serialization](http://msdn.microsoft.com/zh-tw/library/system.xml.serialization) 。

#### 選擇何種序列化

若確認所有使用已序列化資料的用戶端都是.NET Framework 應用程式時，才應該選擇 BinaryFormatter。例如同一支程式的寫入與讀取，使用 [BinaryFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter) 就是最好的選擇。  若是會尤其他應用程式讀取你的序列化資料，或是該資料可能透過網路傳輸時，則最好使用 [SoapFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.soap.soapformatter) 。  另外要注意的是，使用 [SoapFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.soap.soapformatter) 產生的資訊，空間會是 [BinaryFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter) 的３～４倍。

# 如何使用 BinaryFormatter

####  [BinaryFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter.aspx) 建構子：
```c#
public BinaryFormatter();
public BinaryFormatter(ISurrogateSelector selector, StreamingContext context);
```

####  [Serialize](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter.serialize.aspx) and [Deserialize](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter.deserialize.aspx) 方法：
```c#
public void Serialize(Stream serializationStream, object graph);
public void Serialize(Stream serializationStream, object graph, Header[] headers);

public object Deserialize(Stream serializationStream);
public object Deserialize(Stream serializationStream, HeaderHandler handler);
```

#### 序列化的步驟：

- 1. create a Stream object to hold the serialized output..
- 2. create a [BinaryFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter) object
- 3. call the [BinaryFormatter.Serialize](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter.serialize) method
```c#
string data = "Hello World.";

FileStream fs = new FileStream("String.BIN", FileMode.Create);      // 建立 FileStream ,用以儲存序列化後的資料
BinaryFormatter bFormat = new BinaryFormatter();                    // 建立 BinaryFormatter 物件
bFormat.Serialize(fs, data);                                        // Serialize ：序列化物件

fs.Close();
```
```c#
DateTime data = System.DateTime.Now;

FileStream fs = new FileStream("Date.BIN", FileMode.Create);
BinaryFormatter bFormat = new BinaryFormatter(); 
bFormat.Serialize(fs, System.DateTime.Now);

fs.Close();
```

#### 還原序列化的步驟：

- 1. Create a stream object to read the serialized output.
- 2. create a [BinaryFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter) object
- 3. call the [BinaryFormatter.Deserialize](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter.deserialize) method to deserialize the object, and cast it to the correct type.
```c#
FileStream fs = new FileStream("String.BIN", FileMode.Open);    // 建立 FileStream 開啟序列化資料
BinaryFormatter bFormat = new BinaryFormatter();                // 建立 BinaryFormatter 物件
string data = (string) bFormat.Deserialize(fs);                // Deserialize ：反序列化資料流中的資料，並轉成正確型別
fs.Close();     
Console.WriteLine(data);
```

# 如何使用 SoapFormatter

在使用 [SoapFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.soap.soapformatter) 之前，必需先手動在專案中加入 System.Runtime.Serialization.Formatters.Soap.dll 這個組件的參考 (BinaryFormatte 預設已加入)。  接著其使用方法和 [BinaryFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter) 都是一樣的，只是換成 [SoapFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.soap.soapformatter) 類別即可。  SoapFormatter 執行序列化的效率較低，但比較具有相互運作性，它是 [BinaryFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter) 類別的替代方案。  若需要可攜性，請使用 [SoapFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.soap.soapformatter) ，若需要最大的效能，則使用 [BinaryFormatter](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.formatters.binary.binaryformatter) 。  
```c#
NameValueCollection myDic = new NameValueCollection();

myDic.Add("apple", "a fruit ");
myDic.Add("book", "a long written or printed literary composition ");
myDic.Add("cat", "an animal ");

FileStream fs = new FileStream("NameValueCollection.SOAP", FileMode.Create);
SoapFormatter sFormat = new SoapFormatter();
sFormat.Serialize(fs, myDic);

fs.Close();
```
```c#
FileStream fs = new FileStream("NameValueCollection.SOAP", FileMode.Open);
SoapFormatter sFormat = new SoapFormatter();
NameValueCollection myDic = (NameValueCollection)sFormat.Deserialize(fs);
fs.Close();

foreach (string key in myDic) 
{
Console.WriteLine("{0} : {1}", key, myDic[key]); 
}
//apple : a fruit 
//book : a long written or printed literary composition 
//cat : an animal 
```

# 如何建立可被序列化的類別

以下是使用序列化時要注意的事項：

- 1. 一個類別，若要可被序列化及還原序列化，該類別需加上 **Serialzable** 屬性。
- 2. 若沒有其他特別的標示，該類別的所有成員都會被序列化。
- 3. 若有不需被序列化的成員，可對成員加上 NonSerialized 屬性。例如暫存或計算而得的成員，這類值沒有序列化的必要。
- 4. 還原序列化時，若要自動讓程式對 NonSerialized 的成員進行初始化，可使用 [IDeserializationCallback](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.ideserializationcallback) 介面，並實作 [OnDeserialization](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.ideserializationcallback.ondeserialization) 方法。
- 5. 若類別因版本上有所的差異，在還原序列化時，會產生例外狀況。

如下面例子中的 Grade 自訂類別，若第一版只有 math, english 二個成員，但是第二版加了 chinese 這個新成員，若直接針對前版的序列化資料執行還原序列化，會產生例外狀況。有二個方法可以避免這個限制：

- 自訂序列化(custom serialization)，讓它可以匯入舊版的序列化物件。
- 套用 OptionalField 屬性，以解決相容性問題。
```c#
[Serializable] //宣告成可序列化
public class Grade : IDeserializationCallback //--> 這個介面主要功能在當執行完 Deserialization 之後，通知類別
{
public string name;
public int math;
private int english;
[OptionalField] public int chinese;     //[ OptionalField ]-->因序列化的資料是舊版格式，沒有這個欄位，設成[OptionalField]，提供版本的相容性。
[NonSerialized] public int total;       //[ NonSerialized ]-->定義這個項目不要序列化。所以，在還原序列化時這個欄位也不會被還原。常用放暫時性欄位。

public Grade(string _name, int _math, int _english, int _chinese)
{
name = _name;
math = _math;
english = _english;
chinese = _chinese;
total = _math + _english + _chinese;
}

public string result()
{
if ((total) > 180)
return "pass";
else
return "fail";
}

//實作 IDeserializationCallback.OnDeserialization ，這樣子當 Deserialization 之後，會自動執行這段程式碼
public void OnDeserialization(object sender)
{
//因為chinese為[OptionalField]屬性，屬舊版沒有的欄位，若有必要，可以在此做初始化設定
if (chinese == 0)
chinese = 60;

//因為total為加總欄位，沒有必要被序列化，所以當初被設定為 [NonSerialized]，
//但是還原後，這個欄位是有意義的，所以可以在此重新計算
total = math + english + chinese;
}
}
```
```c#
Grade grade = new Grade("vito", 80, 90, 30);

FileStream fs = new FileStream("Class.Soap", FileMode.Create);
SoapFormatter sFormat = new SoapFormatter();
sFormat.Serialize(fs, grade);
fs.Close();
```
```c#
FileStream fs = new FileStream("Class.Soap", FileMode.Open);
SoapFormatter bFormat = new SoapFormatter();
Grade grade = (Grade)bFormat.Deserialize(fs);
fs.Close();
Console.WriteLine(grade.result());  //還原序列化後,試試呼叫 result 這個方法
```
## 參考資料  
![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [序列化](http://msdn.microsoft.com/zh-tw/library/7ay27kt9.aspx)
- [二進位序列化](http://msdn.microsoft.com/zh-tw/library/72hyey7b.aspx)
- [XML 和 SOAP 序列化](http://msdn.microsoft.com/zh-tw/library/90c86ass.aspx)
- [序列化物件](http://msdn.microsoft.com/zh-tw/library/szzyf24s)
- [還原序列化物件](http://msdn.microsoft.com/zh-tw/library/fa420a9y)