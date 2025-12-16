---
title: XML Web Service 認證
layout: default
parent: Web Service and WCF
nav_order: 7
description: "XML Web Service 認證"
date: 2013-03-11
tags: [Web,ASPNET,Web Service and WCF]
postid: "1751425726307273271"
---
Web Service 是一個開放式的架構，任何系統都可以存取。那麼如果有些服務必須限定存取權限，又該如何處理？  

其實 Web Service 就像一個 Web Page 一樣，也是由一個 URL 指向一個檔案，所以可以把它看成同 ASP.NET 資源一樣處理。  

下表摘要說明使用 ASP.NET 建置之 Web 服務可用的驗證選項。前面加上 Windows 的選項是使用 ASP.NET 建立之 Web 服務可用的 Microsoft Windows 驗證選項部分。

| 驗證選項 | 描述 |
| --- | --- |
| Windows Basic Authentication | Windows - 基本驗證  &lt;br&gt;  使用 Base 64 編碼，將編碼過的使用者名稱和密碼以純文字傳送。  適合用於客戶端非Windows的狀況。 |
| Windows Basic Authentication over SSL | Windows - 基本驗證 over SSL 驗證  &lt;br&gt;  同上面方法，但是會 SSL 協定將使用者名稱和密碼加密。 |
| Windows digest | Windows - 摘要式驗證  &lt;br&gt;  類似第一種方法，  使用雜湊加密的方式傳送用戶端認證，所以不會以純文字傳送密碼。 |
| Windows integrated | 整合式 Windows 驗證  &lt;br&gt;  這個機制使用 NTLM 或 Kerberos。  且要求客戶端與伺服器端都必須是Windows，這個安全機制會由客戶傳送加密的認證物件給伺服器端。 |
| client certificates | 用戶端憑證驗證  &lt;br&gt;  用於網際網路和內部網路情況下的用戶端安全識別。它會要求每個用戶端必須從相互信任的憑證授權單位取得憑證。 |
| forms-based authentication | 表單驗證  &lt;br&gt;  Web Service不支援此驗證。  &lt;br&gt;  若要使用表單驗證，可參考這篇做法：[使用Forms实现WebService身份验证](http://www.builder.com.cn/2007/0907/495087.shtml) |
| Custom Security with SoAP Headers | SOAP 標頭 – 自訂驗證  &lt;br&gt;  在安全和非安全的網際網路情況下都適用。它會在 SOAP 訊息的 SOAP 標頭中傳遞使用者認證。無論裝載 Web 服務的平台為何，Web 伺服器都會提供自訂驗證實作。 |

# 使用 Windows 驗證

若使用 windows 驗證當做 web service 的安全認證，  當直接使用 IE 瀏灠器連結 web service 時，IE 會自動傳遞 windows 認證資料給 web server 。  但是如果是連結到某個網頁，要在網頁 postback 之後，才由程式碼去連結 web service 執行服務，那麼就必須自已傳送認證資料給執行 web service 的主機。  

為了將 windows 的認證資訊傳遞給 web service ，  我們必須建立 [NetworkCredential](http://msdn.microsoft.com/zh-tw/library/system.net.networkcredential.aspx) 類別，  用來指定使用者的 username、password、domain ，再將認證資訊指定給 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 的 [Credentials](http://msdn.microsoft.com/zh-tw/library/system.web.services.protocols.webclientprotocol.credentials.aspx) 屬性。  
```c#
TestWebService2.WebService2 ws = new TestWebService2.WebService2();

//使用特定使用者登入
ws.Credentials = new NetworkCredential("testuser", "testuser");

//使用目前的使用者登入
//ws.Credentials = System.Net.CredentialCache.DefaultCredentials;

int counter = ws.GoodCounter();
labGoodCounter.Text = counter.ToString();
```

# 使用用戶端憑證驗證

透過用戶端憑證類別(例如：X509Certificate)的 [CreateFromCertFile](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.x509certificates.x509certificate.createfromcertfile.aspx) 方法載入用戶端的憑證檔案，  再將該資訊加入 [WebService](http://msdn.microsoft.com/zh-tw/library/system.web.services.webservice.aspx) 的 [ClientCertificates](http://msdn.microsoft.com/zh-tw/library/system.web.services.protocols.httpwebclientprotocol.clientcertificates.aspx) 屬性中。  
```c#
// Instantiate proxy class to a Bank Web service.
BankSession bank = new BankSession();

// Load the client certificate from a file.
X509Certificate x509 = X509Certificate.CreateFromCertFile(@"c:\user.cer");

// Add the client certificate to the ClientCertificates property
// of the proxy class.
bank.ClientCertificates.Add(x509);

// Call the method on the proxy class, which requires
// authentication using client certificates.
bank.Deposit(500);
```

# 用 SOAP 標頭自訂驗證

若用戶端包含非 Windows 平台連線，該 Web Service 便不適合使用 Windows 認證的服務，這時可以改用 SOAP header 這個機制來傳遞使用者的資訊。  

#### 1.自訂一個認證所需資料
```c#
public class mySoapHeader : SoapHeader
{
public string User;
public string Password;
}
```

#### 2.在 Web Service 中，加入判斷邏輯。
```c#
/// <summary>
/// WebService2 的摘要描述
/// </summary>
[WebService(Namespace = "http://vito.idv/")]
[WebServiceBinding(ConformsTo = WsiProfiles.BasicProfile1_1)]
[System.ComponentModel.ToolboxItem(false)]
[System.Web.Script.Services.ScriptService]  // 這行要取消註解，才可以允許使用 AJAX 呼叫此 Web 服務。
public class WebService2 : System.Web.Services.WebService
{
public mySoapHeader soapheader;

[WebMethod]
[SoapHeader("soapheader")]  //宣告 WebService 方法搭配哪一個 SoapHeader
public int GoodCounter()
{
if (IsValidUser())
{
int counter = (Application["GoodCounter"] == null ? 0 : int.Parse(Application["GoodCounter"].ToString()));
counter = counter + 1;
Application["GoodCounter"] = counter;
return counter;
}
else
{
throw new SoapException();
}
}

private bool IsValidUser()
{
if (soapheader.User == "testuser" && soapheader.Password == "testuser")
return true;
else
return false;
}
}
```

#### 3.在呼叫端程式中傳遞使用者資訊。
```c#
TestWebService2.WebService2 ws = new TestWebService2.WebService2();

//在這個範例中，會採用可辨讀的純文字格式，在網路傳送文字 (未經加密)。
//如果純文字對您的應用程式而言不夠安全，請新增加密演算法。

//建立 SOAP 訊息
TestWebService2.mySoapHeader soapheader = new TestWebService2.mySoapHeader();
soapheader.User = "testuser";
soapheader.Password = "testuser";

//當加入 web service 參考時, 系統會自動以 [SoapHeader] 宣告中的型別名稱,建立加入 SoapHeader 的方法
ws.mySoapHeaderValue = soapheader;

int counter = ws.GoodCounter();
labGoodCounter2.Text = counter.ToString();
```
## 參考資料  

- 
- 
-