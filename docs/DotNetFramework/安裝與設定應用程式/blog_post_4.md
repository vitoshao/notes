---
title: 組態設定
layout: default
parent: 安裝與設定應用程式
nav_order: 4
description: "組態設定"
date: 2012-02-08
tags: [DotNetFramework,安裝與設定應用程式]
postid: "6328417441574962732"
---
# 組態設定

## 認識組態檔

系統開發時，一定會使用組態檔，來記錄一些可能會變動的資訊，以避免因 hard-code 所造成的不方便。  .NET底下的 [System.Configuration](http://msdn.microsoft.com/zh-tw/library/2a1tyt9s.aspx) 命名空間所提供的類別，就是用來管理組態設定用的，讓開發者可以更輕鬆的存取這些資訊。  

底下是開發系統常會使用到的組態檔

- 應用程式組態檔( XXXX.exe.config )：這個檔一般與執行檔放在同一層目錄。
- 電腦組態檔( Machine.config )：包含套用於整個電腦的設定值。這個檔案位於 %system32%\Microsoft.NET\Framework\[版本號]\Config 目錄。
- Web 組態檔( Web.config )：這個檔是使用在網站專案的組態檔。

組態檔是一個 XML 文件，其中包含了許多的項目，詳情結構可參考[.NET Framework 的組態檔結構描述](http://msdn.microsoft.com/zh-tw/library/1fk1t1t0.aspx)。  雖然這個 XML 文件也可以透過 XmlSerializer 以取得資訊內容，不過，  針對組態檔，直接使用 [System.Configuration](http://msdn.microsoft.com/zh-tw/library/2a1tyt9s.aspx) 命名空間所提供的 [Configuration](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.aspx) 、 [ConfigurationManager](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.aspx) 類別，就可以快速方便讀取組態檔中的設定值。

## Configuration 的屬性和方法

[Configuration](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.aspx) 這個類別是用來記載描述電腦中特定的組態檔類別

#### 屬性

- [AppSettings](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.appsettings.aspx) ：取得 &lt;appSettings&gt; 區段的組態資料。
- [ConnectionStrings](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.connectionstrings) ：取得 &lt;connectionStrings&gt; 區段的組態資料。
- [FilePath](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.filepath) ：取得組態檔的實體路徑。
- [Sections](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.sections) ：取得這個 [Configuration](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.aspx) 物件所定義的區段集合。
- [SectionGroups](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.sectiongroups) ：取得這個組態所定義的區段群組集合。
- [RootSectionGroup](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.rootsectiongroup) ：取得這個 [Configuration](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.aspx) 物件的根 ConfigurationSectionGroup。

#### 方法

- [GetSection](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.getsection) ：傳回指定的 [ConfigurationSection](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationsection.aspx) 物件。
- [GetSectionGroup](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.getsectiongroup) ：取得指定的 ConfigurationSectionGroup 物件。
- [Save](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.save.aspx) ：將包含在這個 [Configuration](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.aspx) 物件中的組態設定寫入至目前的 XML 組態檔。
- [SaveAs](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.saveas.aspx) ：將包含在這個 [Configuration](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.aspx) 物件中的組態設定寫入至指定的 XML 組態檔。

## ConfigurationManager 的屬性和方法

這個類別是用來管理操作組態檔的類別

#### 屬性

- [AppSettings](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.appsettings) ：對應到組態檔的 &lt;appSettings&gt; 區段。
- [ConnectionStrings](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.connectionstrings) ：對應到組態檔的 &lt;connectionStrings&gt; 區段。

#### 方法

- [GetSection](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.getsection) ：取得組態區段
- [OpenExeConfiguration](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.openexeconfiguration) ：開啟應用程式組態檔
- [OpenMachineConfiguration](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.openmachineconfiguration.aspx) ：開啟電腦組態檔
- [OpenMappedExeConfiguration](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.openmappedexeconfiguration) ：開啟指定的應用程式組態檔
- [OpenMappedMachineConfiguration](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.openmappedmachineconfiguration) ：開啟指定的電腦組態檔
- [RefreshSection](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.refreshsection.aspx) ：重新整理具名區段，以便下次擷取它時，會從磁碟重新讀取它。

## 如何開啟組態檔

#### 加入 [System.Configuration](http://msdn.microsoft.com/zh-tw/library/system.configuration.aspx) 參考

因為 Visual Studio 的預設參考不包含 [System.Configuration](http://msdn.microsoft.com/zh-tw/library/system.configuration.aspx) 組件，所以使用這個類別之前，必須先手動加入參考，才可以引用的到 [System.Configuration](http://msdn.microsoft.com/zh-tw/library/system.configuration) 命名空間。
```c#
//開啟應用程式組態檔     //同 XXXX.exe 目錄
Configuration config1 = ConfigurationManager.OpenExeConfiguration(ConfigurationUserLevel.None);

//開啟特定的應用程式組態檔
string ExeFileName = @"C:\App1.config";
Debug.Assert(File.Exists(ExeFileName),"file is miss");   //判斷檔案是否存在

ExeConfigurationFileMap MyMap = new ExeConfigurationFileMap();
MyMap.ExeConfigFilename = ExeFileName;
Configuration config2 = ConfigurationManager.OpenMappedExeConfiguration(MyMap, ConfigurationUserLevel.None);

//開啟電腦組態檔  //%system32%\Microsoft.NET\Framework\[版本號]\Config 目錄。
Configuration config3 = ConfigurationManager.OpenMachineConfiguration();

//開啟特定的電腦組態檔
Configuration config4 = ConfigurationManager.OpenMappedMachineConfiguration(MyMap);
```

# 通用設定

## 什麼是通用設定

前面提過，整個組態檔包含了許多項目，而「通用設定」指的是其中幾項與決定應用程式如何執行有關的組態設定項目。例如：  

1.&lt;supportedRuntime&gt;：若開發專案的.NET版本與執行環境的版本不同，可以在這個區段指定RunTime的版本。  
2.&lt;developmentMode&gt;：在開發階段將某組件設定成共用組件。  
3.&lt;codeBase&gt;：指定某個組件的位置與版本。  
4.&lt;appSettings&gt;：與應用程式設定相關的區塊  
5.&lt;connectionStrings&gt;：與資料庫連線設定相關的區塊  

下面這些例子，示範了上面幾項設定內容：

#### 指定Runtime版本

依 &lt;supportedRun&gt; 中指定的版本啟用 Framework 版本。
```xml
<startup>
<supportedRuntime version="v2.0.50727"/>
</startup>
```

#### 指定使用組件位置：

1.加入一個名為 DEVPATH 的環境變數，並指定組件位置。  
2.將 developmentMode 的值設為 true。  

![](http://127.0.0.1:8080/_images/cs/configulation-devpath.png)
```xml
<runtime>
<developmentMode developerInstallation="true"/>
</runtime>
```

#### 使用 &lt;codeBase&gt; 同時指定組件的位置與版本
```xml
<configuration>
<runtime>
<assemblyBinding xmlns="schemaname">
<dependentAssembly>
<assemblyIdentity name="myprogram" publicKeyToken="xxxxxxxxx" culture="en-us"/>
<assemblyIdentity name="assembly name" publicKeyToken="xxxxxxxxx" culture="en-us"/>
<codeBase version="x.0.0.0" href="http://www.adatum.com/myprogram.dll"/>
</dependentAssembly>
</assemblyBinding>
</runtime>
</configuration>
```

#### 使用 &lt;appSettings&gt;
```xml
<appSettings>
<add key="ConnString" value="Data Source=localhost; Initial Catalog=DBNAME; User ID=sa; Password=Password"/>
<add key="DBAssemblyName" value="DBProvider"/>
<add key="DBClassName" value="SqlDBObject"/>
</appSettings>
```

#### 使用 &lt;connectionStrings&gt;
```xml
<connectionStrings>
<clear/>
<add name="AdventureWorksString" 
providerName="System.Data.SqlClient" 
connectionString="Data Source=localhost; Initial Catalog=DBNAME; User ID=sa; Password=Password; Integrated Security=true"/>
</connectionStrings>
```

上面資訊中，比較特別的是&lt;appSettings&gt;與&lt;connectionStrings&gt;這二個區段：

- 透過 [ConfigurationManager.AppSettings](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.appsettings.aspx) 屬性，可取得 [NameValueCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.namevaluecollection.aspx) 型態的集合物件，它的項目是以 key/value 的方式存放。
- 透過 [ConfigurationManager.ConnectionStrings](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.connectionstrings.aspx) 屬性，可取得 [ConnectionStringSettingsCollection](http://msdn.microsoft.com/zh-tw/library/system.configuration.connectionstringsettingscollection) 型態的集合物件，它的項目是以 name/connectionString 的方式存放。

底下示範如何操作組態檔

## 如何讀取組態檔中 appSettings 區段的項目

透過 [ConfigurationManager.AppSettings](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.appsettings.aspx) 方法就可以取得 [AppSettings](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.appsettings) 區段下的所有設定項目，其回傳值是一個 [NameValueCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.namevaluecollection.aspx) 型別的集合。
```c#
//0) 已過時用法
string ConnString = ConfigurationSettings.AppSettings["ConnString"];

//1) 取得單一個項目
string ConnString = ConfigurationManager.AppSettings["ConnString"];

//2) 取得整個<appSettings>區段
NameValueCollection AllAppSett ings = ConfigurationManager.AppSettings;
ConnString = AllAppSettings["ConnString"];

//3) 列舉<appSettings> 區段
foreach (string key in AllAppSettings) 
Console.WriteLine("Key/Value：{0} = {1}", key, AllAppSettings[key]);  //由 key 取 value
```

## 如何修改組態檔中 appSettings 區段的項目

要修改組態設定值，必須先開啟組態檔，再透過 [Configuration.AppSettings](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.appsettings) 這個屬性回傳 [AppSettingsSection](http://msdn.microsoft.com/zh-tw/library/system.configuration.appsettingssection.aspx) 型別的組態區段資料，再使用其 [Settings](http://msdn.microsoft.com/zh-tw/library/system.configuration.appsettingssection.settings.aspx) 方法，就可新增修改刪除區段中的項目。步驟如下：

- 開啟組態檔。
- 透過 [Configuration.AppSettings.Settings](http://msdn.microsoft.com/zh-tw/library/system.configuration.appsettingssection.settings.aspx) 取得 appSettings 區段的集合物件。
- 新增或修改集合中的資料。
- 透過 [Configuration.Save](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.save.aspx) 方法回存資料到 appSettings 區段。
```c#
//開啟組態檔
Configuration config = ConfigurationManager.OpenExeConfiguration(ConfigurationUserLevel.None);

//取得 AppSettings 區段的組態設定值
KeyValueConfigurationCollection appSettings = config.AppSettings.Settings;

//新增或變更 AppSettings 區段的項目
appSettings["key1"].Value = "value 111";    //修改
appSettings.Add("key2", "value2");          //新增

//儲存到 AppSettings 區段 (要注意使用者的存取權限)
config.Save();   
```

PS.  
當在 Visual Studio 中執行 debug 模式，此時使用的組態檔是 /bin/Debug/XXXX.vshost.exe.config，  
而且，即使執行了 Save() 動作，設定值也是不會變更的。

## 如何讀取組態檔中 connectionStrings 區段的項目

透過 [ConfigurationManager.ConnectionStrings](http://msdn.microsoft.com/zh-tw/library/system.configuration.configurationmanager.connectionstrings.aspx) 可以取得 connectionStrings 區段下的所有設定項目，其回傳值是一個 [ConnectionStringSettingsCollection](http://msdn.microsoft.com/zh-tw/library/system.configuration.connectionstringsettingscollection.aspx) 型別的集合，集合內的元素則是由 [ConnectionStringSettings](http://msdn.microsoft.com/zh-tw/library/system.configuration.connectionstringsettings.aspx) 物件組成。  透過 [ConnectionStringSettings](http://msdn.microsoft.com/zh-tw/library/system.configuration.connectionstringsettings.aspx) 物件就可以取出設定項目中 name 、 connectionString 、 providerName 三個屬性值。  
```c#
//1) 取得單一個項目
ConnectionStringSettings myConn = ConfigurationManager.ConnectionStrings["AdventureWorksString"];

//2) 取得整個<connectionStrings> 區段
ConnectionStringSettingsCollection MyConnections = ConfigurationManager.ConnectionStrings;

// 由集合中取出一個項目
ConnectionStringSettings myConnSetting = MyConnections["AdventureWorksString"];
String ConnString = myConnSetting.ConnectionString;

//3) 列舉<connectionStrings> 區段
if (MyConnections != null)
{
StringBuilder sb = new StringBuilder();
foreach (ConnectionStringSettings ConnSetting in MyConnections)
{
sb.AppendLine("ConnectionString = " + ConnSetting.ConnectionString);
sb.AppendLine("Name             = " + ConnSetting.Name);
sb.AppendLine("ProviderName     = " + ConnSetting.ProviderName);
}
Console.WriteLine(sb.ToString());
}
```

## 如何修改組態檔中 connectionStrings 區段的項目

1. 開啟組態檔。
2. 透過 [Configuration.ConnectionStrings.ConnectionStrings](http://msdn.microsoft.com/zh-tw/library/system.configuration.connectionstringssection.connectionstrings.aspx) 取得 connectionStrings 區段的集合物件。
3. 新增或修改集合中的資料。
4. 透過 [Configuration.Save](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.save.aspx) 方法回存資料到 connectionStrings 區段。
```c#
//開啟組態檔
Configuration config = ConfigurationManager.OpenExeConfiguration(ConfigurationUserLevel.None);

//取得 connectionStrings 區段的組態設定值
ConnectionStringSettingsCollection Connections = config.ConnectionStrings.ConnectionStrings;

//新增或變更項目
Connections["Conn1"].ConnectionString = "Data Source=localhost; Initial Catalog=DBNAME1;...";               //修改
Connections.Add(new ConnectionStringSettings("Conn2", "Data Source=localhost; Initial Catalog=DBNAME;"));   //新增

//儲存
config.Save();
```

## 如何讀取 Web 應用程式的組態檔

如果是 Web 應用程式的話，可改用 [WebConfigurationManager](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.webconfigurationmanager.aspx) 來管理組態資訊，用法幾乎是一樣的：  
```c#
Console.SetOut(Response.Output);  

//1)直接取得<appSettings>底下,Key="ConnString"這個項目的設定值
string ConnString = WebConfigurationManager.AppSettings["ConnString"];

//2)取得整個<appSettings>區段
NameValueCollection AllAppSettings = WebConfigurationManager.AppSettings;

//3)列舉<appSettings>區段
IEnumerator SettingsEnumerator = AllAppSettings.Keys.GetEnumerator();
while (SettingsEnumerator.MoveNext())
{
Response.Write(string.Format("Item: {0}  Value: {1} <BR>", SettingsEnumerator.Current, AllAppSettings[(string)SettingsEnumerator.Current]));
}

//1)直接取得<connectionStrings>底下,Name="AdventureWorksString"這個項目的設定值
ConnectionStringSettings myConn = WebConfigurationManager.ConnectionStrings["AdventureWorksString"];

//2)取得<connectionStrings>區段
ConnectionStringSettingsCollection MyConnections = WebConfigurationManager.ConnectionStrings;

//3)列舉<connectionStrings>區段
if (MyConnections != null)
{
StringBuilder sb = new StringBuilder();
foreach (ConnectionStringSettings ConnSetting in MyConnections)
{
sb.AppendLine("Full Connection String: " + ConnSetting.ConnectionString + "<BR>");
sb.AppendLine("Section Name: " + ConnSetting.Name + "<BR>");
sb.AppendLine("Provider Name: " + ConnSetting.ProviderName + "<BR>");
}
Response.Write(sb.ToString());
}
```

# 應用程式設定

## 什麼是應用程式設定

「應用程式設定」指的是套用到整個應用程式的組態設定項目，例如，專案名稱，公司名稱，Web Service的URL等等的變數。

使用應用程式設定，除了快速方便之外，另一個好處是，這些設定值是支援強型別的。

[ApplicationSettingsBase](http://msdn.microsoft.com/zh-tw/library/system.configuration.applicationsettingsbase) ：這個類別是這個機制的主要核心物件。  透過設定檔設計工具可以方便管理這些設定值，因為系統會自動建立這些設定值的類別，這個類別會就是繼承自 [ApplicationSettingsBase](http://msdn.microsoft.com/zh-tw/library/system.configuration.applicationsettingsbase) 。  若我們要自已手寫一個類別以讀取應用程式設定檔，也只須繼承這個類別即可。  

應用程式設定值有一個屬性，稱為**使用範圍**，可以有２種設定：一是 Application ，另一個是 User 

- **Application**：  
指的是整個應用程式的所有使用者共用的資訊，例如存放專案名稱、公司名稱等。這個區段的設定值是唯讀的。      這個範圍的設定值會存放在 &lt;applicationSettings&gt; 區段內。
- **User**：  
指的是整個各別使用者使用的資訊，例如存放使用者姓名、喜好設定等。這個區段的設定值可供讀寫。      這個範圍的設定值會存放在 &lt;userSettings&gt; 區段內。

## 如何設定預設的應用程式設定檔

在專案的 Properties 目錄底下，預設會有一個設定檔 (Settings.settings)，只要點擊這個檔案，或者在專案的 Property 項目上，按右鍵，選擇開啟，就可以開啟編輯工具，進行維護。操作步驟如下：

開啟設定檔  
![](http://127.0.0.1:8080/_images/cs/setting1.png)

設定屬性名稱、型別、範圍  
![](http://127.0.0.1:8080/_images/cs/setting2.png)

## 如何讀取預設的應用程式設定檔

假設我們在預設的應用程式設定檔中，加入下列項目：

![](http://127.0.0.1:8080/_images/cs/setting4.png)

系統會自動幫我們產生 [Settings](http://msdn.microsoft.com/zh-tw/library/system.configuration.appsettingssection.settings.aspx) 這個類別，透過以下的方法，就可以直接以物件導向的方法取得各個設定值。
```c#
//讀取應用程式設定檔 Settings 這個設定檔,是系統預設的設定檔類別
Console.WriteLine(Settings.Default.Setting1);
Console.WriteLine(Settings.Default.Setting2);
Console.WriteLine(Settings.Default.Setting3);
Console.WriteLine(Settings.Default.Setting4);
//abc123
//abc456
//公司名稱
//系統名稱
```

## 如何新增自建的應用程式設定檔

應用程式設定檔可以多個，也不一定要放在專案的 Property 項目底下。你可以自行新增自訂的應用程式設定檔，如下圖所示：

新增設定檔  
![](http://127.0.0.1:8080/_images/cs/setting3.png)

設定檔不一定要在 Properties 目錄底下  
![](http://127.0.0.1:8080/_images/cs/setting5.png)

## 如何讀取自建的應用程式設定檔

同樣的，當新增一個應用程式設定檔時，系統也會自動幫我們建立這個設定檔類別，透過以下的方法，就可以直接存取設定檔中的設定值。假設設定內容如下：

![](http://127.0.0.1:8080/_images/cs/setting2.png)

有二種方式存取。你可以新增一個設定檔類別的實體去存取設定值，或者是使用 Default 這個 static instance 取得屬性值。
```c#
//讀取應用程式設定檔，SampleApp 這個設定檔,是透過設計工具幫我們建立的類別
SampleApp sa = new SampleApp();
Console.WriteLine(sa.myName);
Console.WriteLine(sa.myBirthday.ToString());
Console.WriteLine(sa.myEmail);
Color c = sa.sysColor;
Console.WriteLine(sa.sysProjectName);

//若不想建立類別實體，也可以使用Default這個static instance取得屬性值
Console.WriteLine(SampleApp.Default.myName);
Console.WriteLine(SampleApp.Default.myBirthday.ToString());
Console.WriteLine(SampleApp.Default.myEmail);
Color c2 = SampleApp.Default.sysColor;
Console.WriteLine(SampleApp.Default.sysProjectName);
```

## 如何自行撰寫自訂應用程式設定檔類別

前面提過， [ApplicationSettingsBase](http://msdn.microsoft.com/zh-tw/library/d1cddstd.aspx) 是應用程式設定檔的基底類別，若要自行撰寫設定檔類別，首先就要繼承這個類別。

下列示範如何自行撰寫繼承自 [ApplicationSettingsBase](http://msdn.microsoft.com/zh-tw/library/d1cddstd.aspx) 類別，以讀取應用程式設定檔
```c#
//讀取應用程式設定檔 SampleApp2這個設定檔,是自行撰寫繼承自ApplicationSettingsBase的類別
SampleApp2 sa2 = new SampleApp2();
Console.WriteLine(sa2.myName);
Console.WriteLine(sa2.myBirthday.ToString());
Console.WriteLine(sa.myEmail);
Color c3 = sa2.sysColor;
Console.WriteLine(sa2.sysProjectName);

class SampleApp2 : ApplicationSettingsBase
{
[UserScopedSetting()]                   //將這個設定值的使用範圍設定為:User
[DefaultSettingValueAttribute("vito")]  //設定預設值
public string myName {
get { return (String)this["myName"]; }
set { this["myName"] = value; }
}

[UserScopedSetting()]
[DefaultSettingValueAttribute("1911/01/01")]
public System.DateTime myBirthday {
get { return (DateTime)this["myBirthday"]; }
set { this["myBirthday"] = value; }
}

[UserScopedSetting()]
[DefaultSettingValueAttribute("")]
public string myEmail {
get { return (string)this["myEmail"]; }
set { this["myEmail"] = value; }
}

[ApplicationScopedSetting()]        //將這個設定值的使用範圍設定為:Application
[DefaultSettingValueAttribute("255, 255, 255")]
public Color sysColor
{
get { return (Color)this["sysColor"]; }
set { this["sysColor"] = value; }
}

[ApplicationScopedSetting()]
[DefaultSettingValueAttribute("aaa bbb")]
public string sysProjectName
{
get { return (string)this["sysProjectName"]; }
set { this["sysProjectName"] = value; }
}
}
```

## 如何以程式碼修改設定值

不管增加了幾個應用程式設定檔，所有應用程式設定檔的資訊內容，都會被複製存一份放在 app.config 底下。  如果將設定值的使用範圍設為 User ，就會放在 &lt;userSettings&gt; 區段內；如果將設定值的使用範圍設為 Application ，就會放在 &lt;applicationSettings&gt; 區段內。  

下列程式碼示範如何讀取與變更設定值：
```c#
private void button8_Click(object sender, EventArgs e)
{
//讀取設定值
SampleApp setting = new SampleApp();
txtName.Text = setting.myName;
dtBirthday.Value = setting.myBirthday;
labColor.BackColor = setting.sysColor;
}

private void button6_Click(object sender, EventArgs e)
{
//寫入設定值
SampleApp setting = new SampleApp();
setting.myName = txtName.Text;
setting.myBirthday = dtBirthday.Value;
setting.sysColor = labColor.BackColor;
setting.Save();
}
```

PS1.

存檔之後，檢查 app.config 或 XXX.exe.config，為什麼資料都沒有改變呢？可是再執行一次程式，會發覺載入的資訊值卻是新的。為什麼？

這是因為，在 &lt;userSettings&gt; 這個區段底下的資訊，若被使用者變更後，會複製一份在 C:\Users\[username]\AppData\Local\Microsoft\.. 底下，檔名為 user.config 。  這時候，若再去變更 app.config 並不會發生什麼作用，因為關於 userSettings 的資訊，會以這個檔案優先讀取，除非它被刪除了，才會去讀取 app.config 裡面原始的設定值。  這也是為什麼&lt;applicationSettings&gt;這個區段是唯讀的原因，因為每個 User 只會讀取到屬於自已的 &lt;userSettings&gt; 部分。  若要在程式中變更全域的設定值，可以在公開的目錄底下建立custom config file。  

下圖顯示， &lt;userSettings&gt; 這個區段真正的存放位置及內容.。  
![](http://127.0.0.1:8080/_images/cs/setting6.png)

PS2.

所讀取的這個 user.config 檔案，是依據組件版號而異的，也就是，不同的版號，會自動建立一個目錄存放 user.config 檔。  所以一旦變更組件版號，就會讀取新的 user.config 檔。

## 利用 ApplicationSettings 儲存使用者習慣

在 WinForm 程式中，有時候我們會想記錄使用者上一次使用時的最後操作，方便下次開啟應用程式時自動載入前一次的設定。  例如下圖中，使用者必須透過 OpenFileDialog 設定操作的 Excel 檔案來源，這時我們可以利用 ApplicationSettings 快速的記錄下這個資訊，方便下次執行時自動載入。  

![](http://127.0.0.1:8080/_images/cs/setting7.png)

要完成這個設定，你只要針對該控制項，新增一個 Text 屬性繫結（PropertyBinding）即可。步驟如下：  

![](http://127.0.0.1:8080/_images/cs/setting8.png)

利用下拉選單中的功能，新增一個 ApplicationSettings 項目。

![](http://127.0.0.1:8080/_images/cs/setting9.png)

![](http://127.0.0.1:8080/_images/cs/setting11.png)

設定完成後，就會有一個 Text 的屬性繫結。

![](http://127.0.0.1:8080/_images/cs/setting10.png)

最後你必須找一個事件（例如:FormClosing），將上面設定儲存成預設值，等到程式下次執行時，就會自動載入這個預設值。
```c#
private void frmGPS_FormClosing(object sender, FormClosingEventArgs e)
{
Properties.Settings.Default.Save();
}
```