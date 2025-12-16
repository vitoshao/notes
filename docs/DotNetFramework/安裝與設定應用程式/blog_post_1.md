---
title: 建立Installer
layout: default
parent: 安裝與設定應用程式
nav_order: 1
description: "建立Installer"
date: 2012-02-08
tags: [DotNetFramework,安裝與設定應用程式]
postid: "878904576499249369"
---
要設計安裝程序，可以在 Visual Studion 中建立一個安裝專案，將應用程式專案打包成一個安裝檔後，再進行安裝。  另外也可以在應用程式專案中，自行加入一個安裝類別，再透過安裝工具 InstallUtil.exe ，將該組件安裝至系統。  

# 建立標準安裝檔

## 如何建立標準安裝檔

使用.NET提供的安裝專案，可以輕鬆快速的建立Setup程式。底下示範如何建立安裝專案：

#### 1.建立一個【應用程式專案】，取名為TestAP。(假設這個專案就是即將被做成安裝檔的專案）

#### 2.加入一個【安裝專案】，取名為Setup1。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi3qZTt5jo3DboI3lphZjeZwMeM9Bzg22obkWjXRydq9SOJ4ND4qZBzWmfrnz6LfGIi-S1z_aiWHeGFe3E-zbb60FlH5WY1X3r9UyHi9h1Vge5-5niBirKtAokmEO7NNTfS1CxBF_MkdVw/s800/setup_newprj.png)

你可以在【安裝專案】的右鍵選單中檢視專案的檔案系統，檔案系統中預設會有下列三個項目：

- 使用者的桌面：用來設定要在使用者的桌面建立哪些檔案，通常是捷徑。
- 使用者的程式功能表：用來設定要在使用者的程式功能表建立哪些檔案，通常也是捷徑。
- 應用程式資料夾：用來設定應用程式安裝目錄中的內容。

你也可以點選檔案系統根目錄上的右鍵選單，加入其他特殊資料夾

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgEXZWZvCswgTqG0pkiM1jGqbr4ZEO204OSD-Q6Ltpi1QFEX-clm4Vk8Ik978MeZ5aK0jzlQ4J8KFuGcHpEf87B-mMC-13lxMN09fdExhWW82OYY4w-LZt42LIuVtis5FtetJFmqQLcMYY/s598/setup_appfolder_add.png)

#### 3.設定安裝專案的屬性。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgbHrr6A1FgkAjm95fkR3hdjTRbl8MxC9gSH_grN6dfJmUjw6AekRUyFb-JpR-0-OvwsAWoFBpgfvWygZNfOKDIAMVnj8Kcoou7jy6aNZRK8dvZrjSRh_nTNVp4mkDXPp67Z1Z5IeC0zPE/s610/setup_property.png)

上圖中，紅色框線的欄位是比較常用的，它們會出現在下圖的畫面中。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi7eC981iFKI0X8H8g8BNqsOCaCmUINsivLkhDfKfAxWFZAAkcAcNE0kK1ObuyxOiIDIAaCfu9QpjbmOb9ifibu7k-YthEPX0sauPVKr52ewPltugZIL1KPMVG7KdHoQ_56HZXZjbP8Jcs/s513/setup_result1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiIQkw5ydIMzY39wzdIMrLEX-rqi5ou_vbTPDhyphenhyphenJJD0dXq9jvZTbk11YKR_FArkqrIKlndzxfCVvwHCcOJBr2cIvSAtrIvmRnEhqueN-wm92LfcISsD5PowgXIVl9dfMWEnYqtrUgGdoqg/s513/setup_result2.png)

另外值的一提的，在應用程式資料夾的屬性有個 DefaultLocation ，就是參考專案屬性中的 [Manufacturer] 和 [ProductName] 設定值。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEimCknePJhfKne_5SvaMrjVL9psIUGGRL9MXpE08ZpBy1CJClGmueGEU_vaLMsvMNrqjuJn2LHCqlEU_VdoDS4cmHjFpzbZixJM74Qf6ULDdNhkg21BtFzsHOoRHzwAOHgYtnPokpwcKCA/s697/setup_appfolder.png)

#### 4.將【應用程式專案】加入到【安裝專案】中

在方案總管中，點選安裝專案，在右鍵選單中，點選「加入專案輸出」。等詢問視窗出現後，在專案選項中，選擇你要安裝的專案。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh_JN6x6X8Qw2JEH1XmeXkmzuUCE897pOg1eVG5kw4fatf5Sqn_IOW8iuXCZfw7hOxWiP64zUlMBwNew8d_ZL-Bcb8xZCls5KFrSFfG6K0tQi4MpghQdHBA6FdLA__0PD-XYpcQZjwFb7U/s636/setup_addoutput.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgTisPc3DC5IrnhXciuD3UmMv_bCfH8T8hwjVHmL0nwq9Wom6HIsrWImUUfS4ZHFT9XrQGk6eFA9vb6RMplLsqPWVbXFgLEL97xjZWCRqibQndxzsemd2LafwOKObPkPylhGGvElOkLmeY/s387/setup_addoutput2.png)

這個步驟完成後，你可以看到以下的內容，它表示我們要將【應用程式專案】產生的執行檔，安裝到指定的目錄中。  所以，如果你同時有其他專案的檔案要打包進來，就可以重複這個步驟。  或者你有特定的檔案要一併安裝進去，也可以在這裡做設定。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgnABCJ4aiehf1ikPUxzd_0eumnqXOTj02Vxd-9BtXtiv9LA6TZirNe3rOScaUtuGBGE1b_GXXe4dI_PeGUpgsCNacTw-1hx7LBLpCz88hp5cvoAZBbim9LUG9ito1jlr3rjfRwPhPgcpI/s540/setup_appfolder_mainoutput.png)

#### 5.建置【安裝專案】

做完以下步驟，原則上就可以開始建置Setup1專案，以產生安裝檔。底下是一些額外功能，也可以參考參考。

## 如何建立一個捷徑

- 點選【應用程式資料夾】，點選【主要輸出從ＸＸＸ】，在右鍵選單中，建立一個捷徑，取名為TestApp。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEirNAE1bMxg1mFTvayIlu3xJZNRZqwN3evAZlW0U_uMZ-1-wf1go3WIu92eRyECMc6bVJAcSE7fVg-u8cvhvqbVFGVyveTTz6wIO0VnpeJTyulPNf3lnyEqB0Qcf0nupG3PbrXdNN6DllE/s788/setup_shortcut1.png)

## 如何讓安裝程式自動在桌面建立捷徑

- 將上一步驟所建立的捷徑，拖拉至【使用者的桌面】。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj1kuLQ5vFknsTh5c-lBZbbsjJw8wZloo79BfQOt_axESQwOM3_CMIbgbFM9XNy8Qs5g_4egToFxgdBLPhbICQH3omm7swgQ8rSuoR9YGCK0Ul3XLHk6YYgu9iQ2YC1gSAs8f66sVK5WnA/s701/setup_shortcut2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4N6bEhlEFAD7nwz_p3mCTEGquLhRV5j6qWAEaJqoPNPMXY4HG4wpuXuWC1GrfAWZKIufDwcTOsQWBsAMbOM6TfXsIVc6dETUSmn0H1IzvOvdyZDybg58DDm6dbVWX2GvwgskhW2Dcz14/s694/setup_shortcut3.png)

## 如何讓安裝程式自動在開始功能表中建立捷徑

- 在Setup1專案的檔案系統功能中，點選【目標電腦上的檔案系統】，在右鍵選單中，點選【加入特殊資料夾】，然後選擇【使用者的開始功能表(User's Start Menu)】。
- 重複步驟5，再建一個捷徑，並將建立的捷徑拖拉至【使用者的開始功能表】。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh20dMk6_7lqkax6JplkJdKAyxclC7zUREYUcN1LmuNdU_h6_VzrNnX0FlbL99O9EqBd0BlvtLCz3H59_5eJW2osQh3K0fQqgd99fti3h9_KG4AAKsUnAGmuTf8HcsbESi5DYmVQIdKwoc/s648/setup_shortcut4.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgh90vl1NfsunA0zKFuVCMJg1UbOYHjNuk0GjoKYl7yRGHZWe941ZtIzrK5iTLYkiIyYhyphenhyphenmQQvmKSgBMz84XefqBzBNzmQ9UUWdx9Ke0HWLj4AKhln-wxt5LHams3KxpED8TTyAmElCX00/s635/setup_shortcut5.png)

## 如何設定捷徑的圖示

- 先在【應用程式資料夾中】，加入圖示檔案
- 點選【使用者的桌面】中的那個捷徑，在其屬性視窗中的Icon欄位，找到上一步驟的檔案。
- 這樣子，安裝完之後，桌面就會有一個包含icon的捷徑。
- 同樣的，若希望【開始功能表】中的捷徑也有圖示，照著做就可以。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgPXBbSZMFWO6iQFaqYOkskneqvEMQlJsbvG7zaxQc2sf6LB2bIMk8LkYUwiP-ZG_p40d0ffPIMI3vrHnCwXPmQrZdtGoEmCTGaFYCtZNLYbreEBxu49_Ze-OPnEUaZrk0NFsSP5Q7yDCs/s574/setup_icon1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjvOjgZnaypmhYvQttN1DUXyFhZUu67IsM2fNX0LIyq7-KAWtldCL2msTMxvAu2ORDrTunGuFcuBycXL04EFvleVWppeO1eiTwt0Ec-5zATUNQjK7XKDUPUnIwUM5fueDl3smnOZNSimoU/s743/setup_icon2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgpoAjhU-iJMDf9rxt4SxLFq2hRYQRfxq0W8EfJTa-evCDERm2k3Xk6h6Rt1Kevm7erNUE6doJimM6JS-ImPF6B38lTgY03IRhWJGq2uZGr0gA1WraGKv_xIOLMEpL6xtorHCxFbxK37yY/s545/setup_icon3.png)

# 建立客製化安裝檔

.NET 提供了 [System.Configuration.Install.Installer](http://msdn.microsoft.com/zh-tw/library/79e7ka7s.aspx) 這個類別，用以建立自訂安裝程式。使用這個類別，必須遵守下列幾個步驟：  

1. 建立一個衍生類別，繼承自 [Installer](http://msdn.microsoft.com/zh-tw/library/system.configuration.install.installer) 類別。
2. 在這個類別上頭加上 RunInstallerAttribute 屬性，並將值設為 true。  這個設定值是用來告訴系統，當組件安裝時，是否要叫用 Visual Studio Custom Action Installer 或 Installutil.exe 。
3. 視實際需求，**覆寫** [Install](http://msdn.microsoft.com/zh-tw/library/system.configuration.install.installer.install) 、 [Commit](http://msdn.microsoft.com/zh-tw/library/system.configuration.install.installer.commit) 、 [Rollback](http://msdn.microsoft.com/zh-tw/library/system.configuration.install.installer.rollback) 、 [Uninstall](http://msdn.microsoft.com/zh-tw/library/system.configuration.install.installer.uninstall) 等方法，並撰寫程式碼。

下面例子將示範如何製作一個安裝檔，且符合以下功能：

- 1.在安裝過程中，加入一個對話視窗，要求使用者輸入公司名稱及電話，並將這些資訊更新到應用程式的組態檔，以便應用程式執行時可讀取使用。
- 2.在安裝進行時，在安裝目錄中產生一個檔案，檔名為 Log.txt。
- 3.在安裝完成時，建立一個檔案 C:\Commit.txt。
- 4.在安裝失敗時，建立一個檔案 C:\Rollback.txt。
- 5.在安裝移除時，建立一個檔案 C:\Uninstall.txt，並將 Commit.txt、Rollback.txt 刪除。

#### 1.建立一個【應用程式專案】，取名為TestAP，並加入一個【應用程式組態檔】，組態檔內容如下。(假設這個就是要被做成安裝檔的專案）
```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
<appSettings>
<add key="CopName" value=""/>
<add key="CopTel" value=""/>
</appSettings>
</configuration>
```

#### 2 ~ 4 同建立標準安裝檔。

#### 5.開啟【使用者介面編輯器】，點選【開始】，在右鍵選單中，點選【加入對話方塊】。等詢問視窗出現後，選擇【文字方塊(A)】，再按確定。(你可依實際需求，加入你要的對話方塊)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg714w5lykLD-pGPgooyXr0MwkjI96km-ums6NswoM80CC_xiRcM4UQc5rFAvxEsHsClUjcB5L8Y3WAPByDxMcgmaPG7vl7t_0GgKhsYeVHoIpqO9LfNo-DE-Z6B5rzsnkxWKYuSVOfuXw/s205/setup_userinterface1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjumRWyg0qM_yxxGFh5f9EM_TLz3Mgr4GnIWA5-4gh8w1N5c3PRMJY0wlWCaVBs4tuXrKNhyzD8HlF8FC9wh4fFwnI8gS6W_px2L0RkXJVz7EeX9w8rP3_giJCvR_JtxN8gYERHEBIVhG8/s335/setup_userinterface5.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkDgphN83b-g6xqb5IPmVemDeEIaKtrCSEJWAgQ0vSVOmG1rFHbpyLvife8cx6kuQIHATFrLDZJ3JoR91ipBlaSHnup4kNxn2ODaZGNgRiIUBnRwIe0OMURQhrTt1bAZ63S2TZdKpmQ-Q/s578/setup_userinterface2.png)

#### 6.將文字方塊(A)，拖拉至【確認安裝】之前。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh3N3qUFdzmzYT7646XdCU-zJoTaWXciNdVZ33ANY6GPUghz2aNbu2J63DfYnRG6qcjLDHUT7eqIMMGJAfKv54RN0kOaW46PGZMf4IUVPyI-7LnM19mLJTHLERPNZDz9LwBKl73vqv-ZIg/s257/setup_userinterface3.png)![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEip289yNzTNslQWT0zOdbzptzvRuL3to63lSn6TpTSQDlNQvSrm49AOl4sRwtV0ZpZ8lxC5BJEkZqaF3lc9Ye96w40L6iAhxOPFko3uvDE_VOYvlzpChNYETWv6xK60936fRkOXoJhRsSs/s257/setup_userinterface4.png)

#### 7.點選【文字方塊(A)】，打開屬性視窗。

屬性視窗中的欄位設定，分別對應到右圖中這個安裝過程中的新增對話視窗，你可以在此做一些文字設定，如 BannerText、BodyText 等等.  
另外文字方塊(A)，預設有４個InputBox，對應到此處的 Edit1~Edit4 四個項目，你可以在此設定輸入方塊前的標題文字，若用不到的輸入方塊，可將其 Visible 屬性設成 False 即可。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEizD86b9lAHsfQGUgc7Z8GjyjxgrkX5sF9S8poZ4dWw7br-Qp2e2P9Nqq1LXv0tDFlRISL2os6BWZj9dGvDMTJat2Qm46lzjbrzl-C2haUj5YG-49F0Q3ATQxoA6CfgIK99P2QF044jRD8/s850/setup_dialog.png)

底下步驟開始客製化程式

#### 8.在TestAP專案中加入一個【安裝程式類別】，取名 myInstaller。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgz9-mFlq6fUOT2qyWY-13FbEbymI_QsrJtgBU6PYpM_-4DKqQFQEIXzkQyw7WvkmVi470QjtTG8-iBJGLuDNRJWNJ4Y5WlVy0NyobfaX6VJJ7n8JD29bOCP_0LljuEwbvZywye6_imQ9I/s800/setup_installer.png)

#### 9.切換到安裝程式類別的檢視程式碼，配合案例需求，我們必須在專案中加入 System.Configulation 參考，以進行組態檔變更，並且引用以下命名空間：
```c#
using System.IO;
using System.Configuration;
```

#### 10.開啟Setup1專案的【自訂動作編輯器】

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgu9_s5xOtCwPr1Z0SPr4o_6Xdhbkpqtq5OLlpSwgO9fedptuYCr2nXn4I_JVmvSwPAbXPmjBRvxcC-CexKpQ-Vkoy4JwLE4miUTV688UG1fi5Bteioq0HcGA8wsGGuRfqVM7_-zK5Y4g4/s323/setup_customaction1.png)

#### 11.在自訂動作編輯器的安裝項目中，利用右鍵選單【加入自訂動作】，完成對話視窗的設定，如下圖所示。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiRgjMDQdU9ejpcmyscxEW08hY0e1K7sC1jFKKcPaZUXgu1vNaWv5CgOl-iLMufVhcwbTq3JFKdg9b8Wy4pLRQgrzCxa-bhOmc82qJO2psA4LA-o_czCPrDnyDXRmVaY59eUW72brc9X2o/s344/setup_add_custom_action1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiBA11v2Y0Q_Q4Unvyrv3OGuXWlVWh8uIXGV0nkSCXLPo0LkQ3LQRAE3OxllFR7_ExFss0mmaJM63jABSkXIbCF6mnnxHnpxnScNw7d51G9-gnbEFukUjOLoaS9grot7rQ30rEQ5yCXNp0/s545/setup_add_custom_action3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiMvCopdtKTf574BRkKALHfvk7jwUwmuSxtGLevF5Zpl5ybpmf7SxEcxZ43rRTRdp5uzu2_Rdm-8quEPTftkGBsY4vIzUilrukAz7s_FdYq9QVXK0aH3LcFpcK0oWvEWFSWk6Wx7TLH7QA/s545/setup_add_custom_action4.png)

#### 12.點選【安裝】項目底下的自訂動作，打開屬性設定視窗，在 CustomActionData 欄位輸入 /para1="[EDITA1]" /para1="[EDITA2]"

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiYoXz4ZXFFhnsgDqZtryHxbUpc9BcqNd4ed_29OzSsCI7Bs_XSlGt5t_FyMLHIX8pxZTaekJ0jKQJkMhP-NURnU3WNCaFF2txhnLG74r-7VuWQ8Oe9AFxnl4eMfNMTc1cTjxqCEOgKv7E/s850/setup_add_custom_action5.png)

這裡比較要特別說明的是 CustomActionData，這是用來對應到使用者所輸入的資料，以便讓程式碼讀取  
配合文字方塊(A)的樣式，所以可以設定４組參數，每一組使用一個空白隔開  
/para1="[EDITA1]" /para1="[EDITA2] /para3="[EDITA2] /para4="[EDITA2]  
para1～para4 是程式中，用來讀取參數的key值，可自行設定，只要程式碼使用一致即可。  
EDITA1～EDITA2 是步驟７中 Edit?Property 欄位的值，也可任意設定，只要二邊一致即可。  
如上設定，使用 Context.Parameters["para1"] 就可取得 EDITA1 這個TextBox的輸入資料。  

#### 13.修改 myInstaller 類別，內容如下
```c#
using System;  using System.Collections;  using System.Collections.Generic;  using System.ComponentModel;  using System.Configuration.Install;  using System.IO;
using System.Configuration;
namespace TestAP  {      [RunInstaller(true)]      public partial class myInstaller : Installer      {          public myInstaller()          {              InitializeComponent();          }            //覆寫原先的Install()方法          public override void Install(IDictionary savedState)          {              base.Install(savedState);                //取得安裝路徑              string InstallPath = Path.GetDirectoryName(Context.Parameters["assemblypath"]);                //建立Log檔              StreamWriter log = System.IO.File.CreateText(Path.Combine(InstallPath, "Log.txt"));  //在安裝目錄中，建立一個Log檔                //for debug              foreach (DictionaryEntry de in Context.Parameters)              {                  log.WriteLine("key:{0} value:{1}", de.Key, de.Value);              }                //開啟應用程式設定檔               var exeConfigurationFileMap = new ExeConfigurationFileMap();              exeConfigurationFileMap.ExeConfigFilename = Context.Parameters["assemblypath"] + ".config";              var config = ConfigurationManager.OpenMappedExeConfiguration(exeConfigurationFileMap, ConfigurationUserLevel.None);                //更新設定檔變數的值              config.AppSettings.Settings["CopName"].Value = Context.Parameters["COPNAME"];  //讀取使用者輸入的公司名稱              config.AppSettings.Settings["CopTel"].Value = Context.Parameters["COPTEL"];    //讀取使用者輸入的公司電話                //儲存              config.Save();              log.Close();                //測試 <a target='_blank' href='http://msdn.microsoft.com/zh-tw/library/system.configuration.install.installer.rollback'>Rollback</a> 區段              //throw new Exception("Rollback test");                // 有時候安裝過程，也常會同時註冊機碼              Registry.CurrentUser.CreateSubKey(@"Software\CustomKey");          }            //覆寫Uninstall()，刪除Install時建立的Log檔          public override void Uninstall(IDictionary savedState)          {              base.Uninstall(savedState);                //..do anything if necessary                if (File.Exists(@"C:\Rollback.txt"))                  File.Delete(@"C:\Rollback.txt");                if (File.Exists(@"C:\Commit.txt"))                  File.Delete(@"C:\Commit.txt");                System.IO.File.CreateText(@"C:\Uninstall.txt");          }            //覆寫Rollback()方法，刪除Install時建立的Log檔          public override void Rollback(IDictionary savedState)          {              base.Rollback(savedState);                //..do anything if necessary                System.IO.File.CreateText(@"C:\Rollback.txt");          }            //覆寫Commit()方法          public override void Commit(IDictionary savedState)          {              base.Commit(savedState);                //..do anything if necessary                System.IO.File.CreateText(@"C:\Commit.txt");          }        }  }  ```

#### 14.分別在自訂動作功能裡的【認可】【復原】【解除安裝】項目底下，重複步驟10，

我們在步驟10的程式碼中，覆寫了Commit、Rollback、Uninstall方法，必須在此加入這些自訂動作，程式才會進入執行。
**=&gt;設定到此大功告成。**    
執行 setup，是否出現要求輸入公司資訊的視窗。  
檢查設定檔，公司資訊是否已變更。  
檢查安裝目錄，檔案Log.txt是否已建立。  
檢查 C:\，檔案Commin.txt是否已建立。  
執行 Uninstall，檢查檔案 Commin.txt 是否有被刪除。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhA97ufJvhKPD_t3m-GKWhKr1zhphnv471BvTgH56TnPqe8_CBgIl7C4qCVHitfE5gud9F30f3667XkyenvMPE_5VESS3XNMX6auSbNOCNBWeIwMxERZYWffTIq6_UrrPMZFDsXvvV-7yU/s348/setup_result6.png)

# 啟動安裝程式

上面這個例子，我們在應用程式專案中加入了一個繼承自 Installer 的安裝類別，同時也建立一個安裝專案，以便設計一個自訂的安裝畫面，讀取使用者的公司名稱及電話。  若透過安裝專案進行安裝，安裝過程會將使用者輸入的資料帶入 Install 方法中，如果使用 InstallUtil.exe 工具安裝，依然會執行 Install 方法，就不會帶入使用者輸入的資料。  底下示範的是，如何使用 InstallUtil.exe 工具進行安裝。  

#### 在命令列中使用 InstallUtil.exe 工具安裝

InstallUtil.exe 這個工具會對應用程式中的組件進行反射(reflection)，找出所有繼承 Installer 且具有 RunInstaller(true) 屬性的類別，然後執行其 Install 方法。
```c#
//安裝
InstallUtil myAssembly.exe

//移除安裝
InstallUtil /u myAssembly.exe
```

#### 透過程式碼啟動安裝

若要以程式方式啟動安裝程式，可以使用 [AssemblyInstaller](http://msdn.microsoft.com/zh-tw/library/system.configuration.install.assemblyinstaller) 或 [ComponentInstaller](http://msdn.microsoft.com/zh-tw/library/system.configuration.install.componentinstaller) 類別。使用這二個類別時必須執行下列兩個步驟：

- 建立其物件的執行個體。
- 呼叫安裝程式中想要執行的方法
```c#
// 建立一個安裝類別的執行個體
AssemblyInstaller assInstaller = new AssemblyInstaller("Practice.exe", null);

// 使用新安裝內容
assInstaller.UseNewContext = true;

// 以下視需求，執行必要的方法：

// 執行 Install
assInstaller.Install(actions);

// 執行 Commit 
assInstaller.Commit(actions);

// 執行 Uninstall 
assInstaller.Uninstall(actions);

// 執行 Rollback 
assInstaller.Rollback(actions);
```