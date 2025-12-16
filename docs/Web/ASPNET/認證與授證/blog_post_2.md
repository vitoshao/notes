---
title: Membership
layout: default
parent: 認證與授證
nav_order: 2
description: "Membership"
date: 2012-10-08
tags: [Web,ASPNET,認證與授證]
postid: "2544063110847092047"
---
前一章介紹的 Profile 類別，是用來存取個人資料。  而這一章介紹的 [Membership](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.aspx) 類別，則是用來管理帳號與授權的類別。  

# 使用網站管理工具設定安全性

## WSAT

You can use the Web Site Administration Tool (WSAT) to define and manage users, roles, and security on your site.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4lMyddteTyOhUmCIgOIQwKsgzNyp15YH5XFt1mIG2RqM9fFAmezbEUDt8zcutkBgZMmeSislP_7pQNS6nioeKAfPQbMT_dT5JjXPxPSFvz1oBuHaTHd1Xp-EQlPtWpg5skcWpkqwb-1Y/s699/web-config-tool.png)

## Creating Users

這個畫面中的項目限制會依據 [MembershipProvider](http://msdn.microsoft.com/zh-tw/library/9x1zytyd.aspx) 中的屬性設定來管理，例如指定密碼的最小長度或者使用特定字元等限制。

關於這個機制的安全性的限制，可參考 MSDN: [MembershipProvider 屬性](http://msdn.microsoft.com/zh-tw/library/9x1zytyd%28v=vs.100%29.aspx)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiQuWj086Wt3fKrjkRKTRjk2AFyPLEc33rs2QHammEIIlZ8vnLicLXRhUQ8mdjVY3HhYpFhgPqGOWshuTdMhv5CoxBD3r_L0XNHtE3uATB78y5RSHnA_tBPP2VHnWraik1EokHXYKbX4_I/s613/webtool-profile.png)

預設 [MembershipProvider](http://msdn.microsoft.com/zh-tw/library/9x1zytyd.aspx) 的屬性值，會要求密碼長度至少為７，非英數字元至少為１。  
若要變更這些設定，可以參考下面範例：  
```xml
<system.web>
<membership>
<providers>
<remove name="AspNetSqlMembershipProvider"/>
<add
name="AspNetSqlMembershipProvider"
connectionStringName="LocalSqlServer"
type="System.Web.Security.SqlMembershipProvider"
requiresQuestionAndAnswer="false"
passwordFormat="Clear"
minRequiredNonalphanumericCharacters="0" 
minRequiredPasswordLength="4"/>
</providers>
</membership>
</system.web>
```

- passwordFormat="Clear" ： 使用明碼。
- minRequiredNonalphanumericCharacters="0" ： 非英數字元數量。
- minRequiredPasswordLength="4" ： 最小長度。

## Creating Roles

要啟用會員機制的角色功能，必須啟用角色。
```xml
<system.web>
<roleManager enabled="true" />
</system.web>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhHAY6duGyaWaY9N5-4MLmC-kI9tqoZmI9go-YNNU9jpCH_AcsHoHmrs92A2BZ0dnonSPpm1XSMfj9iEoXd5_g4BuI4FpT_pTfbI6Kw-vU7sDezR3tVeM3zafZfgX7aKvK_XpKwXeQBhMk/s613/webtool-role.png)

## Creating Access Rules

這個會員機制也提供存取權限設定，它可以針對使用者或角色定義目錄層級的存取權限。  並將設定內容存放在 web.config 。  
```xml
<authorization>
<deny users="?" />
<allow roles="系統管理員" />
</authorization>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgB4aXv5CdhJqrZpGNhoNFAf0iB_dExsrl_xaqxZu8WaYLUjm_1nr-rly_LI2eJudCkaH5fX324vnUlpAF8vjlXta0usAuUPsKFe_Em4ufXyhOZh9TDP5rjzc8v6jBPDCZz5Q0EgihKm1I/s639/webtool-accessrule.png)

# 登入控制項

ASP.NET 提供許多驗證或管理使用者的控制項，以簡化登入功能的程。與登入相關的控制項，共有７個：  

1. [CreateUserWizard](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.createuserwizard.aspx) ：建立帳號的頁面。
2. [Login](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.login.aspx) ：登入頁面。
3. [LoginView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.loginview.aspx) ：
4. [LoginStatus](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.loginstatus.aspx) ：提供登入／登出連結。
5. [LoginName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.loginname.aspx) ：提供登入者名稱
6. [PasswordRecovery](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.passwordrecovery.aspx) ：提供登入者密碼還原的頁面。
7. [ChangePassword](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.changepassword.aspx) ：提供登入者變更密碼的頁面。

底下四個主題，分別介紹如何使用登入控制項，在不撰寫程式碼的原則下，設計可以執行「建立使用者」，「登入」，「密碼變更」，「密碼復原」功能的頁面。  

## 註冊頁面

You can use the [CreateUserWizard](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.createuserwizard.aspx) control to create a page that allows users to create their own accounts using the standard ASP.NET membership.   This control can be added to a page and will automatically work with the provider talking to ASPNETDB.  

這個 [CreateUserWizard](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.createuserwizard.aspx) 控制項，也會依據 [MembershipProvider](http://msdn.microsoft.com/zh-tw/library/9x1zytyd.aspx) 中的屬性設定值來當做密碼強度的驗證基礎。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhVi9Gdi8YSw86XNLlP6sR32A0Ddk5oPDBq1cIAX4BRFvh7_0XOCVGLx9DVhePUcfCOuiWDmLu1FykR8oefTYo5Jj0Ec_79Bo4W0dnw7w3nMWVNmT5b2SEBmzff4IGfid1XI1bxRQrbBRg/s417/CreateUserWizard1.png)![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEif97CUD4V8ljIB4EPb-RGRcYOhWmJbeTob0vXVH6dO3u_jUBNPvFfNRdlHwEg0K4I0TMy97U9Zy7C5P1wbWRPVi6On8sTggG5WA1UlrlzbYHlqcWAOzFhIr3hsu0cbimI0foWrgXYGq98/s307/CreateUserWizard2.png)
```xml
<asp:CreateUserWizard ID="CreateUserWizard1" runat="server" 
DisplaySideBar="True" 
ContinueDestinationPageUrl="~/UserHome.aspx"
oncontinuebuttonclick="CreateUserWizard1_ContinueButtonClick" 
oncreateduser="CreateUserWizard1_CreatedUser">
<TextBoxStyle Font-Names="細明體" />
<SideBarStyle VerticalAlign="Top" />
</asp:CreateUserWizard>
```

#### 事件

- [ContinueButtonClick](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.createuserwizard.continuebuttonclick.aspx) ：這個事件發生於使用者按一下最終使用者帳戶建立步驟中的 [繼續] 按鈕時。
- [CreatedUser](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.createuserwizard.createduser.aspx) ：個事件發生於建立新的網站使用者帳戶之後。可以用來繼續設定額外的會員資料。

#### 屬性

- [ContinueDestinationPageUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.createuserwizard.continuedestinationpageurl.aspx) ：設定使用者在完成註冊後使用者會造訪的網頁 URL，例如首頁或成員網頁。
- [EditProfileText](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.createuserwizard.editprofiletext.aspx) ：設定連往使用者設定檔編輯頁之連結的文字標題。
- [EditProfileUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.createuserwizard.editprofileurl.aspx) ：設定使用者設定檔編輯頁的 URL。

## 登入頁面

This [Login](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.login.aspx) control is used to prompt a user for his or her credentials.   The [Login](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.login.aspx) control also includes features for validation to ensure the user types a user name and password.  

### 設定預設的登入頁

在建立登入頁面前，必須先在 web.config 中指定登入頁名稱 (loginUrl) ，若沒有指定，預設使用 login.aspx 。
```xml
<authentication mode="Forms">
<forms loginUrl="Login.aspx" />
</authentication>
```

### 建立 Login 控制項

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgkrrH43z-eO5IB8NCosL935B2B_w5WjbOe72o47oKKLi1gGo324_kRrEJy5CBW5l0sqqE7DLiKELCgYr2fsiStl7W23ifY1MLQfs3Bm3P-uQBD9zR3iAqMMfhv7yHoYcGoea-Fe8blPTE/s467/login-control.png)
```xml
<asp:LoginView ID="LoginView1" runat="server">
<AnonymousTemplate>
請輸入登入資料
</AnonymousTemplate>
<LoggedInTemplate>
<asp:LoginName ID="LoginName1" runat="server" />您好.
<asp:LoginStatus ID="LoginStatus1" runat="server" />
</LoggedInTemplate>
</asp:LoginView>

<asp:Login ID="Login1" runat="server" 
DisplayRememberMe="true"
RememberMeSet="true" 
CreateUserText="請按這裡立即註冊" CreateUserUrl="~/Register.aspx"
PasswordRecoveryText="忘記密碼?" PasswordRecoveryUrl="~/RecoverPassword.aspx"
VisibleWhenLoggedIn="false"
PasswordLabelText="請輸入密碼:" 
UserNameLabelText="請輸入名稱:">
<TextBoxStyle Font-Names="細明體" />
</asp:Login>
```

### 設定 Login 控制項的驗證欄位

這個控制項中的每個屬性都有各自的驗證控制項，如果不符合設定，就會出現星號提示。  你也可以再自行增加一個 [ValidationSummary](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.validationsummary.aspx) 控制項，以顯示實際的驗證錯誤訊息。  要設定 [ValidationSummary](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.validationsummary.aspx) 控制項，只要將其 [ValidationGroup](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.validationgroup.aspx) 屬性設成 [Login](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.login.aspx) 控制項的 ID 即可。  
```xml
<asp:Login ID="Login1" runat="server" onloggingin="Login1_LoggingIn"></asp:Login>
<asp:ValidationSummary ID="ValidationSummary1" runat="server" ValidationGroup="Login1" />
```

### 使用自己的會員機制驗證

若已有現成的會員資料庫系統，若想搭配 [Login](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.login.aspx) 控制項，則可以在 [Authenticate](http://msdn.microsoft.com/zh-tw/library/system.web.security.formsauthentication.authenticate.aspx) 事件撰寫驗證邏輯。  這個事件會由使用者按下「登入」按鈕時觸發。：
```c#
protected void Login1_Authenticate(object sender, AuthenticateEventArgs e)
{
string sUserID;
if (MyValidate(Login1.UserName, Login1.Password, out sUserID))
{
//導到預設網頁 FormsAuthentication.DefaultUrl
bool isPersistent = Login1.RememberMeSet;
FormsAuthentication.RedirectFromLoginPage(sUserID, isPersistent);

//isPersistent=false, 則使用暫時性 Cookie ，也就是將 Cookie 存放在記憶體之中，瀏覽器關掉就自動清除。
}
}
```

## 密碼復原

The [PasswordRecovery](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.passwordrecovery.aspx) control assists users if they forget their password.   This control enables users to type their user name and receive a new, random password via e-mail. E-mails are sent based on the configured e-mail provider in Web.config.   Optionally, users can also be required to answer a security question before their password is sent.  
```xml
<system.net>
<mailSettings>
<smtp from="user@gmail.com">
<network host="smtp.gmail.com" userName="user@gmail.com" password="pwd" port="587" enableSsl="true"/>
</smtp>
</mailSettings>
</system.net>
```
```xml
<asp:PasswordRecovery ID="PasswordRecovery1" runat="server">
</asp:PasswordRecovery>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEie4rhUbGD0adVuXslXl8nOxGi3oT8_YhdTH6DPM2CCH-7-XsSv40zJQqlrKoESV4DWhDhhMb3lO-quBzjJHfgqTgYy5rwaTpInsQKgbtPCN5stTifSB2d4itvv2oK-WntEGInSeE0syQQ/s361/PasswordRecovery1.png)![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgnMJDBQGhDqxgQRIMAZUFYVEeTeXxPjS7T_Gu_HnoKmRe2ZapEecHE5KmnfxAXTzfMEF38hzElI0c0ks_4EKMOkRatQVTSGjJxCaqcwQOeEymUiWi5Nr7WWNs94kCkj51MwE46FNhKStg/s218/PasswordRecovery2.png)

這個控制項提供幾個事件，可以讓我們記錄使用者輸入的資訊。  

- [PasswordRecovery.UserLookupError](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.passwordrecovery.userlookuperror.aspx) ：找不到使用者所輸入的使用者名稱。
- [PasswordRecovery.AnswerLookupError](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.passwordrecovery.answerlookuperror.aspx) ：當使用者輸入不正確的密碼復原確認問題的解答時。

## 密碼變更

This [ChangePassword](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.changepassword.aspx) control allows users to enter their current password and create a new one.   On completion, you can either show a success message or automatically navigate to another page.   To enable the latter scenario, you can set the [SuccessPageUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.changepassword.successpageurl.aspx) property of the [ChangePassword](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.changepassword.aspx) control.  
```xml
<asp:ChangePassword ID="ChangePassword1" runat="server" 
SuccessPageUrl="~/UserHome.aspx" 
EditProfileText="修改個人資料" 
EditProfileUrl="~/UserHome.aspx"  >
<SuccessTemplate>
</SuccessTemplate>
</asp:ChangePassword>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg0jhuWHvBl7aFYUwzr5y522aOp9Jh_UH4Df7sNDgVHev_5OI-ZLUUtAngT6V6NA7Lj6FBXSNnbDinK3W6ceA4QwNFgoyOqTJStXJtDdlhqRXUco_4P43ktyPfulMwa1YfPgvTbJqWDA1g/s305/ctl-ChangePassword.png)

# 使用程式碼建立會員資料

由上一節介紹知道，只要透過登入控制項，就可以不用寫任何程式碼，就可以做到建立與管理會員資料的功能。  如果想要完全由程式碼來操作，當然也可以，只要透過 [Membership](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.aspx) 和 [Roles](http://msdn.microsoft.com/zh-tw/library/system.web.security.roles.aspx) 類別，就可以使用程式嗎來管理會員與角色資料。  

## The Membership Class

[Membership](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.aspx) 是一個靜態類別，可以用來建立、刪除、尋找使用者的資料。它提供以下方法：

- [CreateUser](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.createuser.aspx) ：
- [DeleteUser](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.deleteuser.aspx) ：
- [FindUsersByEmail](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.findusersbyemail.aspx) ：
- [FindUsersByName](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.findusersbyname.aspx) ：
- [GeneratePassword](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.generatepassword.aspx) ：This method creates a random password of the specified length.
- [GetAllUsers](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.generatepassword.aspx) ：
- [GetNumberOfUsersOnline](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.getnumberofusersonline.aspx) ：
- [GetUser](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.getuser.aspx) ：This method returns a MembershipUser object representing the current logged-on user.
- [GetUserNameByEmail](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.getusernamebyemail.aspx) ：
- [UpdateUser](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.updateuser.aspx) ：
- [ValidateUser](http://msdn.microsoft.com/zh-tw/library/system.web.security.membership.validateuser.aspx) ：當使用自訂的登入頁面時，可以使用這個方法，來驗證所提供的使用者名稱和密碼是否有效。

## The Roles Class

[Roles](http://msdn.microsoft.com/zh-tw/library/system.web.security.roles.aspx) 也是一個靜態類別，  主要功能有新增或移除角色中的成員、建立或刪除角色、判斷使用者的角色。  它提供以下方法：

- [AddUserToRole](http://msdn.microsoft.com/zh-tw/library/system.web.security.roles.addusertorole.aspx) ：
- [AddUserToRoles](http://msdn.microsoft.com/zh-tw/library/system.web.security.roles.addusertoroles.aspx) ：
- [CreateRole](http://msdn.microsoft.com/zh-tw/library/system.web.security.roles.createrole.aspx) ：
- [DeleteRole](http://msdn.microsoft.com/zh-tw/library/system.web.security.roles.deleterole.aspx) ：
- [GetAllRoles](http://msdn.microsoft.com/zh-tw/library/system.web.security.roles.getallroles.aspx) ：
- [IsUserInRole](http://msdn.microsoft.com/zh-tw/library/system.web.security.roles.isuserinrole.aspx) ：判斷使用者是否在指定的角色中。
- [RemoveUserFromRole](http://msdn.microsoft.com/zh-tw/library/system.web.security.roles.removeuserfromrole.aspx) ：
- [RoleExists](http://msdn.microsoft.com/zh-tw/library/system.web.security.roles.roleexists.aspx) ：指出指定的角色名稱是否已存在於角色資料來源中。
- ... ：

[Roles](http://msdn.microsoft.com/zh-tw/library/system.web.security.roles.aspx) 類別是用來管理會員機制的群組，若網站是使用 Windows 認證，是不可以用 [Roles](http://msdn.microsoft.com/zh-tw/library/system.web.security.roles.aspx) 來管理 Windows 使用者群組的。

# 自訂 MembershipProvider

當預設的會員機制不夠需求，又不想完全實作會員機制，就可以利用繼承 MembershipProvider 的方式，來擴充該機制的功能。  可以參考底下幾篇文章的做法。  

- [http://www.codeproject.com/Articles/165159/Custom-Membership-Providers](http://www.codeproject.com/Articles/165159/Custom-Membership-Providers)
- [http://mywct.pixnet.net/blog/post/38140863-%E8%87%AA%E8%A8%82-membershipprovider](http://mywct.pixnet.net/blog/post/38140863-%E8%87%AA%E8%A8%82-membershipprovider)
## 參考資料  

- [ASP.NET 登入控制項](http://msdn.microsoft.com/zh-tw/library/d51ttbhx%28v=vs.80%29.aspx)
- 
-