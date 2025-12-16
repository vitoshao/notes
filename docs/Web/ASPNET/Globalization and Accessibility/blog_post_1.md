---
title: 設定協助工具
layout: default
parent: Globalization and Accessibility
nav_order: 1
description: "設定協助工具"
date: 2012-10-08
tags: [Web,ASPNET,Globalization and Accessibility]
postid: "7176701697946537354"
---
網站的協助工具 (Accessibility, VisualStudio 中翻成「網頁可及性」) 指的就是無障礙網頁的設計。  它的目的是協助使用非傳統I/O介面的使用者，也可以很方便地使用網站。  例如：有些使用者使用特殊的滑鼠，或者使用螢幕助讀器 (screen readers) 存取網站內容，而不是傳統的螢幕。  

# ASP.NET 控制項對 Accessibility 的支援

ASP.NET controls are designed to be accessible by default.   For example, login controls such as Login, ChangePassword, PasswordRecovery, and CreateUserWizard       **use text boxes with associated labels** to help a user who uses a screen reader or who does not use a mouse.  

Another way some ASP.NET controls support accessibility is by allowing users to       **skip link text**.   Screen readers typically read the text of links from the top to the bottom of a page, enabling users to choose a specific link.   ASP.NET controls that include navigation links provide the SkipLinkText property,   which is enabled by default and allows users to skip past the link text.   Such as CreateUserWizard, Menu, SiteMapPath, TreeView, and Wizard support skipping links.

# 在視覺上提供協助

幾種提供視覺協助的設計原則：

- 使用 [AlternateText](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.alternatetext.aspx) 屬性描述 Image
- 使用 [Table.Caption](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.table.caption.aspx) 屬性描述 Table
- 避免指定字型大小
- 避免使用 Scripts
- 背景色使用 Solid Colors ，文字使用 Contrasting Colors .

## Table

## Image

關於 Image 標籤有幾個屬性是針對視覺障礙使用者設計的，它只會在特殊的瀏覽器，例如螢幕助讀器 (screen readers) 產生作用。  關於更多「無障礙網頁」的需求可參閱 [無障礙網頁開發規範](http://www.webguide.nat.gov.tw/wSite/np?ctNode=14408&mp=1)  

- [AlternateText](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.alternatetext.aspx) ：取得或設定當沒有影像時，要顯示於 Image 控制項的替代文字。
- [DescriptionUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.descriptionurl.aspx) ：  
通常我們會使用 [ToolTip](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.tooltip.aspx) 屬性來設定圖片的說明文字，但這是給一般瀏覽器使用的，它會 render 成 &lt;img&gt; 標籤的 Title 屬性。  針對螢幕助讀器，必須使用 [AlternateText](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.alternatetext.aspx) 屬性，它會 render 成 &lt;img&gt; 標籤的 Alt 屬性。  如果一個說明文字的內容大長，超過 [AlternateText](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.alternatetext.aspx) 屬性限制，你也可以將說明文字放在檔案中，再使用 [DescriptionUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.descriptionurl.aspx) 屬性指定，它會 render 成 &lt;img&gt; 標籤的 LongDesc 屬性。
- [GenerateEmptyAlternateText](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.generateemptyalternatetext.aspx) ：  
若圖片的說明內容不重要，例如一些邊框的圖片，你就可以使用這個屬性，讓螢幕助讀器忽略這個說明文字。  如果只是設定 AlternateText="" ，將不會產生 alt 屬性。
```xml
<asp:Image ID="Image1" runat="server" 
ImageUrl        = "~/Images/ad1.jpg" 
ToolTip         = "this is ToolTip" 
AlternateText   = "this is AlternateText"
DescriptionUrl  = "Img1Desc.html"
GenerateEmptyAlternateText="True" />

<--Render 後的結果-->

<img id="Image1" 
src     = "../Images/ad1.jpg" 
title   = "this is ToolTip" 
alt     = "this is AlternateText"
longdesc= "Img1Desc.html"  />     
```

# 在表單輸入上提供協助

To make your application as accessible as possible using a keyboard, follow these guidelines :

- 在表單中設定 DefaultFouce 屬性
- 定義 tab 鍵的移動順序
- 使用 DefaultButton 屬性設定預設按鈕。
- 使用有意義的連結文字。
- 使用 [AccessKey](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.accesskey.aspx) 屬性設定按鈕的快速鍵。  

因為 [TextBox](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.textbox.aspx) 控制項沒有 description 的區域，所以可以在前面放置 [Label](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.label.aspx) 來協助 screen readers 理解。  雖然 [TextBox](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.textbox.aspx) 就可以直接設定快速鍵，你也可以將快速鍵設定在 [Label](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.label.aspx) 控制項上，  並指定 [AccessKey](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.accesskey.aspx) 和 [AssociatedControlID](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.label.associatedcontrolid.aspx) 屬性，將 [Label](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.label.aspx) 和指定的控制項建立關連。  
```xml
<asp:Label ID="Label6" runat="server" Text="<u>N</u>ame:"/>
<asp:TextBox ID="txtName" runat="server" TabIndex="8" AccessKey="N" /><br />

<asp:Label ID="Label7" runat="server" Text="<u>A</u>ddress:" AssociatedControlID="txtAddress" AccessKey="A" />
<asp:TextBox ID="txtAddress" runat="server" TabIndex="9" />
```

[偵側瀏灠器支援的快速鍵](http://www.quirksmode.org/js/keys.html)
- 使用 [Label](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.label.aspx) 控制項的 [AccessKey](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.accesskey.aspx) 和 [AssociatedControlID](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.label.associatedcontrolid.aspx) 屬性，協助定義 [TextBox](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.textbox.aspx) 的快速鍵。
- Use the Panel control to create subdivisions.  ```xml
&nbsp;<asp:Panel ID="Panel1" runat="server" GroupingText="Profile" >
....
</asp:Panel>
<br />
<pre>```
- specify meaningful error messages in the [Text](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.text.aspx) and [ErrorMessage](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.errormessage.aspx) properties of validator controls

# Testing Accessibility

Visual Studio can test Web pages or entire Web applications for compliance with WCAG and Section 508 standards.

- WCAG Priority 1 ： WCAG 1.0 版中的標準檢查，定義必要的功能
- WCAG Priority 2 ： WCAG 2.0 版中的指定檢查，定義建議的功能
- Access Board Section 508 ： 美國聯邦政府復健法案第 508 條中建立的標準

## Checking the Accessibility of a Single Page

To use Visual Studio to test the accessibility of a Web page, follow these steps:

1. In Visual Studio, open the page you wish to check.
2. From the View menu, select Error List to display the Error List window.  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhivrHlo_iizdL6CSouTjfEppbUWz4q2o6WB_1_KT0yMtm53USxtC_fOQfWU8X2IOBBRthcHVWDxl7wvgy9Abg_zD_-GxZbdjSzZU-OpN_DpllvaoW6UJHqQfuGQ6tVZIiW8aRRylyQDI8/s313/Error-List-window.png)
3. From the Tools menu, select Check Accessibility.   

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhG26M6AnLvz5DFN3685pf37Jl4chFWaGd3BphL-Fgf6XGXK_U2ZBTMZlgTX6SY579rdq0s0wHqPgOa_UbRDFitVtVkZfGdrfhXx5bQdqyuhyphenhyphen8vs1qLrT_NuN5AjNZk3afGHVEgqmPJgGk/s341/CheckAccessibility.png)
It will show the Accessibility Validation dialog box .  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgxl3AsFNVLOkYeb_aC_Z896Q2oYuvZjl1ZP8mWr0HfO8-BJDLVoNTyw98bxPf5LQuxgeE4od9sXDcnV_C2hy7Q0PuJZEdlSrmUgwCcPsMMaNOsvRO0KJqZwiHABFNXlKU7Gv87bC3cJDA/s356/Accessibility-Validation-DialogBox.png)
4. Select the check boxes for the type and level of accessibility checking that you want to perform

## Checking the Accessibility of a Web Application

You can use Visual Studio to automatically test the accessibility of an entire Web application when you build it. To do so, follow these steps :

1. In Solution Explorer, right-click your Web site and select Property Pages.
2. Click the Accessibility node.  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgtkEH076H7cFKzIPm2dALbhCSuj5nn_bYv9tA9u_zITVIpX1wACmNtsH5_7klTMOhzEKq0z1o3w2lTPFBSlyOlbSKOnZIhRMzc7EGlXfsewoKO1qp2z1zxQkG6UJUmjvtWzMLGWOm-LT8/s602/PropertyPage-Accessibility.png)
3. Select the check boxes for the type and level of accessibility checking that you want to perform, and then click Apply.
4. Next, select the Build node of the Property Pages dialog box.
5. Checked the options in the Accessibility Validation group .  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi1YrIhxiF7aczPCcPTIMrg21mpFce7-yfl8ni__cgcwTIb2ExA2mqiBy79dA6kZZdCoT2BiitdJrtdUayZ8SQtqXc7M8BLeO8DeZmdcIdrqrDHj3BvNBkIQiG7SsLN7-IoWWa-zxlisDM/s648/Accessibility-Validation.png)
6. Click OK to close the Property Pages dialog box.

Now, when you build your Web application, Visual Studio automatically generates a list of accessibility warnings.   Accessibility warnings won't prevent a successful build.   You will have to manually view the Error List to examine any accessibility issues.
## 參考資料  

- [網頁可及性驗證對話方塊](http://msdn.microsoft.com/zh-tw/library/ex3w0c3d.aspx)
- 
-