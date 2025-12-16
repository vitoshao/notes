---
title: Form驗證
layout: default
parent: 
nav_order: 2
description: "Form驗證"
date: 2012-12-31
tags: []
postid: "1048822038454350589"
---
# 什麼是 Forms 認證

Forms 認證是最廣泛應用的認證方式，尤其是應用在對外公開的網站，而不是僅內部人員使用的網站。

Forms 認證需要使用者提供密碼驗證，通常他們的資料來自於外部資料來源，如 [Membership](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.aspx) 資料庫，或是應用程式的組態檔中。  當使用者透過表單驗證成功之後，ASP.NET 會回應一個 cookie 給瀏灠器，作為驗證語彙基元(authentication token)，用來表示這個認證過的使用者。  瀏灠器後續提出給網站的需求，都會同時送出這個 Token ，這樣子就不用每次要求都提供認證。  ASP.NET 則可以依這個 Token 驗證使用者的權限。  

#### Froms 認證流程

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhN-6b00tjToWIj6Y9jF__eibANOXz-m66WCG6U6okzEKIzYiE_OK_w0OHiapbs4FLZJCOSe_S-EPCtzFjzlHJeVZJ0cnOJbyhPA75atDpcMikfdLKCYoH5lJu-mgoSY6yJoq8mitp5uW0/s428/forms-authentication-flow.gif)

# Forms 認證的設定

## 如何啟用 Forms 認證

要啟用 Forms 認證，只要將 authentication 組態項目的 mode 屬性設定為 Forms 即可。
```xml
<authentication mode="Forms">
<forms loginUrl="~/login.aspx" 
defaultUrl="~/default.aspx" 
cookieless="AutoDetect" 
protection="Validation"/>
</authentication>
```

## Forms 認證的相關屬性設定

#### 底下是 Form 標記的屬性範例
```xml
<authentication mode="Forms">
<forms 
loginUrl="~/login.aspx" 
defaultUrl="~/index.aspx" 
protection="All"
timeout="30"
requireSSL="false"
cookieless="UseDeviceProfile"
enableCrossAppRedirects="false"/>
</authentication>
```

#### 下表是 forms 屬性的簡要說明：

- **loginUrl**：指定找不到有效的驗證 Cookie 時，將要求重新導向以進行登入的 URL。( 預設值為 login.aspx )
- **defaultUrl**：驗證後重新導向的預設 URL。
- **name**：指定用來驗證的 HTTP Cookie。(預設值為 ".ASPXAUTH")  
如果在單一伺服器執行多個應用程式，而且每個應用程式都需要唯一的 Cookie，就必須在每個應用程式的 Web.config 檔中設定 Cookie 名稱。
- **path**：應用程式發出 Cookie 的路徑。預設值為 (/)。
- **requireSSL**：指定傳輸驗證 Cookie 是否需要 SSL 連接。（預設值為 False）
- **slidingExpiration**：指定是否啟用滑動期限。（預設為 True）
- **timeout**：指定 Cookie 過期的時間，以整數分鐘為單位。（預設為 30 分鐘）
- **ticketCompatibilityMode**：指定是否要使用 UTC 或當地時間做為表單驗證的票證到期日。（預設值是 Framework20）
- Framework20：指定使用當地時間儲存票證到期日。
- Framework40：指定使用 UTC 儲存票證到期日。
- **protection**：指定 Cookie 要使用的加密類型。 (預設值為 All)
- All：指定應用程式同時使用資料驗證和加密來協助保護 Cookie。
- Encryption：指定應用程式使用 3DES 加密 Cookie，但是不對 Cookie 執行資料驗證。
- None：不加密也不驗證。
- Validation：指定驗證配置驗證加密且轉換時未被改變的 Cookie 。
- **cookieless**屬性：定義 Token 是否使用 Cookie 存放。（預設值為 UseDeviceProfile）
- UseCookies：不論用戶端支援為何，都使用 cookie 表單驗證，也就是永遠傳送 Cookie 給用戶端。
- UseUri：不論用戶端支援為何，都使用無 cookie 表單驗證，也就是將 Token 存放於 URL。
- AutoDetect：若裝置設定檔支援 Cookie 就使用 cookie 表單驗證。不過 ASP.NET 仍會測試用戶端是法支援 cookie ，若支援，則使用 cookie 表單驗證；若不支援，則使用無 cookie 表單驗證。
- UseDeviceProfile：只要瀏覽器有支援 Cookie ，即便用戶端停掉 Cookie，都會傳送 Cookie。但是，若用戶端停掉 Cookie ，則表單驗證無法順利執行。

更詳情的說明，請參考 MSDN: [表單驗證的設定項目](http://msdn.microsoft.com/zh-tw/library/1d3t3c61%28v=vs.100%29.aspx)

# FormsAuthenticationModule

[FormsAuthenticationModule](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthenticationmodule.aspx) 是 ASP.NET 提供的表單驗證機制，  在 machiene-level 的 Web.config 檔中，預設會包含加入這個機制。  如果要啟用只需要如下的設定即可：  
```xml
<authentication mode="Forms" />
```

The [FormsAuthenticationModule](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthenticationmodule.aspx) class constructs a       [GenericPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.genericprincipal.genericprincipal.aspx) object and stores it in the **HTTP context**.   The [GenericPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.genericprincipal.genericprincipal.aspx) object holds a reference to a **FormsIdentity** instance that represents the currently authenticated user.   You should allow forms authentication to manage these tasks for you.     If your applications have specific requirements, such as setting the **User** property to a custom class that implements the       **IPrincipal** interface,  your application should handle the **PostAuthenticate** event.      The **PostAuthenticate** event occurs after the [FormsAuthenticationModule](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthenticationmodule.aspx) has verified the forms authentication cookie and created the       [GenericPrincipal](http://msdn.microsoft.com/zh-tw/library/system.security.principal.genericprincipal.genericprincipal.aspx) and **FormsIdentity** objects.   Within this code, you can construct a custom **IPrincipal** object that wraps the       **FormsIdentity** object, and then store it in the **HttpContext**.   

**Note**   If you do this, you will also need to set the **IPrincipal** reference on the       **Thread.CurrentPrincipal** property to ensure that the **HttpContext** object and the thread point to the same authentication information.

# FormsAuthentication 類別

The [FormsAuthentication](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.aspx) class creates the authentication cookie automatically when the [FormsAuthentication.SetAuthCookie](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.setauthcookie.aspx) or [FormsAuthentication.RedirectFromLoginPage](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.redirectfromloginpage.aspx) methods are called.  

下表是 [FormsAuthentication](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.aspx) 類別提供的方法和屬性，可以用來檢驗應用程式中使用者的驗證資訊。

#### 屬性：

- [FormsCookieName](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.formscookiename.aspx) ：取得用來存放表單驗證票證的 Cookie 名稱。
- [FormsCookiePath](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.formscookiepath.aspx) ：取得表單驗證 Cookie 的路徑。
- [RequireSSL](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.requiressl.aspx) ：指出表單驗證 Cookie 是否需要 SSL 才能傳回至伺服器。
- [Timeout](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.timeout.aspx) ：取得驗證票證逾時前的時間量。
- [SlidingExpiration](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.slidingexpiration.aspx) ：取得一個指示是否啟用變動到期的值。

#### 方法：

- [Authenticate](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.authenticate.aspx) ：根據存放於應用程式組態檔中的認證，驗證使用者名稱和密碼。
- [Decrypt](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.decrypt.aspx) ：根據傳遞至方法的已加密表單驗證票證，建立 [FormsAuthenticationTicket](https://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthenticationticket%28v=vs.110%29.aspx) 物件。
- [Encrypt](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.encrypt.aspx) ：建立包含已加密表單驗證票證 (適用於 HTTP Cookie 中) 的字串。
- [GetAuthCookie](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.getauthcookie.aspx) ：建立指定使用者名稱的驗證 Cookie。
- [GetRedirectUrl](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.getredirecturl.aspx) ：取得被導到登入頁前的原本 URL。
- [RedirectFromLoginPage](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.redirectfromloginpage.aspx) ：將已驗證的使用者重新導向回到原來要求的 URL 或預設 URL。  
這個方法的第二個參數，若設成 true ，表示建立持久性 Cookie (跨瀏覽器工作階段儲存的 Cookie)，                                若設成 false，表示建立暫時性 Cookie ，也就是只存放在記憶體之中，瀏覽器關掉就自動清除。          這個功能相當於 [Login](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.login.aspx) 控制項中，「記憶密碼供下次使用」的設定。
- [RedirectToLoginPage](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.redirecttologinpage.aspx) ：將瀏覽器重新導向至登入頁。
- [SetAuthCookie](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.setauthcookie.aspx) ：為所提供的使用者名稱建立驗證票證，並將該票證加入至回應的 Cookie 集合，或加入至 URL (若使用的是 Cookieless 驗證)。
- [SignOut](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.signout.aspx) ：從瀏覽器移除表單驗證票證。

# 實作 Forms 認證

當使用 Windows 認證，瀏灠器會自已產生一個對話視窗，要求使用者輸入帳號密碼，所以不用自行設計登入頁面。  若是採用 Forms 認證，則必須自行設計登入頁面。  

要使用 Forms 認證可以有多種方法，例如：搭配 ASP.NET 的 [Login](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.login.aspx) 控制項，或者使用自訂的表單。  

## 使用 Login 控制項進行驗證

使用 [Login](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.login.aspx) 控制項進行驗證，請參考前一節中的介紹。

## 使用自訂表單進行驗證

使用 [Login](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.login.aspx) 控制項，可以省下很多事情，但有時候遇到需要客制化時，還是得自已來。  底下示範，如何使用自訂驗證表單驗證使用者的技巧。  依照使用者資料來源的不同，方法有所不同：  

### 驗證 Web.config 中的使用者資料

假設我們在 web.config 設定了以下幾組使用者資料
```xml
<authentication mode="Forms">
<forms loginUrl="login.aspx" protection="Encryption" timeout="30" >
<credentials passwordFormat="SHA1" >
<user name="Eric" password="7110EDA4D09E062AA5E4A390B0A572AC0D2C0220"/>   <!--1234-->
<user name="Sam"  password="81FE8BFE87576C3ECB22426F8E57847382917ACF"/>   <!--abcd-->
</credentials>
</forms>
</authentication>
```

如果是要驗證上述組態檔中的 credentials 區段中的使用者，可以用以下方法：
```c#
//驗證 Web.config 中的使用者資料
if (FormsAuthentication.Authenticate(UserName, Password)) 
myMessage.Show(this, "Authenticate Successed");
else
myMessage.Show(this, "Authenticate Failed");
```

#### 這個方法注意事項：

- [Authenticate](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.authenticate.aspx) 方法，在 .NET 4.5 已過時。
- 在組態中的密碼資料是經由 [FormsAuthentication.HashPasswordForStoringInConfigFile](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.hashpasswordforstoringinconfigfile.aspx) 方法加密過的.

### 驗證 Membership Provider 中的使用者資料

如果是驗證 ASP.NET 的 [Membership](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.aspx) 的使用者，可以使用 [Membership.ValidateUser](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.validateuser.aspx) 方法驗證：
```c#
//驗證 Membership 中的使用者資料
if (Membership.ValidateUser(UserName, Password))
myMessage.Show(this, "Authenticate Successed");
else
myMessage.Show(this, "Authenticate Failed");
```

### 驗證自訂的使用者資料

如果是驗證自訂的使用者，則自行撰寫驗證方法：
```c#
//驗證 自訂 的使用者資料 （Authentication 為自訂類別）
string sUserID;
if (MyValidate(UserName, Password, out sUserID))
{
string ReturnUrl = "";
if (Request.QueryString["ReturnUrl"] != null)
//取得原來要求的 URL
ReturnUrl = Request.QueryString["ReturnUrl"].ToString();    ///TestAuthenticationWebSite/UserHome.aspx
else
//取得 web.config 中預設的 URL
ReturnUrl = FormsAuthentication.DefaultUrl;     ///TestAuthenticationWebSite/default.aspx

//不過, 不需要以上那麼麻煩, 只要使用以下方法, 就可以自動判斷有沒有原來要求的 URL
//因為 FormsAuthentication 會自動判斷網址列中的所有參數，是否含有網址的參數值。
//若有, 就會導向原來要求的 URL; 若沒有就會導向 預設的 URL
FormsAuthentication.RedirectFromLoginPage(UserName, true);
}
```

### 變更狀態成為已登入

不管使用何種驗證方式，當驗證過關後，你必須實作一段程式碼來記住使用者已經登入的狀態，例如使用 Session 來記錄，不過底下我們直接利用 [FormsAuthentication](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.aspx) 物件來設計，它會使用 Cookie 來記錄。  你只要叫用它的 [SetAuthCookie](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.setauthcookie.aspx) 或 [RedirectFromLoginPage](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.redirectfromloginpage.aspx) 方法，它就會變更 User.Identity.IsAuthenticated 值，並且將登入記錄送到用戶端 Browser 的 Cookie 中。  

[RedirectFromLoginPage](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.redirectfromloginpage.aspx) 這個方法的第二個參數，若設定為 true ，則 Cookie 資訊會儲存在 Client 端的磁碟中，有效期限預設為 30 分鐘；  若設定為 false ，則 Cookie 資訊只會存放在 Client 端瀏灠器的記憶體中，所以則當使用者關閉瀏灠器後， Cookie 就會失效。  
```c#
if (Authentication.Validate(UserName, Password, out sUserID))
{
bool bPersistentCookie = true;
FormsAuthentication.RedirectFromLoginPage(UserName, bPersistentCookie);
}
```

當叫 [FormsAuthentication.RedirectFromLoginPage](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.redirectfromloginpage.aspx) 方法時，它會自動建立一個 forms-authentication ticket ，並將該 ticket 編碼後儲存在使用者 Browser 的 Cookie 中。  如果我們要儲存特定的使用者資訊，可以自己建立 FormsAuthenticationTicket，然後寫入 Authentication Cookie。  
```c#
int version = 1;
string name = account;
DateTime issueDate = System.DateTime.Now;
DateTime expiration = System.DateTime.Now.AddMinutes(2);  //1分
bool isPersistent = rememberme.Checked;
string userData = password;
string cookiePath = FormsAuthentication.FormsCookiePath;

FormsAuthenticationTicket ticket = new FormsAuthenticationTicket(
version,                        /\*票證的版本號碼\*/
name,                           /\*使用者名稱\*/
issueDate,                      /\*核發時間\*/
expiration,                     /\*到期時間\*/
isPersistent,                   /\*是否將票證資訊存放到硬碟的 Cookie 中\*/
userData,                       /\*使用者特定資料\*/
cookiePath                      /\*存放於 Cookie 中時的路徑\*/
);

// 對 ticket 進行編碼
string encTicket = FormsAuthentication.Encrypt(ticket);

// 將該票證加入至回應的 Cookie 集合
FormsAuthentication.SetAuthCookie(name, isPersistent);
```

當叫 [FormsAuthentication.SetAuthCookie](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.setauthcookie.aspx) 方法時，該 Cookie 的有效時限預設為 30 分，可由 web.config 底下屬性設定變更。  
```xml
<system.web>
<authentication mode="Forms">
<forms loginUrl="~/Login.aspx" defaultUrl="~/SysAdmin/index.aspx" timeout="30" name="VitoWebAuth" />
...
```

若要由程式決定 Cookie 到期時間，則自行輸出 Cookie 即可。  
```c#
// 對 ticket 進行編碼
string encTicket = FormsAuthentication.Encrypt(ticket);

// 手動方式將該票證加入至回應的 Cookie 集合
HttpCookie cookie = new HttpCookie(FormsAuthentication.FormsCookieName, encTicket);
cookie.Expires = System.DateTime.Now.AddDays(3);  //３天
Response.AppendCookie(cookie);
```

#### 重新導向

上面的 [RedirectFromLoginPage](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.redirectfromloginpage.aspx) 靜態方法，除了會記住使用者登入記錄，另外會將已驗證的使用者重新導向回到原來要求的 URL 或預設 URL。  它會自動判斷網址列中是否帶有 ReturnUrl 參數，若有就會導向該網址，若沒有就會導向 web.config 中預設的 URL 。  如果你不想導向 ReturnUrl 或者預設的 URL ，你只要使用 [SetAuthCookie](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.setauthcookie.aspx) ，再 Redirect 你要的網址就好。  
```c#
// 對 ticket 進行編碼
string encTicket = FormsAuthentication.Encrypt(ticket);

// 將該票證加入至回應的 Cookie 集合
FormsAuthentication.SetAuthCookie(name, isPersistent);

// 因為採用 SetAuthCookie 回應 authentication ticket , 所以必須自行做重新導向.
// 若使用 FormsAuthentication.RedirectFromLoginPage 則不用.
string ReturnPath = Request.QueryString["ReturnUrl"];
if (ReturnPath != null)
{
Response.Redirect(ReturnPath);
}
else
{
Response.Redirect(FormsAuthentication.DefaultUrl);
}
```

### 判斷使用者是否已經登入

當我們透過 [RedirectFromLoginPage](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.redirectfromloginpage.aspx) 或 [SetAuthCookie](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.setauthcookie.aspx) 或手動回應一個 authentication ticket 型態的 cookie 後，  等到下一次的 request 這個 cookie 會自動被帶回 server 端，這時候在 sever 端可以直接使用以下方法，判斷是否已經驗證。  
```c#
//使用 User.Identity.IsAuthenticated 判斷
if(User.Identity.IsAuthenticated)
{
Response.Write("您現在是已登入狀態。");
}
//或者使用 Page.Request.IsAuthenticated 也可以
if(Page.Request.IsAuthenticated)
{
Response.Write("您現在是已登入狀態。");
} 
```

### 取得登入的帳號

登入後要取得登入的帳號，可以用以下程式碼片段：
```c#
string sUserID = User.Identity.Name;
```

### 登出

若要執行登出，可以用以下程式碼片段：
```c#
// 清除 cookie
FormsAuthentication.SignOut();

// 導至登入頁　（依需求）
FormsAuthentication.RedirectToLoginPage()
```

### ticket expiration v.s. cookie expiration

FormsAuthentication 物件有 Expiration 屬性，而 Cookie 本身也有自已的 Expires 屬性，它們有什麼關係？  

當我們使用 [RedirectFromLoginPage](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.redirectfromloginpage.aspx) 或 [SetAuthCookie](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.setauthcookie.aspx) 方法輸出 authentication ticket 時，該 ticket 的 Expiration 屬性會參考 web.config 中的設定值，若沒有設定，預設值為 30 分。  此時實際上回應給 client 的 cookie ，其 Expires 也會等於此 ticket 的 Expiration 屬性值。  

若你自行建立 authentication ticket ，那麼這個票證的到期時間，以你指定的 Expiration 屬性值為主  而當你在輸出回應的 cookie 時，也可以指定 cookie 的 Expires 屬性值。  最後真正的有效期限，當然就以二者較小的值為主。

# Cookie 小常識

### Cookie 的運作

Cookie 是網頁運作中，唯一可以由 Server 端自由寫入到 Client 端的一種機制，常被用來做為保存使用者狀態的工具。  其運作過程如下：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgVFz51sW16Mbp2vyLj9JS2iBfT-N83_j7d20GHsZxB83000p3ta4F2bvYqYIYao6uwnHJhkrYf4KSuFFz5aR5joUg2omDGKL6x6aS6MNuWal7DD64SPxcERwAbceDtZabNfnUb7vbX9XE/s0/http-request.jpeg)

1. 若 Server 回應一個 Cookie 給瀏覽器時，這個資訊會加入到回應內容 HTTP Header 中的 "**Set-Cookie**" 資訊裡。
2. Client 接收到 Set-Cookie 內容後，會將 Cookie 的名稱與值儲存在 Browser 的 Cookie 存放區，並記錄該 Cookie 隸屬的網域、網址路徑、過期時間、是否為安全連線
3. 當瀏覽器再次送出 HTTP Request 指令到 Server 時，就會比對目前在 Browser 內的 Cookie 存放區有沒有「該網域」、「該目錄」、「過期時間尚未過期」且「是否為安全連線」的 Cookie，如果有的話就會一併包含在 HTTP Request 指令的 "**Cookie**" Header 中。
4. Cookie 雖然有 Name、Value、Expires、Domain、Path ...等屬性，但瀏覽器只會傳送 Cookie 的 name 和 value ，其他屬性都無法由 request 中取得的。  如果要讀取某個 cookie 的到期時間，可以將時間寫在另一個 cookie 的 value 來記錄。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjPJ0PezpTC51bpEAEVzdEt8flzXnMGBpJ1nq28NV-FI9kkmyUFvC76T2s8RmjA59UuZXCRKuOBJ1ooapaZPYIYjLHT4s-18poyLjlNsFTO0iZ40-0fTCU5uX0sfs6pU5ZXq3OQS9RowFQ/s0/http-set-cookie.png)

### Cookie 的保存期限

##### 1. Persistent Cookie

當你明確指定了 Cookie 的 Expires 時間，這個 Cookie 資訊就會被 Browser 儲存下來，即便 Browser 全部關閉或重開機後再開啟也還會存在，直到過了有效期限為止。  

##### 2. Session Cookie

若你沒有特別指定 Cookie 的 Expires 時間，只要 Browser 全部關閉後 Cookie 就會自動被清除。

### Cookie 的操作

底下列出一些 ASP.NET 針對 Cookie 操作的一些範例：

##### 1. 寫入或更新一個 Cookie
```c#
Response.Cookies["UserName"].Value = "vito";
Response.Cookies["UserName"].Expires = DateTime.Now.AddDays(1);
```

你也可以直接使用 .Net 的 HttpCookie 物件來操作。
```c#
HttpCookie cookie = new HttpCookie("UserName");
cookie.Expires = System.DateTime.Now.AddDays(1);
this.Response.Cookies.Add(cookie);
```

##### 2. 讀取一個 Cookie
```c#
HttpCookie cookie = Request.Cookies["UserName"];
string username = cookie.Value;
```

##### 3. 刪除一個 Cookie
```c#
// 只要將 Cookie 的 Expires 設定成 "昨天" 就可以刪除了! 
Response.Cookies["UserName"].Expires = DateTime.Now.AddDays(-1); 
```

##### 4. Path - 限制該 Cookie 只能由某個資料夾或應用程式存取 
```c#
// 只要將 Cookie 的 Expires 設定成 "昨天" 就可以刪除了! 
Response.Cookies["UserName"].Path = "/Web1";
```

##### 5. Domain - 限制 Cookie 的網域範圍 
```c#
//直接指定網站的網域名稱，若沒有額外設定則該 Cookie 僅能被當前的網域所存取
Response.Cookies["UserName"].Domain = "www.abc.com";

//所有 \*.abc.com 網域的所有網站都能接收此 Cookie
Response.Cookies["UserName"].Domain = ".abc.com";
```

##### 6. HttpOnly - 設定 Cookie 是否能由 Javascipt 所存取，預設為 false 能被 Javascript 所存取 
```c#
//設定為 true 代表該 Cookie 不能被 Javascript 存取
Response.Cookies["UserInfo"].HttpOnly = true;
```

##### 7.Secure - 設定 Cookie 僅在使用 HTTPS 時才會送出去 
```c#
//設定為 Https 狀態下才能發送
Response.Cookies["UserName"].Secure = true;
```
## 參考資料  

- [簡介 ASP.NET 表單驗證 (FormsAuthentication) 的運作方式](http://blog.miniasp.com/post/2008/02/20/Explain-Forms-Authentication-in-ASPNET-20.aspx)
- [Explained: Forms Authentication in ASP.NET 2.0](http://msdn.microsoft.com/zh-tw/library/aa480476.aspx)
- [Sys.Services.AuthenticationService 類別](http://msdn.microsoft.com/zh-tw/library/bb310861.aspx)
- [Understanding the Forms Authentication Ticket and Cookie](https://support.microsoft.com/en-us/kb/910443)