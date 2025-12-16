---
title: User Profile
layout: default
parent: 認證與授證
nav_order: 1
description: "User Profile"
date: 2012-10-08
tags: [Web,ASPNET,認證與授證]
postid: "618295331975638974"
---
# ASP.NET 會員管理機制

[Profile](http://wiki.asp.net/page.aspx/184/profile/) 指的是 ASP.NET 用來存取個人資料的一個類別，它是 ASP.NET 內建的一套管理機制，預設是必須搭配使用 SQL 的 aspnetdb 資料庫。  該資料庫系統中，同時含有 [Membership](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.aspx) 機制，可以實現會員管理功能。  也包含 roleManager 機制，可以做到群組化的會員管理機制。  

這三個與會員相關的機制都是 ASP.NET 內建的，它的相關設定都定義在 framework 版本的 machine.config 中，  如：C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config  
```xml
<connectionStrings>
<add name="LocalSqlServer" connectionString="data source=.\SQLEXPRESS;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|aspnetdb.mdf;User Instance=true" providerName="System.Data.SqlClient"/>
</connectionStrings>
<system.web>
<profile>
<providers>
<add name="AspNetSqlProfileProvider" connectionStringName="LocalSqlServer" applicationName="/" 
type="System.Web.Profile.SqlProfileProvider, System.Web, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"/>
</providers>
</profile>
<membership>
<providers>
<add name="AspNetSqlMembershipProvider" 
type="System.Web.Security.SqlMembershipProvider, System.Web, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" 
connectionStringName="LocalSqlServer" enablePasswordRetrieval="false" enablePasswordReset="true" 
requiresQuestionAndAnswer="true" applicationName="/" requiresUniqueEmail="false" 
passwordFormat="Hashed" maxInvalidPasswordAttempts="5" minRequiredPasswordLength="7" 
minRequiredNonalphanumericCharacters="1" passwordAttemptWindow="10" passwordStrengthRegularExpression=""/>
</providers>
</membership>
<roleManager>
<providers>
<add name="AspNetSqlRoleProvider" connectionStringName="LocalSqlServer" applicationName="/" 
type="System.Web.Security.SqlRoleProvider, System.Web, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"/>
<add name="AspNetWindowsTokenRoleProvider" applicationName="/" 
type="System.Web.Security.WindowsTokenRoleProvider, System.Web, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"/>
</providers>
</roleManager>
</system.web>
```

如何建立 aspnetdb ，可參考[這篇](aspnetdb.aspx)

# 建立 User Profile

## 設定 User Profile 資料庫

#### 使用附加檔案的 User Profile 資料庫

要使用這個會員機制，當然要有一個會員資料庫。若使用 VS2005 建立新網站，它會自動在 App\_Data 目錄中加入 aspnetdb.mdf 資料庫，如上所述，它預設會使用 SQL Express 當做資料庫引擎。  如果電腦中有安裝 SQL Express ，那麼程式執行時，很輕鬆的就可以連上資料庫。  

#### 在 SQL Server 中建立新的 User Profile 資料庫

若要使用 SQL Server 取代 SQL Express 以作為 Membership、Roles、Profiles 等內建功能的資料庫，總共只有兩個簡單的步驟，詳述如下。  

1. **執行 aspnet\_regsql.exe 建立資料庫**：  
這個工具位於 C:\Windows\Microsoft.NET\Framework\v2.0.50727\ 或 C:\Windows\Microsoft.NET\Framework64\v2.0.50727\ 或類似的子目錄下，它會啟動一個精靈，幫你在 SQL Server 中建立所需的資料庫。  你要做的事情很單純，就是輸入連線方式，指定資料庫名稱（不需取名為 ASPNETDB，可以是任何名稱），接著，它會幫你把資料庫建立起來。  

或者直接使用命令方式建立資料庫： aspnet\_regsql.exe -E -S localhost -Ap
2. **在 Web.config 檔案中重設連線字串**：  
請參考以下範例。  要注意這套機制使用的連線字串為 **LocalSqlServer**， 這個字是固定的，不能改它。  applicationName 是用來區分不同系統的使用者，若所有系統要共用使用者，設定相同的值即可。
```xml
<connectionStrings>
<remove name="LocalSqlServer" />
<add name="LocalSqlServer" connectionString="Data Source=XXX; Initial Catalog=aspnetdb; User ID=xxx; Password=xxx" providerName="System.Data.SqlClient" />
</connectionStrings>

<system.web>
<profile>
<providers>
<remove name="AspNetSqlProfileProvider" />
<add name="AspNetSqlProfileProvider" 
connectionStringName="LocalSqlServer"
applicationName="/" 
type="System.Web.Profile.SqlProfileProvider, System.Web, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
/>
</providers>
</profile>

<membership>
<providers>
<remove name="AspNetSqlMembershipProvider" />
<add name="AspNetSqlMembershipProvider" 
connectionStringName="LocalSqlServer" 
applicationName="/"
type="System.Web.Security.SqlMembershipProvider, System.Web, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
requiresQuestionAndAnswer="false"
passwordFormat="Clear"                    
minRequiredNonalphanumericCharacters="0"
minRequiredPasswordLength="8" />
</providers>
</membership>

<roleManager enabled="true">
<providers>
<remove name="AspNetSqlRoleProvider" />
<add name="AspNetSqlRoleProvider" 
connectionStringName="LocalSqlServer" 
applicationName="/"
type="System.Web.Security.SqlRoleProvider, System.Web, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"/>
</providers>
</roleManager>
</system.web>
```

關於會員機制的安全性的限制，可參考 MSDN: [MembershipProvider 屬性](http://msdn.microsoft.com/zh-tw/library/9x1zytyd%28v=vs.100%29.aspx)

## 使用 ASP.NET 網站管理工具

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4lMyddteTyOhUmCIgOIQwKsgzNyp15YH5XFt1mIG2RqM9fFAmezbEUDt8zcutkBgZMmeSislP_7pQNS6nioeKAfPQbMT_dT5JjXPxPSFvz1oBuHaTHd1Xp-EQlPtWpg5skcWpkqwb-1Y/s699/web-config-tool.png)

# 定義 User Profile

ASP.NET的這個會員機制只簡單定義了帳號、密碼、信箱等屬性的資訊，如果需要使用到其他的個人資料，就必須在 Web.config 的 &lt;profile&gt; 區塊中定義。  

## 自行定義 User Profile 欄位

下面這個範例，我們自行定義了四個欄位，要注意的是，除了字串以外，若要使用其他型別，就必須明確宣告型別。
```xml
<profile>
<properties>
<add name="FirstName" />
<add name="LastName" />
<add name="LastVisit" type="System.DateTime" />
<add name="Age" type="System.Int16" />
</properties>
</profile>
```

## 定義群組式的欄位
```xml
<properties>
<group name="Address">
<add name="Street" />
<add name="City" />
<add name="PostalCode" />
</group>
</properties>
```

## 使用自訂型別的欄位

你也可以使用自訂類別當作 Profile 的屬性欄位。例如：
```xml
<properties>
<add name="Spouse" type="Practice70515.Common.Spouse" serializeAs="Xml"/>
</properties>
```
```c#
namespace Practice70515.Common
{
[Serializable]
public class Spouse
{
public string Name { get; set; }
public string ID { get; set; }
public Int16 Age { get; set; }
}
｝
```

更詳盡的會員屬性設定，可參考 MSDN: [定義 ASP.NET 設定檔屬性](http://msdn.microsoft.com/zh-tw/library/d8b58y5d.aspx)

# 存取 User Profile

ASP.NET 提供 Profile 機制讓開發人員可以很容易建立具備個人化功能的 Web Application，不過在實作上有點小技巧必須注意。  若您想要以程式方式直接讀取設定在 web.config 中的 Profile 設定時，必須是「Web Site」類型的專案，ASP.NET 引擎才會自動建立 DefaultProfile 物件，才可以用下列程式碼來存取 Profile 中的資訊。  
```c#
string s = Profile.FirstName;
```

若是在「Web Application」類型的專案中，則必須自行實作 ProfileBase  類別，才可以使用強型別存取 Profile 。  要不然就得透過 HttpContext.Current.Profile 物件來存取，使用方法如下：  
```c#
//read profile
ProfileBase profile = Context.Profile;
txtFirstName.Text = profile.GetPropertyValue("FirstName").ToString();
txtLastName.Text = profile.GetPropertyValue("LastName").ToString();
txtAge.Text = profile.GetPropertyValue("Age").ToString();
```
```c#
//save profile
ProfileBase profile = Context.Profile;
profile.SetPropertyValue("FirstName", txtFirstName.Text);
profile.SetPropertyValue("LastName", txtLastName.Text);
profile.SetPropertyValue("Age", Int16.Parse(txtAge.Text));
profile.Save();
```

如何實作 ProfileBase  類別，可參考以下文章作法：[http://www.dotblogs.com.tw/phoenix7765/archive/2008/09/19/5417.aspx](http://www.dotblogs.com.tw/phoenix7765/archive/2008/09/19/5417.aspx)

# 匿名使用者的 User Profile

如上面示範的，在使用者驗證身份登入網站後，即可使用透過 Profile 存取到個人資訊。  例如，我們可以將網站的喜好設定儲存在 Profile 裡，等到使用者登入時，再依個人喜好配置網站。  如果該網站允許匿名存取，那麼，對於匿名的使用者，要如何記錄這些資訊呢？  

其實，除了已驗證身份的使用者可以使用 Profile 使用者設定檔之外，匿名的使用者其實也可以使用，但預設是關閉的，必須自行將相關設定打開後，匿名使用者的使用者設定檔才能夠作用。  

對於匿名使用者的個人資訊，不會儲存在資料庫中，而是使用 cookie 存放在客戶端電腦中。  
```xml
<anonymousIdentification enabled="true" />
```

除此之外，也必須指明哪些屬性是開放匿名使用者存取的，該屬性必須設定 **allowAnonymous=true** 。  
```xml
<properties>
<add name="FirstName" allowAnonymous="true" />
<add name="LastName" allowAnonymous="true" />
<add name="LastVisit" type="System.DateTime" />
<add name="Age" type="System.Int16" />
</properties>
```