---
title: 部署網站
layout: default
parent: ASP.NET WebSite
nav_order: 7
description: "部署網站"
date: 2012-10-17
tags: [Web,ASPNET,ASP.NET WebSite]
postid: "2818895865929690218"
---
Websites, by default, are entirely file based.   This means that the source code is usually deployed to the server.   The server then compiles that code when pages are requested.   In this scenario, you can deploy a website to a web server by simply copying the files to the correct directory on the server.  No special deployment software is required.  

If your website is to be deployed to an array of web servers, you can use any file-synchronization tool to copy the files between the servers.   This allows you to deploy to a master server and have the deployment synchronized across the web farm.  

If your website has a database, requires special web server configuration, or needs different configuration settings in a release environment,   simply copying files will be insufficient.   

Visual Studio 2010 includes a new Publish Web dialog box that allows you to configure each of these settings once,   and then publish a website to one or more web servers with a single click.   

# 部署網站

## 使用 Web Setup Project 部署網站

這個方法是使用 Web Setup Project 建置後產生檔案，它有二種格式：

- **setup.exe**  
這是一個可以用來安裝檔案的可執行檔，啟動後，安裝精靈會一步一步提示使用者，以完成安裝步驟。
- **WebSetup1.msi**  
這是一個 Windows Installer 檔案，包含了所有加入到 Web Setup Project 中的檔案。      使用者也可以直接使用這個檔案進行安裝，就如同setup.exe。      除此之外，網路管理人員也可以使用 AD 軟體發佈工具或者 Microsoft System Management Server 來發佈.msi 檔案。

#### Microsoft Install (msi)

雖然大都數人都習慣 setup.exe，但是 .msi 卻是一個檔案較小且用途更廣的檔案。  唯一缺點就是目標主機上必須要有 Windows Installer ，不過現在大部份機器上都己包含這個元件。

## 使用 Copy Web Tool 部署網站

如果只是要做網站更新的工作，直接使用 Copy Web Tool 會比透過安裝程式來的方便且快速。  「Copy Web Tool」僅適用於 Web 網站，若是 Web 專案，可使用下節的「單鍵發行網站」。  

Copy Web Tool 可以僅複製幾個檔案，也可以複製整個網站。  透過這個工具的搬移功能，就可以將檔案更新到遠端主機。  同時它也支援同步功能來更新檔案。  

有二個地方可以啟動這個功能，如下圖：

1.由方案總管中，開啟網站的右鍵選單

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhXsP_DWwSIw9hM5tMfv4kDoaWYCphDsbvDPOO1QX_Iz1ltXKdxZar635ipOgV3vWKGD5iGOnwDXYvXC1iQ4Y4zMyZqpZqUATXsyNYrI200-g5AaBRs2tftcRhyUL2SAi1xRBet9u2-GXM/s376/websetup-copy-web-tool-open1.png)

2.由主選單中的[網站]功能

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjTTsiqM_s7tOs2pir2oiRCz-iMd8lMP4BbDKswvsV-gJcyJiNRc6FkizgZ1UpOlry8dAwd-pltD5aSNqCm2CMp0y_hX2oQQ2R_rWhOLpRe7PHUVFNs5Trw-JEtxAqhIL2v6LX3AiZveCI/s400/websetup-copy-web-tool-open2.png)

啟動後如下圖畫面：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhuA6pVfZOuv-uI9G7f7xFAWUtObmqdqv3gyEtoFb-ytz1L5HfcDlQEqE2YEhlAjL5sJRAKKFyAqm4KmHHSidNyBkcJScECY5ek2VO2op0T4tyjLLibyfQKiweAlcuvBLA14fffb3XiIZg/s792/websetup-copy-web-tool.png)

點選「連接」，設定主機資訊，共有以下四種連線方式：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhEhZDaqIFCeSCNrSDdytvbglRo-7UnPJUnroSo3L88CViRYjqmp85Lh5fFYELIVsXo-tiGnZBSJT8ohRHjLtYOeYBmV30N8IlZoCoT6nwT3pjLwccXQobUc0yN-OKJw1MwjMsm0hEIONA/s676/websetup-copy-web-tool-connect.png)

建立連結後，可以在來源與遠端網站間，以多種不同方式複製或同步檔案：

要注意，若同時有多人在維護同一個網站，要注意彼此覆蓋的問題。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMdD8woXnFRFl6jDxj6tcjyoqftdIIq__EZer90LqL6Owy-YKOtQhcC1FInridY9L-fDbjHHUf73s_2_2KPb4gjGAtJI-2gWHIo4jL9kpGzhyeLnZeMg0xNPz5N8yAAC84ighFk4rtKSc/s722/websetup-copy-web-tool-upload.png)

## 使用 Publish Web Site Tool 部署網站

這個方式適用於 Web 網站和 Web 專案，只是二種使用方式不太相同。  

### Web 網站的發行

#### 設定編譯選項。

使用Web 網站的發行，可以設定是否先行編譯，以減少網頁執行時才編譯的回應時間。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiA2UaNRKeYhr0QhNSjKnHPC_ZJiCrBLcIBxqjplkT2QlmX-oRxm3dLTMwpLxSD5eLP71Q3FDJAd_p01i4pMj3RZxvWBru0iSg0CRptO9X_uOgSfLS3ubRgpbXOTZgJ92wVoTSGMCGnU4w/s661/websetup-publish-web-site-1.png)

#### 設定發行目標位置。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhmZq92BBok1wpSWI-EFVWv-e0HJevcU3gdvXvn4cMSBXDOJDBnrsxRb9dbUv1Th3OeuKDYujrnSwtQff1G8Xoz66wm8zfblBCPPq4EP1DvAmH899zRiErDzSRsIekedW6vbNUshdM5MNs/s676/websetup-publish-web-site-2.png)

### Web 專案的發行

使用Web 專案的發行，一律會先行編譯。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgxFPo3dqnxrIcGCtvD5ecmrz-yF6gfQDa6pM3mIBB9mZqYs2o3YFiTs0CjsuGAtGH3JsaxJLtSeyCM5jQ8zuxL3eFWgKQOJ0BgNbx18tkH3k1KMSx0Y52Bi8wwvVthiNZbZ3vYVwG5-sg/s479/websetup-publish-web-site-3.png)

## 使用「單鍵發行（1-click）」部署網站

「單鍵發行網站」僅適用於 Web Application 專案，是 VS2010 之後所提供的一種方便快速的發行方式。  請情可參考這篇 [單鍵發行網站](http://vito-note.blogspot.com/2013/03/blog-post.html)

## 使用 Web Deployment Projects 部署網站

當發行一個Web型網站時，其建置出來的 dll 檔名會依特定規則給予不同的名稱，如下圖：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiqLe1vh1Ih_h_915aPz1B4yhI0htCXaQmDHqWojSjSWJQ88mtbQ4cFhyphenhyphenkz3G139JEzE9mwOKTBtRTLOMs-quF2vFmFcU1XBcCT7sYinyNmGMtBB9d0i0HwoQbsMWAL1pe4zQTltweprwg/s486/deploy-web-build-result.png)

若是使用單一頁面固定命名，還可以由檔名判斷dll與程式碼的對應關係；  若沒有使用固定命名，要想知道各個dll對應到的是哪個aspx，就必須要先找到該aspx，開啟後看一下他的inherits才能找出到底要版更時我要換哪個dll才對：  

總之，發行一個 Web 型網站後，會產生一大堆 dll 。在部署到主機時，就會顯的有點麻煩。若不想這個狀況，就可以改用 Web Deployment Projects 。  這個 WDP 工具是在 VS2010 時被釋出，它是一個外掛在 Visual Studio 上的專案類型，主要目的是提供你將現有 ASP.NET **Web型網站**或**專案型網站**另一種部署網站的方式  

與 Publish Web Site 的不同的是，Publish Web Site 是直接將網站發佈到一個目錄下，但 Web Deployment Projects 必須先新增一個獨立的 WDP 專案到方案中，然後先預先設定組件名稱(Assembly Name)與網站建置時的輸出目錄。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiWQs53RKMlzikuymLzREwOjGbSBQN4I3rImPiBkRSI0JG20S-ikqhgGeboGzgRCOF7-l0LJfehZF2H7Xmw59FyU0g6tY8OIo-toIiylyO8Awp03Baz0SAwf3ukQvSgQA77vsLLmIa1WQU/s379/deploy-add-web-deployment-project.png)

若要下載 Web Deployment Projects 可以參考以下網址：

- [Visual Studio® 2010 Web Deployment Projects - RTW](http://www.microsoft.com/en-us/download/details.aspx?id=24509)
- [Visual Studio® 2008 Web Deployment Projects - RTW](http://www.microsoft.com/en-us/download/details.aspx?id=25163)

關於詳細的 Web Deployment Projects 使用方法，可以參考以下網址：

- [\[Will保哥\] ASP.NET 網站部署可考慮使用 Web Deployment Project](http://blog.miniasp.com/post/2009/01/Visual-Studio-2008-Web-Deployment-Project.aspx)
- [Deployment Project的使用](http://www.dotblogs.com.tw/jimmyyu/archive/2009/07/14/9500.aspx)

# Web.config 轉換 (Transformations)

Web.config 轉換是 ASP.NET 4.0 新增的功能，這個功能僅適用 Web 專案  它可以協助你在部署網站時，針對不同的部署主機設定不同的 web.config 內容。  在專案型網站網站中，預設 web.config 檔案底下會有二個設定檔，web.debug.config 和 web.release.config ，  你也可以新增一個設定檔，例如，自行建立一個 web.staging.config 設定檔。  這些不同的設定檔，讓你可以針對執行主機的實際狀況，建立不同的設定值，例如，資料庫連線資訊等。  等到真正要發行或部署專案時，就會動態的以適當的內容取代 web.config 相同項目的設定值。  

## 如何建立自訂的組態設定檔（build configuration）

當建置專案時，系統預設只會提供 debug 和 release 二個組態設定檔，你也可以建立自已的組態設定檔。  

1. 在組態管理員中，新增方案組態：  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi52rQN-m8MMFa83geR9EKkejSIS9mRmesdEwNBJSF557kCChP8emVCFYqaiLqTwKdAzIJgnKtee6QrGMkwJvTGzOn64-fCZ-u4w20LOTj-qoCEwiMgS2KwremRQmU6lBWPRWXYvd93KHs/s201/solution_configuration1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhfEX1yke2YZjTgoB8ZhRo2eUP1eF086W1UF2sMWfozOJkjJ179m8LlY2X9vmv8NuqrWKwjs3pvUpXECpBGzuFWe0bcLO3h8AhJOLLiRso4kYRl-40osK9R-BkJN8nxUsMEBVDExzQCNEc/s717/solution_configuration2.png)
2. 新增組態轉換：  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiIKaioUud6xb44ZUQlC7hihG1g6mRmn1eiFwsGcdmpBwv4Y7PB7zIB1iFTh7LFz34oJRevHEM9lCfMBxqcsFkfn2wl5JBbA1-p7ipiHVQdrL9neBHgnD-TmuuZ06PBcGFtvj52zorM8no/s376/solution_configuration3.png)

最後你就可以在 web.config 底下看到新的組態設定檔。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiVnIZKnsGBTutq3zPbTI339xscEPHlbh9uV-a3-fvYGWG5o9W_sgqdHe2zwpuVCVaSFmW8WIa4jLbLGWljRHcUPFYIbgEaLdNCtdHUY_4OVuLeT79Q15rXdGEOzgcFCuJAK1LKwAubrLY/s348/solution_configuration4.png)

## 建立組態設定檔轉換內容

經由上一個步  Web.Config 中的設定內容是基礎的內容，當進行網站部署時，你可以針對不同的建置組態，對特定的內容進行「新增」、「取代」、「刪除」的操作。  

### 加入設定

Transform="Insert"  
你可以使用這個技巧，將共同的設定內容放在 web.config 檔中，然後將不同的設定內容放在各別的組態設定檔中，當網站發行時，這些各別的設定內容就會被加入到 web.config 之中。  
```xml
<appSettings>
</appSettings>
```
```xml
<appSettings>
<add key="gClientId" value="1042829788251-l58ngmtphk14g9nc855hu0couvkmle8s" xdt:Transform="Insert" />
<add key="gClientSecret" value="6QKgG13hKIOCuo" xdt:Transform="Insert" />
</appSettings>
```

### 取代設定

Transform="Replace"  
你可以使用這個技巧，修改 web.config 檔中特定的項目。  
```xml
<appSettings>
<add key="gClientId" value="1042829788251-l58ngmtphk14g9nc855hu0couvkmle8s" />
<add key="gClientSecret" value="6QKgG13hKIOCuo" />
</appSettings>
```
```xml
<appSettings>
<add key="gClientId" value="1042829788251-6fbhecc8ut752l7umrotjnd0gn2c4bq3" xdt:Transform="Replace" xdt:Locator="Match(key)" />
<add key="gClientSecret" value="YoF2E-GRsesx5" xdt:Transform="Replace" xdt:Locator="Match(key)" />
</appSettings>
```

你也可以將整個群組取代掉。  
```xml
<appSettings xdt:Transform="Replace">
<add key="gClientId" value="1042829788251-6fbhecc8ut752l7umrotjnd0gn2c4bq3" />
<add key="gClientSecret" value="YoF2E-GRsesx5"/>
</appSettings>
```

### 移除設定

移除設定是指移除設定項中的特定屬性，而不是整個設定項。
Transform="RemoveAttributes()"  
例如，若要移除 &lt;compilation&gt; 中的 &lt;debug&gt; 屬性，可以這麼做：
```xml
<configuration>
<system.web>
<compilation xdt:Transform="RemoveAttributes(debug)" />
</system.web>
</configuration>
```

## 組態設定檔轉換語法

Web.config 的轉換語法可參考 MSDN：[http://msdn.microsoft.com/zh-tw/library/dd465326.aspx](http://msdn.microsoft.com/zh-tw/library/dd465326.aspx)

# 如何建立 Web Setup Project

## 建立安裝專案

建立網站的安裝專案和建立桌面程式的安裝專案是類似的，而且還提供Web應用程式所需要的特定功能。

1. Open your Web site in Visual Studio.
2. Add a new Web Setup Project  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhbxXmKJZFu71uSSrhU_dkxc3uMNDNheNFPsGKvEKRPplcVZODEWAySprM6EHgjPrPXr5zkcbjBGShn8ronDB2gkaJZQnki2aBB58B7hmvQuARc2sPJMTYWBDhAxah7-BIHdeUEcgVbGk4/s538/add-web-setup-project.png)
3. Add a project output  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjqNoIbjBK0Aens8buriGcHd9a_ILfz-H9xMhurE38zw2RmB_ihgNHHh6deB0aIYgpZ4TVT7_bKE_IYk01GPh9mFcx-HA2KWD8DGmtVd5RXvGRKC1s7BMgkzf9IMGhGFcE0ZiEkZcP2v-4/s559/project-output-create.png)
create a project output group  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9tA032cMWhGPHOfLSvmVejtfya-DuGzWrEvKzwPbDuq3BdwbGvgyBVnsjG7jZ-FCw9U4XKQNFlcQA8idZDD9pf1p71E7hN9h2U98twxnc7SLWyioss_bxAL0JwZ1ZROcaj0uMs0-HzFM/s398/project-output-group.png)
4. Add additional folders, files, and assemblies that are not part of your project output.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgM7_d2boABS51sAexgp8lVd9zMBxL7k6xhBUHjUUp0bv9K5nFWemad-Jvxj8DL6jyupIr_18lkyQWS0cBevkF8UIhI0zmyTXSTgZB5sdbTNI1UWeoSTZtv_8-k8o1Waxeau7c8nU6RLBQ/s703/project-output-additional.png)
5. Building a Web Setup Project  

當build網站時，安裝專案並不會自動build，必須手動點選安裝專案再build。          當build安裝專案時，VisualStudio會先驗證網站的程式碼，然後產生 .msi 檔案，將專案的相關檔案都包裝進 .msi 檔。          透過 VisualStudio 的輸出視窗，可以看到所有的建置部驟以及最後 .msi 檔案的產生位置。          

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhWPI28-SLfemuVsF2hSdy2jNwGdvLdICbvLsI0fy5HnywIihaeOET2GySW7T9Ug7nwnrVVNYld6UgPLeA3RJXcR18ZTAun_kCHuwazijZJSALNEb9xuJET7tJh05XLvJ8u1PczUpIet-A/s756/web-setup-build-process.png)

## 設定部署屬件

大部份專案在以上步驟就可以完成建置專案，但是如果比較複雜的網站可能會使用到如：自訂register或偵側作業系統版本等。

要設定部署屬件，必須打開啟動條件編輯器(Launch Conditions editor)，步驟如下：

- 在「方案總管」點選你的安裝專案
- 選擇「檢視」→「啟動條件」      
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhCMnrt5j5HgmD4vLWqsLe55IWGkEzIym1CRNDwyjT758gRQMzIVOhIMPueZJArOVEEFpudxW8OWh1HhMeqEzI7kP_aKhsd7dZBkv1-E_e7j68gS2qNXRviN1-qylSHLapSuTfe6NlXsRg/s540/websetup-add-launch.png)
- 出現「啟動條件」頁簽。      
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhafne2I03z23iyk8vpqCfuB-RhfUGJSidbZf0LstysdxaL1w3Lr_H3TWkzDM4sHxrDTAfnB7_aRlAtMboE1A9HSOZWbcEyyfI2xkTC_dzYeFpC1Wvvtqrcg_Toj7fJtlQgZSeem1DAq-Q/s356/websetup-launch-tab.png)

There are two main branches of the Launch Conditions editor

- 搜尋目標電腦：定義安裝前的搜尋條件，以判斷是否需要變更，如：登錄資訊、版本資訊等。
- 啟動條件：定義安裝前需要符合的條件，如 OS 版本、IIS、.NET Framework 版本等。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhi9GUmNjYtCQfXIml-c9QJ0vqBAgQIG1lpCKAemrrPP3zfKbiyKUvOqQy66_ZMyXZRTO1AxXn4Wt8hx-Pj6RLaXXPCAgjoSRTwGu20Cp_f7Oi2cDa0Me864285EXr4ml_p3xpkWkHqgng/s354/websetup-target-requirement.png)

Typically, you must add an item to each of these two nodes to require a single component as part of your install.   For example, if you want to verify that a specific DLL is present,  you must create a search condition under Search Target Machine and store the result of the search in a property.  You then create a launch condition that uses the search condition's property.   If the required condition is not met (file is missing), you specify an error message to be displayed to the user.   You can also optionally add a Uniform Resource Locator (URL) to the required component for download.  

通常一個需求都會必須同時在這二個節點加入項目，  例如：要判個特定的dll檔，就必須先在「搜尋目標電腦」節點上建立一個搜尋條件，然後再依搜尋條件的結果在「啟動條件」節點中建立一個啟動條件。  

所以，比較簡便的方法就是在「目標電腦上的需求」節點上，由右鍵選單功能表中直接加入「啟動條件」，  系統就會自動在「搜尋目標電腦」節點中加入一個搜尋條件，並且在「啟動條件」節點中加入一個啟動條件。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjEUCCeAD6fkzbUX9gLoFR6cnjgnCUu5qpmmlR0w8E9uF4458qCMnwivEMlb-O7JF1_y9hyphenhyphenWuZSF5utt7NYpTV-o606yBEbJvzxOzezmsSMBgy8rlK15Zz4WDU9nbJm53P1t_nQ4jCPAN4/s367/websetup-target-requirement2.png)

#### 加入搜尋條件

另外，我們也可以單獨由「搜尋目標電腦」節點中加入一個搜尋條件，它共有３種類型：

#### 1. File Search

這個類型是用來定義搜尋特定檔案。  若要搜尋檔案，你只要指定 FileName 屬性；若要尋找目錄，就指定 Folder 屬性即可搜尋。   

#### 2. Registry Search

這個類型是用來定義搜尋特定的登錄資訊。  

- **Root** ：設定要開始搜尋的節點
- **RegKey** ：設定要搜尋節點的Key
- **Value**：a value you hope to be set for the given key

#### 3. Windows Installer Search

這個類型是用來定義搜尋已註冊的元件。  

以上，每一個類型都有其各自的搜尋屬性可以設定。  你必須在 Property 屬性設定一個名稱，以定義搜尋結果，如此啟動條件便可以依這個名稱取得搜尋結果。如下圖所示：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh517bGtW-m63uwuniotI4wTo-4ht3mXKti40mYYwG931x-mxe4dnEeYcB_e0DQhmWGLNZrC8RbVTKfJ3SLo3Wi8MajF1CcC90EFxX6DfTmOyyI456koL2GAdYOtdo9eOSGWMfxe8xcMAs/s533/websetup-search-condition.png)

#### 設定啟動條件

加入一個啟動條件之後，屬性視窗中有以下屬性可以設定：

- Condition ：指定特定搜尋條件，依其結果判斷是否安裝。
- InstallUrl ：若 Condition 不符合，安裝專案用來取得需求元件的網址(非必要)。
- Message ：設定若安裝條件為false的顯示訊息。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhtIWJdz313gSZgSsgvePIPPwjr2w6Kl-la-Zt72GZ_SS8hpF1nBMXegDFB_NbbFrzvi4oRRmMlv-zllHHG7ggQm7D7otDdc3ErpA47Q1MtowlCmxdAiD1_rETZo06mWAWWCV_QrWXNbX4/s512/websetup-launch-condition.png)

除了可以利用搜尋條件的結果來判斷是否啟動外，也可以直接設定啟動條件的判斷式。

例如辨識環境變數，可以在變數名稱前面加上 "%" 符號，如：

%HOMEDRIVE = "C:" (verify that the home drive is C:\)

檢查特定的屬性：

IISVERSION &gt;= "#6" (檢查 IIS 必須 6.0 以上)  
VersionNT = 601 (檢查必須是 Windows 7 (32-bit 版本))  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiuUiHj7Lp4ce9ogBKEnWASWLbIkd5T3sZsOkf_tf0Dcj9WL6dUaLGwCb_BSQCqmxwJylugHr0CDrNnWsz0Yr4RlgwKTWWS8aYxAIQDYWRR0QhijFgpSOy5JHp0krEK2m2RdVU3wHL1bUs/s344/websetup-launch-condition2.png)

底下是一些常用的條件:

- IISVERSION ：Version of IIS, if installed
- VersionNT ：Version number for Microsoft Windows NT–based OS
- Version9X ：Version number for Microsoft Windows 95–based OS
- ServicePackLevel ：Version number of the OS service pack
- WindowsBuild ：Build number of the OS
- SystemLanguageID ：Default language identifier for the system
- AdminUser ：Tool that determines whether the user has administrative privileges
- PhysicalMemory ：Size of the installed RAM in megabytes

## 範例：如何設定檔案的啟動條件

？？

## 如何寫入登錄資料

有時候在安裝過程中，會需要寫入相關資訊到註冊區，可以如下操作：

1. 在方案總管中，點選你的安裝專案。
2. 由右鍵選單中，執行[檢視]→[登錄]  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg5btUQhohf_cnDBD3BYksJpgJO4n5yvCIMvcdc4syklNAVxla0FscbdhgI7AMyfzKrSOjrqmTPdzvzuIm1_UjhSvo1L7LJYbC4cix-J3419jJrN93uv9Az6B6k29NOHl8qyNLHxFkK2YA/s549/websetup-add-registry.png)
3. 在出現的「登錄編輯器」中，加入登錄設定。  
這個步驟是巢狀的，例如：如果要加入 HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\ASP.NET\ 設定，就必須加入SOFTWARE、Microsoft、ASP.NET機碼。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgeO0rTklsLq15ZBNJCW_e72fEfIyeqv3mDwcx2ju93OUDweW4u54K9BgqpCxSB6cpfMlKtKT_N9oVq-9ASCaC1fjgvCqaajjpHDmorH-F5qp992PD4y71EWaB0ITBo-7FnE2vseEKjG38/s558/websetup-nested-key.png)
4. 點選想要設定的機碼，在右鍵選單中，執行[新增]→[字串值]、[二進位值]...  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjM1rqk-7mlDDar6WpEpbPHxwWhBFe7R8ajQ4r6J5yFb8nja28K_QuUwWUhVYHPCbaga7xU9JP_qMXk_PvFT7IK3sxi7g85NCeSywNoG3_ceDeKK2C_2x9aerym33X3Ig7XERxuZ_oo_Kk/s519/websetup-key-stringvalue.png)
5. 右側的Property視窗，可以用來修改設定值。如果要設定安裝的條件，就必須設定「Condition」屬性。      
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh1PgPykN5Ivx-PTHbcQk2ajUGz2pQFCu7Zv_PCpbszkrdmkm33DpXbYrWKYXqI8RaSrzbCgCWe48qm88RhvY_1qUks7ynNmMWn3o3GSTon9h9iSEN40ndcQS8Gd6hdoG7TXXke7aMxoq4/s778/websetup-key-condition.png)
6. 當解除安裝時，登錄機碼預設是不會同時刪除的，如果想要自動移除登錄機碼，請選擇機碼，並且檢視屬性視窗，將「DeleteAtUninstall」屬性設為 True 。

## 加入自訂安裝步驟

在安裝過程中，若我們想要收集使用者的特定資訊，就可以加入自訂對話方塊。  對話方塊可分成以下幾種工作類型：  

- Display a license agreement  
這個步驟就是製作一個顯示授權合約的對話範本，同意才可以繼續操作。
- Modify settings in the Web.config file  
這個步驟可用來編輯設定檔
- Perform custom configuration  
這個步驟可用來提供儲存在登錄區或特殊位置的資料給使用者。
- Activate or register your application  
這個步驟可用來顯示給使用者產品金鑰或註冊資訊。

要加入自訂安裝步驟，必須使用「使用者介面編輯器(User Interface Editor)」([檢視]→[使用者介面])。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8X21dg7b7fNSJr9_iihHZWU_AMwFk5_9vzTc-MSfshtjjuQc_d4LMQRbaJUOWuPh7Crb_BzYBDw6SrdUxnE2GWJiWm00ExNxqrJXu4Loal42l6z0O9JRiB3xjnvFyG4auQRV0mlI7BNY/s561/websetup-add-user-interface.png)

使用者介面編輯器可以用來顯示或管理不同的安裝階段。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjvHz2P5k-VG1kwe4q-jSNVangVEomqMMpx7JDEQEmDRejWtu2PhWQMesfRqMmchFYPZlmMHa2ztPcJpebzatYh2sainkbrumdQhfXNKEQsbC3CYr8GKFU85yC_Rvewcq22h94wkv7f9ao/s478/websetup-user-interface-editor.png)

加入自訂安裝步驟，示範如下：

1. 點選需要新增步驟的階段，在其右鍵選單上點選「加入對話方塊」。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiSRRO2FKM2ZjKQubwXSYOscaZm7I3aoY_N5E1Er3JTn_a_uWymhlVBqg8JNyxbC0Nzl2SEvk7osfdjTPwWzaBiGw1UGvCw71NM8VP_Ik70DIGp8_s8vXxQdE3Ck6uqBszjhV803okDQI0/s589/websetup-add-dialog.png)
2. 對話方塊有不同範本可以用來收集不一樣的資訊類型，若用不到的欄位可以隱藏即可。
3. 點選加入的對話方塊，可在右鍵選單中使用上移下移功能調整順序。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEihTnVOt4xYGNVo2OA_KPOSqTZWtJ-i43MZvTOTgf2NipnWnYOQ9mvobhq6NMB5h-I3mGCps4xL70lOHhqkezgPI8k-AhUqjv1znZf_o84cdOOyJCMknOu4aHJj5tigtaFh-6stoS0_mJg/s531/websetup-setup-dialog.png)
4. 底下為自訂對話方塊的畫面顯示  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgFlhOj2HF2QGynSzcIawzmwkWQ0WGjRZF3xhF0At4vHjyGGb_-2WgH6QMDoSiz_R8narn_gzxB8_jXgWxx0gWKITAZsCzxqI0SRHQ9Fy5gF1W-7eq0Qo6EekT3JsNCCv_PLouA9wQ-8D0/s524/websetup-dialog-page.png)

## 加入自訂安裝動作

如果說在安裝過程中，必須執行特定的程式，就可以加入自訂動作。  例如：使用自訂步驟中取得的資料去更新設定檔，或者驗證產品金鑰等等的行為。  

要加入自訂安裝動作，必須使用「自訂動作編輯器(Custom Actions Editor)」([檢視]→[自訂動作])。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhuVaOWQX-5jRRgHUfn21lSTOAo10E58o0sOPeFMPfBAkhLRVSP-ZoJj_9YnO4BnCUTtE20nOqodzp8DFLMvnTxnGF8YL2Vp-MPqHhuJ4H9rtgAlnroZAin09vh_AKublCIfoPtmHcdK48/s294/websetup-add-user-action.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh0f7aDe9OQifSLy0ad2oSmx66CFiNmM1guaiwhDxG_rXG3BeghoHHZEHTJuWKq3Bfr1uHeTCyib6ZbSAGyBbX3hmwskZcjnfPsupUR3uiHyWSKDmoQGfNp6eNWalZ6y_z2ySOa8Emi10Q/s556/websetup-add-user-action2.png)
## 參考資料  

- [HOW TO：新增檔案啟動條件](http://msdn.microsoft.com/zh-tw/library/0x2fc4kd%28v=vs.100%29.aspx)
- [HOW TO：在部署 Web 應用程式專案時轉換 Web.config](https://msdn.microsoft.com/zh-tw/library/dd465318%28v=vs.100%29.aspx)
- [Web 應用程式專案部署的 Web.config 轉換語法](https://msdn.microsoft.com/zh-tw/library/dd465326%28v=vs.100%29.aspx)
- [逐步解說：使用發行網站工具部署網站專案](http://msdn.microsoft.com/zh-tw/library/1y1404zt%28v=vs.100%29.aspx)
- [\[Will\]如何使用 Visual Studio 2010 的「單鍵發行」功能 (MsDeploy)](http://blog.miniasp.com/post/2010/05/03/Deploying-Web-Application-Project-Using-One-Click-Publish.aspx)