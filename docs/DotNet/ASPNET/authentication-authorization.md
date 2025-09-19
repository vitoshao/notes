---
title: Authentication and Authorization
layout: default
parent: Dot Net
description: "Claims-based 與 Token-Based 授權"
date: 2025-02-20
tags:
  - authentication
  - authorization
---

Http 是一種無狀態的通訊方式，Server 端為了記錄使用者狀態，大柢透過以下幾種方式儲存用戶識別資料。

- Cookie-based authentication
  
  Server 端在驗證後，將必要的識別資訊透過 cookie 發送至用戶端，用以識別使用者的做法。

- Session-based authentication
  
  Server 端在驗證後，將必要的識別資訊使用 Session 記錄下來，用以識別使用者的做法。

- Token-based authentication

  Server 端在驗證後，將必要的識別資訊，以 Token 型式傳送給用戶端。後續，如果用戶端想要存取資源時，都必需要發送此令牌。
  令牌通常包含有關使用者、用戶端、身份驗證時間戳記和其他具有唯一 ID 的有用資訊。

- Claims-based authentication

  這與 Token-based 的身份驗證相同，只是它在 Token 中添加更多的用戶資料（如角色，權限等等），這些資料與授權相關，涉及客戶端是否有權限對資源進行操作。

## 驗證

在 ASP.NET Core 中, 「驗證」是由 [IAuthenticationService](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.authentication.iauthenticationservice?view=aspnetcore-9.0) 這個中介的驗證服務來處理。
我們必須在這個服務中註冊必要的驗證處理程序，驗證處理程序就會負責提供 ClaimsPrincipal，以作為授權的依據。

當一個 HTTP request 傳到 .Net 主機端，都會產生 HttpContext 物件，這個物件會存有目前 request 的相關資訊，
HttpContext 物件裡頭有一個叫做 User 屬性（型別為ClaimsPrincipal），這個屬性值就是由 UseAuthentication() 引入的驗證處理程序產生的。

若使用 Cookie 當作驗證配置，驗證處理程序就會依據收到 Cookie 內容產生 ClaimsPrincipal，若使用 Bearer，則會依據 Header 中的 JWT token 產生 ClaimsPrincipal。

### 設定驗證配置

已註冊的驗證處理常式及其設定選項稱為「schemes」（驗證配置），也就是一種驗證使用者的方法。

下面這段程式碼就是在設定驗證配置方式，
它告知 Authenticate 要使用 JwtBearerDefaults.AuthenticationScheme 當做預設的驗證配置（DefaultScheme），這個驗證配置會針對 cookie 及 JWT 持有人進行驗證程序。

在 Program.cs 指定驗證配置

```c#
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme,
        options => builder.Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme,
        options => builder.Configuration.Bind("CookieSettings", options));
```

這段程式碼，指定了二個驗證配置，並同時指定使用 Bearer 當作預設的驗證配置。
如果只註冊單一驗證配置時，可以不指定預設的驗證配置，因為它會自動變成預設配置

### 啟用驗證功能

最後在 Program.cs 中，呼叫 UseAuthentication ，它會依據上面指定的驗證配置，啟用驗證處理常式。
```c#
app.UseAuthorization();
```

## 授權

### Claim-Based Authentication

當我們在開發一個 Claim-Based Identity 的 asp.net core web應用程式，在程式運行的過程當中，
我們讓用戶輸入帳號密碼，來判斷用戶身分是否正確，這個過程就是驗證(authentication)，
完成驗證之後，伺服器端會建立出一個或多個 Claim，在 Claim 裡頭就包含著可以識別用戶的資訊，
然後，系統會把這些 Claim 包裝起來(變成cookie)，傳遞到用戶端。

接著，當用戶未來透過瀏覽器與伺服器端互動時(例如 : 查詢或輸入資料、上傳檔案…etc.)，
瀏覽器都會在http header中自動帶著這個 cookie，
如此一來，伺服器端(的網站)就可以透過該 cookie 中的資訊(Claim)，來識別當前的用戶是誰。

Program.cs 
```c#
builder.Services
    .AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(option =>
    {
        option.LoginPath = new PathString("/Account/Login"); 
        option.ExpireTimeSpan = TimeSpan.FromMinutes(30);                                                                  
        option.SlidingExpiration = false;

app.UseAuthorization();
```
Account Controller
```c#
[HttpPost]
[ValidateAntiForgeryToken]
public IActionResult Login(DtoLogin model)
{
    if (!ModelState.IsValid)
    {
        return View(model);
    }

    //驗證登入
    Demo1.Models.SignInResult result = this.CheckPassword(model.CmpCode.Trim(), model.Account.Trim(), model.Password.Trim());

    if (result.SignInStatus == EmSignInStatus.Success && result.User is not null)
    {
        //1. 建立識別身份(ClaimsIdentity)
        Claim[] claims = [
            new Claim("Id", result.User.Id.ToString()),
            new Claim("UserName", result.User.UserName),
        ];
        ClaimsIdentity claimsIdentity = new(claims, CookieAuthenticationDefaults.AuthenticationScheme);//Scheme必填
        ClaimsPrincipal principal = new(claimsIdentity);

        //2. 實現登入，SignInAsync會將使用者資訊存入cookie，以便後續寫入瀏覽器
        HttpContext.SignInAsync(new ClaimsPrincipal(claimsIdentity));

        if (string.IsNullOrEmpty(model.ReturnUrl))
            return RedirectToAction("Index", "Home");
        else
            return Redirect(model.ReturnUrl);
    }
    else
    {
        ModelState.AddModelError(string.Empty, "登入失敗");
        return View(model);
    }
}
```

上面程式碼中的 SignInAsync() 就是登入機制的實作，它會把我們建立的 ClaimsPrincipal 以 cookie 的形式，傳到用戶端，寫入瀏覽器。
後續瀏覽器會拿著這個資訊，在每次與伺服器端交互的過程中，提供給伺服器端看，以便於讓伺服器端確認用戶的身分。

### Token-Based Authentication

Token-Based Authentication 在驗證後，一樣要建立 Claims 資訊，實作方式則是將 Claims 資訊當做 Jwt Token 的 Payload ，再將該 Token 回傳給用戶端。
後續用戶端每次與伺服器端交互時中，都必須將 Token 內容放入 Http Request 的 Header 裡頭，這樣伺服器端就可以透過該 Token 中的資訊(Claim)，來識別當前的用戶是誰。

Token 是一段經過編碼後的文字，在 Web 應用程式中，使用上不像 Cookie 那樣方便，但對於其他不能使用 Cookie 的應用程式，如 Ｎative, Mobile, API 等等情境，就特別合適。

如果想在 Web 應用程式中使用 Token 進行用戶端伺服器端互動，有個變通的方法，就是將 Token 儲存在 Session 中，然後在 Program.cs 中加入一段 Middleware，將這個儲存在 Session 中的 Token，寫入到 Request.Header 中，如此一來，伺服器端(的網站)就可以透過該 Header 中的 Token 資訊，來識別當前的用戶是誰。

Program.cs 
```c#
builder.Services.AddSession();
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,          //是否驗證Issuer
            ValidateAudience = true,        //是否驗證Audience
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidateActor = true,
            ValidIssuer = builder.Configuration["AppSettings:JwtSettings:Issuer"],
            ValidAudience = builder.Configuration["AppSettings:JwtSettings:Issuer"],

            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(builder.Configuration["AppSettings:JwtSettings:SignKey"]!))
        };
    });

...

app.UseSession();
app.Use(async (context, next) =>
{
    var JWToken = context.Session.GetString("JWToken");
    if (!string.IsNullOrEmpty(JWToken))
    {
        context.Request.Headers.Append("Authorization", "Bearer " + JWToken);
    }
    await next();
});
app.UseAuthentication();
app.UseAuthorization();
```

Account Controller
```c#
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public IActionResult Login(VmLogin model)
{
    if (!ModelState.IsValid)
    {
        return View(model);
    }

    //驗證登入
    Demo2.Models.SignInResult result = this.CheckPassword(model.CmpCode.Trim(), model.Account.Trim(), model.Password.Trim());

    if (result.SignInStatus == EmSignInStatus.Success && result.User is not null)
    {
        //1. 建立識別身份(Token)
        var token = _tokenService.BuildToken(
            _appSettings.JwtSettings.SignKey,
            _appSettings.JwtSettings.Issuer,
            result.User);

        if (token != null)
        {
            //2. 將 token 寫入 Session
            HttpContext.Session.SetString("JWToken", token);
            return RedirectToAction("WhoAmI");
        }
        else
        {
            throw new Exception("Create Token Error");
        }
    }
    else
    {
        //登入失敗
        ModelState.AddModelError(string.Empty, "登入失敗");
        return View(model);
    }
}
```

以上測試程式碼，只是為了了解 Token-Based Authentication 的運作方式，開發網站時，我想應該不會有人會採用這樣的認證機制。
以上範例可以在 [Github](https://github.com/vitoshao/DemoAuthentication) 下載。

## 參考資料
- <a target="_blank" href="https://learn.microsoft.com/en-us/aspnet/core/security/authentication/?view=aspnetcore-9.0">Overview of ASP.NET Core authentication</a>
- <a target="_blank" href="https://www.codemag.com/Article/2105051/Implementing-JWT-Authentication-in-ASP.NET-Core-5">Implementing JWT Authentication in ASP.NET Core 5</a>
- <a target="_blank" href="https://blog.miniasp.com/post/2022/02/13/How-to-use-JWT-token-based-auth-in-aspnet-core-60">如何在 ASP.NET Core 6 使用 Token-based 身份認證與授權 (JWT)</a>
- <a target="_blank" href="https://studyhost.blogspot.com/2023/10/claim-based-identity.html">什麼是Claim-Based Identity</a>