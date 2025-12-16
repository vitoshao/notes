---
title: 自訂組態設定
layout: default
parent: 自訂網站
nav_order: 5
description: "自訂組態設定"
date: 2013-01-23
tags: [Web,ASPNET,自訂網站]
postid: "3328136637923007150"
---
# 設定 Web.Config 檔

使用 [System.Configuration](http://msdn.microsoft.com/zh-tw/library/system.configuration.aspx) 命名空間裡的 [Configuration](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.aspx) 類別讀取 Web.config 檔案。

使用 [Configuration.GetSection](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.getsection.aspx) 讀取組態檔的區段資訊。

## 讀寫 Web.Config 檔

### 讀取標準組態項目

Web.Config 中的每一個標準項目都有各自的類別，而且必須使用其類別存取組態資訊。例如：

- AuthenticationSection：&lt;system.web&gt;&lt;authentication&gt;
- AuthorizationSection：&lt;system.web&gt;&lt;authorization&gt;
- HttpCookiesSection：&lt;system.web&gt;&lt;httpCookies&gt;
- MembershipSection：&lt;system.web&gt;&lt;membership&gt;
- PagesSection：&lt;system.web&gt;&lt;pages&gt;
- SiteMapSection：&lt;system.web&gt;&lt;siteMap&gt;
- ......
```c#
protected void btnReadWebConfig_Click(object sender, EventArgs e)
{
//讀取 system.web 底下特定的 section
AuthenticationSection Authentication = (AuthenticationSection)WebConfigurationManager.GetSection("system.web/authentication");

//使用該類別的方法，讀取該類別的屬性
AuthenticationMode mode = AuthenticationMode.Forms;
}
```

### 讀取應用程式設定
```c#
protected void btnAppSetting_Click(object sender, EventArgs e)
{
string sLogo = WebConfigurationManager.AppSettings["LogoImagePath"];
}
```

### 讀取連接字串設定
```c#
protected void btnConnectionSetting_Click(object sender, EventArgs e)
{
//========================================
//讀取 ConnectionStrings
//========================================
string sConn = WebConfigurationManager.ConnectionStrings["TestDBConnectionString"].ConnectionString;

//========================================
//修改 ConnectionStrings
//========================================

//開啟設定檔
Configuration config = WebConfigurationManager.OpenWebConfiguration(null);

//讀取 ConnectionStrings
ConnectionStringSettingsCollection Connections = config.ConnectionStrings.ConnectionStrings;

//新增 ConnectionString
if (Connections["Conn2"] == null)
{
Connections.Add(new ConnectionStringSettings("Conn2", "Data Source=localhost; Initial Catalog=DBNAME;"));
}
else
{
Connections["Conn2"].ConnectionString = "Data Source=localhost; Initial Catalog=DBNAME1;";  
}

//儲存 ConnectionStrings
config.Save();
}
```

### 儲存組態設定

因為組態資訊有可能來自於 machine.config 或 web.config ，所以要變更組態資訊就必須選擇組態檔的位置。  

- [WebConfigurationManager.OpenWebConfiguration](http://msdn.microsoft.com/zh-tw/library/system.web.configuration.webconfigurationmanager.openwebconfiguration.aspx) ：開啟 Web 應用程式組態檔
- [Configuration.Save](http://msdn.microsoft.com/zh-tw/library/system.configuration.configuration.save.aspx) ：將組態設定寫入至組態檔。
```c#
protected void btnWebConfig_Click(object sender, EventArgs e)
{
//開啟設定檔
Configuration rootConfig = WebConfigurationManager.OpenWebConfiguration(null); 

//讀取 system.web 底下特定的 section
AuthenticationSection Authentication = (AuthenticationSection)rootConfig.GetSection("system.web/authentication");

//設定 AuthenticationSection 的屬性
Authentication.Mode = AuthenticationMode.Forms;

//儲存設定檔
rootConfig.Save();
}
```

## 如何使用 ConfigSource 設定關聯的組態區段

當設定 Web.Config 中的區段資料時，有時候為了管理方便，可以將區段內的設定資訊，獨立成一個設定檔，再透過 file 或 configSource 屬性 include 進來。  

- **file**：延伸外部檔案的setting
- **configSource**：使用外部檔案的setting

在外部設定檔中，其根節點必須是設定的區段項目，而不是 configuration 項目。例如以下範例：

### 使用 configSource 設定關聯組態

透過 **configSource** 屬性，可以將 appSettings 區段的設定內容，獨立於外部檔案。  要注意的是，獨立到外部檔的內容必須以整個區段為範圍，所以該原始區段裡頭就不能含有任何設定。  

#### 範例一：appSettings

原始web.config
```xml
<?xml version="1.0"?>
<configuration>
<appSettings>
<add key="CorpName" value="MyCorpName" />
<add key="ProjectName" value="MyProjectName" />
</appSettings>
</configuration>
```

使用外部檔案的設定
```xml
<?xml version="1.0"?>
<configuration>
<appSettings configSource="MyAppSetting.config" > 
<!--內容獨立出去，不能再包含任何內容-->
</appSettings>
</configuration>
```
```xml
<?xml version="1.0"?>
<appSettings>
<add key="CorpName" value="MyCorpName" />
<add key="ProjectName" value="MyProjectName" />
</appSettings>
```

#### 範例二：mailSettings

原始web.config
```xml
<?xml version="1.0"?>
<configuration>
<system.net>
<mailSettings >
<smtp from="vitouserNameshao@gmail.com">
<network host="smtp.gmail.com" userName="userName@gmail.com" password="password" port="587" enableSsl="true" />
</smtp>
</mailSettings>
</system.net>
</configuration>
```

使用外部檔案的設定
```xml
<?xml version="1.0"?>
<configuration>
<system.net>
<mailSettings >
<smtp configSource="smtp.config">
</smtp>
</mailSettings>
</system.net>
</configuration>
```
```xml
<?xml version="1.0"?>
<smtp from="userName@gmail.com">
<network host="smtp.gmail.com" userName="userName@gmail.com" password="password" port="587" enableSsl="true" />
</smtp>
```

### 使用 file 設定關聯組態

透過 **file** 屬性延伸外部檔案的設定，則該區段裡頭有重複項目，會以外部檔案的設定為優先。
```xml
<?xml version="1.0"?>
<configuration>
<appSettings file="MyAppSetting.config">
<add key="ProjectName" value="MyProjectNameB" />
</appSettings>
</configuration>
```