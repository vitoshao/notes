---
title: User Controls
layout: default
parent: 自訂控制項
nav_order: 2
description: "User Controls"
date: 2012-10-02
tags: [Web,ASPNET,自訂控制項]
postid: "8650684280940043047"
---
在 Web 應用程式中，要建立自訂控制項有二種方法：

- User Control：將數個Web控制項組合成單一的控制項。
- Custom Web Server [Control](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.control.aspx) ：繼承現有Web控制項並加以擴充功能成新的控制項。

下圖是 VS2008 裡的項目名稱

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhY8sV2X1bMS1lf2NGa9yg3m5lRIl29dAGSz-mTaUE26gaQF2yq-OUJoBEHdtzTBZRCZvqFXokIEXd5RZfj853cgG9kIk_6KvjTxdTk13TX9WnBT7Ih6RKNM9JXhn85ezFN_fHC_s_O-IE/s461/add-custom-control-template.png)

在 VS2010 裡的項目名稱則略有不同

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjO0Q81rzwwC0PYL5Rlx0qz6oUkaa-dVQ5in2gDHnnIN1qHdko3Ug8w5X05scaVpte2tKWZuRfLQswm6YbbNDIJPlaAPKKB7JvdW_hTgZXmWnjFcnoenw9H8TN8liZOuMOaujBto_VZ9rk/s472/add-custom-control-template2.png)

若是在 Windows 應用程式中，你可以新增以下二種項目來建立自訂的控制項。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgSMUnDw2tOhccHZ7x4tkt-OhiAoqgZoyfhVWTY7OkvE4k5y_tFmUVPwi_o8XMxjXa-Oq3CfgstuU3LiUx7cZFrw04jSth3S4WcYG8jeaTAOFA2v9p6YmPh4kqVWxiAXQHqWMBVUjsmaLs/s612/add-win-custom-control-1.png)

- User Control：這個項目會直接繼承 [UserControl](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.usercontrol.aspx) 類別，可用來將數個 Windows Form 控制項組合成單一的控制項。
- Custom Web Server Control：這個項目會直接繼承 [Control](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.control.aspx) 類別，所以它只提供控制項的基本功能，其它功能如：圖形介面、事件程式碼等都要自行撰寫。

當然，若要自訂的控制項，功能與現有的 Windows Form 控制項類似的話，最簡單的方法就是直接繼承 Windows Form 控制項。  這樣做可以保留 Windows Form 控制項的所有繼承功能，然後藉由加入自訂屬性、方法或其他功能來擴充該功能。  

# Creating User Controls

#### A User Control

- provides an easy way to combine several controls into a single control.
- encapsulating functionality for reuse.
- helps define a consistent user interface.
- The user controls inherit from the [UserControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.usercontrol.aspx) class.
- similar to building a standard Web page.
- a file with the .ascx extension.

#### The [UserControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.usercontrol.aspx) class hierarchy

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiQCugl8JB24GZFGRYwPEMdpAe6Xv0rIP-k0XpWvgSB1IB4H9cqthoE63snr4O6Dizfw4pY4aBIqK73bas-GkOc4qoSu_0zp4-k3atD4RFegCaVBc9itsKr-zvA8NWmglabIEBPKxxe7LE/s588/UserControl-class-hierarchy.png)

#### To create a user control

建立自訂控制項，必須新增一個「Web 使用者控制項」項目，然後就像設計網頁一般，可以將各種Web控制項拖拉到頁面上。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiu8wG5LVFYg5rCq9RY3WSaJJ2tsIkZOTshx5rNzZTF_c2bdeTWOMy2qNLy-o5PuOQTeubEuclhRbS7sCSEwmhR3jnCxHNklVN1laZJzfupvEZ6186V6mcwBctl-CvajNNww44F8BHnJtA/s271/web-user-control.png)

使用者控制項就像一個網頁，但是它使用 **@ Control** 宣告詞，而不是 **@ Page** 。  
```xml
<%@ Control Language="C#" 
AutoEventWireup="true" 
CodeBehind="ucLogin.ascx.cs" 
Inherits="ucLogin" %>
```

#### Note:

Generally, it is wise to refrain from putting much style information beyond layout inside a user control. This allows a user control to take on the styles and themes defined by the  pages and site to which it is added.

# Adding a User Control to a Page

若要在頁面中加入自訂控制項，最快的方法是在 **Design View** 中，直接將 Solution Explorer 中的自訂控制項拖拉至頁面中。  它除了會建立自訂控制項的標籤之外，也會自動在頁面上頭加入該自訂控制項的註冊宣告。  

#### 1. 在網頁中加入使用自訂控制項的宣告

當拖拉自訂控制項至頁面中後，系統會自動在網頁上頭，加入自訂控制項的使用宣告。  底下是宣告中的屬性說明：  

- **@ Register** ：this directive is required to register the user control on the page.
- **TagPrefix** ：this attribute is a namespace identifier for the control.
- **TagName** ：this attribute is the name of the control.
```xml
<%@ Register src="../UserControls/MyUserControl.ascx" tagname="MyUserControl" tagprefix="uc1" %>
```
```xml
<uc1:MyUserControl ID="MyUserControl1" runat="server" Kind="Utc" />
```

#### 2. 在組態檔中加入使用自訂控制項的宣告

自訂控制項的使用宣告也可以在 Web.config 中設定，這樣子就不用在每個網頁中重複宣告。
```xml
<configuration>
<system.web>
<pages>
<controls>
<add tagPrefix="uc" tagName="ucTest1" src="~/UserControls/ucTest.ascx"/>
<add tagPrefix="uc" tagName="ucTest2" src="~/UserControls/ucTes2.ascx"/>
</controls>
</pages>
</system.web>
</configuration>
```

# Defining Events in User Controls

User controls can have their own encapsulated events.   This includes life cycle events such as Init and Load.   These events are called in sync with the events on the page.   For example, after the page Init event is called, the control's Init event is called in turn.  

User controls can also cause PostBack for the Web page to which they belong.   When doing so, the user control's events are raised accordingly along with the events of the page.  The event handlers for a user control, however, are typically encapsulated in the given user control.   This ensures the code for these events can be maintained independent of the pages that use the user controls.  

If you want to pass the Button control's click event to the Page,   you can define an event on your user control and passing this event to the Web page.  

The following steps show how to passing events up to the page.

## 1) define the event arguments class

You can define the event arguments class inherits from EventArgs. It will be used by the event to pass the information from the user control to the page.  
```c#
public class MyUserControlEventArgs : EventArgs
{
public DateTime GetDateTime { get; private set; }
public string GetDate { get; private set; }
public string GetTime { get; private set; }

public MyUserControlEventArgs(DateTime dt)
{
GetDateTime = dt;
GetDate = dt.ToString("yyyy/MM/dd");
GetTime = dt.ToString("hh:mm:ss");
}
}
```

## 2) declare a delegate

Next, you should declare a delegate if you are using C#.   The delegate can be put in the same class file that contains both the event arguments and the user control class.   However, the delegate does not go into one of those classes.   Note that if you are using Visual Basic, you can rely on the event model to handle this step for you.  
```c#
public delegate void GetTimeButtonClickHandler(object sender, MyUserControlEventArgs e);
```

## 3) raises events in the user control

The next step is to add code to the user control that defines an event and raises that event when the user clicks the button.   This event will be trapped by the page that consumes the user control.   
```c#
public delegate void GetTimeButtonClickHandler(object sender, MyUserControlEventArgs e);
public partial class MyUserControl : UserControl  {      public event GetTimeButtonClickHandler GetTimeButtonClick;      public DateTimeKind Kind { get; set; }
protected void GetTime_Click(object sender, EventArgs e)      {          if (GetTimeButtonClick != null)          {              DateTime now ;                if (Kind == DateTimeKind.Utc)                  now = DateTime.UtcNow;              else                  now = DateTime.Now;                MyUserControlEventArgs args = new MyUserControlEventArgs(now);              GetTimeButtonClick(sender, args);          }      }  }  ```

## 4) In the web page , add trap event code exposed by the user control

Finally, you add code to the page that contains the user control.   This code should trap the event exposed by the user control.  In C#, you wire up a handler using the **+=** syntax inside the **Page\_Init** method.  
```c#
protected void Page_Init(object sender, EventArgs e)
{
MyUserControl1.GetTimeButtonClick += MyUserControl1_GetTimeButtonClick;
}
protected void MyUserControl1_GetTimeButtonClick(object sender, MyUserControlEventArgs e)
{
myDebug.ResponseBR(e.GetDateTime.ToString());
myDebug.ResponseBR(e.GetDate);
myDebug.ResponseBR(e.GetTime);
}
```

## 補充說明

#### 使用 EventHandler&lt;TEventArgs&gt; 泛型委派

在步驟２和步驟３中，我們宣告了一個委派和一個事件。這二行程式碼也可以直接使用 .NET 預先定義好的泛型委派 ([EventHandler&lt;TEventArgs&gt;](http://msdn.microsoft.com/zh-tw/library/db0etb8x.aspx)) 來宣告事件。  
```c#
public event EventHandler<MyUserControlEventArgs> GetTimeButtonClick;
```

[EventHandler&lt;TEventArgs&gt; ：](http://msdn.microsoft.com/zh-tw/library/db0etb8x.aspx)   這是一個預先定義的，而且可以傳遞事件資料的委派，使用它就不必再自行定義一個委派。  

#### 使用自訂控制項的事件

上面範例中，我們在 Page\_Init 中，使用 += 語法來封裝委派要執行的方法。  
你也可以在控制項中，找到一個以 On 開頭的事件名稱，直接設定它的執行方法。  
```xml
<uc1:MyUserControl ID="MyUserControl2" runat="server" OnGetTimeButtonClick="MyUserControl_GetTimeButtonClick" />
```

# Defining Properties in User Controls

The user controls you create will often need configuration data.   You can define this configuration data through properties.   Properties added to user controls can then be configured in the page markup that uses the control.  

When developers use the user control, they can set the properties declaratively through markup.   In fact, these properties are also available through IntelliSense.  
```c#
public partial class MyUserControl : UserControl  {    public delegate void GetTimeButtonClickHandler(object sender, MyUserControlEventArgs e);
public event GetTimeButtonClickHandler GetTimeButtonClick;      public DateTimeKind Kind { get; set; }
public string UserName      {          get { return txtUserName.Text; }          set { txtUserName.Text = value; }      }      public string Password      {          get { return txtPassword.Text; }          set { txtPassword.Text = value; }      }  }  ```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEirHo1PjSWUf-19lHBE8CJxfgbc8g2eaFQFmV-lX_X5K5ywrF1jh7N3kx4nIqwHxOW7EVCYhxUlPKLJW_etmFHwE2-T_eRNnQUw8wIHr9hyYbd2mLq6ddXelGLPSd0DcqrYPlwK2G4ZZv8/s727/user-control-IntelliSense.png)

## 在 User Controls 中加入 Properties
```c#
public DateTimeKind Kind { get; set; }
```

## 讀取 User Controls 中控制項的值

Another common scenario is the need to access the values of the controls contained by a user control.   Controls on a user control are declared as **protected members** of the control.   This means they are not directly accessible outside the user control class.   Therefore, to access a control, you can return a reference to it through a public property setting.  
```c#
public string Account
{
get { return txtAccount.Text; }
set { txtAccount.Text = value; }
}
public string Password
{
get { return txtPassword.Text; }
set { txtPassword.Text = value; }
}
```

# Dynamically Loading User Controls

Like other server controls, user controls can be loaded dynamically.   Loading controls dynamically can be useful in situations in which you wish to add a variable quantity of a given control to a page.  

To dynamically load a user control,   you have to use the [LoadControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.loadcontrol.aspx) method of the **Page** class in the **Page\_PreInit** Event.  The method also returns a reference to the control class it creates.   You can set this to a variable by casting the returned object to a strong type.   For this to work, the control must already be registered with the page.  

#### LoadControl
```c#
public Control LoadControl(string virtualPath);
public Control LoadControl(Type t, object[] parameters);
```
```c#
MyUserControl ctl = (MyUserControl) LoadControl("MyUserControl.ascx");

form1.Controls.Add(ctl);
```

PS1.    
雖然在任何事件中，都可動態載入自訂控制項，但是，如果在動態控制項中，有使用到 ViewState ，就必須考量到載入的時機。  

PS2.  
上面範例中，用來載入自訂控制項用的 [LoadControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.loadcontrol.aspx) 方法，它是 Page 物件的方法。  
但是，自訂控制項也是一個 Server [Control](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.control.aspx) ，它必須被建立在 runat="server" 的容器之中。  
ASP.NET 的 Form 本身就是一個容器，所以上面範例中，使用 form1.Controls.Add 來加入自訂控制項。

# Creating a Templated User Control

A templated user control provides separation of control data from the presentation of that data.   A templated user control does not provide a default UI layout.   Instead, this layout is provided by the developer who uses the user control on his or her page.   This provides increased flexibility in terms of layout while keeping the encapsulation and reuse benefits of a user control.  

## Creating a Templated User Control

The following steps are outlines to creating a templated user control.  

1. **Adding a user control file to your Web application**
2. **Place a [Placeholder](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.placeholder.aspx) control in the user control.**   
This defines a placeholder for the template.   You will expose this template as a property.   The users of your control will then define their code inside this template.
3. **Define a class to serve as a [naming container](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.namingcontainer.aspx)**  
In this step , you have to define a class file in your application to serve as a naming container.   This class file will contain a reference to the data for your user control. This data will be  bound to when a user of your control creates his or her own layout template.    
This class inherits from [Control](http://msdn.microsoft.com/zh-tw/library/983zwx2h.aspx) and implements the [INamingContainer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.inamingcontainer.aspx) interface.   The class should also contain public properties for any data elements it is meant to contain.
4. **Implement a property of type [ITemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.itemplate.aspx)**   
In this step , you have to implement a property of type [ITemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.itemplate.aspx) in your in User Control.  This property will serve as the template for users of your control.  The name you give this property is the name of the template tag in any consuming page's markup.
5. **Apply the attribute to the property**  

- [TemplateContainer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.templatecontainerattribute.aspx) ：  
This attribute will marks it as a template.      To this attribute, you pass the type of the [naming container](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.namingcontainer.aspx) class as an argument of the constructor.       This serves to allow binding between the container and the template definition markup when the user control is added to a page.
- [PersistenceMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.persistencemodeattribute.aspx) ：  
You pass the enumeration value of PersistenceMode.[InnerProperty](http://msdn.microsoft.com/zh-tw/library/system.web.ui.persistencemode.aspx) into the attribute's constructor.
6. **Add code to the Page\_Init method**  
Here you test for the [ITemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.itemplate.aspx) property.   If the [ITemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.itemplate.aspx) property is set, you create an instance of the [naming container](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.namingcontainer.aspx) class and create an instance of the template in the naming container.   You then add the [naming container](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.namingcontainer.aspx) instance to the Controls collection of the PlaceHolder server control.    
[InstantiateIn](http://msdn.microsoft.com/zh-tw/library/system.web.ui.itemplate.instantiatein.aspx) ：  這個方法是 LayoutTemplate 物件中唯一的方法，你必須在 Page\_Init 事件中，用它來初始化樣板中的子控制項。
7. **Pass data from your user control to the [naming container](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.namingcontainer.aspx)**   
This allows users to set properties of your user control and store and use them in the container.  In this case, you have to define this data in your user control as properties that a user of the user control can set.   You then must pass a reference to this data to the naming container.   This ensures the [naming container](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.namingcontainer.aspx) is updated when the property values change on the user control.
8. **define the naming container**  
In the previous code , we just used the [naming container](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.namingcontainer.aspx) as if it were already defined.

#### 建立樣板控制項
```xml
<asp:PlaceHolder ID="PlaceHolder1" runat="server"></asp:PlaceHolder>
```
```c#
using System;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;

namespace C10_Creating_Custom_Web_Controls
{
public partial class Product : System.Web.UI.UserControl
{
//建立一個 ITemplate 樣板屬性
//提供設計者定義真正的 layout

[PersistenceMode(PersistenceMode.InnerProperty)]
[TemplateContainer(typeof(ProductContainer))]
public ITemplate LayoutTemplate { get; set; }

//建立可被樣板中的 Container 物件存取的屬性

public string ProductId { get; set; }
public string ProductName { get; set; }
public int UnitPrice { get; set; }

public void Page_Init()
{
//clear the controls from the placeholder
PlaceHolder1.Controls.Clear();

if (LayoutTemplate != null)
{
//在內嵌樣板內定義子控制項
ProductContainer product = new ProductContainer(ProductId, ProductName, UnitPrice);
LayoutTemplate.InstantiateIn(product);

//add the controls to the placeholder
PlaceHolder1.Controls.Add(product);
}
else
{
PlaceHolder1.Controls.Add(new LiteralControl("No Template Defined"));
}
}
}
}
```

#### 建立具名容器
```c#
namespace C10_Creating_Custom_Web_Controls
{
/// <summary>
/// 建立具名容器
/// 這個類別用來定義樣板控制項中會用到的資料，
/// 當使用者使用樣板控制項時，就可以與這些資料繫結
/// </summary>
public class ProductContainer : Control, INamingContainer
{
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
}
```

## Using a Templated User Control

Like any user control, a templated user control must be used within the same project   and can be added to a page by dragging it from Solution Explorer onto a Web page.   

In the previous example, we using the &lt;LayoutTemplate&gt; tag to define the [ITemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.itemplate.aspx) property.   Inside the template you can reference data by calling the [Container](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.container.aspx) object.   This object is an instance of the [naming container](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.namingcontainer.aspx) class created as part of the templated user control process.   The following markup shows the definition of the user control on a page:  
```xml
<form id="form1" runat="server">
<div>
<uc1:Product ID="Product1" runat="server" ProductId="E0002" ProductName="ASUS A53S" UnitPrice="25000" >
<LayoutTemplate>
<h3>Product ID: <%# Container.ProductId %></h3> 
<table border="1">
<tr><td>Name:</td><td><%# Container.ProductName %></td></tr>
<tr><td>Unit Price:</td><td align="right"><%# Container.UnitPrice.ToString("C") %></td></tr>
</table>                
</LayoutTemplate>
</uc1:Product>
</div>
</form>
```
```c#
protected void Page_Load(object sender, EventArgs e)
{
////bind data to controls
Page.DataBind();
}
```

上面範例，我們將資料直接寫在自訂控制項的屬性裡面，如果要在 code-behind 設定，則必須寫在 Page\_PreInit 事件中。  因為自訂控制項是在它的 Page\_Init 事件中產生樣板資料，而 UserControl.Page\_Init 會比 Page.Page\_Init 還早發生。  所以若要在程式碼中變更資料，就必須寫在 Page.Page\_PreInit 事件中。  
```c#
protected void Page_PreInit(object sender, EventArgs e)
{
Product1.ProductId = "E0002";
Product1.ProductName = "ASUS A53S";
Product1.UnitPrice = 26000;
}
```