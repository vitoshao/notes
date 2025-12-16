---
title: 建立網站
layout: default
parent: ASP.NET WebSite
nav_order: 8
description: "建立網站"
date: 2012-11-21
tags: [Web,ASPNET,ASP.NET WebSite]
postid: "8459580374693850558"
---
# Creating Web Sites

You can create a Web project connected to a **file-system-based server** on your computer, an **IIS server**, or an **FTP server**.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEji8AxYsXQZ_WoqR6viKKwFFQJRyIsh97WDyR98UKJbPMHH5hGxWJWdl4Fhgmf6lrHkbeE7kVQYoy3VxI6_BfHR5_RvHBeAZM4wkF7iwc2XchsAnP_zDmJV3fJ5BfhfnMHkumjha-3BCfI/s811/new-website-type.png)

## Creating a Web Site Project

#### Creating a File-System Web Site

The file-based Web site stores all of the files for the Web site inside a directory of your choosing.   This Web site uses the lightweight ASP.NET Web server that is included in Visual Studio.  

#### Creating an FTP-Based Web Site

An FTP-based Web site is one that communicates with a remote server over FTP. This can be  useful if you are using a hosting provider's server, communicating to it over FTP, and wish  to use Visual Studio to edit your files directly on the server.  

#### Creating a Local or Remote HTTP-Based Web Site

You can use Visual Studio 2008 to create Web sites backed by IIS.   This can be useful if you want to run your application against the full version of IIS (and not just a local Web server).   In addition, you might do so if you are coding against a separate development server.

## Creating a Web Application Project

Visual Studio provides you to create another type of porject which is called ASP.NET Web application project.   This project can be useful if you wish to add a Web application to an existing solution that contains other projects   or if you wish to treat your Web application less like a Web site and more like a standard Visual Studio project.  

You do not have the option of selecting the Web site type.  Visual Studio creates the project with the assumption that you will use the file system type and the ASP.NET server.  You can modify this at a later point for ASP.NET Web applications only (as following figure.).  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjG5jzon54Y7-oJP7d7Jy77LiKVB0b53SRgRrAh2eNE1bTZdzTSdj1gpdlnptfO2KNoZHehU_7O2KLniErx__63iK5n6tV09shUYJXFJpLJMEQDUqSlGfiSTj11dCNC0fEKFw2eO1HrbKg/s685/web-project-point-to-iis.png)

## 「Web Sites」轉「Web Application」

有時候基於某些原因，你必須將「Web Sites」專案轉換成「Web Application」專案，例如：您想用編譯器將整個網站建立成單一組件，或者使用 Visual Studio 2010 所提供的自動化部署工具等等。  

Visual Studio 並沒有現成工具可以直接將「Web Sites」專案轉換成「Web Application」專案，你必須參考以下步驟進行轉換：  

### 1. 新增「Web Application」專案

### 2. 在新專案中加入參考設定

### 3. 將檔案複製到新的「Web Application」專案

### 4. 轉換專案檔

Visual Studio 中有個［轉換成 Web 應用程式］選項，可以將 Web 應用程式專案中的網頁與類別轉換成使用部分類別。   當你將檔案複製到新的「Web Application」專案後，你只要在新的應用程式專案中，執行［轉換成 Web 應用程式］功能， Visual Studio 就會自動將 Web Site 的格式轉換成 Web Application 的格式。  

這項轉換流程會讓 Visual Studio 遞迴地檢查專案中的每個網頁、主版頁面與使用者控制項，並自動產生每個項目的 .designer.cs 或 .designer.vb 檔案。   Visual Studio 也會將 .aspx 或 .ascx 檔案變更為使用 codeBehind 屬性，而不使用 codeFile 屬性。   此轉換程序也會將 App\_Code 資料夾重新命名為 Old\_App\_Code。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhbhdkUX-mGxEzrlCwpJTHu15KZK4tZeasoajpTDL1VwnghaoGTplAq2Zu_ZTTqt4jU9j2p2fJYXRGWGWZZXHODkd8JpbkZedcWQ3F7QvXbdMn3W_ovrVG4E8qyYJtkNpGMk2DczzCsHrM/s380/build-action-3.png)

#### 特別注意事項：

為了程式需求，我們常常會在網站中建立自訂類別。  在「Web Sites」專案，我們必須將它放在 App\_Code 目錄中，當系統進行編譯時，ASP.NET 就會自動地對 App\_Code 資料夾中的所有類別檔進行編譯。  但是在「Web Application」專案中，並沒有這樣子的架構，  「Web Application」專案中的每一個檔案都有它的檔案屬性，ASP.NET 會依檔案屬性中的「建置動作」來進行處理，如下圖：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi9ihOJCU3hUd81v87OgILw7B6CuxCjDrIUVzbkPLGUwOl7bmE2smDvswQxRlL07coaoeO-RJJe1zIw_H9NG9Ls6uCxhdOqSDndbanyUX-P24LhgzlPcM96Pojbp2yd4asbl72dH2awWMU/s388/build-action-1.png)

當你對「Web Sites」專案執行 [轉換成 Web 應用程式] 命令後，系統會自動將 App\_Code 目錄重新命名為 Old\_App\_Code ，並且將目錄下的類別檔的「建置動作」屬性設定為「編譯」。  如果之後你又手動複製其他類別檔案到新的「Web Application」專案中，這時候你就必須注意其「建置動作」屬性是否有設定為「編譯」，否則在建置網站時，就會發生遺失組件參考的錯誤。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhO6UvRAWLPI0lnCk1dXfcpJqH8IT_1tN0xLxX_hmanGYZluDr_XQckPwuOHzB9-ZprHr-OUsGiSPR_2-YUp7-8fUsAIXFSl6wwMZxDkf9wuNCNPFmmau3dtnzhq3KxrGON8VOQuWcMecg/s306/build-action-2.png)

# Web Site Compilation

#### pros and cons for dynamic compilation

Most Web applications are not precompiled.   Instead, pages and code are typically copied to a Web server and then dynamically compiled by that Web server the first time they are requested by a user.   On compilation, the code is placed inside an assembly and loaded into your site's application domain.   Additional requests for the same code will be served by the compiled assembly.  

#### pros

- Changes to a file in the site will result in an automatic recompilation of a given resource.   This allows for easier deployment.
- The entire application does not need to be recompiled every time a change is made to a single page or component.   This is great for large Web sites.
- Pages that contain compile errors do not prevent other pages in the Web site from running.   This means that you can test Web sites that contain pages still in development.

#### cons

- This model requires the first user to take a compilation performance hit.   That is, the first request for an uncompiled resource will be slower than subsequent requests.
- This model requires your source code to be deployed to the server   (typically not a major concern in most ASP applications).

#### pros and cons for precompilation

Visual Studio 2008 also allows you to precompile your Web site.   In this case your entire site is compiled and error checked, and only the layout code and associated assemblies are deployed to the Web server.   

#### pros

- Provides verification that all the pages and their dependencies can be compiled.  This includes code, markup, and the Web.config file.
- Performance for the first user requesting a resource is improved as he or she is not required to take the compilation hit.

#### cons

- Precompiled Web sites can be more difficult to deploy and manage, as you have to compile and then copy only the necessary files to the server   (versus simply copying your code files to the server).
## 參考資料  

- [逐步解說：在 Visual Studio 中將網站專案轉換成 Web 應用程式專案](http://msdn.microsoft.com/zh-tw/library/aa983476.aspx)
- 
-