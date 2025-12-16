---
title: Debugging 網站
layout: default
parent: 除錯與系統監控
nav_order: 1
description: "Debugging 網站"
date: 2012-10-17
tags: [Web,ASPNET,除錯與系統監控]
postid: "5798565542142164129"
---
# Configuring ASP.NET for Debugging

To configure ASP.NET for debugging,   there are two areas where you set this information: the project's property page and the Web.config file.

## 1. Activate the ASP.NET Debugger

The first step is to enable the ASP.NET debugger in your project's Property Pages dialog box.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjX42kYmvLBoLL76E6rMPtNilSiPlrJehEONHpu3q69YCupG0qELnj0KRBkwR6NQHAVzeQQjaJ2QOsI6ctYZsXTPMVsAjhJtxnXT-n1TVDQUvcOrV2PmLX1zHrBOYCfF6IM25clgltwiBA/s850/debug-aspnet-enable.png)

## 2. Configure Debugging

The second step is to enable debugging either for your entire site or on a page-by-page basis.

#### for entire site
```xml
<configuration>
<system.web>
<compilation debug="true">
</compilation>
<system.web>
</configuration>
```

#### for page-level debugging
```xml
<%@ Page Debug="true" ... %>
```

# Defining Custom Errors

## Configuring a Custom Site-Level Error Page

在 web.config 指定錯誤頁的網址。例如：
```xml
<customErrors mode="On" defaultRedirect="~/ErrPage.aspx" />
```

底下是相關屬性說明：

- [defaultRedirect](http://msdn.microsoft.com/zh-tw/library/h0hfz6fc.aspx) ：指定發生錯誤時，要將瀏覽器導向至的預設 URL。
- [mode](http://msdn.microsoft.com/zh-tw/library/h0hfz6fc.aspx) ：顯示錯誤訊息的模式  

- On ：啟用自訂錯誤。如此 ASP.NET 的標準詳細錯誤就不會顯示。
- Off：停用自訂錯誤。如此 ASP.NET 的標準詳細錯誤可在用戶端和本機端顯示。
- RemoteOnly：(預設值)。如此 ASP.NET 的標準詳細錯誤僅會在本機端顯示，用戶端顯示自訂錯誤。

當導向發生時，伺服器會將錯誤頁的網址透過 QueryString 以 **aspxErrorPath** 名稱傳遞給指定的 Error Page 。如下所示：

http://localhost/examples/SiteErrorPage.aspx?aspxerrorpath=/examples/Default.aspx

## Configuring Error-Specific Error Pages

針對不同的狀態碼(status code)也可以定義不同的錯誤頁。例如：
```xml
<configuration>
<system.web>
<customErrors defaultRedirect="SiteErrorPage.aspx" mode="RemoteOnly">
<error statusCode="403" redirect="RestrictedAccess.aspx" />
</customErrors>
<system.web>
</configuration>
```

### Error Status Codes

關於錯誤的狀態碼，位在 4xx 和 5xx
&lt;!--#include file="../C01_ASPNET_WebSite/status-code.htm" --&gt;

# Debugging Remotely

透過 Remote Debugging Monitor (**Msvsmon.exe**) 工具，可以進行遠端除錯，這個工具位在開發環境安裝目錄中，例如：Program Files\Microsoft Visual Studio 9.0\Common7\IDE\Remote Debugger\x64 。  會使用這個方法除錯，最常使用這個除錯技巧，大都是遇到 bug 只會發生在主機上，但又沒辨法在 Server 上安裝 VS.NET 時。  它主要原理是在 Server 上執行 Msvsmon.exe ，建立起一個除錯的橋樑。  然後再開啟本機上的 VisualStudio ，選擇「附加至處程序」功能，就可以透過這個橋樑進行遠端的偵錯。  如果偵錯的對象是 IIS, 那就點選「**w3wp.exe**」處理序，以進行 ASP.NET 網頁的遠端偵錯。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgmJtNN1CWWhAyXEAN-l90JZ_H9dJIhDPzryIfxl3Efosizgl1RkFm6WQ8M1PyJMB4v6sWdxXiMTx4e5lFDeex_yO25ugqrYz-uts3SNozGfKCNM0U6clbJPKQ55SbXnxSedSVLT9qrybs/s334/debug-append-to-process.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgQvBpNLnioDbj1WnkEjurcxbbgk1O38_EAkFYEBzN26dDGrlndJQ5C7cvMO3fgVfBIFNLW-ZiAzhQDzIWvLakWpGdsGFIdUqGgpBq2kNh3MNrAeASVbrA5VYcGk605RcgWWRJ-n0iBZ0I/s850/debug-append-to-process2.png)

詳細做法可參考：

- [VS2010 遠端偵錯](http://www.dotblogs.com.tw/orhuang/archive/2011/12/26/63461.aspx)
- [Remote Debug in VS.NET 2008](http://www.dotblogs.com.tw/rainmaker/archive/2009/10/15/11072.aspx)

# Debugging Client-Side Script

Visual Studio 也允許對 java-script 進行除錯，不過必須先停用以下功能，就可以一行一行的執行 java-script 程式嗎：  

IE: 工具 → 網際網路選項 → 進階 → 瀏覽 → 停用指令碼除錯  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEikCa69ynBX7PkaANBkcv_X6OaWmPs4k26SOe-8vVJMpWihcjMRz9EzPuk_eSO-6sg5pjyoyxmArUnYrtfL3UTQy-jVBykATXWvm3zjLhtnmhYDKOF0K4FHvVXWoUuQAeg75Jxynm91Zn8/s434/ie-stop-debugging.png)

# Break When an Exception is Thrown

當指令碼發生錯誤時，如果你沒有處理例外狀況，這個例外狀況可能就無法被測得。  如下面範例，如果發生錯誤，你將無法欄截到。  
```c#
protected void btnSQException_Click(object sender, EventArgs e)
{
try
{
SqlConnection conn = new SqlConnection(@"Data Source=.;Database=GUARANTEED_TO_FAIL");
conn.Open();
}
catch (SqlException ex)
{

}
}
```

所以，在 debug 時，你可以將 Visual Studio 的 debugger 設定成「若發生例外錯誤即中斷」。  也就是任何一行程式碼產生錯誤時，程式會立即中斷在該行程式碼。  這個設定是針對特定的例外類型做設定，你可以參考下列的設定步驟：  

開啟 Exceptions Dialog Box   
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg4oYlYwdXcnKNv7WfgIw1tFdHOuoHGBZ_7JfOvPVoNWs50BHb03cywD3mBatBST6CQbymXg_sxW58aawtngLubjXJXxuF7ciWvf4gtRMhvUxH2QfMho9HRi1FY39muzdmfJNNcAdEoEhI/s346/debug-exception.png)

勾選必須中斷的錯誤類型   
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjR1Vza1xrAS4ow7X4oEwQBbJz6QCU2kB23QkMEfo_PBJIR56JL7MObFWfaQ8oJJbGJbvPyvMeRMIbXSwBe5cjakj5K5X_-gJ_2OKwBcHmgLpVG_dG33RV4kCFkd6ovkYWaBMMYuZUnys4/s746/debug-exception-throw.png)

當發生錯誤時，程式立即停在錯誤的程式碼  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj6aftomOTyhtDFETSkfu4Kd4FSrd1rDdb5wy_ss5-5AYVMirEYxavxoT4Z0WNMAbzTjINBbHtbCWf7qQdbWbvoW48d8fHojcVL2KjnWv4j5VwmBfca3xeEvkQ7cxmE1CDZrf2noNN62n8/s328/debug-throw.png)