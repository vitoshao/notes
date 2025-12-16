---
title: 建立 WCF 服務
layout: default
parent: Web Service and WCF
nav_order: 3
description: "建立 WCF 服務"
date: 2012-11-09
tags: [Web,ASPNET,Web Service and WCF]
postid: "7783639126912160611"
---
# 建立 WCF Service Application 網站（Server端）

## WCF 的設計步驟

1. Define the service contract.
2. Implement the service contract.
3. Configure a service endpoint or endpoints.
4. Host the service in an application.
5. Call the service from client application.

不過以上的步驟1,3,4，在我們建立 WCF Service Application 專案時，預設的範本上都有相關的設定可參考。  所以只需要將重點放在如何撰寫服務內容以及如何由客戶端叫用服務。

## VS2010 內建的 WCF 範本

要設計 WCF 服務，必須開啟 WCF 服務專案。  在 Visual Studio 2010 裡，與建立 WCF 服務相關的專案的範本有：「WCF 服務應用程式」和「WCF 服務程式庫」。  前者為支援 WCF 服務的網站，後者為提供 WCF 服務的類別庫。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiurJRjihHWZz22KP1yqELlmJ1vvISSrY7mJcWXDItS3KPg-TWHSLi-CHvAy-UCkwj8esDPwTJ8kP3JGCFbYm1FQb_kvb5uXyTYBNUBFPGqk2nSsTiYwZusY8ttbNRBM7wFs45roneFp9Q/s493/wcf-template.png)

### 一. WCF 服務應用程式

「WCF 服務應用程式」是一個支援 WCF 服務網站專案，只是組態檔中預設包含了支援 WCF 的設定。  你也可以在一般的網站專案中，直接加入 WCF 服務項目，系統也會自動幫你修改 Web.config 以符合需求，這二者意義是相同的。  

這個範本預設會包含以下檔案：

- 服務主機檔案 (service1.svc)。
- 服務合約檔案 (IService1.cs)。
- 服務實作檔案 (Service1.svc.cs)。
- Web 組態檔案 (Web.config)。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEidETEmFZhagYf81L61_bsDrY_nqmIiGZ5vFV6kl3AT_u7qcjlGyn132guc9eSmCvNL65EWuAVqhBvuuOIczUoAQXLgjxDY1yMWSyAwh5lXoHgR33PU3XHqtIM-1AYdCgxR6-kQMs-x1E8/s227/wcf-service-application-template.png)

在網站加入 WCF 服務，它會自動以 IIS 為預設的裝載主機，並且使用 HTTP 端點。  這些設定資訊，都可以在 Web.config 檔的 &lt;system.serviceModel&gt; 區段中找到。

### 二. WCF 服務程式庫

「WCF 服務程式庫」是一個支援 WCF 服務的類別庫，必須先透過 host 載入服務程式庫後才可由用戶端叫用。預設專案會包含以下檔案：

- 服務合約檔案 (IService1.cs)。
- 服務實作檔案 (Service1.cs)。
- 應用程式組態檔案 (App.config)。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi95lTdZweWD9MyBDTne1zpNtPeIq2gH-V5kB9VeTOJ0piw2MJhmhLj2DsLYZhrfIO2adnFWULlJHGkeZmzRx85N4tDP583VoHQWVwXKu_vNcryhyopuAo0HlD3ZXmezIh1pJDXbqXZkwI/s214/wcf-service-library-template.png)

## 1. 建立 WCF Service 網站

如果你是由全新開發，你可以直接先新增一個「WCF 服務應用程式」專案。  如果你是在現有網站中，要加入 WCF 服務，則直接加入「WCF 服務」項目即可。  

### 1. 加入 WCF 服務項目

新增「WCF 服務項目」到網站中。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiMRBAEwiEjX_bYNiuZeT9Gd6T0_vKVgBbMuevKVO34_HL3BmWRfRdryuQPA9XRcPzsqef_2Ybkepeeq1K6ueFoODzrobkun7yRaVmq3nRG4npOdTnwcNWg9ut7p8kP_NbEUor2Jlzq5YI/s534/wcf-create-wcf-service.png)

預設系統會建立一個 .svc 的 WCF 服務檔，以及定義服務的類別檔。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEidETEmFZhagYf81L61_bsDrY_nqmIiGZ5vFV6kl3AT_u7qcjlGyn132guc9eSmCvNL65EWuAVqhBvuuOIczUoAQXLgjxDY1yMWSyAwh5lXoHgR33PU3XHqtIM-1AYdCgxR6-kQMs-x1E8/s227/wcf-service-application-template.png)

### 2. 撰寫服務程式碼

\*.SVC 檔是一個提供 WCF 服務的網頁檔，它使用 **@ ServiceHost** 宣示詞宣告它為一個服務網頁。
```xml
<%@ ServiceHost Language="C#" Debug="true" Service="WcfService.Service3" CodeBehind="Service3.svc.cs" %>
```

真正服務的類別、方法、資料則定義在 .svc.cs 檔中，如下範例：
```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Runtime.Serialization;
using System.ServiceModel;
using System.Text;
using System.ServiceModel.Web;

namespace WcfService1
{
[ServiceContract]
public class HRService 
{
[OperationContract]
[WebInvoke]
public Employee Employee1(string EmpID)
{
Employee emp = new Employee() { EmployeeID = EmpID, FirstName = "vito", LastName = "shao", Age = 30 };
return emp;
}

[OperationContract]
[WebGet]
public Employee Employee2(string EmpID)
{
Employee emp = new Employee() { EmployeeID = EmpID, FirstName = "vito", LastName = "shao", Age = 30 };
return emp;
}
}

public class Employee
{
public string EmployeeID { get; set; }
public string FirstName { get; set; }
public string LastName { get; set; }
public int Age { get; set; }
}
}
```

## 2. 設定 WCF 服務位置與繫結

當服務元件建立完成之後，接下來就是要設定服務的位置與繫結資訊。  

### 預設服務組態

#### 網站專案

若在一個網站中加入一個新的「WCF 服務」項目，該專案除了會新增一個 .svc 檔案外，也會在組態檔中自動加入以下設定。  其中 &lt;serviceBehaviors&gt; 項目中，使用 httpGetEnabled = true 屬性來設定允許使用 HTTP/GET 取得該服務的中繼資料。  
```xml
<system.serviceModel>
<behaviors>
<serviceBehaviors>
<behavior>
<serviceMetadata httpGetEnabled="true"/>
<serviceDebug includeExceptionDetailInFaults="false"/>
</behavior>
</serviceBehaviors>
</behaviors>
<serviceHostingEnvironment multipleSiteBindingsEnabled="true" />
</system.serviceModel>
```

上面範例，因為這是一個 ASP.NET WCF 服務網站，預設這個服務將掛載在 IIS 上，  它會使用 **basicHttpBinding** 做為端點的預設繫結方式，  並使用 .svc 檔中的類別當成服務合約，所以設定上看起來比較簡單。  你也可以使用以下設定，看起來會比較完整：  
```xml
<system.serviceModel>
<behaviors>
<serviceBehaviors>
<behavior>
<serviceMetadata httpGetEnabled="true"/>
<serviceDebug includeExceptionDetailInFaults="false"/>
</behavior>
</serviceBehaviors>
</behaviors>
<serviceHostingEnvironment multipleSiteBindingsEnabled="true" />

<services>
<service name="WcfService1.HRService.SOAP">
<endpoint address=""
binding="basicHttpBinding"
contract="WcfService1.HRService" />
</service>
</services>
</system.serviceModel>
```

#### 類別庫專案

如果是一個類別庫專案，它的設定檔範例如下：
```xml
<system.serviceModel>
<services>
<service name="WcfServiceLibrary1.Service1">

<!--指定服務的基底位址-->
<host>
<baseAddresses>
<add baseAddress = "http://localhost:8732/Design_Time_Addresses/WcfServiceLibrary1/Service1/" />
</baseAddresses>
</host>

<!-- Service Endpoints -->
<!-- address可以是完整位址，也可以是相對位址。若為相對位址，則以 host 的 baseAddress 為基底位址-->
<endpoint address ="" 
binding="wsHttpBinding" 
contract="WcfServiceLibrary1.IService1" />

<!-- Metadata Endpoints -->
<!-- 服務會使用中繼資料交換端點向用戶端描述自己。--> 
<endpoint address="mex" 
binding="mexHttpBinding" 
contract="IMetadataExchange" />
</service>
</services>

<behaviors>
<serviceBehaviors>
<behavior>
<serviceMetadata httpGetEnabled="True"/>
<serviceDebug includeExceptionDetailInFaults="False" />
</behavior>
</serviceBehaviors>
</behaviors>

</system.serviceModel>
```

這個範例中，幾個重要設定說明如下：  
1. 指定了一個 host 的位址  
2. 設定了二個 services endpoint   
第一個 endpoint   
address = "" ：它是一個相對於 host 位址的欄位，所以空白表示它的服務位址就在 host 位址的目錄下。  
binding = "wsHttpBinding"   
contract = "WcfServiceLibrary1.IService1"  
第二個 endpoint   
address = "mex" ：表示它是一個中繼資料交換端點，用來向用戶端描述自己。  
binding = "mexHttpBinding"   
contract = "IMetadataExchange"  

#### PS. 關於 BasicHttpBinding 的一些預設值內容：

- 它使用 HTTP 做為傳送 SOAP 1.1 訊息的傳輸。
- 它的安全性預設是關閉的，若要啟用，可以將 [BasicHttpSecurityMode](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.basichttpsecuritymode.aspx) 設為非 None 的值
- 它使用「Text」做為 MessageEncoding 和 「UTF-8」做為 TextEncoding。

### 使用「WCF 服務組態編輯器」設定服務組態

在 VS2010 開發工具中，提供「WCF 服務組態編輯器」來減化以上的設定工作。你只要在 Web.config 或 App.Config 組態檔上，按滑鼠右鍵，選取「編輯 WCF 組態」項目，即可開啟這個工具。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg_Kr5oT_yUj6prQB33k_45RTBJIELxsNXz_1qEeYr7f5U6XjtdCcxxPqkTVWp-GMoJazudp9jQRP2GGbzdtLCtytITlRi64L2Ty2pnGMFjZqfmxYmhod1sxy8kCf7ac0g5S58vXm4s-Fs/s413/wcf-config-editor-1.png)

WCF 組態設定工具，可以用來進行服務端或用戶端的設定：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhuulJYYnAL38tjkfT27qmm4cyNEDFs9-YxEINzDbQ02Mrf_Ng03z2baHKby-sInGmk5gW0IRvEmSfLZftAddtgGPanvT1z1GWaCYoYDL-W-1Hx7bkLUie29PSr_3RC-FgcC6PPVoJod3g/s747/wcf-config-editor-2.png)

### 如何使用「WCF 服務組態編輯器」建立服務端組態

#### 1. 新增服務宣告

在「服務」節點上的右鍵選單，選取「新增服務」，即可增加服務節點。  你可以自行給定一個服務名稱，或者按下Name屬性旁的[...]按鈕，選取專案 bin\Debug 目錄下的組件，然後按開啟，它就會自動捉取一個適當的名稱來使用。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh-8yhY10STF3kBTfO5BXNtCX-MnTvGuHrNRKozD8mdi8NiL1r0icM7aalmJXaDejnW-Zdj0gtPWcucyal9zpJFwEpcIgLFYeLlNVbqjkEVJbAz6H6sU8KZVfWHnGcvc_Evkm4B18XSDqI/s511/wcf-config-editor-3.png)

#### 2. 設定基底位置

WCF 架構的服務位置設定是相當彈性的，你可以為每個服務端點指定絕對位址；  或者先指定服務主機的基底位址，再使用相對位址來定義每個服務端點。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjl052qi-6C5xrZzcb6Yd1kFs5Aa5NhNnSrpALs4Cw775cWRoDMwy5QghOtka8OdlKlsYdqDLgcMAjpK_HLoFoxLGArjVnFnv_EQWWtTc72eulxCxwLQ4N2ARHgMLe6_N-6U64N0_MLQHA/s766/wcf-config-editor-4.png)

#### 3. 設定服務端點

在 WCF 架構中，一個服務可以同時對應多個不同的端點，而每個服務端點都可以有不同的存取方式設定。  要新增服務端點設定，只要在「端點」資料夾的右鍵選單中，選取「新增服務端」點即可。  

新增服務端點定義之後，有三個一定要設定的屬性：

- Address：服務端點的位置，可以指定絕對位置或是參考基底的相對位置。
- Binding：服務端點的繫結方式，設定服務端點使用何種通訊協定。
- Contract：服務端點所要提供的服務合約。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiewo6xdvpwpTrPy-Z4NyiMXGugGXUcU1P7eEhKytxxqzk8uPMkE7JZzYl-0eZLwWJT_UGp3VUtt7up7RU77o6bFtRAEOI0n-D7J6r7ceHrKPmqlIcF1f4swC8XenqPumIasmhC78RFOpQ/s766/wcf-config-editor-6.png)

#### 4. 設定服務行為

「服務行為」指的是 WCF 服務的一些屬性組態設定。  例如，你可以將「serviceMetadata」項目的「HttpGetEnabled」屬性值設為 True，以允許用戶端使用 HTTP/GET 取得該服務的中繼資料。  若要設定「服務行為」，你可以在「進階」→「服務行為」節點中找到相關功能。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgErQQtvBa4jlOoOCyHHdUk3rVkLJVOUoEfb4Wxw5gpIKhHAq20ELH8qgPP48edN8qUeNerWA7Q__gg-e2cP2H4mIomsUE0cHkd711G9hEAOB2u5icjeyt0p4OM1boHTAF4ely3MZ2g9Rs/s758/wcf-config-editor-7.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgGNn_sFXtllLukG_GIn-6MOCVkd_s8m_yzWXcl14Psuo2K7fsZry7pQikKLJWj-orxShdOpK0fhHKtp8bKLoRkRJVGs-FqKZGMxZQMlXzCl9yJOqrDTtTP-MNpIRbUxNEQk2v_FvGCoOk/s766/wcf-config-editor-8.png)

## 3. 測試 WCF Service

#### WCF 服務

若這個服務是使用「WCF 服務」，程式完成之後，你可以直接在瀏覽器中檢視 .svc ，測試服務是否可以正常啟動。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgOq7xIlMgAM87P728HvjRJTM0hmlRrrvSO6YpehGKVoVdM6ecdBLQAmeU2s4pKqoo1xWHPDmwSroHnSKt_0yNdaC-W9723XAXOrBcL-BYQrar7K0K7m-Bv7l8LWny7EXj_n3OS558XulI/s481/wcf-view-with-browser-2.png)

如果沒有問題，應該可以看見如下畫面。該畫面中寫著如何測試此服務的方法。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhS5W5oT98zVH6Wm8l4x7MB8znvhqf9Cq6hnXd8c4hNbedlDn6wRtixFRrXHfGFaQs_fvvv0cmI5GpyGuu-eO59IwJuMI0o8gPKWHWewXc5ji98TyAnWS92JO65VfSDBW5bRsgEbe2pNkU/s788/wcf-view-with-browser.png)

#### 

#### WCF 服務程式庫

若這個服務是使用「WCF 服務應用程式」或「WCF 服務程式庫」範本專案建立的。  這時候你可以直接使用 F5 執行，該服務會被裝載在「WCF 服務主機 (WcfSvcHost)」中，並且自動開啟「 WCF 測試用戶端」以連結服務，進行測試。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjKm7PSHwEGSgNfDrnFvRxXD2oLJ917vRnX952qYFWzPLQYWC51nnSIlXJLCdb1pZwki6ppXpNsuikQIf8vbgNTErKwubGBvxrH5zWGpTmFprbgf9nfQ051KfkP5Gc7cmxiSGFFnL6zWnc/s803/wcf-test-client-point.png)

## 在 IIS 啟用 WCF 服務

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi8Jr5hLQnu6-lkgWva2VdrFV_OHE6p8J26D7_MCcrB3Viszp3NL1JwHSusxttzYgtNzSeYgqZzEEZ94ds3uMLHK8NpOhvifC-yq9hmKultsvBVHYcNIeRAV-tQdjwnIGLK2TFitE9u53Y/s839/wcf-activation.png)

詳細內容可參考 [How to: Install and Configure WCF Activation Components](http://msdn.microsoft.com/en-us/library/ms731053.aspx)

# 叫用 WCF 服務（Client端）

當 WCF 服務程式完成後，我們就可以將它部署到 IIS ，或者直接用本機 Web 服務(local web server)來掛載。  剩下的工作就是如何叫用這個服務。  

上面範例，我們建立一個 WCF 服務，它使用 basicHttpBinding 掛載在 IIS 中，若我們要在用戶端的程式碼中叫用這個服務，它的方法如下：

## 1. 建立存取服務的 WCF Proxy

要叫用 WCF service ，首先必須先產生用來叫用服務的 proxy class ，因為 proxy 穩藏了 WCF 複雜的實作細節，方便 client 端程式叫用 WCF 服務。  要建立  proxy class 只要在 Visual Studio 中執行［加入服務參考］，系統就會自動完成 proxy class 的建置。  另外也可以直接利用 svcutil.exe 工具來建立 proxy class 。  

### 使用「加入服務參考」，建立 proxy class

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhM1ANN9hrxHO8FF-8HeoNdu1rgRn702m2tvreNfxO-TOc1JAPVwqAdeGn426KauSJQH-MXHejeG0d1pHnqQ2wVpwaopIp1TSygLbhyphenhyphenZbTMwUZInvr53yALsryhXTkAFXImm2jsehGDVS4/s437/wcf-add-service-reference1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjk1Jr6Dk0__EMRYkWz2qmjujrpSeTm2Jj972gTw6v9bYcV-_K1sPbFYNIBcNl6AUb5ZraBCZQEhL3Ax_iMjb_TKN2y49WacaaP7fOXJlmmNOLdS-jf-fp_0paDYKmAMT-GlS6gDES3IrM/s642/wcf-add-service-reference2.png)

底下是加入服務參考後，系統自動在 Web.config 中幫我們建立的資訊。主要包含二件事：  

1. 新增一個與服務端點相容的**用戶端點**
2. 並且建立用戶端點的繫結方式
```xml
<system.serviceModel>
<bindings>
<basicHttpBinding>
<！--2. 新增用戶端點要用的繫結方式-->
<binding name="BasicHttpBinding_IService1" 
messageEncoding="Text"
textEncoding="utf-8"
useDefaultWebProxy="true"
transferMode="Buffered"
allowCookies="false"
closeTimeout="00:01:00" openTimeout="00:01:00" 
receiveTimeout="00:10:00" sendTimeout="00:01:00" 
bypassProxyOnLocal="false" hostNameComparisonMode="StrongWildcard" 
maxBufferSize="65536" maxBufferPoolSize="524288" maxReceivedMessageSize="65536" >
</binding>
</basicHttpBinding>
</bindings>
<client>
<！--1. 新增用戶端點-->
<endpoint address="http://localhost:7962/Calculation.svc" 
binding="basicHttpBinding"
bindingConfiguration="BasicHttpBinding_ICalculation" 
contract="MyWcfServices.ICalculation"
name="BasicHttpBinding_ICalculation" />
</client>
</system.serviceModel>
```

### 使用「svcUtil.exe」命令列工具，建立 proxy class

除了使用 Visual Studio ［加入服務參考］外，也可以透過 svcUtil.exe 命令列工具建立 proxy class
```xml
svcutil.exe http://localhost:47001/Service2.svc?wsdl
```

## 2. 設定 WCF 的組態

這個步驟的主要的工作就是要設定一個用戶端點。  基本上，當完成前一步驟時，系統會自動根據該服務的 metadata 自動幫我們建立，但是如果該服務沒有將 httpGetEnabled 屬性設成 true ，就無法取得服務的 metadata ，  這時候就必須自已建立用戶端點，底下是一個用戶端點的基本設定內容：  
```xml
<system.serviceModel>
<client>
<endpoint address="http://localhost:7962/Calculation.svc" 
binding="basicHttpBinding"
contract="MyWcfServices.ICalculation" />
</client>
</system.serviceModel>
```

### 使用 WCF 組態編輯器

關於 WCF 的組態設定，也可以在 web.config 的右鍵選單中開啟 WCF 的組態編輯器來設定，如下圖。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNe-YpFbTnWzD06wPRkPCzDRzKOkCjSfU29Tmb8cCb5tq7neGcTbFCwDKiVgc_O80Y6MgDvP2UwJc8meU622zuBFKHmmdOl-yyzFHjWARCzNv7nlcNGT_50UAyPairpILpQy9uKpGHHEU/s302/wcf-config-editor1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhNgmzZKFpfTW_tgQEoQJN8RMEU2hpE9fKV_E36pFhGvmOGENKAA7_NqWxwfjPh7srv2L_LOteZP_hjg5kwc-djiYKt94HYPfdu8Mf88SzQgZdLVDnnaiwdkAZ2hYuLy8c3UdBtw36qFDA/s694/wcf-config-editor.png)

### 找不到「編輯 WCF 組態」選單項目嗎？

關於 VS2010 的 「編輯 WCF 組態」，通常按右鍵就會出現在如上圖中的選單中，但是有時候這個選單項目就是會出不來。它已確認是個 bug：  

[http://connect.microsoft.com/VisualStudio/feedback/details/617967/edit-wcf-configuration-link-does-not-appear-in-the-wcf-service-web-config-file-at-the-first-instance](http://connect.microsoft.com/VisualStudio/feedback/details/617967/edit-wcf-configuration-link-does-not-appear-in-the-wcf-service-web-config-file-at-the-first-instance)  

這時候可以使用主選單點 ［工具 &gt; WCF 服務組態編輯器］來開啟組態編輯器。  開啟之後就可以立刻關了，此時再去 Web.config 上點右鍵，就會看到「編輯 WCF 組態」選項了。  

或者也可以將 WCF 編輯器的程式加入到開啟方式選單中，它的位置可以在如下的位置找到：  
"C:\Program Files (x86)\Microsoft SDKs\Windows\v7.0A\Bin\SvcConfigEditor.exe"  同樣的，開啟 WCF 編輯器之後就可以立刻關了，此時再去 Web.config 上點右鍵，就會看到「編輯 WCF 組態」選項了。

## 3. 透過 proxy class 叫用 WCF 服務

系統幫我們自動建立的 proxy class ，這個類別的名稱，會直接使用服務類別的名稱，然後再加上 Client 來命名。  
例如，若服務類別名稱為 Calculation ，則 proxy class 的類別就會命名為 CalculationClient。  

使用方法如下：
```c#
using WcfClient_WebApp.MyWcfServices;

namespace WebApplication1
{
public partial class WebForm1 : System.Web.UI.Page
{
protected void Button1_Click(object sender, EventArgs e)
{
CalculationClient service = new CalculationClient();
int total = service.Add(3, 5);

service.Close();
}
}
}
```

# 使用 AJAX 叫用 WCF Service

只要服務端點的定義不同，（設定服務端點的ABC），WCF 就可以提供不同類型的服務。  .Net Framework 已經內建數種服務類型，以支援不同的訊息格式與通訊協定。  其中之一就是以**REST (Representational State Transfer)** 和 **JSON (JavaScript Object Notation)**為基礎的服務類型。  這個服務類型讓用戶端可以透過 AJAX 叫用服服。  

## 比較 webHttpBinding 、 basicHttpBinding 、 wsHttpBinding

- **webHttpBinding** is the REST-style binding, where you basically just hit a URL and get back a truckload of XML or JSON from the web service.
- **basicHttpBinding** and **wsHttpBinding** are two SOAP-based bindings which is quite different from REST. SOAP has the advantage of having WSDL and XSD to describe the service, its methods, and the data being passed around in great detail (REST doesn't have anything like that - yet). On the other hand, you can't just browse to a wsHttpBinding endpoint with your browser and look at XML - you have to use a SOAP client, e.g. the WcfTestClient or your own app.  
So your first decision must be: REST vs. SOAP . Then, between basicHttpBinding and wsHttpBinding, their differences are as follows:  

- **basicHttpBinding** is the very basic binding - SOAP 1.1, not much in terms of security, not much else in terms of features - but compatible to just about any SOAP client out there --&gt; great for interoperability, weak on features and security
- **wsHttpBinding** is the full-blown binding, which supports a ton of WS-\* features and standards - it has lots more security features, you can use sessionful connections, you can use reliable messaging, you can use transactional control - just a lot more stuff, but wsHttpBinding is also a lot \*heavier" and adds a lot of overhead to your messages as they travel across the network

## 建立具備 REST 和 JSON 基礎的 WCF Service

由於 ASP.NET 3.5 開始內建支援 REST 和 JSON 為基礎的 WCF 服務，要建立具備 REST 和 JSON 基礎的 WCF Service 就變的相當簡單。  在樣版中有一個「**AJAX-enabled WCF Service template**」項目，它就是用來建立支援 AJAX 的 WCF 服務。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjb03p3m6yWdOKeW9hQ5FWP_6t7FDjajvkrzyqSFe70M8abEe7xBdcQt-8-0JvDN1VJa0E5y8bnEto4tdb4clV2MsoFm66GlF9Vp57PblISn4KyqrXA8m1591NTOJ_GoOt7-NJj9Iprjus/s507/wcf-ajax-enabled-service.png)

#### 設定檔內容

在專案中加入 AJAX-enabled WCF Service 項目之後，Visual Studio 會自動更新 Web.config ，如下：  
```xml
<system.serviceModel>
<serviceHostingEnvironment 
aspNetCompatibilityEnabled="true"
multipleSiteBindingsEnabled="true" />

<services>
<service name="WcfService2.HRService">
<endpoint address=""
binding="webHttpBinding"
contract="WcfService2.HRService"
behaviorConfiguration="WcfService2.AspNetAjaxBehavior" />
</service>
</services>

<behaviors>
<endpointBehaviors>
<behavior name="WcfService2.AspNetAjaxBehavior">
<enableWebScript />
</behavior>
</endpointBehaviors>
<serviceBehaviors>
<behavior name="">
<serviceMetadata httpGetEnabled="true" />
<serviceDebug includeExceptionDetailInFaults="false" />
</behavior>
</serviceBehaviors>
</behaviors>

</system.serviceModel>   
```

在這個設定內容裡，與前一節中的 WCF 服務最大的不同在於：  

- 端點行為中加入 **enableWebScript** 屬性。它表示這個 endpoint 是一個支援 **JSON** 資料格式的 **REST** 服務，因此可以透過 AJAX 直接叫用。
- 端點的繫結方式採用 **webHttpBinding**。它允許該服務可以直接透過 HTTP 呼叫，而不是透過 SOAP。

#### 服務檔內容

當加入「AJAX-WCF 服務」項目之後，系統除了會幫我們產生一個服務類別檔之外，這個服務類別，不再使用介面而是直接透過類別來定義服務合約。  它同時也會進行以下設定：  

1. 修改 Web.config ，加入 [aspNetCompatibilityEnabled = True](http://msdn.microsoft.com/zh-tw/library/aa702682.aspx) 的屬性  
這個設定指使 IIS 使用相同的 AppDomain 來裝載 WCF 服務和 ASPX ASMX 頁面。  這樣子，ASP.NET 就可以和 WCF 共用 state。
2. 在服務類別(ServiceContract)的屬性中，加入 [AspNetCompatibilityRequirements](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.activation.aspnetcompatibilityrequirementsattribute.aspx)   
由於 ASP.NET request 的語意原本就與 WCF 預設值不同，  所以透過 [AspNetCompatibilityRequirements](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.activation.aspnetcompatibilityrequirementsattribute.aspx) 來指出是否支援 ASP.NET 相容性模式。這個屬性的預設值為 Allowed。

底下是一個「AJAX-WCF 服務」項目的新增範例
```c#
namespace WcfService2
{
[ServiceContract(Namespace = "WcfService2")]
[AspNetCompatibilityRequirements(RequirementsMode = AspNetCompatibilityRequirementsMode.Allowed)]
public class HRService
{
// 預設的 Invoke 為 POST
// 預設的 ResponseFormat 為 WebMessageFormat.Json 

[OperationContract]
public Employee GetEmployee(string EmpID)
{
Employee emp = new Employee(){EmployeeID="001", FirstName="vito", LastName="shao", Age=30};
return emp;
}
}

public class Employee
{
public string EmployeeID { get; set; }
public string FirstName { get; set; }
public string LastName { get; set; }
public int Age { get; set; }
}
}
```

## 使用 AJAX 叫用 WCF 服務

The ScriptManager control allows you to set a service reference to the given [RESTful](http://msdn.microsoft.com/zh-tw/magazine/dd315413.aspx) WCF service.   It then defines a JavaScript proxy class for you to call.   This proxy class manages the call from the AJAX-enabled page to the WCF service.  

要呼叫前面範例中的服務，只要在 ScriptManager 控制項中使用 **ServiceReference** 項目指定實際的服務，如下所示：
```xml
<head id="Head1" runat="server">
<script language="javascript" type="text/javascript">
function Button1_onclick() {
var service = new WcfService2.HRService();
var resutl = service.GetEmployee('001', onSuccess, onFail, null);
}
function onSuccess(result) {
alert(result.FirstName + ' ' + result.LastName);
}
function onFail(result) {
alert(result);
}
</script>
</head>
<body>
<form id="form1" runat="server">
<div>
<asp:ScriptManager ID="ScriptManager1" runat="server">
<Services>         
<asp:ServiceReference Path="HRService.svc" />       
</Services> 
</asp:ScriptManager>

<input id="Button1" type="button" value="invoke .svc with ajax" onclick="return Button1_onclick()" />
</div>
</form>
</body>
```
## 參考資料  

- [MSDN：Windows Communication Foundationn](http://msdn.microsoft.com/zh-tw/library/ms735119.aspx)
- [WCF 入門練習](http://huan-lin.blogspot.tw/2009/03/hello-wcf-using-vs2008.html)
- [Windows Communication Foundation 4.0 的新功能](http://msdn.microsoft.com/zh-tw/library/vstudio/dd456789%28v=vs.100%29.aspx)
- [在 IIS 與 WAS 中以組態為基礎的啟動](http://msdn.microsoft.com/zh-tw/library/vstudio/ee358764%28v=vs.100%29.aspx)
- [HOW TO：建立 Windows Communication Foundation 用戶端](http://msdn.microsoft.com/zh-tw/library/ms733133%28v=vs.110%29.aspx)
- 
-