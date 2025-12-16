---
title: 驗證使用者
layout: default
parent: 認證與授證
nav_order: 3
description: "驗證使用者"
date: 2012-10-08
tags: [Web,ASPNET,認證與授證]
postid: "7796991568699404532"
---
ASP.NET 支援至少四種認證：

- Windows authentication
- Forms authentication
- Passport authentication
- Anonymous access

# Windows Authentication

When a Web application requires Windows authentication, the application rejects any request that does not include a valid user name and password in the **request header**.   The user's browser then prompts the user for a user name and password.   Because the browser prompts the user for credentials, you do not have to create a page to request the user's user name and password.  

Some browsers, such as IE, automatically provide the current user's name and password when the server is located on the intranet.  This seamlessly authenticates the user, eliminating the need to retype the password for intranet site visits.  

## 如何設定 Windows 認證

#### 在 IIS 設定

![設定認證](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgQFFY5kfcSxMi_rjBu8I-eveOk84VurGvNflANWldqzA1xyAjO-AWLVJDj3jg9y-dIxb8tMcmaOBP2G8SPhKnoJWcUEcNdkMTXCNQyf4vfcZh4YXyN-qhQqDuAvsWmeXvQS77LnR6MFFs/s629/iis-authentication.png)

![設定使用 Windows 認證](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgLn31ttzO6wfqI53ymTCsT9WmhvhdOJz1lILh9Dzsq_PMtiQS55VU2anDPEBdCWoA5T7VJkDlSDEnSEhNmzP1UTVQTidu4L3t1OrDAZ3GNuH-7p8VdC9FvbEBgIpucHocOCqu54LLD0iw/s458/iis-windows-authentication.png)

#### 在 Web.config 設定

在 web.config 裡設定 windows 認證，範例如下：
```xml
<system.web>
<authentication mode="Windows" />

<authorization>
<deny users="?" />
</authorization>
</system.web>
```

在授權區塊中，會使用到「?」和「\*」符號，其意義如下：  
「?」：表示未經過驗證的使用者；  
「\*」：表示所有的使用者(包含通過和未通過驗證的使用者)。  
所以上面範例說明，限制未經過驗證的使用者。  

#### 在 Web 管理工具中設定

另外在 Web 管理工具中，也可以設定 Windows 認證。如下圖範例，請選擇從區域網路(From A Local Network)。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhQChWl65UbEk_LfVwgNsaVk1f1DvUjPdbFK80I3hh9_MPMwd_1DUaHGQge-aC9m7pyhk21NmkXylluUqF28SbhY0KObgxGqbx59XWegwQRR-fCdQa6KzHDWCNOdPKNSYqiqJB-O5NUbJU/s644/webtool-authentication.png)

# Forms Authentication

Forms 認證是最廣泛應用的認證方式，尤其是應用在對外公開的網站，而不是僅內部人員使用的網站。

Forms 認證需要使用者提供密碼驗證，通常他們的資料來自於外部資料來源，如 Membership 資料庫，或是應用程式的組態檔中。  當使用者透過表單驗證成功之後，ASP.NET 會回應一個 cookie 給瀏灠器，作為驗證語彙基元(authentication token)，用來表示這個認證過的使用者。  瀏灠器後續提出給網站的需求，都會同時送出這個 Token ，這樣子就不用每次要求都提供認證。  ASP.NET 則可以依這個 Token 驗證使用者的權限。  

#### Froms 認證流程

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhN-6b00tjToWIj6Y9jF__eibANOXz-m66WCG6U6okzEKIzYiE_OK_w0OHiapbs4FLZJCOSe_S-EPCtzFjzlHJeVZJ0cnOJbyhPA75atDpcMikfdLKCYoH5lJu-mgoSY6yJoq8mitp5uW0/s428/forms-authentication-flow.gif)

更詳細的 Forms 認證介紹，[請看另一篇文章](http://vito-note.blogspot.com/2012/12/form.html)

# Passport Authentication

略

# 停用驗證機制（允許匿名存取）

如果確定系統只有匿名存取機制，可以直接停用網頁的驗證機制
```xml
<configuration>
<system.web>
<authentication mode="None" />
</system.web>
</configuration>
```

# Impersonation

預設情況下，ASP.NET 是以本機的 ASPNET 帳號或 **Network Service** 這個系統內建的帳戶啟動執行，以確保應用程式的安全。  如果網站需要更多的權限以存取系統資料的話，可能就得建立一個專屬帳戶並授予執行的權限，然後以這個帳號啟動 ASP.NET 。  

上面的做法比較不彈性，我們也可以在 web.config 中，啟用模擬來達到相同的需求。  

#### 模擬IIS認證帳號

若我們希望 ASP.NET 執行的權限是依每個連線的使用者帳戶來決定，那就可以使用以下設定
```xml
<identity impersonate="true" />
```

在這種情況下，使用者身份的認證交給 IIS 來進行。  當允許匿名存取的情況下，IIS 會傳遞一個特定的帳戶(預設是IUSR\_MACHINENAME)給 ASP.NET 應用程式，以執行模擬。  當系統僅允許驗證過的使用者時，IIS 則將認證過的身份傳遞給 ASP.NET 應用程式，ASP.NET具體的權限則由該帳號的權限決定。  

#### 模擬特定的使用者帳號

若 ASP.NET 應用程式需要以特定的使用者帳號執行，也可以使用以下設定，直接指定模擬特定的使用者帳號。  這時候 ASP.NET 應用程式的所有頁面請求，都會以這個指定的帳號執行。  
```xml
<identity impersonate="true" userName="accountname" password="password" />
```

#### 透過程式碼模擬IIS認證帳號

#### 透過程式碼模擬特定的使用者帳號

# 限制使用權限

在 web.config 中，authorization 項目是用來設定使用者對 URL 的使用權限。  它有 &lt;deny&gt; &lt;allow&gt; 二個子項目，分別用來限制和允許使用者對資源的存取。  

## 設定使用者和群組的存取權限

預設 machine.config 包含以下授權資訊：
```xml
<authorization>
<allow users="\*"/>
</authorization>
```

這個授權設定允許所有使用者，若想要變更成只允許 Eric 和 Sam 或者允許 admin 角色，我們可以在 web.config 中加入：
```xml
<authorization>
<allow users="Eric, Sam"/>
<allow roles="admin"/>
<deny users="\*"/>
</authorization>
```

- ~~授權判斷由上而下，每一條都要符合才被授權；但若有同時符合允許與禁止授權，則以排在比較上方的授權為主。~~
- 授權判斷由上而下，逐一查看 allow 和 deny 項目，直到找出符合某位特定使用者的第一項存取規則為止。
- 使用者或角色的屬性，是一串名稱的清單，中間以逗號隔開。
- 問號表示匿名使用者；星號表示所有已驗證或未驗證的使用者。
- 若使用 windows 驗證，角色名稱要與 AD 完全一致，如 Contoso\IT 。

## 限制檔案和目錄的存取權限

前面的設定是針對整個網站，如果只要限制特定檔案或目錄，則必須在 &lt;configuration&gt; 區段加入 &lt;location&gt; 設定，並指定 path 屬性。  path 屬性必須使用網站的相對路徑指定。  
```xml
<configuration>
<location path="AdminFunction">
<system.web>
<authorization>
<allow roles="manager" />
<deny users="?" />
</authorization>
</system.web>
</location>
</configuration>
```

使用多個 &lt;location&gt; 區段時，檔案或子目錄會自動繼承父係的設定，因此不需要對子目錄作相同的設定，但要注意是繼承特性卻也可能造成安全性的弱點。  例如下面這個例子：Protected這個目錄繼承了來自 machine.config 和 web.config 的第一層授權，所以只要驗證過的使用者都會獲得授權。  
```xml
<authorization>
<allow users="\*"/>
</authorization>
```
```xml
<configuration>
<system.web>
<authorization>
<deny users="?"/>
</authorization>
</system.web>

<location path="Protected">
<authorization>
<allow roles="admin" />
</authorization>
</location>
</configuration>
```

若要設定成只有 admin 群組才可以存取，則必須明確限制不被授權的對象。  
```xml
<location path="Protected">
<system.web>
<authorization>
<allow roles="admin" />
<deny users="\*" />
</authorization>
</system.web>
</location>
```
## 參考資料  

- [概略解釋 Forms Authentication 的運作](http://blog.miniasp.com/post/2008/02/21/Explain-Forms-Authentication-in-ASPNET-20.aspx)
- [ASP.NET 自訂角色的方式（不用實作 Role Provider）](http://blog.miniasp.com/post/2008/06/11/How-to-define-Roles-but-not-implementing-Role-Provider-in-ASPNET.aspx)
- [Explained: Forms Authentication in ASP.NET 2.0](http://msdn.microsoft.com/zh-tw/library/aa480476.aspx)
- [ASP.NET 2.0 建置自己的成員資格提供者](http://www.flag.com.tw/book/cento-5105.asp?bokno=FS497&id=378)
- [授權的 deny 項目 (ASP.NET 設定結構描述)](http://msdn.microsoft.com/zh-tw/library/8aeskccd.aspx)