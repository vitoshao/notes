---
title: 組件
layout: default
parent: Foundation
nav_order: 6
description: "組件"
date: 2012-03-08
tags: [DotNetFramework,Foundation]
postid: "773340667631310733"
---
## 什麼是組件 (Assembly)

[組件](http://msdn.microsoft.com/zh-tw/library/xbe1wdx9.aspx)是組成 .NET 應用程式部署、版本控制、重複使用、啟動範圍設定和安全權限的基本單位。      組件的形式為可執行檔 (.exe) 或動態連結程式庫檔案 (.dll)，而且是 .NET Framework 的建置組塊。 它們提供 CLR 需要注意的型別實作資訊。您可以將組件視為構成一個功能邏輯單位、而且必須共同合作的一組型別和資源集合。

## 組件分成二種：

1. 私有組件
1. 不須強式名稱(Strong Name)
2. 預設擺放和應用程式同一目錄，或在該層目錄底下。
2. 共用組件
1. 須具強式名稱(Strong Name)
2. 可放在本機或遠端電腦(網際網路)，或GAC資料夾
- .NET 2.0 / 3.5 ，GAC 的實體目錄在 C:\Windows\Assebly 這個目錄。
- .NET 4.0 ，GAC 的實體目錄在 C:\Windows\Microsoft.NET\Assembly

## 組件檔案的存放位置

若有一個視窗程式專案Ａ，一個類別程式庫專案Ｂ，若專案Ａ參考專案Ｂ，則在建置專案Ａ之後，預設在/bin/Debug/目錄底下會產生 A.exe B.dll

當組件B發生下列情況時：

- 1. B.dll 被刪除：則Ａ無法正常執行
- 2. B.dll 被置放在 A.exe 所在目錄之下的 bin 子目錄：  
這時必須更改Ａ的組態檔，Ａ才能正常讀取。  ```xml
<!--指定一個privatePath，讓應用程式可以找的到參考的檔案位置。若有多個子目錄，可用分號隔開-->
<runtime>
<assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
<probing privatePath="dll; bin" />
</assemblyBinding>
</runtime>
</pre>
</div>
</li>
<li>3. B.dll 被置放在A.exe所在目錄之外：則Ａ無法正常執行，除非將Ｂ註冊到 GAC 裡面。<br />
並不是任何組件都可以放到GAC之中的，必項具有<strong>強式名稱</strong>的組件，才可以當成全域組件。 </li>
</ul>
<p>
<strong>註：</strong>若同一版本的dll，同時存放在[GAC目錄]、[組態檔指定的子目錄]、[應用程式目錄]，其被讀取的優先順序：<br />
<span style="color: #FF0000">[GAC] ＞ [組態檔指定的子目錄] ＞ [應用程式目錄]</span>
</p>
</div>

<h2 class="collapsable" onclick="displayDiv('什麼是強式名稱')">什麼是強式名稱 (Strong Name)</h2>
<div id="什麼是強式名稱">
<p>
<a href="http://msdn.microsoft.com/zh-tw/library/wd40t7ad.aspx">強式名稱簽章</a> (Strong
Name) (也稱為組件簽署) ，會提供應用程式或元件的<strong>全域唯一識別</strong> (Identity)， 它是由組件的識別 (簡單的文字名稱、版本號碼及文化特性資訊) ，加上公開金鑰和數位簽章所組成的。
其他軟體可以用這個資訊，在需要參考該組件時，達到明確識別的目的。 它也可以讓使用該組件的開發人員能確保這個組件是來自於建置的廠商或開發人員，而不是從中被竄改過。
這份資訊會儲存在金鑰檔中；它可以是個人資訊交換 (PFX) 檔或目前使用者的 Windows 憑證存放區中的憑證。 若要使用強式名稱簽署組件，必須擁有公開/私密金鑰組。</p>
</div>

<h2 class="collapsable" onclick="displayDiv('使用強式命名的原因')">使用強式命名的原因</h2>
<div id="使用強式命名的原因">
<p>
強式命名可以當成應用程式或元件的唯一識別，其他軟體則可以用這個識別來明確參考應用程式或元件。 例如，在早期的 windows 系統中，我們透過 regsvr32
指令，將組件註冊到登錄檔之中，若同一組件，遇到不同版本時，往往只能選擇覆蓋，難免引響到舊系統的穩定性。 那時候，因為組件是用檔名當做識別，所以不同的版本，無法同時存在系統之中。
如今，具有強式命名的組件，就可以同時存在不同的版本，而不會影響其他應用程式。 此外，您可以將元件的強式名稱當做安全性舉證來使用，在兩個元件之間建立信任關係。
</p>
<p>另外要注意的是，.NET平台規定，<b>在具有強式名稱的程式之中，若要引用其他的組件，該組件也必須要有具有強式名稱</b>。</p>
<p>
還有，為了確保開發過程中，金鑰檔的保密性，有時候會使用到「延遲簽章」的方法開發組件。可參成下面文章:<br />
<a href="http://msdn.microsoft.com/zh-tw/library/t07a3dye.aspx">延遲簽署組件 (http://msdn.microsoft.com/zh-tw/library/t07a3dye.aspx)</a><br />
<a href="http://www.dotblogs.com.tw/huanlin/archive/2008/04/23/3194.aspx">強式名稱組件 (http://www.dotblogs.com.tw/huanlin/archive/2008/04/23/3194.aspx)</a>
</p>
</div>

<h2 class="collapsable" onclick="displayDiv('如何建立強式名稱')">如何建立強式名稱</h2>
<div id="如何建立強式名稱">
<h4>方法一：使用工具</h4>
<ol>
<li>先使用 <a href="http://msdn.microsoft.com/zh-tw/library/k5b5tt23.aspx">Sn.exe</a> (強式名稱工具)產生金鑰檔(.snk)。產生的金鑰檔 MyKey.snk 裡面，會包含一組公開金鑰與私密金鑰。</li>
<li>再使用 <a href="http://msdn.microsoft.com/zh-tw/library/c405shex.aspx">Al.exe</a> (組件連結器)產生一個包含組件資訊清單(Assembly Manifest) 的檔案，例如。</li>
</ol>
<div class="source_code syntax"><pre class="brush:c#; gutter:false;">
sn.exe -k MyKey.snk
```

#### 方法二：透過開發環境提供用來簽署組件的工具

1. 開啟專案的屬性設計工具，切換到[簽署]頁。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiRXlRtdfbPG-hj8NANxEmXrgmxhbnTFzolFXbycXKlmire-kmZjcs-yX6yhv45tBk6G18Xe-1LyNaG50By7l-Olxn8FAZU-fInvKDPcviERFZCMOwlGAirr2xn8vTb0EEwsQE8N6P43Gs/s771/vs_signing_tab.png)
2. 輸入金鑰檔名稱及密碼  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhtCV1N2szFQMplpa1Qz6QpZHEjK3t5tBHWiDCxQNrunz69NXUc49_ei-jSM3bv1hRq-hBC_v0kRBsmPVxBPc2bT0DyZ4BSSON26brTbVCbOt3nX6IUMDr_6GJNHRGHdVS6Pzz587valH4/s444/vs_signing_tab2.png)
3. 完成後，專案底下會新增一個金鑰檔(.pfx)  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgpgrvTwcE9SX_QdxTfaBRHAhnZwartfe9bUhFUmiQB1G4yxaAT5J-ISCeAUz7b-l3d3LHG3V5t3Y9t9hYVpsRoIEUL7bMztL5q5WImjb4QkDRlKd4aMiqyCpRh_lSAqTWKcHFuFnnr9AA/s173/vs_signing_tab3.png)
4. 建置

## 如何將組件安裝到全域組件快取

#### 方法一：

直接將.dll檔拖拉至組件快取區 (C:\Windows\Assebly)，.NET4.0 則放至(C:\Windows\Microsoft.NET\Assembly)

#### 方法二：

使用 Microsoft .NET Framework 2.0 組態工具，加入組件

控制台&gt;系統及安全性&gt;系統管理工具&gt;Microsoft .NETFramework 2.0 組態

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiuC22TKTyFQTekNObj4WbbapdVJiEDuN916jYIWAHSLhKJY56Q9-VjoUOeyY_LsfaoxaSRC09peRZzD1aSFNF4EuHvEz6k0A-oq2hSemY1aP5fqiTDcF4P75Sie6mr9B-j9VnTWdcZHeY/s850/mscorcfg1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEicDT-3gAXYToejCrDGGa3dNrMAC7OSiotR94vCKE5unEEmBM74K6tAuUk0zkwq7U9nQBdXnwHhJCVstAsjyMlBOP02LKHbF06YYYFO6c61mCq-sH1ukZPa4es68oJZWdmlrDlerld4jAs/s812/mscorcfg2.png)

#### 方法三：

使用 GACUtil.exe 進行註冊及反註冊的動作
```c#
gacutil /i ClassLibrary1.dll
gacutil /u ClassLibrary1 (切記：不要加上 .dll - 這個指令，會將GAC 中所有叫這個名字的組件一律移除 )
```

## 如何引用全域組件快取中的組件

如同加入一般組件的方法即可，例如：將參考指向 ClassLibrary1 專案中的 ClassLibrary1.dll，或者指向 ClassLibrary1 專案。  不過，因為 ClassLibrary1.dll 已經在 GAC 中註冊，該元件就不會再隨著應用程式一起部署或複製。

## 如何設定開發階段共享組件

一個共用組件，通常我們會把它放在 GAC 中，以方便多個應用程式參考與管理。  但是如果還在開發階段，這個組件可能不時的都在變更版本，放在 GAC 就顯得麻煩，那要如何讓參考到它的其他應用程式，隨時保持使用最新版本呢？  這時我們可以使用環境變數 DEVPATH 來取代 GAC 

- 1.在應用程式的組態檔，將 developmentMode 設為 true  ```xml
<?xml version="1.0"?>
<configuration>  
<runtime>  
<developmentMode developerInstallation="true"/>  
</runtime>
</configuration>             
```
- 2.設定 Windows 環境變數 DEVPATH，輸入參考組件的路徑即可。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJPve5oHMfowUXrC7GHvHALRHhuLrFHiyfetkZrMgyuREmppysBG5m9p1lA46_HqbSPsVGe9uGOJDQbR7pNXpmH-XBGLc44XCvKu-54MBwG9qZEZgvw2GR9IRIHuCs2OU_e2br6rMs_No/s368/configulation-devpath.png)