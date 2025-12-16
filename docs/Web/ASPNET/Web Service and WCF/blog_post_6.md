---
title: 掛載 WCF 服務
layout: default
parent: Web Service and WCF
nav_order: 6
description: "掛載 WCF 服務"
date: 2013-02-27
tags: [Web,ASPNET,Web Service and WCF]
postid: "5483781435380303287"
---
要讓設計好的 WCF 服務發揮作用，必須先將服務裝載到適當的主機身上，這個行為就稱為 hosting 。  WCF 服務可以裝載於任何 Managed 應用程式上，這是最彈性的選項，因為它只需要最少的基礎結構就可部署。  例如：IIS, WAS, 或自行撰寫裝載的應用程式都可以用來裝載 WCF 服務。  且因為 WCF 服務使用統一的程式設計模型，這個程式設計模型與部署服務的執行階段環境是互相獨立的，   所以不管裝載選項為何，服務的程式碼看起來都差不多。   

WCF 服務裝載選擇的通訊協定比較

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh5qcJ2iUrxZN_vuNr8yQD1FGGyvJdkUyCfDYmHLnDKs41Xcs8Dtb2OZIxyWfHfYRCQWD_Wh05hosRe-9tWoKbi8uZ2Utyaf9phzGeMQ9sj7_d5ginXiGkUbSCwb6QlxKGDH10ri5Ecb-8/s484/wcf-host-compare.png)

WCF 服務裝載選擇的優缺點比較

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi1lNatNG8Pedp6Hl2iGB0nzrF0DoUEaOKbjTyTDlaqgfLRY1Y6ZfPqYFY_ADD3BJ70Nc0cXQotB4mmd-Uadx4R-tDR9lyEkBcIp01gk7o90OOjbTsMOR5RE8EONpzsiX53lmX-EfeptxE/s494/wcf-host-compare2.png)

# 使用.NET 應用程式裝載 WCF 服務（自主裝載）

自主裝載（self-hosting）就是由開發人員自己提供可裝載服務的應用程式，並管理其生命週期。  自主裝載的應用程式可以是個 Windows Forms 應用程式、WPF 應用程式、Console 應用程式、或 Windows 服務。  自主裝載的 WCF 服務可使用任何 WCF 支援的傳輸協定（不像裝載於 IIS 5/6僅支援 HTTP/HTTPS），而且可以使用 WCF 的全部功能，例如：服務匯流排、服務探索等等。  

要建立自主裝載的應用程式，可透過 System.ServiceModel 組件中的 ServiceHost 類別。  你只要建立 ServiceHost 執行個體，並指定裝載的服務型別，就可以使用其 Open() 方法來開啟服務。參考程式碼如下：  
```c#
public partial class Form1 : Form
{
private ServiceHost serviceHost;
public Form1()
{
InitializeComponent();
serviceHost = new ServiceHost(typeof(WcfServiceLibrary1.CalculatorService));
}

private void btnStartService_Click(object sender, EventArgs e)
{
try
{
serviceHost.Open();
MessageBox.Show("服務已啟動");
}
catch (Exception ex)
{
MessageBox.Show("服務啟動失敗\n" + ex.Message);
Console.WriteLine(ex.Message);
}
}

private void btnStopService_Click(object sender, EventArgs e)
{
try
{
serviceHost.Close();
MessageBox.Show("服務已終止");
}
catch (Exception ex)
{
MessageBox.Show("服務終止失敗\n" + ex.Message);
}
}
}
```

PS1.  
若要將 WCF 服務裝載在自訂的應用程式中，那麼該應用程式的組態檔中，也必須加入這個 WCF 服務會使用到的服務組態與端點組態的設定。  

PS2.  
裝載服務的應用程式，必須以系統管理員身份執行。

# 使用 Windows Service 裝載 WCF 服務

你也可以使用 Windows Service 專案來裝載 WCF 服務，並在 OnStart 方法中，建立並開啟 ServiceHost 物件，然後在 OnStop 方法關閉該物件。  當 Windows Service 被安裝於系統中後，就可以透過作業系統的服務管理員來啟動或關閉這個 WCF 服務。

# 使用 IIS 裝載 WCF 服務

WCF 服務也可以直接裝載於 IIS 中，它會與 ASP.NET 執行環境整合，所以也只能使用 HTTP 通訊協定。  

一般來說，在 IIS 或 WAS 底下裝載 WCF 服務時，您必須提供 .svc 檔案。  svc 檔案中使用 @ServiceHost 指示詞描述服務的資訊，參考內容如下：  
```xml
<%@ ServiceHost Language="C#" Debug="true" Service="WcfServiceApplication1.Calculation"%>
```

同時，你也必須在 web.config 中，加入 WCF 服務的組態設定。
```xml
<system.serviceModel>

<services>
<service name="WcfServiceApplication1.Calculation">
<endpoint address="Calculation" binding="basicHttpBinding" contract="WebApp_WcfServer.Calculation" />
<host>
<baseAddresses>
<add baseAddress="http://localhost:7401/" />
</baseAddresses>
</host>
</service>
</services>

<behaviors>
<serviceBehaviors>
<behavior name="">
<serviceMetadata httpGetEnabled="true" />
<serviceDebug includeExceptionDetailInFaults="false" />
</behavior>
</serviceBehaviors>
</behaviors>

</system.serviceModel>
```

# 使用 WAS 裝載 WCF 服務

「Windows 處理程序啟動服務（[Windows Process Activation Services, WAS](http://technet.microsoft.com/zh-tw/library/cc770745.aspx)）」是 Winodw Server 2008 及 Windows Vista 上全新的處理序啟動機制。  在 IIS 中，所有功能都必須依賴 HTTP 通訊協定，  WAS 整合了 IIS 的功能，並將處理序模型一般化，同時去除了與 HTTP 的相依性。  所以，若將 WCF 裝載在 WAS 中，除了可以使用與 IIS 相同的功能，也可以使用非 HTTP 通訊協定。  

使用 WAS 裝載服務的設定方式跟發行至 IIS 是一樣的，差別是可以在組態中建立非 HTTP 通訊協定的端點。  

更多相關內容請參考 MSDN：

- [HOW TO：在 WAS 中裝載 WCF 服務](http://msdn.microsoft.com/zh-tw/library/vstudio/ms733109.aspx)
- [HOW TO：安裝和設定 WCF 啟用元件](http://msdn.microsoft.com/zh-tw/library/ms731053%28v=vs.110%29.aspx)
- [Hosting WCF in Activation service](http://www.wcftutorial.net/wcf-was-hosting.aspx)
## 參考資料  

- [\[王寧疆老師線上教學\] 服務端點與行為設定，WCF 服務偵錯及分析 ─ WCF服務裝載選擇](http://www.youtube.com/watch?v=N7a5KS7yv1c&feature=autoplay&list=PL9901020A0FA51A2C&playnext=2)
- [\[王寧疆老師線上教學\] 服務端點與行為設定，WCF 服務偵錯及分析 ─ WCF服務與用戶端程式設定](http://www.youtube.com/watch?v=0MV6OPROIDo&feature=autoplay&list=PL9901020A0FA51A2C&playnext=3)
- [HOW TO：在 WAS 中裝載 WCF 服務](http://msdn.microsoft.com/zh-tw/library/vstudio/ms733109.aspx)
- [HOW TO：在 Managed 應用程式中裝載 WCF 服務](http://msdn.microsoft.com/zh-tw/library/ms731758%28v=vs.110%29.aspx)
- 
- 

&lt;!--相關文章--&gt;  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi2Q1Ch0u6DdE7o3mJIqe9Q-tlW1K61LeLq7ZCdEAeAF3NC0R3zMj_CxYX_YlXVbOgBUmVX0U7tPO0ylNpH5PN3_JfocbUJ4-yQdOr_TB_dYYQ3UO5Fuf9CpKQVykMY9LOtKVOFiTiPWCA/s120/RelationPage.png)

- [建立 WCF 服務](http://127.0.0.1:8080/Documents/70562/Chapter09/creating-wcf-service.aspx?DID=D0182)
- [裝載 WCF 服務](http://127.0.0.1:8080/Documents/70562/Chapter09/creating-wcf-service2.aspx?DID=D0183)
- [RESTful WCF 服務](http://127.0.0.1:8080/Documents/70562/Chapter09/creating-wcf-service3.aspx?DID=D0184)