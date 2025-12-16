---
title: 單鍵發行網站
layout: default
parent: ASP.NET WebSite
nav_order: 10
description: "單鍵發行網站"
date: 2013-03-12
tags: [Web,ASPNET,ASP.NET WebSite]
postid: "296579511351069539"
---
「單鍵發行網站」（web 1-click publish）是 VS2010 才加進來的功能，  使用這個功能發行網站時，它會透過 Web Deploy API 直接與 IIS 溝通，並同時將網站內容安裝到 IIS ，  同時，相關的組態設定、應用程式集區、IIS 細部設定、安裝組件進 GAC、安裝憑證等等的工作，也都可以事先設定好「發行設定檔」，就可以輕鬆的在發行網站時一次解決。  

「單鍵發行網站」只適用於 Web Application 專案，不適用 Web Site 網站。  詳情可參考保哥這篇文章介紹：[如何使用 Visual Studio 2010 的「單鍵發行」功能](http://blog.miniasp.com/post/2010/05/03/Deploying-Web-Application-Project-Using-One-Click-Publish.aspx)。  

![](http://127.0.0.1:8080/_images/cs/deploy-1click-publicsh-01.png)

要使用「單鍵發行網站」（web 1-click publish），必須先確認以下設定：  

## 環境設定

### IIS 管理服務（IIS Web Management Service, WMSvc）

若要允許 IIS 管理員可以連線到站台，必須先啟用「IIS 管理服務」。  

在 Windows 7 ，可透過 [ 開啟或關閉 Windows 功能] 來啟用「IIS 管理服務」。

![](http://127.0.0.1:8080/_images/cs/deploy-1click-publicsh-02.png)

在 Windows Server 2008 ，可透過 [網頁伺服器(IIS)] 安裝「管理服務」這個角色服務。

![](http://127.0.0.1:8080/_images/cs/deploy-1click-publicsh-03.png)

### 啟動 Web Deployment Agent Service

若在 Windows 7 使用 Web Platform Installer 預設不會安裝 Web Deployment Agent Service，你必須手動安裝 MSI 檔的方式才會安裝進去，安裝步驟請參見 [Installing Web Deploy](http://learn.iis.net/page.aspx/421/installing-web-deploy/) 說明。

![](http://127.0.0.1:8080/_images/cs/deploy-1click-publicsh-04.png)

## 在 Visual Studio 2010 設定 [ Web 發行 ]

在 Visual Studio 2010 必須設定一個 [ Web 發行 ] 要使用的「發行設定檔」。  

![](http://127.0.0.1:8080/_images/cs/deploy-1click-publicsh-05.png)

如果沒看見 [ Web 單鍵發行 ] 工具列，可以在工具列的地方按下滑鼠右鍵選取 [Web 單鍵發行] 即可：  

![](http://127.0.0.1:8080/_images/cs/deploy-1click-publicsh-07.png)

設定「發行設定檔」。  

![](http://127.0.0.1:8080/_images/cs/deploy-1click-publicsh-06.png)

## 執行「單鍵發行」

上一步驟，若發行成功後，下次就可以直接使用「單鍵發行」的功能：

![](http://127.0.0.1:8080/_images/cs/deploy-1click-publicsh-08.png)

在發行過後，當再次使用「單鍵發行」時，因為它只會發行更新過的檔案，所以可以提升不少效率。