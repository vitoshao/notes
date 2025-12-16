---
title: WCF 簡介
layout: default
parent: Web Service and WCF
nav_order: 2
description: "WCF 簡介"
date: 2012-10-26
tags: [Web,ASPNET,Web Service and WCF]
postid: "7944344814281603396"
---
Windows Communication Foundation   
WCF 它是微軟於 .NET 3.0 開始加入的一組用於通訊 (Communication) 的應用程式開發介面。  主要功能在開發以服務為導向的分散式系統。  

# 前言

在早先 .NET 2.0 裡，若要進行資料交換，為了因應不同的環境需求，必須使用 Web Service、.Net Remoting、MSMQ、COM+ 等不同的分散式元件開發技術。  這些技術各自使用特定的通訊協定，而且交換訊息格式也不盡相同，彼此就無法溝通。  若某個功能要同時滿足不同環境需求，往往必須使用不同技術，重新撰寫。  

WCF 就是希望透過單一方法來統一這些問題。  但是 WCF 並不是提供一個新的技術要來取代這些方法，  它只是一個統一的程式開發模型(unify programming model)  同時支援多種的分散式通訊服務(例如 TCP、FTP、SOAP、HTTP 等通訊協定)。  
&lt;!-- more --&gt;    
#### HTTP

超文本傳輸協定（HyperText Transfer Protocol）  
是用來在客戶端和主機端之間進行請求與回應的一種協定  

#### SOAP

簡單物件存取協定（Simple Object Access Protocol）  
是用來在網路上進行資料交換的一套標準協議  

#### FTP

檔案傳輸協議（File Transfer Protocol）  
是用來在網路上進行檔案傳輸的一套標準協議。  

#### TCP

傳輸控制協議（Transmission Control Protocol）  
是用來在網路上通訊的協議。

# WCF 簡介

Windows Communication Foundation (WCF) 是用於建置[服務導向(SOA)](http://zh.wikipedia.org/wiki/%E9%9D%A2%E5%90%91%E6%9C%8D%E5%8B%99%E7%9A%84%E6%9E%B6%E6%A7%8B)的應用程式架構。  使用 WCF，您可以在各個服務**端點**(Endpoin)之間傳送**訊息**資料。  

- WCF 透過全新的服務導向程式設計模型，簡化所連接應用程式的開發。
- WCF 以提供分層架構的方式支援分散式應用程式開發的多種樣式。
- WCF 通道架構在其基底上提供非同步、無類型的訊息傳遞基本。
- WCF 可以用來進行安全、可靠、交易的資料交換。

每一個 WCF 端點可分成：**服務端點**和**用戶端點**。  服務端點可能裝載在 IIS 上頭，也可以裝載在一般應用程式之中。  用戶端點則大致是某項服務的用戶端，會向服務端點要求資料。  而中間傳送的訊息可以是簡單的單一字元或以 XML 傳送的字組，或者像二進位資料的資料流這般複雜的形式都可以。  

### 傳訊和端點（Messaging and Endpoints）

#### Messaging

WCF 以訊息通訊的概念為基礎，只要是可以模型化為訊息的項目 (例如 HTTP 要求或訊息佇列 (MSMQ) 訊息) 都能在程式設計模型中以制式方式表示。  如此一來，不同傳輸機制上就可以有統一的 API。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj9s2o6T_IHvLgfIiif7hLIuYNX7ToRkJPEL6lVxwxS9zKhebHK0Ha01hQsJLSQbWiZdGzXOa5iiYUDan9dUL45qMVaCYsIaOjwX1w4-70DEfFWON7-97pw5H6oVTuu1S7HBuCimoiVFX4/s259/wcf-endpoints.png)

此模型區別「**用戶端**」(Client) 和「**服務**」(Service)。  前者是啟始通訊的應用程式，後者是等待與用戶端進行通訊並回應通訊的應用程式。  單一應用程式可以同時做為**用戶端**和**服務**。  

#### Endpoint

不管是**用戶端**和**服務端**都必須提供「端點」(Endpoint)，以便讓 WCF 的訊息可以在端點之間傳送。  「端點」裡頭定義了訊息交換所需要的相關資訊，也就是被稱為 WCF 的 ABCs 三要素：**a**ddress、**b**inding、**c**ontract。  如此一來，只要用戶端和服務端的端點設定都相同，.NET 平台的底層就知道如何進行溝通，不需要為發佈服務元件或者繫結務元件而開發一堆程式碼。  

1. 服務的存取位置（**A** **ddress**） ：  
Address 是用來描述服務的位置，每個服務端點都有一個唯一的位置，以 URI (Uniform Resource Identifier) 格式表示。  例如： http://xxx.xxx.xxx/MathService 。  因此，假如該服務同時提供多個端點，你就必須使用不同的通訊協定或者使用不同的通訊埠來區分。
2. 服務的繫結方式（**B** **inding**） ：  
Binding 是用來描述訊息傳遞的通訊協定。  例如：HTTP, TCP, MSMQ, Binary HTTP 等等。
3. 服務的合約內容（**C** **ontract**） ：  
Contact 是用來表達這個服務的公開定義或介面，也就是用來描述服務提供的內容。  在 WCF 中，有多種合約型別(contract type)，例如：service contract, operation contract, message contract, fault contract , and data contract。  這些合約定義是用來讓用戶端知道該如何叫用服務以及該傳送什麼格式的訊息。

### 裝載與叫用（Hosting and Calling)

#### 裝載 (Host)

定義好 WCF 服務，且設定好端點之後，必須再將服務裝載起來，才可以開始執行服務。  例如裝載在 IIS、WAS、COM+、Windows Service、Windows AP、Console AP 等等。  

裝載好的 WCF 服務會提供一個或多個端點，並將這些資訊公開成中繼資料，讓**用戶端**可以用來處理以產生相容的 WCF 用戶端和通訊堆疊。  

#### 叫用 WCF 服務

要叫用 WCF 服務，用戶端必須依據該服務所公開的中繼資料，先建立與服務端點相容的用戶端點。  該用戶端點必須指明**服務位置**、**通訊協定**、**訊息格式**。  再將需求送交給服務，WCF 服務處理完需求後便回傳結果。

# WCF 架構

WCF 是由多個層合力提供功能選項以建置服務導向的應用程式。共分四個層：  

- Contract layer
- Runtime layer
- Messaging layer
- Hosting layer

下圖說明 Windows Communication Foundation (WCF) 架構的各主要層。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiR6FVtjnQ3ar125i2vtWXNatq9nduNktY3OK8KSi68RmWiarDj8txKasi5yEKsLcEGP9zrWroLyT0nAan4D8LzvHH_N4Fcuk8iW0ZF0CSqr9lCPtekpozGZVfGlIsq-rdCbGxWiEEugmU/s322/wcf-architecture.gif)![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh2qVzUlkBFwe_ncJSNOvtBeIAak7IW6qxJF7KcVFUPKhOtU5qsg-KJA5BjyCnB80ZVRxtrn0eito_OZshxnUScuIx3fwaCqi_YhPC7kf9EA3Z2a1WwAx8fEP_sHs0nHkGBWnGEcGCjciE/s322/wcf-architecture-c.gif)

### 1. 合約 (Contract)

The contract layer defines the contract your service exposes to end clients.   This includes the message that the service supports for calling operations, receiving results, and managing errors.   In addition, the contract includes the endpoint information for policy and binding.   For example, the contract might indicate that the service requires HTTP with a binary encoding.  

WCF的基本概念是以合約（Contract）來定義雙方溝通的協定，所以合約層就是用來定義對外服務的合約。合約分成以下四種：  

1. **服務合約**（）：  
服務合約會指定服務的實際方法簽章。
2. **資料合約**（）：  
用來宣告服務功能執行時，所傳遞的資料規格。
3. **訊息合約**（）：  
訊息合約會使用 SOAP 通訊協定來定義特定訊息部分，並允許在互通性 (Interoperability) 要求時對訊息的部分進行更精細的控制。
4. **原則和繫結**（）：  
原則和繫結會規定與服務進行通訊時的必要條件。例如，繫結的傳輸協定，以及編碼方式。

下面程式碼是一個簡單的介面，包含加法與減法二個方法：
```c#
public interface IService
{
int Add(int n1, int n2);
int Subtract(int n1, int n2);
}
```

你只要使用標記語法，在介面中加入 [ServiceContract](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.servicecontractattribute.aspx) 與 [OperationContract](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.operationcontractattribute.aspx) 標記。
```c#
[ServiceContract]   
public interface IService
{
[OperationContract]        
int Add(int ValueA, int ValueB);
[OperationContract]         
int Subtract(int ValueA, int ValueB);
}
```

最後只要實作這個介面，就完成了 WCF 服務。
```c#
// 實作 IwcfService
public class wcfService : IwcfService
{
public int Add(int ValueA, int ValueB)
{
return ValueA + ValueB;
}

public int Subtract(int ValueA, int ValueB)
{
return ValueA - ValueB;
}
}
```

上面這個簡單例子，因為是使用的是字串當作資料交換的格式，所以無需再額外定義 [DataContract](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.datacontractattribute.aspx) 。  如果你的交換格式為物件型別，則必須在物件類別中另外定義 [DataContract](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.datacontractattribute.aspx) 。
```c#
[DataContract]          
public class MyData     
{         
[DataMember]         
public int ValueA { get; set; }         
[DataMember]         
public int ValueB { get; set; }    
}

[OperationContract]
int AddData(MyData data);

[OperationContract]
int SubtractData(MyData data);
```

### 2. 服務執行階段 (Service Runtime)

服務執行階段就是用來控制服務執行階段的行為。這些行為包含：

- 節流設定：控制要處理多少訊息
- 錯誤行為：指定在服務發生內部錯誤時所要發生的動作
- 中繼資料行為：控管採用什麼方式將中繼資料提供給外界
- 執行個體行為：指定可以執行服務之執行個體的數目。例如，「單一」就是指定僅能使用一個執行個體來處理所有訊息。
- 交易行為：使用這個行為讓交易作業在失敗時，可以進行復原(rollback).
- 分派行為：控制 WCF 基礎結構處理訊息的方式。

### 3. 訊息 (Messaging)

訊息層是由「通道」(Channel) 所組成。通道是指能以某種方式 (例如，藉由驗證訊息) 處理訊息的元件。  一組通道又稱為「通道堆疊」(Channel Stack)。  通道運作的對象是訊息和訊息的標頭。這點和服務執行階段層有所不同，該層主要著重於處理訊息的本文部份。   

#### 通道類型：

1. **傳輸通道 (Transport channels)**  
傳輸通道會從網路讀取和寫入訊息。有些傳輸會使用編碼器在訊息和網路所使用的位元組資料流表示之間來回轉換。  這類傳輸的範例包括 HTTP、具名管道 (Named Pipe)、TCP 及 MSMQ。這類編碼的範例包括 XML 和最佳化的二進位編碼。
2. **通訊協定通道 (Protocol channels)**  
通訊協定通道會實作訊息處理通訊協定，而此通常是藉由讀取或寫入訊息的額外標頭來完成。  這類通訊協定的範例包括 WS-Security 和 WS-Reliability。

訊息層說明資料的可能格式與交換模式，包含下列幾種通道（也就是 WCF 支援的通訊協定）：

- WS-\*：
- WS-Security：WS-Security 是 WS-Security 規格的實作(Implementation)，此規格會啟用訊息層的安全性。
- WS-Reliable：WS-Reliable 訊息通道可保證訊息的傳遞。
- Encoders                ：編碼器會提供可以用來滿足訊息需要的各種編碼方式。
- HTTP Channel            ：HTTP通道會指定使用 HTTP 協定傳遞訊息
- TCP Channel             ：TCP通道會指定使用 TCP 協定傳遞訊息
- Transation Flow Channel ：交易流程通道會管理交易的訊息模式。
- Named Pipe Channel      ：具名管道通道會啟用處理序間通訊。
- MSMQ Channel            ：MSMQ 通道會啟用與 MSMQ 應用程式的互通。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjOzjy1OhXEOtQtRAfYYup24PALslBCBh2lrU8v73cOHf7t8-86_HQVoVudTRWigblEI0KoRsmyv5_OBrV03RDJ2QQlPMKy-wHKZBzCt9Fy3wrv_LASVdxLSJebcLz-WqQUqnhR2UojKLU/s373/wcf-service-client-endpoint.png)

#### 通訊協定 (Communication Protocols)

「**傳輸通訊協定**」(Transport Protocol) 是通訊堆疊 (communication stack) 的一個必要項目。  例如使用通用傳輸協定 (HTTP, TCP)，才可以在內部網路和網際網路上傳送訊息。  

「**編碼方式**」(encoding) 是通訊堆疊中的另一個必要項目，它是用來指定訊息的格式化類型。  
WCF 提供下列編碼方式：   
&gt;**Text encoding**, an interoperable encoding.   
&gt;**Message Transmission Optimization Mechanism (MTOM) encoding**, for efficiently sending unstructured binary data to and from a service.   
&gt;**Binary encoding** for efficient transfer.   

WCF 的「傳輸通訊協定」和「編碼方式」都可以由過 WCF 的內建擴充點加入（built-in extension points of WCF）。  

#### 訊息模式 (Message Patterns)

WCF 支援數種傳訊模式，包括要求-回覆、單向和雙工通訊。(request-reply, one-way, duplex communication)  
不同傳輸支援不同傳訊模式，因此會影響所支援的互動類型。  
WCF API 和執行階段也有助於安全、可靠地傳送訊息。

### 4. 裝載和啟動 (Hosting and Activation)

WCF 服務必須被裝載(hosting)才可以執行服務，就像 XML Web Service 必須裝載在 IIS 上。  WCF 程式可以裝載在 IIS、WAS、COM+、Windows Service、或自行撰寫裝載的應用程式。  同一個 host 可以裝載多個 WCF 服務。  

#### WAS 裝載

其中 WAS 稱為 Windows Activation Service ，它是微軟提供的通用裝載引擎，  在 Windows Vista、Windows Server 2008、Windows 7 的系統服務中就可以看到這個服務。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjVZC-3eGnOLX5IfYUQWZ6MLVU7dZ2B3PtdZW_iSFSJ3VZ_TucIzb7iwcRlX5wB0kCc1r0DsCd9cCIiFBp1pCL0T233nLDGihSw5MXp96wK_4ocx4zdJNYf7fVHxcXxrN5hihTyB5OYQw8/s776/wcf-was.png)

#### 自主裝載

若開發人員自已撰寫一支應用程式裝載 WCF 服務，就稱為自主裝載(self-hosting)。  這個應用程式可以是 Windowss Form, WPF, Console, Windows Service。

# WCF 服務合約

由 WCF 架構圖和設計步驟中的說明可以知道，WCF 的一切都是由合約開始。  WCF 共包含下列四種合約：  

- [服務合約(ServiceContract)](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.servicecontractattribute.aspx) ： 用來標記**類別**，表示這個類別是用來定義 WCF 服務的類別。
- [操作合約(OperationContract)](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.operationcontractattribute.aspx) ： 用來標記**方法**，表示這個方法是 WCF 服務所提供的操作方法。
- [資料合約(DataContract)](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.datacontractattribute.aspx) ： 用來標記**自訂型別**，自訂型別一定要定義型別的資料合約，才能讓型別能夠進行序列化。
- [資料成員(DataMember)](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.datamemberattribute.aspx) ： 用來標記**自訂型別的成員**，指定該成員屬於資料合約的一部分。

其中，[ServiceContract](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.servicecontractattribute.aspx) 和 [OperationContract](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.operationcontractattribute.aspx) 這二個屬性類別定義在 [System.ServiceModel](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.aspx) 命名空間。  [DataContract](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.datacontractattribute.aspx) 和 [DataMember](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.datamemberattribute.aspx) 這二個屬性類別定義在 [System.Runtime.Serialization](http://msdn.microsoft.com/zh-tw/library/system.runtime.serialization.aspx) 命名空間。  

- 合約的定義，在C#中以中括號([])表示，VB.NET中以角括號(&lt;&gt;)表示。
- 標示為[OperationContract]的方法，表示是公開的服務功能，否則就為服務內部才可使用的方法。
- 合約的定義，可以用來產生服務的 Metadata ，用來讓用戶端程式分析後以建立 Proxy 程式。

底下是一個簡單的 WCF 服務範例：
```c#
[ServiceContract]
public interface ICalcService
{
[OperationContract]
int Add(int a, int b);
}
```

# WCF 繫結支援

所有的通訊服務，都會因為其服務的對象，不同的通訊協定，提供不同的服務程式。  為了簡化這一個層程式碼的複雜度， WCF 採用繫結（Binding）的方式來簡化設計。  透過「繫結」來指定用戶端與服務彼此通訊所需要的傳輸、編碼與通訊協定詳細資料。   簡點講，「繫結」定義了用戶端與服務端的溝通方式。  

## WCF 內建的繫結方法

下列幾項都是 WCF 內建的繫結方法，以因應各種不同的需求。你也可以使用自訂的繫結，以支援特定的需求。  所有內建的繫結方法，都定義在 [System.ServiceModel](http://msdn.microsoft.com/zh-tw/library/system.servicemodel.aspx) 命名空間。  

### 01. Basic binding

這個繫結由 BasicHttpBinding 類別提供。

主要提供與舊有 ASMX web service 相容的服務，以支援還在使用 ASMX web service 的用戶端。

### 02. Web binding

這個繫結由 WebHttpBinding 類別提供。

主要支援 Http-Get, Http-Post ，也就是提供 Http requests 支援。  因此，像採用 XML 與 JSON 資料格式的 REST/POX 服務，就可以使用這個繫結。

### 03. Web Service binding (WS-\* binding)

這個繫結由 WSHttpBinding 類別提供。

它類似 Basic binding, 並使用 http 或 https 傳輸協定。  但是它可以提供各種進階的 WS-\* 規格服務，例如：WS-Transaction、WS-Security 等。

### 04. WS Dual binding

這個繫結由 WsDualHttpBinding 類別提供。

它類似 Web Service binding, 並支援雙向訊息傳輸，也就是 client 和 server 二端都可以接收和傳送訊息。

### 05. TCP binding

這個繫結由 NetTcpBinding 類別提供。

支援 TCP 傳輸訊息，適合跨電腦之間的溝通。  這個繫結會將訊息以二進位方式編碼，與 Http 繫結比較的話，算是比較快速且可靠的繫結方式。

### 06. IPC binding

這個繫結由 NetNamedPipeBinding 類別提供。

這個繫結使用命名管道(name pipe)，適合使用在同一台電腦中，多個系統間的溝通方式。

### 07. MSMQ binding

這個繫結由 NetMsmqBinding 類別提供。

搭配訊息佇列，適用於跨電腦的通訊，尤其是離線的環境。

### 08. Federated WS binding

這個繫結由 WSFederationHttpBinding 類別提供。

能與支援WS-Federation安全性的WCF服務溝通。

### 09. Peer Network binding

這個繫結由 NetPeerTcpBinding 類別提供。

適用於對等網路(Peer-To-Peer)。

### 10. MSMQ Integration binding

這個繫結由 MsmqIntegrationBinding 類別提供。

能和現有支援COM的MSMQ應用程式和WCF整合。

## 選擇適當的繫結方法

依據需求，你可以參考下圖中的分類，選擇一個適用的繫結方法：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjarIvvVP-IAltg9n7ca7av7BbDGlODlmeTXcXHeCxeqI5V1lES54XW1N4WDR2u41Wrz4O9mN4V7URiQi5hfKqD0LsdmmiUuxLqI6T5TbjOYFmF27ML1fMCMknriTxN1uJtqwR5rlOwmQ8/s583/wcf-choosebindings.png)
## 參考資料  

- [Windows Communcation Foundation 繫結](http://msdn.microsoft.com/zh-tw/library/ms733027%28v=vs.110%29.aspx)
- [使用繫結來設定 Windows Communication Foundation 服務與用戶端](http://msdn.microsoft.com/zh-tw/library/ms733824%28v=vs.110%29.aspx)
- [設定系統提供的繫結](http://msdn.microsoft.com/zh-tw/library/ms731092.aspx)
- 
- 
-