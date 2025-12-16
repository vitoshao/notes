---
title: Windows驗證
layout: default
parent: 認證與授證
nav_order: 6
description: "Windows驗證"
date: 2013-04-23
tags: [Web,ASPNET,認證與授證]
postid: "7207689717652342329"
---
# 瞭解 ASP.NET 的執行身份識別

## 預設的 ASP.NET 執行識別

當 ASP.NET 在 IIS 伺服器上執行時，其工作處理序會有一個執行的識別，如下圖

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgYNSCK9Os-ZPIHjZVbWei4SwTs6CnPYricMJCdNZX8TEQBc1FmwItHxTESMay15xKkffHlG1xGkE-H3_ouA_Aa3FPbnlAB_bi8aWXe6GQ3fvckA2NuTZm1t77e1h4L04C6PWk0Y8Fn97w/s423/auth-w3wp-process-identity.png)

這個 ASP.NET 背景工作處理序的執行識別，使用不同的 IIS 版本會有所不同：

- 在 Microsoft Windows 2000 和 Windows XP Professional 上，是本機 **ASPNET** 帳戶。
- 在 IIS6 或 IIS7 的傳統模式下，預設是 **NETWORK SERVICE** 帳戶。
- 在 IIS7 整合管線模式下，取得的身份識別預設是所屬之 IIS 應用程式集區的識別，所以不同的應用程式集區會有不同的識別，如 IIS AppPool\DefaultAppPool .

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgOT6dpECKae8zI_TbU3wdHmhvMVv3_PsbDF7BRlkTOavM5bJvtUnWkRo1ztTULOphuO3BLm4mF07noZVSgIaWvIcnKUl7UMytBrnA9mHMnUjMc4nIK20jauifIML-QbVyucmwDYuM5BeI/s349/auth-app-pool.png)

## 變更 ASP.NET 的執行識別

若你不喜歡預設的執行識別，你也可以變更應用程式集區的執行識別：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjp8I7lgoRItr2LntpiH_IYc_U-ZA3Dz59Fx5mWn50pD8J8YT2m-ii1HGqBjxVo_-zf6G9y8MHkMw3fe1b5l78GNu1qZp3q3ax5KxMpo9HkvEA4ny7nicN8C7N0xVAEGm8hCxCZuARH4sQ/s850/auth-app-pool-identity-setting.png)

- 如果 AppPool 的識別設定為 ApplicationPoolIdentity ，這是管線模式下的預設值，其執行識別是 IIS APPPOOL\[AppPoolName
- 如果 AppPool 的識別設定為 NetworkService ，那就是 NetworkService
- 你也可以指定特定的帳戶。

【IIS APPPOOL\[AppPoolName】這是一個虛擬帳戶，若要對特定資料夾存取，要注意其存取權限，相關的設定可參考[保哥的這篇設定](http://blog.miniasp.com/post/2009/09/09/Introduce-IIS-75-Application-Pool-Identity-and-Virtual-Account.aspx#continue)。

# Windows 認證

## 啟用 Windows 認證

要啟用 Windows 認證，你可以在 IIS 中設定啟用 Windows 認證，也可以直接更改 Web.config 組態檔。
```xml
<authentication mode="Windows" />
```

## 使用匿名存取

匿名存取的預設值為 false。若要使用匿名存取，必須完成以下二個設定：

#### 1) 在 IIS 中設定啟用「匿名存取」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh6UQIIvkpBI9aTcbziM8gk9LYOG3HLKEljFGaXQN6iCFRGXbez66HREbmBIVTncd6WFlmTDYifPofjTPS6UQ5SNqbi0efkEwT8ZM-lgJsBgDHRcVR48RkCw5q0N2GI0ggWEw3-uKHqOzs/s258/auth-allow-anonymous.png)

#### 2) Web.config 組態檔也必須設定成允許所有使用者
```xml
<authorization>
<allow users="\*" />
</authorization>
```

## 以登入者身份模擬 ASP.NET 執行身份識別

要使用登入者身份來模擬 ASP.NET 執行身份識別，你可以在 IIS 中設定啟用「ASP.NET 模擬」，也可以直接更改 Web.config 組態檔。

在 IIS 中設定啟用「ASP.NET 模擬」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4Uq8-y0um78G8SYt0elWgWaaGdltNylSyCVeP4d0C7ltARpjsEvIsmZOID84Ueq6T4fqsb5HMUWJJLN4qhmjHzxX6vaB9s0BfSD8ZXoHpuc0l0PRozXA7iPny81QUlnZliQ6hcUwcvN0/s260/auth-enable-impersonate.png)

在 Web.config 組態檔中設定啟用「ASP.NET 模擬」

### 使用特定帳戶模擬
```xml
<identity impersonate="true" userName="vito" password="2213" />
```

### 以登入者帳戶模擬
```xml
<identity impersonate="true" />
```

### 使用匿名帳戶模擬

若你使用「ASP.NET 模擬」，又啟用「匿名存取」，則你會看到 500.24 錯誤。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhq13upE4GOxiaxtY6TnwGf9pxFmyFq1pMMlwJ_PXNQkFHIRm3y4_cEKs_uMkzJkrucg4P5modb5xY-fmBPacCkGJou95d7m5_chF9Of8lrhvDLVB1eZ8Ql-dcJ_klYnoGw-O99w8_u2mc/s555/auth-windows-impersonate-error.png)

這是因為在IIS7 的整合管線模式下是不允許的。有二個解決辦法：

1) 改用傳統模式

2) 修改 Web.config 組態檔

若要在整合管線模式兼顧傳統模式，則必須停用整合模式的驗證，設定如下：
```xml
<system.webServer>
<validation validateIntegratedModeConfiguration="false" />
</system.webServer>
```

## 取得執行識別的名稱

若你要取得ASP.NET執行識別的名稱，你可以使用以下程式碼：
```c#
string identityName = WindowsIdentity.GetCurrent().Name
```

若你要取得的是登入者身分識別，則你可以使用以下程式碼：
```c#
string userIdentity = HttpContext.Current.User.Identity.Name;
```

## 自動啟用整合式 Windows 驗證

當你連到一個使用 Windows 驗證的網站，系統通常會彈出訊問使用者資訊的小視窗，如下圖：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEVA1N14wixc8oMmIJigcvMBe93NukJasmNwCCmkpjlwSNB66WxfKmHhmz5DkWlp9Dk6dl8NOetTvVYpki7EiyADiTBQgMMFYgY3BWVugltMM9ez4PQQ-JI3L4E8JtT2FBbe5OnyNYWWk/s450/auth-windows-logon.png)

若你使用 IE 瀏灠器，只要變更以下設定就可以啟用自動驗證，如下圖：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEioxk7hYoh687ibAN7YUuvoW7FVvxh0ijyudNkkQZ-1o9kSzCRPruj5PKC0dxDM8Y97nZfO2ft85ne0hzLdbRCtgOwIvU19Lob-sykBh0fC5geZd1UkNCQ8OVfJOWBePYRb7mvo2QKz264/s393/auth-auto-windows-logon.png)

若你使用 FireFox 瀏灠器，可以參考[保哥這篇設定](http://blog.miniasp.com/post/2011/05/19/How-to-enable-Integrated-Windows-Authentication-in-Firefox-automatically.aspx)。

# ASP.NET執行識別 VS 使用者識別

#### Windows 認證

|  | 匿名存取 |
| --- | --- |
| 啟用 | 停用 |
| 模擬 | 啟用 | NT AUTHORITY\IUSR | domain\vito |
| 停用 | \* IIS APPPOOL\DefaultAppPool | \*  IIS APPPOOL\DefaultAppPool |

#### Form 認證

|  | Windows Identity | User Identity |
| --- | --- | --- |
| Windows 認證(impersonate="false") | IIS AppPool\DefaultAppPool | domain\vito |
| Windows 認證(impersonate="true") | domain\vito | domain\vito |
| Form 認證 | IIS AppPool\DefaultAppPool | formUser |
## 參考資料  

- [\[Will\] IIS 執行的身份識別與 Windows 權限控管不是你想的那樣](http://technet.microsoft.com/zh-tw/dd632964.aspx)
- [\[Will\] 如何讓 Firefox 瀏覽器自動啟用整合式 Windows 驗證](http://blog.miniasp.com/post/2011/05/19/How-to-enable-Integrated-Windows-Authentication-in-Firefox-automatically.aspx)
- [\[Will\] 介紹 IIS 7.5 的應用程式集區與新增的「虛擬帳戶」特性](http://blog.miniasp.com/post/2009/09/09/Introduce-IIS-75-Application-Pool-Identity-and-Virtual-Account.aspx#continue)
- [探討ASP.NET Run Account](http://pinnynet.blogspot.tw/2010/07/aspnet-run-account.html)
- [使用 IIS 驗證和 ASP.NET 模擬](http://msdn.microsoft.com/zh-tw/library/134ec8tc%28v=vs.100%29.aspx)