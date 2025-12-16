---
title: 建立 WCF 服務類別庫
layout: default
parent: Web Service and WCF
nav_order: 5
description: "建立 WCF 服務類別庫"
date: 2013-02-27
tags: [Web,ASPNET,Web Service and WCF]
postid: "8151110189119045759"
---
# 建立 WCF Service Library 專案

## 1. 建立WCF Service Library專案

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgqB4cTcMHj_V1_kGpsxgoV11LhnqW3VmY4AlHSY3wEVrG7htycXLNp-jHbKZ2NdroWfe_SZpKcbSS2R5NSyWpAFdkdmWlRtxg7DZxTaGt2Ww0bNxDLVFZMkXUNjN5ZMLb4veBOhJEOaIM/s811/wcf-add-project.png)

這個專案範本預設會幫我們加入：IService1.cs、Service1.cs、和App.config。  可以依以下步驟更改成我們想要的名稱。

## 2. 替服務介面更名

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhPqbOB5EixPu4oSnTI4aL3uI4hjWo_ILWpHsRclbRnPcvPl-N5DjHRYAf-K0CGdZT2Bh0H0qHnYT87-_ZX-prSy_Yi2dVxd5BEynf49iyOVUhDeU_-sJHEH6pOn44pPMoKIfn8kfALvIo/s456/wcf-rename-interface.png)

## 3. 在介面中加入新的服務方法

當加入服務方法時，必須先定義一個 Contract，  用來說明服務包含哪些 Operation，  要使用哪種 Message Pttern。  在服務合約中至少要有一個標示為 [OperationContract](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.operationcontractattribute.aspx) 的方法，否則載入服務時會有例外。   

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhL-jKp83j9p-KUuj-xdusBGxcPKxTQFWI8qYcsampEE9doJCcFLotNgz34cQj21MYSI_t4dCLqZTaEZfFPLmMKFqUWFhG6aG9cFx09C1nTSmqvuLxaSPNTvUNOMbj3UPvfB79ZqUI4o6A/s456/wcf-add-operation-contract.png)

## 4. 替服務類別更名，並實作 ICalcService 介面

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2qPfH7a9AG8LV1F5QuuIgFToA6S0faAuuRL60k835ARVFf3QpXQnGyhlZZxdSB_Y7zB37apEKlE2VPuIBWfip4wYfIHYDncJkHyzznALklxk6d6A_GnZ6Pi25LhdSfcWk3WnhWMhTQ1I/s484/wcf-implement-interface.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjrTKzzN_gCdEvOIEebCCGr8AhZx9xV-qNLBvbikBmdw1yC0dfJG4KxyTF3c0T-zgfvRBVUuieCQOuno7xHcASUoonloEh8QZkw4MptPW14CJ5bRGl5w-QCScuQfzt8JKwQjYo6M9VxqnE/s343/wcf-implement-interface2.png)

底下是精簡過的ICalcService.cs程式碼。  包含一個 ICalcService 介面和 CompositeType 類別。  其中 ICalcService 介面套用屬性 ServiceContract，表示它是定義服務合約的介面，裡面就是用來定義要提供給用戶端呼叫的操作。  而 CompositeType 類別則套用屬性 DataContract，表示它是用來定義呼叫操作時所需傳遞的資料型別。  
```c#
[ServiceContract]
public interface ICalcService
{
// TODO: 在此新增您的服務作業
[OperationContract]
string Add(int a, int b);
}

[DataContract]
public class CompositeType
{
bool boolValue = true;
string stringValue = "Hello ";

[DataMember]
public bool BoolValue
{
get { return boolValue; }
set { boolValue = value; }
}

[DataMember]
public string StringValue
{
get { return stringValue; }
set { stringValue = value; }
}
}
```

## 5. 按F5執行

在 Visual Studio 中執行一個 WCF 類別庫，實際上是 VS 啟動「[WCF 服務主機](http://msdn.microsoft.com/zh-tw/library/bb552363%28v=vs.110%29.aspx)」（WcfSvcHost.exe），並自動掛載這些 WCF 服務。  所以你可以在桌面的右下角看到以下提示訊息。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEigfdF47gWl225Sqs35cO_8Gz7ybmYOdGifoyLdAvkQm8WS-dCR4i9ZFxd5L7d1l1m5iwB2Q5-AwnhOPy-UkyzPloNDjQYFSIq-mPoTodUGnoPlLz_snVqj7L5aiKJQy0QctYO3ZoFfwnI/s285/wcf-svc-host.png)

## 6. 測試 WCF 服務

通常當 WCF 裝載到服務主機上之後，你必須另外建立用戶端程式，以進行 WCF 服務的測試工作。  不過， Visual Studio 除了提供「[WCF 服務主機](http://msdn.microsoft.com/zh-tw/library/bb552363%28v=vs.110%29.aspx)」，另外也提供一個「[WCF測試用戶端](http://msdn.microsoft.com/zh-tw/library/bb552364%28v=vs.110%29.aspx)」（WcfTestClient.exe）。  當WCF服務主機裝載服務完成之後，就會自動開啟WCF測試用戶端工具。如下圖：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjNDL_xawk_JGAakCvANX-L4feDJH-ZrwShRKj8TfdQqAhMDa34PqM6IoAaxsm9iKbfXNddOJhswb9ZqCf2B-LhcN8B0cQ02CXcf42wabvyLo4VKJJVY3vsvxn124tH5kyG33dwm5hMApA/s696/wcf-run-test.png)

在WCF測試用戶端中，你只要雙擊測試的方法，WCF測試工具就建立測試的介面讓你輸入測試資料，再按下「叫用」按鈕即可進行測試。

# 變更預設的繫結類型

當建立 WCF Service 時，預設會使用 wsHttpBinding 這個繫結類型，  下面示範如何建立一個 basicHttpBinding 繫結類型，並將變更服務的繫結類型。  

#### 1.開啟組態設定工具

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgQpX0fBU5vpmK_KcfIsVt0ilcbB5wETEt-0fLJBidaB0qWBW3sHtBnZQE_sK7VH4cLH1-PnyN9y74IG2fKKvlg9tKf_ahcMY-px3HUBBp3tWphq3ZAVp8jTo1j2_6D5CzfDQL0aliy4Rc/s318/wcf-edit-config.png)

#### 2.將預設的 wsHttpBinding 修改成 basicHttpBinding

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjck4Iqpag1g5zI4YOzjrOek6aqlQC1aTA7ct0aCQLToxUYHtgjZVaWU5Lga2gM1ChLyKpbLklsU5dze1RcPsC6HYwCA0F8dJ-6h-6hR6J-vIYJpWPxHLSpIP-5mXTP5YrHOjsVqXG3nDU/s693/wcf-edit-binding.png)

#### 3.新增繫結組態 (BindingConfiguration)

3.1 點選新增繫結組態

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiVf4Y-Xr2qsruy6K4dbSYmX9fKsgEeNq9oGWGsCNrxZEeTggL0z19CYceqP5oTmKEsizd3n72UYSR2xTQBrFYeOSZIE5aEMBMp2-90bIAMZVc0pDIFLSRv4G1EAFalfZ2gOOEY2b4o-zg/s654/wcf-new-binding.png)

3.2 選取 basicHttpBinding 繫結類型

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh38s6T96WV4nT2rUn_zcizCISKZB7HDxHNMpGhn0W4ZwMiVI_pYxgnmo-L9Nhyphenhyphenltscpa3w33tp0rQCkIYxTuMTGNfwoUAur_C7Sfaylo3-eXBBeErRTUTRThHXU6xZOZze_X7RWKyzqcE/s286/wcf-new-basicHttpBinding.png)

3.3 設定 SendTimeOut 為 10 分鐘

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhiIKx8WEURXRJFDpbvk8RX48zf3F_cZR6PdebLVUhfVYPxRCJwaIEft6z9mMrHwFgCe9wqrS-vw4VlPSnogDI9ITd1VENPi-gc5ezCjUQt4GN_oX2JqROT2R6RFRXD7vmpzdaPhl0p9sQ/s654/wcf-set-timeout.png)

3.4 CTRL+S 儲存

#### 4. 設定服務的繫結組態
將服務的 BindingConfiguration 設定成上一步驟新增的繫結組態  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEivXZGk2kbfxueFrNAthb738kqe9sk_6pEr0ZJIUIxWKu4a8b6rKKPIcvRtsKx7KrrbsAzvRR_7gVe-7nvLjje1I5elZvzkhIlmPztxTbG8bS8kAmwVtguGzutDV4vhU438oH6evhNRxAk/s654/wcf-setting-bindconfig.png)

#### 5.F5, Run，可以正確執行運算

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiaK6Yb5cCYkdkMGyVbgsLd8mO0sWmaPCVg8indS3cVVVYZZ6apf8YzUSs8-OITCVZZhmDIXUI9dv2DpIqd8l5sZKSGuxwD5MkQyKwZ-q1TWUaQpNy5KZqX7KvW8_cX8mGIj67pb9-3uAc/s703/wcf-run-basicHttpBinding.png)

#### 備註：

WSHttpBinding 與 BasicHttpBinding 類似，不過前者提供更多的 Web 服務功能。  它使用 HTTP 傳輸並提供訊息安全性，如同 BasicHttpBinding，不過它也提供交易、可靠傳訊以及 WS-Addressing。

# 裝載 WCF 服務

上面範例中，我們已經建立好 WCF service。  contract 的定義是放在 ICalcService 介面中，  contract 的實作則放在 CalcService 類別裡。  而且已經將 endpoint 的繫結組態由預設的 wsHttpBinding 變更成 basicHttpBinding。  底下是 App.config 所顯示的資訊。  
```xml
<services>
<service name="TestWcfServiceLibrary.CalcService">
<endpoint address="" binding="basicHttpBinding" bindingConfiguration="NewBinding0"
contract="TestWcfServiceLibrary.ICalcService">
<identity>
<dns value="localhost" />
</identity>
</endpoint>
<endpoint address="mex" binding="mexHttpBinding" contract="IMetadataExchange" />
<host>
<baseAddresses>
<add baseAddress="http://localhost:8732/Design_Time_Addresses/TestWcfServiceLibrary/Service1/" />
</baseAddresses>
</host>
</service>
</services>
```

WCF 服務必須運行於 host process 中，這個 host process 可以是 IIS 、WAS、Windows 服務、或你自己開發的 .NET 應用程式（即所謂的 self-hosting）。運行於 IIS 和 WAS 環境都可享有生命週期控制的優點，這裡要示範的是將剛才建立的 WCF 服務部署到 IIS。

1. 在 IIS 中建立一個網站，指定一個連接埠，如 8060 ，實體路徑指向一個新建立的空資料夾。  例如：D:\myProject\WCF\_Service。  並將驗證選項設定成「啟用匿名存取」。
2. 回到 VS2008，在方案總管中的專案名稱上點右鍵，選 Publish，參考下圖設定：  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj4-eNBubTMIPpqkvWt8Z8dr44CzPazb5qF2AbgEbyv1OizL6gaF3r5LEr4V1_qXVNKnKEAwM-CSBu3mWiiz9iKGi3M3Y1HrVJx74xRJj2NparPSkbvGmsOqUCqp_g6EwxX_0y3EBKP47Y/s328/wcf-publish.png)  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhSZdt-lYcFfuTt2NxtJ3y7sRyjpbxHiEcozQKhyphenhyphenJssgCyfLNuu1zO24MzjhKMUb-Hkf42_vqAci-ZRqbKUm1mChZjkpDq0LqRhgk8YMZNVIk22-oHM6AdbXilKNBmKTpPrhcz1a_pK-ok/s569/wcf-publish-to.png)
3. 部署完成後，到實際部署的資料夾中看一下產生了哪些檔案。你應該會看到：  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiWk0ZE1x-bMA3eVdNGP2LzPNsOcKDPNQD9KapA_mgGWPIV9ZJDC7DmBKjbSLYiMMtEklyQg6oEvUMn7mX8Y7Qwoq-uoTyUduEBY4yY3aJSiOwTNFoU3UUo7rEd5pS8JX1jzp4alH-IW0s/s435/wcf-publish-file.png)  
#### PS.

其實發行所做的事情，就是新增一個 .svc 檔，並指定服務的類別名稱。如下所示內容：  
```xml
<%@ ServiceHost Service="TestWcfServiceLibrary.Service1" %>
```
4. 開啟瀏覽器測試一下：網址輸入 http://localhost:8060/TestWcfServiceLibrary.Service1.svc ，若一切順利，應該會看到類似這樣的畫面：  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgOxJErrgtIuTOn6NMNKFYcSieiH1hhP97r48Fk8GXcagCDsvnjl0c43EAG19YQ5n9bnxUfe101Z_Iq6WHoe_LrcNHHoTo9dw51luxnU-ecp-9ZHJCFKQymovhOdGcnxumgj0bcxpYmNOU/s850/wcf-connect-service.png)

# 撰寫 WCF 用戶端程式

寫一個簡單的用戶端程式來測試 WCF 服務。步驟如下：

1. 在測試專案中，在專案的右鍵選單中，執行「加入服務參考」，並完成以下設定：  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjZEVfc3S7zElBy0xgSPuvz5frwNzBlklWaDMyBHK88x8E1mnnrgktxKNVHFO8a1W2fZ36ohse_ajkx8gJddbj0bx4X5q4lo3OuNx0eq4_r3SxHIvQf-p-SmwHYv6DsPWErmEax6ikWEzQ/s360/wcf-add-refference.png)  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjIenmy50MmjMTk25dzls4wNpH6XNzi3AoqyvcFjTordGkpUKz92XMCyeORF6QZcLRUzs7DRWh9G9HYsa5YKZciiYuBdNdzIdjX4poDO9K_UIA1vWM-OKn777ycSSt0F6a4Ipm5MbcX788/s642/wcf-add-refference2.png)
2. 編輯測試程式碼：  ```c#
protected void btnConnectWCFService_Click(object sender, EventArgs e)
{
myCalcService.CalcServiceClient wcf_service;
using (wcf_service = new myCalcService.CalcServiceClient())
{
string result = wcf_service.Add(5, 10);
myDebug.ResponseBR(result);
}
}
```
3. 執行驗證。