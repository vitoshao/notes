---
title: SSIS安裝
layout: default
parent: SSIS
nav_order: 4
description: "SSIS安裝"
date: 2013-12-20
tags: [SQL,SSIS]
postid: "1781781612564101612"
---
由 SQL Server 2005 開始，新的 SSIS 平台取代了原本的 DTS 功能，你可以在這個平台上執行資料整合與工作流程作業。  而 SSIS 的核心元素就是「SSIS Service」，無論封裝是部署到檔案系統或者資料庫系統，所有的封裝都會掛載到這個服務之中，由該服務負責執行。  

不過，到了 SQL Server 2012 ，「SSIS Service」這個服務程式就不再是必須的，它的存的，只是為了回溯相容於舊的封裝部署模式。  而封裝的管理與執行都由新的服務程式「SSIS Server」負責，它就是 SQL Server 執行個體本身，只不過要在執行個體上，特別掛載 SSISDB catalog 。  因為所有的 project, package, parameter 等等的資料，都儲存在這個 catalog 之中。  

# Installation

在 SQL Server 2012 中，Integration Services 服務，除了為了執行回溯相容的工作外，另外的功能還可用來管理 SSIS 封裝。  若只是要部署或執行新版的 SSIS 封裝，並不需要 Integration Services 這項服務。  

一部主機上只能安裝一個 Integration Services 的執行個體。  此服務並非特定 Database Engine 執行個體所特有。  您只要使用執行該服務所在之電腦名稱即可連接至服務。   

參考以下文章：

- [安裝 Integration Services](http://msdn.microsoft.com/zh-tw/library/ms143731.aspx)
- [升級 Integration Services 封裝](http://technet.microsoft.com/zh-tw/library/cc280547.aspx)
- [設定 Integration Services 服務](http://msdn.microsoft.com/zh-tw/library/ms137789.aspx)

#### Verify the SSIS Installation

安裝完後，可以檢查服務是否正常啟動。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgj32-Y-Gn1JdOdziCiQIoVJI7NfuvePa9aSs8ELSeAZRSmV0qj9u7VCgd8nsJxkKb10Wodl6PbGSP5BdGn8tyo2Qy4NCZTY66-5ZgyG_sssItUww39CO1Kz3c52ePUbu0tc-yHm4KOa4I/s0/ssis-verify-installation.png)

# SSIS Tools

下列項目都是 SSIS 2012 提供的工具程式：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhY52GEPSLqoDZVA6tyJ7Wh4tXVwnHvLyvNln8Y_PVus1k8z0jkpW3ndFJrrolt34x9ZDhfX2lt-oOE2LwrrVEfaW-_QPUCB_5IwZAOVag_1Zx-tqB7LFk5sh07EoV2X4RypP3tLMwMpfQ/s0/ssis-tools.png)

### 1. SQL Server Import And Export Wizard

參考上圖＜1＞

### 2. SSIS Deployment Wizard

參考上圖＜2＞

### 3. SSIS Project Conversion Wizard

參考上圖＜3＞

### 4. SSIS Execute Package Utilities

參考上圖＜4＞

### 5. SSIS Package Upgrade Wizard

使用 [Package Upgrade Wizard](http://technet.microsoft.com/zh-tw/library/cc280547.aspx) 可以用來升級 SSIS 封裝，你可以由以下三個方式執行這個精靈：  

- 在 SSDT 中，開啟舊版封裝，在節點上選擇 [升級所有封裝]。
- 在 SSMS 中，連接至 Integration Services ，找到封裝節點，然後按一下 [升級封裝]。
- 在命令提示字元處，於 C:\Program Files\Microsoft SQL Server\110\DTS\Binn 資料夾執行 SSISUpgrade.exe 檔案。

### 6. SSIS Package Utility

Used to manage SSIS packages  (for example, to copy, move, or delete them, or to verify their existence) from the command line.

### 7. SSIS Package Installation Utility

由於舊版的 package 無法直接部署到 SSIS Server。必需先使用 SSIS Project Conversion Wizard 轉換成 SSIS 專案，才可以部署到 SSIS Server。  但是，若你不想升級 package ，你還是可以利用 [SSIS Package Installation Utility] 這個工具，將舊版的 package，部署到SQL2012執行個體。
## 參考資料  

- [Integration Services 服務](http://technet.microsoft.com/zh-tw/library/ms137731.aspx)
- [設定 Integration Services 服務](http://technet.microsoft.com/zh-tw/library/ms137789.aspx)
- [SSIS 目錄](http://technet.microsoft.com/zh-tw/library/hh479588.aspx)
- [建立 SSIS 目錄](http://technet.microsoft.com/zh-tw/library/gg471509.aspx)
- [專案部署和封裝部署](http://msdn.microsoft.com/zh-tw/library/hh113290.aspx)
- [將專案部署至 Integration Services 伺服器](http://technet.microsoft.com/zh-tw/library/hh131102.aspx)
- [封裝部署 (SSIS)](http://msdn.microsoft.com/zh-tw/library/ms137592.aspx)
- [使用 SSIS 封裝升級精靈來升級 Integration Services 封裝](http://technet.microsoft.com/zh-tw/library/cc280547.aspx)