---
title: ASP.NET 驗證機制
layout: default
parent: IIS
nav_order: 2
description: "ASP.NET 驗證機制"
date: 2012-04-19
tags: [MISC,IIS]
postid: "4820322277335482910"
---
# 認識網站驗證機制與ASP.NET執行時的身份識別

### ASP.NET 的驗證機制

IIS針對網頁的認證提供了３種機制：Windows、From、Passport。加上不認證 None ，所以共有四種選擇。  要變更驗證機制，可以透過 IIS 的管理服務工具來設定，也可以在網站的組態檔中變更設定值。  
```xml
<system.web>
<authentication mode="Windows"/>
</system.web>
```

### Windows識別  vs  User識別

#### Windows識別

**Windows識別**是指作業系統對處理序執行身份的識別。

#### User識別

**User識別**是指IIS對登入使用者身份的識別。

當 IIS 啟動時，是以 **NETWORK SERVICE** 這個帳戶身份執行的，所以即使 UserA 具有 admin 身份，當他登入網站時，也不見得就能透過網站存取特定的資料夾。  因為此時能否存取特定資料夾的權限，是取決於 **NETWORK SERVICE** 這個帳戶。  

雖然使用 Windows 驗證模式時，ASP.NET 會將登入使用者的身份儲存在 WindowsIdentity 物件裡，但是它並不是用來提供給作業系統的 Windows 識別。  提供給作業系統的 Windows 識別是用於使用權限檢查，例如存取 NTFS 檔案系統，或是用於使用整合式安全性連接至資料庫。  依照預設，這個 Windows 識別是執行 ASP.NET 處理序的識別，也就是 **NETWORK SERVICE** 這個帳戶。  

不同版本的 IIS ，這個服務帳戶各有不同，請參考這篇這篇。
```c#
protected void Page_Load(object sender, EventArgs e)
{
WindowsIdentity currentIdentity = WindowsIdentity.GetCurrent();
Response.Write("</BR>執行網站程式的帳號：" + currentIdentity.Name);
Response.Write("</BR>登入者的帳號：" + User.Identity.Name);
//執行網站程式的帳號：NT AUTHORITY\NETWORK SERVICE
//登入者的帳號：VITO-2011W7\vito 
}
```

不過，這個預設的 NETWORK SERVICE 帳戶，其預設的權限就大到嚇人了。  預設的 C:\Inetpub\wwwroot 目錄，因為系統在建立時就已經限制了部分權限，所以還算安全。  但是，如果將網站安裝在非預設網站目錄下（例如： D:\Website ），且目錄權限又沒有特別設定過的話，該目錄就會直接繼承 D:\ 目錄下所定義的權限。  也就代表著不管你是用 NETWORK SERVICE 或 IUSR\_MachineName 身份執行任何程式(PHP, ASP, ASP.NET, ISAPI, CGI, ...)，其實都可以任意「建立資料夾」、「附加資料」、「建立檔案」、「寫入資料」到你的網站目錄內 ( 也包括以外的目錄 )。  可想見網站目錄若套用預設權限的設定有多危險，風險指數極高！例如以下的範例程式碼，一般狀況下是可以寫入的。  
```c#
protected void Page_Load(object sender, EventArgs e)
{
StreamWriter writer = File.AppendText(@"C:\temp\test1.txt");
writer.WriteLine("Some data :" + System.DateTime.Now.ToString());
writer.Close();
}
```

若要安全的設定網站，可參考以下建議：

- 將主機單純用作 Web 用途，不要同時處理太多不同的任務
- 將 C:\ 與 D:\ 預設的 Users 與 Everyone 權限全部移除！
- 網站目錄必須明確拒絕匿名登入帳號的寫入權限，包括 NETWORK SERVICE 與 IUSR\_MachineName 兩個預設的群組。
- 僅開放真的需要透過 Web 上傳或寫入的目錄有「寫入」權限！
- 如果上傳或寫入的檔案不需要直接透過 Web 存取，建議不要將目錄放在 Web 可存取的目錄下！
- 若需透過 Web 介面上傳檔案至網站，建議限制上傳檔案的「副檔名」（需明確表列）
- 透過 Web 介面上傳檔案至網站目錄，如果不需要執行權限者，建議直接將該目錄的執行權限設定成「無」，避免駭客上傳可執行的指令檔 (如: \*.asp , \*.php , ... ) 把你的網站當成跳版!   如下圖示：  
![](http://127.0.0.1:8080/_images/cs/dd632964_image_3.png)

# 模擬 Windows 識別

您可以藉由啟用模擬，將 ASP.NET 應用程式的 Windows 識別設定為 IIS 所提供的 Windows 識別。  也就是說，對於 Windows 作業系統驗證的所有工作，包括檔案和網路存取在內，您可以指示 ASP.NET 應用程式模擬 IIS 所提供的識別。  若要啟用 Web 應用程式的模擬，請在應用程式的 Web.config 檔中，將 identity 項目的 impersonate 屬性設定為 true，如下列程式碼範例中所示。  
```xml
<system.web>
<authentication mode="Windows"/>
<identity impersonate="true" />
</system.web>
```
```c#
protected void Page_Load(object sender, EventArgs e)
{
WindowsIdentity currentIdentity = WindowsIdentity.GetCurrent();
Response.Write("</BR>執行網站程式的帳號：" + currentIdentity.Name);
Response.Write("</BR>登入者的帳號：" + User.Identity.Name);
//執行網站程式的帳號：VITO-2011W7\vito
//登入者的帳號：VITO-2011W7\vito 
}
```

有時為了在伺服器端列印或是連接網路磁碟等因素，必須使用不同的身份時，也可直接指定模擬特定的使用者，例如以下設定：
```xml
<system.web>
<authentication mode="Windows"/>
<identity impersonate="true" username="vxxxxxxo" password="12345678"/>
</system.web>
```
```c#
protected void Page_Load(object sender, EventArgs e)
{
WindowsIdentity currentIdentity = WindowsIdentity.GetCurrent();
Response.Write("&lt/BR>執行網站程式的帳號：" + currentIdentity.Name);
Response.Write("&lt/BR>登入者的帳號：" + User.Identity.Name);
//執行網站程式的帳號：VITO-2011W7\vxxxxxxo
//登入者的帳號：VITO-2011W7\vito 
}
```

上面這個設定，會讓整個網站的執行身份都變成這個user，比較容易有安全性漏洞。另外就是在程式碼中才變換使用者的身份。

# 網站中設定局部目錄或網頁不為Windows整合認證

有時候，網站中並不是所有頁面都有套用Windows認證的必要，就可以將某些目錄或網頁設定為不須要Windows整合認證。

- 選定欲變更的目錄或檔案
- 執行 IIS -&gt; 「驗證 (authentication) 」功能
- 啟用「匿名驗證」
- 停用「Windows驗證」

![](http://127.0.0.1:8080/_images/cs/partial_windows_authentication_1.png)

![](http://127.0.0.1:8080/_images/cs/partial_windows_authentication_2.png)


- [ASP.NET 安全性架構](http://msdn.microsoft.com/zh-tw/library/yedba920.aspx)
- [IIS 驗證](http://msdn.microsoft.com/zh-tw/library/aa292114%28v=vs.71%29.aspx)
- [ASP.NET 驗證](http://msdn.microsoft.com/zh-tw/library/aa291347%28v=vs.71%29.aspx)
- [Windows 驗證提供者](http://msdn.microsoft.com/zh-tw/library/907hb5w9.aspx)
- [表單驗證提供者](http://msdn.microsoft.com/zh-tw/library/9wff0kyh.aspx)
- [如何在 ASP.NET 應用程式中實作模擬](http://support.microsoft.com/kb/306158/zh-tw)
- [ASP.NET － Winodws驗證](http://blog.xuite.net/sugopili/computerblog/22005160)