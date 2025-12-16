---
title: Custom Web Server Controls
layout: default
parent: 自訂控制項
nav_order: 1
description: "Custom Web Server Controls"
date: 2012-10-02
tags: [Web,ASPNET,自訂控制項]
postid: "5203233555148613753"
---
Custom Web Server Control 是繼承至 Web server control。  它有二種做法：一是繼承現有的 Web Server Control (TextBox, Label, ...)；另一是直接繼承 [WebControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.aspx) 類別。  這樣的做法直接繼承了控制項的基本功能，後續只須要在上面擴充功能即可。  

前面介紹的 User Control ，它必須建立在網站專案中，所以只有同一網站下的網頁可以共用，無法和其它網站共用。  Custom Web Server Control 也可以直接建立在網站專案中，讓同一網站下的網頁可以共用。  不過，它也可以建立在類別庫專案中，然後產生 .dll 檔，讓多個專案都共用相同的 Custom Web Server Control 。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiS0JpGWW51mwJM891bxI2VEXofqrv8Xkvtqym5_czaIszvQVvE16HSRLv07npSxWZzjV0jmWHJR0XF64TnCgVYABGilPedYKToM4ko6cGyPrKiEZu-JA-IDEcXuunqdgFOkK4Jd5Yz-Og/s850/custom-sever-control-hierarchy.png)

# 如何建立自訂控制項 (Custom Web Server Controls)

要建立自訂控制項 (Custom Web Server Control)，有二個方式：  

- 新增一個「ASP.NET 伺服器控制項」項目，預設這個控制項會繼承 [WebControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.aspx) 。
- 新增一個類別，直接繼承現有的控制項。

## 繼承現有的 Web Server Control

A common scenario is to create a custom Web server control by inheriting from an existing control to add additional functionality.   This method ensures that you get a lot of base functionality from the inherited control.   You then extend this functionality with your custom control.  

To render the display of your custom control, you must override the [Render](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.render.aspx) method of the [TextBox](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.textbox.aspx) control.   This will use an [HtmlTextWriter](http://msdn.microsoft.com/zh-tw/library/system.web.ui.htmltextwriter.aspx) to output your display information and the base rendering of the [TextBox](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.textbox.aspx) control.   The following code shows an example of the complete, custom LabeledTextBox Web user control.  
```c#
namespace MyWebServerControl
{
public class LabeledTextBox : TextBox
{
public string LabelText { get; set; }
public int LabelWidth { get; set; }

protected override void Render(HtmlTextWriter writer)
{
if (LabelText == null)
LabelText = "";
if ((LabelWidth == 0) && (LabelText.Length > 0))
LabelWidth = 100 ;

writer.Write(@"<span style=""display:inline-block;width:{0}px"">{1}&nbsp;</span>", LabelWidth, LabelText);
base.Render(writer);
}
}
}
```
```xml
<mcc:LabeledTextBox ID="LabeledTextBox1" runat="server" LabelText="First Name:" LabelWidth="100" />
<mcc:LabeledTextBox ID="LabeledTextBox2" runat="server" LabelText="Last Name:" LabelWidth="100" />
```

上面範例，我們將資料直接寫在自訂控制項的屬性裡面，如果要在 code-behind 設定，可以在 Page\_Load 事件中給定值即可。  因為這個控制項繼承自 [TextBox](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.textbox.aspx) ，且是在它的 [Render](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.textbox.render.aspx) 事件中才依據資料建立樣式，所以只要在該事件之前變更值都會生效。  
```c#
protected void Page_Load(object sender, EventArgs e)
{
LabeledTextBox1.LabelText = "FirstName：";
LabeledTextBox2.LabelText = "LastName：";
}
```

## 繼承 WebControl 類別

Another approach to creating a custom Web control is to inherit directly from the [WebControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.aspx) class.   This approach is desirable when there is no control that currently provides default behavior similar to the control you wish to implement.   When inheriting from the [WebControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.aspx) class, you must override the [Render](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.render.aspx) method to provide the HTML output you want for your control.  
```c#
namespace MyWebServerControl
{
public class MyLogo : WebControl
{
public string LogoUrl { get; set; }
public string CompanyName { get; set; }

protected override void Render(System.Web.UI.HtmlTextWriter writer)
{
string content = string.Format(@"
<img src=""{0}"" title=""{1}"" /><br />
<span>{1}</span><br />", LogoUrl, CompanyName);

writer.WriteFullBeginTag("div");
writer.Write(content);
writer.WriteEndTag("div");
}
}
}
```

Rendering Methods of the [WebControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.aspx) Class

- [Render](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.render.aspx) ：
- [RenderContents](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.rendercontents.aspx) ：
- [RenderBeginTag](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.renderbegintag.aspx) ：
- [RenderEndTag](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.renderendtag.aspx) ：
- [RenderChildren](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.renderchildren.aspx) ：

# 把自訂的控制項加入 Toolbar

## 如何將自訂控制項加入 Toolbar

- Right-click the Toolbox, select 「Choose Items」
- Browse for the dll file to add the controls

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjP6-5Z6Ty6YHrLPCb4nls5F_497vuFIu8mEnj_M_GrzbOSLSVO1yb7iBgCmDPRKlxnf5_z27hfzhaoNMN1OZq0mbB5hPQtN7Hjijm_b40mZjQcPepdOaWk3IoBdxyX2jkcPlUwmq9JLg0/s426/add-control-to-toolbar.png)

## 設定自訂控制項的[顯示圖示]

透過 [System.Drawing](http://msdn.microsoft.com/zh-tw/library/system.drawing.aspx) 命名空間的 [ToolboxBitmap](http://msdn.microsoft.com/zh-tw/library/system.drawing.toolboxbitmapattribute.aspx) 屬性，可以用來設定自訂控制項的圖示。  圖示必須是 16x16 大小的 bitmap，它有幾種指定方式：  

### 1.使用絕對路徑

使用圖檔的絕對路徑當做參數即可。
```c#
using System.Drawing;

[ToolboxBitmap(@"D:\....\MyWebServerControl\MyLogo.bmp")]
public class MyLogo : WebControl
{
...
}
```

### 2.使用內嵌資源

1. 設計一個與控制項同名的圖檔。
2. 將圖檔加入專案中。
3. 在屬性視窗中，將建置動作設定成「內嵌資源」。
4. 使用 ToolboxBitmap 屬性指定上述內嵌資源的型別。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjxkbvamOAtXj3z8J_AJ7IiUvYMJOD__nl7_mdRC9_9WiEtdLuDQf09ldUlvrmZMn-17tAx5YDJTAMdhsJZ1TK-5Vek9NqohgvEGV2d0ob0soL5FZPsJlcoTCHMMd2QVoODKLMpUVLqehA/s299/setup-custom-control-icon.png)
```c#
using System.Drawing;

[ToolboxBitmap(typeof(MyLogo1))]
public class MyLogo1 : WebControl
{
...
}
```

顯示結果：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEicekaxDFDfTrnkh9MiFuMEjOpIiaXInf3GKw-tmz3NHuA7thN8XtWyl-7vm4Qf0iso9y5ZQwX_bfBysmPD3PpNUDQrK0I2VEcInvmmggCL1flH-e7Dee3Ru783t1yjnFeFA8J2WGWi-LY/s193/setup-custom-control-icon-2.png)

注意事項：

基於效能原因，由專案本身自動填入[工具箱]區域的元件不會顯示自訂點陣圖，且不支援 ToolboxBitmapAttribute。  若要在 [工具箱] 中顯示自訂元件的圖示，請使用 [選擇工具箱項目] 對話方塊載入您的元件。  [http://msdn.microsoft.com/zh-tw/library/fw694kde.aspx](http://msdn.microsoft.com/zh-tw/library/fw694kde.aspx)

## 設定[預設屬性]

透過 [System.ComponentModel](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.aspx) 命名空間的 [DefaultProperty](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.defaultpropertyattribute.aspx) 屬性，可以用來定義自訂控制項的預設屬性。  就像 Text 是 [TextBox](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.textbox.aspx) 的預設屬性。  
```c#
[DefaultProperty("CompanyName")]
public class MyLogo : WebControl
{
...
}
```

## 設定[預設標籤內容]

當拖拉自訂控制項到網頁上時，還可以進一步控制標籤裡面的內容。  透過 [System.Web.UI](http://msdn.microsoft.com/zh-tw/library/system.web.ui.aspx) 命名空間的 [ToolboxData](http://msdn.microsoft.com/zh-tw/library/system.web.ui.toolboxdataattribute.aspx) 屬性，就可以用來定義自訂控制項產生的預設標籤名稱。   
```c#
[ToolboxData(@"<{0}:MyLogo runat=""server"" LabelText="" "" LabelWidth="" "" />")]
public class LabeledTextBox : TextBox
{
public string LabelText { get; set; }
public int LabelWidth { get; set; }
...
}
```

上面這個例子，placeholder {0} 是保留給標記前置詞(namespace prefix)使用。  當建立該控制項時，系統就會自動幫我建立如下的標籤內容。  
```xml
<mcc:MyLogo ID="MyLogo1" runat="server" LabelText=" " LabelWidth="100" />
```

## 設定自訂控制項的[namespace prefix]

透過 [System.Web.UI](http://msdn.microsoft.com/zh-tw/library/system.web.ui.aspx) 命名空間的 [TagPrefix](http://msdn.microsoft.com/zh-tw/library/system.web.ui.tagprefixattribute.aspx) 屬性，可以用來定義網頁中使用的標記前置詞。  
```c#
[assembly: TagPrefix("MyWebServerControl", "mcc")]
namespace MyWebServerControl
{
...
}
```

當拖拉自訂控制項到網頁上時，系統在網頁上頭加入的註冊宣告，就會使用指定的預設前置詞，如下所示：。
```xml
<%@ Register Assembly="MyWebServerControl" Namespace="MyWebServerControl" TagPrefix="mcc" %>
```

## 總結與 Toolbar 相關的屬性設定

- [ToolboxBitmap](http://msdn.microsoft.com/zh-tw/library/system.drawing.toolboxbitmapattribute.aspx) ( [System.Drawing](http://msdn.microsoft.com/zh-tw/library/system.drawing.aspx) )：設定控制項在Toolbox中的顯示圖示
- [DefaultProperty](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.defaultpropertyattribute.aspx) ( [System.ComponentModel](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.aspx)  )：設定預設屬性
- [ToolboxData](http://msdn.microsoft.com/zh-tw/library/system.web.ui.toolboxdataattribute.aspx) ( [System.Web.UI](http://msdn.microsoft.com/zh-tw/library/system.web.ui.aspx) )：設定預設標籤內容
- [TagPrefix](http://msdn.microsoft.com/zh-tw/library/system.web.ui.tagprefixattribute.aspx) ( [System.Web.UI](http://msdn.microsoft.com/zh-tw/library/system.web.ui.aspx) )：設定預設標記前置詞
```c#
using System.Drawing;
using System.ComponentModel;
using System.Web.UI;

//標記前置詞
[assembly: TagPrefix("MyWebServerControl", "mcc")]
namespace MyWebServerControl
{
//顯示圖示
[ToolboxBitmap(@"D:\...\MyLogo.bmp")]
//預設屬性
[DefaultProperty("CompanyName")]
//預設標籤內容
[ToolboxData(@"<{0}:MyLogo runat=""server"" CompanyName="" "" LogoUrl="" "" />")]
class MyControl : MyLogo
{
public string LogoUrl { get; set; }
public string CompanyName { get; set; }
}
}
```

# 設計自訂控制項的 Designer 

要設計自訂控制項在設計階段的 Designer 樣式，必須先加入 System.Design.dll 組件參考。  然後建立一個繼承 [ControlDesigner](http://msdn.microsoft.com/zh-tw/library/system.web.ui.design.controldesigner.aspx) 的新類別，再覆寫 [ControlDesigner](http://msdn.microsoft.com/zh-tw/library/system.web.ui.design.controldesigner.aspx) 的 [GetDesignTimeHtml](http://msdn.microsoft.com/zh-tw/library/system.web.ui.design.controldesigner.getdesigntimehtml.aspx) 方法。  這個方法就是用來 render 設計階段的 HTML 標籤。  
```c#
using System.Web.UI;
using System.ComponentModel;

public class LabeledTextBoxDesigner : ControlDesigner
{
private LabeledTextBox _labeledTextBoxControl;
public override string GetDesignTimeHtml()
{
if (_labeledTextBoxControl.LabelText.Trim().Length == 0)
return "<div style='color: Gray'>[LabeledText Undefined]</div>";
else
return base.GetDesignTimeHtml();
}
public override void Initialize(IComponent component)
{
_labeledTextBoxControl = (LabeledTextBox)component;
base.Initialize(component);
return;
}
}
```

最後再將這個自訂的新類別，指派給自訂控制項的 [Designer](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.designerattribute.aspx) 。
```c#
[Designer("MyWebServerControl.LabeledTextBoxDesigner, MyWebServerControl")]
public class LabeledTextBox : TextBox
{
....
}
```

底下是加入 Designer 的控制項顯示的結果：

當 LabeledTextBox 控制項的 LabelText 沒有被指定時，看到的樣子。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg42ZgS8Wxusg_bQN8VNwuRUyxHIe1DmzX68wUc61NXY3YoGq-hqgii_NxyTmp02Nu7jlCMXxt09V5_bdt3PCKJIDle7Y09HPcpfZ5BgfGzp6ql0wYLaqOtF78POE5CzPtpHyZqCtE3qWU/s297/ControlDesigner1.png)

當 LabeledTextBox 控制項的 LabelText 有設定資料時，看到的樣子。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgoD3m8UdOoWb693HFvqABZmHvGRbEVHlyEwQhLMlQiHCdfKzS9vd3lDUY_t_Mj44wuH5BWPQkI5i5wYylQfqq9Q7i0eGLZdY-vQO6ctNVVtP2r8Rpkb5llOBnXABv5HWHh64Ms3cy04kM/s297/ControlDesigner2.png)

# 組合式的自訂控制項 (Composite Control)

A composite control is a custom web control that contains other controls.   This sounds like a user control, but the composite control doesn't provide a designer screen for creating the control, nor an .ascx file that lets you drag and drop controls on it at design time.   Instead, you create a custom control that inherits from the [CompositeControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.compositecontrol.aspx) class.   You then add constituent controls to this control. The composite control then handles events raised by its child controls.  

## 如何建立組合式的自訂控制項

1. Creating a class that inherits from the [CompositeControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.compositecontrol.aspx) class and implements the [INamingContainer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.inamingcontainer.aspx) interface.
2. Overrides the [CreateChildControls](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.createchildcontrols.aspx) method   
This method contains the code to instantiate the child controls and set their properties.
3. Creating a [Panel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.panel.aspx) class as a container to put all child controls.
```c#
public class UserPasswordControl : CompositeControl
{
public event EventHandler Submitted;

public string UserName {
get
{
TextBox txt = (TextBox)FindControl("UserName");
return txt.Text;
}
set
{
TextBox txt = (TextBox)FindControl("UserName"); 
txt.Text = value;
}
}
public string Password {
get
{
TextBox pwd = (TextBox)FindControl("Password");
return pwd.Text;
}
set
{
TextBox pwd = (TextBox)FindControl("Password"); 
pwd.Text = value;
}
}

protected override void CreateChildControls()
{

//建立一個 Panel 物件,用來置於所有的子控制項
Panel pnl = new Panel();

//建立子控制項
TextBox txtUserName = new TextBox(); 
TextBox txtPassword = new TextBox(); 
Button btnSubmit = new Button(); 
btnSubmit.Click += new EventHandler(btnSubmit_Click);

//建立 Panel 控制項
Controls.Add(pnl);

//add user name row
pnl.Controls.Add(new LiteralControl("<table>"));
pnl.Controls.Add(new LiteralControl("<tr><td>User Name:</td><td>"));
pnl.Controls.Add(txtUserName);
pnl.Controls.Add(new LiteralControl("</td></tr>"));

//add password row
pnl.Controls.Add(new LiteralControl("<tr><td>Password:</td><td>"));
pnl.Controls.Add(txtPassword);
pnl.Controls.Add(new LiteralControl("</td></tr>"));

//add submit button row
pnl.Controls.Add(new LiteralControl(@"<tr><td colspan=""2"" align=""center"" >"));
pnl.Controls.Add(btnSubmit);
pnl.Controls.Add(new LiteralControl("</td></tr></table>"));

//設定子控制項的屬性
pnl.Style.Add("background-color", "silver");
pnl.Style.Add("width", "275px");
txtUserName.ID = "UserName";
txtUserName.Style.Add("width", "170px");
txtPassword.ID = "Password";
txtPassword.TextMode = TextBoxMode.Password;
txtPassword.Style.Add("width", "170px");
btnSubmit.Text = "Submit";

}

void btnSubmit_Click(object sender, EventArgs e)
{
if (Submitted != null) Submitted(this, e);
}
}
```

## 如何取得動態產生的自訂控制項的資料

下面程式碼示範如何由程式碼建立自訂控制項，同時訂閱事件處理函式。  並且在事件處理函式中取得使用者在這個自訂控制項輸入的資料。  
```c#
protected void Page_Init(object sender, EventArgs e)
{
UserPasswordControl upc = new UserPasswordControl();
upc.Style.Add("position", "absolute");
upc.Style.Add("left", "25px");
upc.Style.Add("top", "50px");
form1.Controls.Add(upc);
upc.Submitted += SubmittedHandler; 
}
protected void SubmittedHandler(object sender, EventArgs e)
{
UserPasswordControl UserPasswordControl1 = (UserPasswordControl)sender;
myMessage.Show(this, UserPasswordControl1.UserName + "\r\n" + UserPasswordControl1.Password);
}
```

# 建立自訂樣板控制項 (Templated Custom Web Control)

## 什麼是自訂樣板控制項

A templated control provides separation of control data from its presentation, which means that a templated control does not provide a default user interface.   For example, if you know that you need to display product data,   but you don't know how the page designer wants to format the product data,   you could create a templated control called ProductControl that allows the page designer to supply the format for the product data as a template.  

Templated control 必須要符合以下須求：

- 提供具名的樣板。
- 提供可存取主頁面資料的屬性或方法。

## 如何自訂樣板控制項

A templated user control must provide a [naming container](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.namingcontainer.aspx) and a class with properties and methods that are accessible to the host page.   The template contains the UI for the templated user control and is supplied by the page developer at design time.   The template can contain controls and markup.  

- 繼承 [Control](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.aspx) 類別
- 實作 [INamingContainer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.inamingcontainer.aspx) 介面  
任何實作這個介面的控制項，會建立一個新的命名空間，讓它的所有子控制項的 ID 屬性在整個應用程式內可以保證唯一，以避免發生名稱衝突。  也支援資料繫結 (Data Binding) 的 Web 伺服器控制項內之動態產生的伺服器控制項執行個體的唯一命名。

設計 Templated Control 的步驟：

1. 建立一個 [Container](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.container.aspx) 類別  
This container class must has public properties that you wish to access via the [Container](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.container.aspx) object in your template.  
For Example:      ```c#
public class ProductContainer : Control, INamingContainer
{
public ProductContainer() { }

public ProductContainer(string productId, string productName, int unitPrice)
{
ProductId = productId; 
ProductName = productName;
UnitPrice = unitPrice;
}

public string ProductId { get; set; }
public string ProductName { get; set; }
public int UnitPrice { get; set; }
}
```

Container 類別中的屬性，將會在 template 設計中，透過 [Container](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.container.aspx) 物件取得。
```xml
<mcc:ProductControl ID="ProductControl2" runat="server" >
<LayoutTemplate>
<h3>Product ID: <%# Container.ProductId %></h3> 
<table border="1">
<tr><td>Name:</td><td><%# Container.ProductName %></td></tr>
<tr><td>Unit Price:</td><td align="right"><%# Container.UnitPrice.ToString("C") %></td></tr>
</table>
</LayoutTemplate>
</mcc:ProductControl>
```
2. 建立一個 Templated 類別  

- 加入一個或多個樣板欄位 (ITemplate Type)
- 加入 template 會用到的屬性欄位
- 覆寫底層 [Control](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.aspx) 類別的 [DataBind](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.databind.aspx) 方法  
在這個方法中，叫用 EnsureChildControls 建立子控制項。
- 覆寫底層 [Control](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.aspx) 類別的 [CreateChildControls](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.createchildcontrols.aspx) 方法  
在這個方法中，叫用 [ITemplate.InstantiateIn](http://msdn.microsoft.com/zh-tw/library/system.web.ui.itemplate.instantiatein.aspx) 方法, 以實體化 template
```c#
[ParseChildren(true)]  //指示剖析器，將伺服器控制項標記內所包含的項目解譯成屬性 (Property)。
public class ProductControl : Control, INamingContainer
{
//建立一個 ITemplate 樣板屬性
//供設計者定義真正的 layout

[PersistenceMode(PersistenceMode.InnerProperty)]    //指定 ASP.NET 伺服器控制項屬性或事件在設計階段保存至 ASP.NET 網頁的方式。
[TemplateContainer(typeof(ProductContainer))]       //宣告 container 的型別
public ITemplate LayoutTemplate { get; set; }

//建立可被樣板中的 Container 物件存取的屬性

public string ProductId { get; set; }
public string ProductName { get; set; }
public int UnitPrice { get; set; }

//覆寫 DataBind
//在此方法中，呼叫 EnsureChildControls 方法

public override void DataBind()
{
//判斷伺服器控制項是否包含子控制項。如果不包含，則建立子控制項。
EnsureChildControls();
base.DataBind();
}

//覆寫 CreateChildControls
//在此方法中，呼叫 ITemplate.InstantiateIn 方法, 以實體化 template

protected override void CreateChildControls()
{
this.Controls.Clear();
if (LayoutTemplate != null)
{
ProductContainer container  = new ProductContainer(ProductId, ProductName, UnitPrice);
LayoutTemplate.InstantiateIn(container);
this.Controls.Add(container);
}
else
{
this.Controls.Add(new LiteralControl("No LayoutTemplate Defined"));
}
}
}
```

## 使用自訂樣板控制項
```xml
<mcc:ProductControl ID="ProductControl1" runat="server"
ProductId="E0002" ProductName="ASUS A53S" UnitPrice="25000">
<LayoutTemplate>
<h3>Product ID: <%# Container.ProductId %></h3> 
<table border="1">
<tr><td>Name:</td><td><%# Container.ProductName %></td></tr>
<tr><td>Unit Price:</td><td align="right"><%# Container.UnitPrice.ToString("C") %></td></tr>
</table>
</LayoutTemplate>
</mcc:ProductControl>

<mcc:ProductControl ID="ProductControl2" runat="server"
ProductId="E0002" ProductName="ASUS A53S" UnitPrice="25000">
<LayoutTemplate>
<asp:Label ID="Label1" runat="server" Text="<%# Container.ProductId %>"></asp:Label><br />
<asp:Label ID="Label2" runat="server" Text="Name："></asp:Label>
<asp:TextBox ID="TextBox1" runat="server" Text="<%# Container.ProductName %>"></asp:TextBox><br />
<asp:Label ID="Label3" runat="server" Text="Price："></asp:Label>
<asp:TextBox ID="TextBox2" runat="server" Text="<%# Container.UnitPrice %>"></asp:TextBox>
</LayoutTemplate>
</mcc:ProductControl>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjty_nFsF9T9Nn35XfrHLae9LUaDpz5wJ_U-NRcpFsQ4U8KrHadcoP4_RRdWgZkleZyk1hbzVDdXV0bnIGfkMR9Hj13X8E2avt1dszM2TMqcK5-OXm3TD-QGt3iH1581Y6_LCRL82n6m2Y/s320/template-custom-control-result.png)
## 參考資料  

- [為控制項提供工具箱點陣圖](http://msdn.microsoft.com/zh-tw/library/4wk1wc0a.aspx)
- 
-