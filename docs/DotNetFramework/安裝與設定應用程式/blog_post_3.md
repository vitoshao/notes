---
title: 組態管理
layout: default
parent: 安裝與設定應用程式
nav_order: 3
description: "組態管理"
date: 2012-02-08
tags: [DotNetFramework,安裝與設定應用程式]
postid: "5867160596753692047"
---
在組態檔中，通常我們將通用設定存放在 [AppSettings](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.appsettings.aspx) 區段、 資料庫連線設定存放在[ConnectionStrings](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.connectionstrings.aspx) 區段。  然後將應用程式設定存放在 userSettings 或 applicationSettings 區段。如果某個區段中的設定項目很多，那麼該區段內的資料就容易變的雜亂。  這時候就可以使用**自訂區段**，將設定值資料分區段存放，避免將所有設定值都儲存在同一區段底下，在管理維護上會比較明確。  

# 使用 IConfigurationSectionHandler 介面，建立自訂區段

## 如何在組態檔中自訂一個區段：

- 首先在組態檔的 **&lt;configSections&gt;** 區段，添加一個自訂的 **&lt;section&gt;** 項目，並為其**命名**及**指定型別**。
- 此時必須注意 Section 項目的型別 (type) 屬性設定，在逗號前為物件名稱，在逗號後面為組件名稱。
- 依 **&lt;section&gt;** 項目的名稱建立一個自訂區段，並在區段內，建立自訂區段的內容
```xml
<configSections>
<!--宣告一個自訂區段-->
<section name="MyCustomSection1" type="PracticeMCTS.Chapter09.MySettings1Section, PracticeMCTS" />
</configSections>

<!--設定自訂區段的內容-->
<MyCustomSection1>
<FirstName>Peter</FirstName>
<LastName>Wang</LastName>
<Age>28</Age>
</MyCustomSection1>
```

## 如何撰寫存取自訂區段的類別

[IConfigurationSectionHandler](http://msdn.microsoft.com/zh-tw/library/system.configuration.iconfigurationsectionhandler) 這個介面是用來處理特定組態區段的存取，  我們只要實作它的 [Create](http://msdn.microsoft.com/zh-tw/library/system.configuration.iconfigurationsectionhandler.create) 方法，就可以快速建立自訂區段的存取物件。  

[Create](http://msdn.microsoft.com/zh-tw/library/system.configuration.iconfigurationsectionhandler.create) 方法中的第三個參數，會傳進來一個 XmlNode 型態的節點資料，它代表的就是該自訂區段的資料。  
```c#
public class MySettings
{
public string FirstName;
public string LastName;
public int Age;

public MySettings()
{
}
}

class MySettings1Section : IConfigurationSectionHandler
{
public object Create(object parent, object configContext, System.Xml.XmlNode section)
{
MySettings1 settings = new MySettings1();
settings.FirstName = section.SelectSingleNode("FirstName").InnerText;
settings.LastName = section.SelectSingleNode("LastName").InnerText;
settings.Age = int.Parse(section.SelectSingleNode("Age").InnerText);
return settings;
}
}
```

## 如何存取自訂區段

要讀取自訂組態區段，可以叫用 [GetSection](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.getsection.aspx) 方法截取指定的區段。
```c#
private void button1_Click(object sender, EventArgs e)
{
//將 GetSection 傳回的值，轉型成預期的組態型別
MySettings1 mySetting = (MySettings1)ConfigurationManager.GetSection("MyCustomSection1");
Console.WriteLine(mySetting.FirstName);
Console.WriteLine(mySetting.LastName);
Console.WriteLine(mySetting.Age.ToString());
//Peter  //Wang  //28
}
```

PS.  [IConfigurationSectionHandler](http://msdn.microsoft.com/zh-tw/library/system.configuration.iconfigurationsectionhandler.aspx) 在 .NET Framework 2.0 (含) 以上的版本中己被取代。  可改用實作 [ConfigurationSection](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationsection) 的衍生類別以處理組態區段處理常式。

# 使用 ConfigurationSection 類別，建立自訂區段

另一種要讀取自訂組態區段的方法，是自訂一個繼承自 [ConfigurationSection](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationsection) 的衍生類別。  

[ConfigurationSection](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationsection.aspx) 類別是代表組態檔中的區段，我們可以繼承它來擴充我們須要的功能。  使用這個方法好處是**可以透過屬性 (attributes) 來設定 default value 、 validators 或其他必須的屬性**。這也是目前MSDN比較推薦的方法。  其步驟大至如下：  

1. 在組態檔的 &lt;configSections&gt; 區段，添加一個自訂的 &lt;Section&gt; 項目，並為其【命名】及【指定型別】。
2. 依 Section 項目的名稱，建立項目的內容。
3. 實作 MyCustomSection 類別，( 繼承自 [ConfigurationSection](http://msdn.microsoft.com/zh-tw/library/x0kca287.aspx) 類別)。
```xml
<configSections>
<section name="MyCustomSection2" type="PracticeMCTS.Chapter09.MySettings2Section, PracticeMCTS" />
</configSections>

<!--設定自訂區段的內容2-->
<MyCustomSection2 FirstName="Tony" LastName="Chen" Age="30"  />
```
```c#
public class MySettings2Section : ConfigurationSection
{
[ConfigurationProperty("FirstName", DefaultValue = "Peter", IsRequired = true)]
[StringValidator(InvalidCharacters ="~!@#$%^&\*()[]{}/;'\"|\\",MinLength = 1, MaxLength = 60)]
public string FirstName
{
get{return (string)this["FirstName"];}
set{this["FirstName"] = value;}
}

[ConfigurationProperty("LastName", DefaultValue = "Huang")]
public string LastName
{
get{return (string)this["LastName"];}
set{this["LastName"] = value;}
}

[ConfigurationProperty("Age", DefaultValue = "0")]
public int Age
{
get{return (int)this["Age"];}
set{this["Age"] = value;}
}
}
```
```c#
private void button2_Click(object sender, EventArgs e)
{
//將 GetSection 傳回的值，轉型成預期的組態型別
MySettings2Section Section2 = (MySettings2Section)ConfigurationManager.GetSection("MyCustomSection2");
Console.WriteLine(Section2.FirstName);
Console.WriteLine(Section2.LastName);
Console.WriteLine(Section2.Age.ToString());
//Tony  //Chen  //30
}
```

# 如何在自訂區段中使用項目集合

在組態檔中，&lt;appSettings&gt; 與 &lt;connectionStrings&gt; 區段中，我們都可以看到 &lt;add&gt; 屬性，它將好幾個項目加在同一個區段裡，那麼，在自訂區段裡，是否也可以如此使用呢？  

當然是可以的，不過，  首先要瞭解的是，這種架構稱為**組態項目集合 (Configuration Element Collection)**。  **每一個 &lt;add&gt; 即一個項目**，以 [ConfigurationElement](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationelement.aspx) 表示。  所有項目合稱項目集合，以 [ConfigurationElementCollection](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationelementcollection.aspx) 表示。  而整個 Section ，則是由這些項目集合組成，以 [ConfigurationSection](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationsection.aspx) 表示。  

#### 組態設定內容：
```xml
<configSections>
<section name="MyCustomSection3" type="PracticeMCTS.Chapter09.MyCustomSection3, PracticeMCTS" />
</configSections>

<!--設定自訂區段的內容3-->
<MyCustomSection3>                                            <!-- <== ConfigurationSection                   -->
<Employees>                                                  <!-- <== ConfigurationElementCollection         -->
<add FirstName="Tony" LastName="Chen" Age="30" />          <!-- <== ConfigurationElement                   -->
<add FirstName="Peter" LastName="Wang" Age="20" />         <!-- <== ConfigurationElement                   -->
<add FirstName="Allen" LastName="Huang" Age="40" />        <!-- <== ConfigurationElement                   -->
</Employees>
</MyCustomSection3>
```

#### 自訂區段類別：
```c#
//1.實作ConfigurationElement
public class MySettings3 : ConfigurationElement
{
[ConfigurationProperty("FirstName")]
public string FirstName
{
get{return (string)this["FirstName"];}
set{this["FirstName"] = value;}
}

[ConfigurationProperty("LastName")]
public string LastName
{
get{return (string)this["LastName"];}
set{this["LastName"] = value;}
}

[ConfigurationProperty("Age")]
public int Age
{
get{return (int)this["Age"];}
set{this["Age"] = value;}
}
}

//2.實作ConfigurationElementCollection
public class MySettings3Collection : ConfigurationElementCollection
{
public MySettings3Collection() { }

public override ConfigurationElementCollectionType CollectionType
{
get
{
return ConfigurationElementCollectionType.AddRemoveClearMap;
}
}

public MySettings3 this[int index]
{
get { return (MySettings3)BaseGet(index); }
set
{
if (BaseGet(index) != null)
{
BaseRemoveAt(index);
BaseAdd(index, value);
}
}
}

public MySettings3 this[string key]
{
get { return (MySettings3)BaseGet(key); }
set
{
if (BaseGet(key) != null)
{   
BaseRemove(key);
BaseAdd(value);
}
}
}

protected override ConfigurationElement CreateNewElement()
{
return new MySettings3();
}

protected override Object GetElementKey(ConfigurationElement element)
{
return ((MySettings3)element).FirstName;
}

protected override void BaseAdd(ConfigurationElement element)
{
BaseAdd(element, false);
}
}

//3.實作ConfigurationSection
public class MySettings3Section : ConfigurationSection
{
[ConfigurationProperty("Employees", IsRequired = false, IsDefaultCollection = true)]  
public MySettings3Collection MySettings3
{
get { return (MySettings3Collection)this["Employees"]; }
}
}
```

#### 存取自訂區段：
```c#
private void button6_Click(object sender, EventArgs e)
{
MySettings3Section Section3 = (MySettings3Section)ConfigurationManager.GetSection("MyCustomSection3");

foreach (MySettings3 dataitem in Section3.MySettings3)
{
Console.WriteLine(dataitem.FirstName);
Console.WriteLine(dataitem.LastName);
Console.WriteLine(dataitem.Age.ToString());
}
//Tony Chen 30
//Peter Wang 20
//Allen Huang 40
}
```

# 如何分組自訂區段

...
## 參考資料  
![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [HOW TO：使用](http://msdn.microsoft.com/zh-tw/library/2tw134k3)[ConfigurationSection](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationsection.aspx) 建立自訂組態區段
- 
-