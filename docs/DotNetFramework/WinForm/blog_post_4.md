---
title: WebBrowser 三二事
layout: default
parent: WinForm
nav_order: 4
description: "WebBrowser 三二事"
date: 2015-08-27
tags: [DotNetFramework,WinForm]
postid: "4796444140058292498"
---
[WebBrowser](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.aspx) 是 Windows Form 應用程式中用來顯示 Web 網頁的控制項。  底下幾個與 [WebBrowser](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.aspx) 相關的議題，做個記錄備忘一下。  

## 如何設定使用新版的IE

使用 [WebBrowser](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.aspx) 時，它會依據 .Net Framework 版本，自動使用其預設的 IE 版本。  例如可能你的主機已經更新到 IE10 ，可是 [WebBrowser](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.aspx) 還在使用 IE9 或 IE8 的版本。  如果要取得目前運行的 IE 版本，可以透過 [WebBrowser.Version](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.version.aspx) 屬性。  

### 變更預設版本

[WebBrowser](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.aspx) 不會因為系統已更新了新的 IE 版本，就自動使用新的 IE 版本。你必須自行變更系統的註冊碼，讓 Windows 系統知道應用程式想使用的 IE 版本。  
32 bit: 
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Internet Explorer\MAIN\FeatureControl\FEATURE_BROWSER_EMULATION 

64 bit: 
HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Internet Explorer\MAIN\FeatureControl\FEATURE_BROWSER_EMULATION  
在不同版本的系統下，你可以依據上面資訊找到 FEATURE\_BROWSER\_EMULATION 機碼。  若特定的應用程式，要使用特定的 IE 版本，你只要在該機碼下新增一個與應用程式相同名稱的 Dword 值，設定的值可參閱 [Internet Feature Controls (B..C)](https://msdn.microsoft.com/en-us/library/ee330730%28v=vs.85%29.aspx#browser_emulation) 這篇文章，不同的值會對應到不同的 IE 版本。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiDCsHMmr-wdVCu8oaVDPJMxTAbmgyd8iXNi60_OLlKQYA90UI-s4dJKtzrLBWHdjCzOt_dENq9QYq7kDR651sVXVlY_xdn_ugcXcs3VCK6GD3fz6laoPXify75NXd7ZLicZc4vpEhZR9w/s538/web-browser-ie-versions.png)

像下圖中的例子，就是指定 MyTrip.exe 應用程式，使用 IE11 版本  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgBzHMnAVDrOujsiVIvCQsLv8enaWL7hjwUHbqWgSyFScrQU2iM5nnh8N9bpgwG4tBJCrD4J2VDCJOymYg6Oz9NTrhrSK8N4ooayTK2KFxAgSueJDQge3BlyKDa8Tl3DMAMWNI-Ej6vUVM/s755/web-browser-default-version.png)

### 使用程式修改預設版本

為了方便設定，也可以透過程式碼，直接進行以上註刪碼的變更。  
```c#
public Form1()
{
InitializeComponent();

var appName = Process.GetCurrentProcess().MainModule.ModuleName;
Registry.SetValue(@"HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Internet Explorer\MAIN\FeatureControl\FEATURE_BROWSER_EMULATION", appName, 11000, RegistryValueKind.DWord);
}
```

## 如何在不同的 WebBrowser 控制項中使用獨立的 Cookie

有時候，你必須**同時**使用多個 [WebBrowser](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.aspx) 連結特定網站進行操作，  若這個網站使用 Cookie 儲存識別資訊，而你又必須分別使用不同使用者進行登入，那麼你將難以避免互相干擾的問題。  這是因為，只要在同一個 Process 中，所有的 [WebBrowser](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.aspx) 都會共用同一個 Cookie 資訊。  也就是若在同一個應用程式中，使用到多個 [WebBrowser](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.webbrowser.aspx) 控制項，不管它們是否位在同一個 [Form](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.aspx) 中，它們都將共用同一個 Cookie 資訊，  

目前不知道如何解決，只能先重覆執行多組應用程式，才可分別在不同表單中建立不同使用者連線。
## 參考資料  

- [WebBrowser 控制項概觀](https://msdn.microsoft.com/zh-tw/library/w290k23d%28v=vs.100%29.aspx)
- [C# 視窗程式：如何使用新版的瀏覽器控件？](http://cs0.wikidot.com/newbrowser)
-