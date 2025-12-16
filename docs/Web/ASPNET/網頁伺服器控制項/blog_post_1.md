---
title: 認識 Server Controls
layout: default
parent: 網頁伺服器控制項
nav_order: 1
description: "認識 Server Controls"
date: 2012-10-26
tags: [Web,ASPNET,網頁伺服器控制項]
postid: "3142024964228662037"
---
Web server controls provide more functionality and a more consistent programming model than HTML server controls.  Web server controls are based on .NET Framework classes, typically inherited from the WebControl class.  ASP.NET renders web server controls as standardized HTML.   In other words, if you use the Button web server control, ASP.NET renders it using the &lt;button&gt; HTML markup tag.  

# Life Cycle of an ASP.NET Web Page and Its Controls

#### Life Cycle of Page Processing

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhL4wyvfW_ZbdR53iQ4Gqy38NdDScMkNu9DR2qOFus3UAkoDSKsU7UNVeWWWBZ18KT2DCbbXRMFIpDYysaBDZYR8ypzvA1ZEq0qoM79kFY2RXaLDo5rBx-bEMZuSydOAyW9XtjrdcrBrwg/s588/life-cycle-of-page.png)

#### Life Cycle of Page Events
&lt;!--#include file="../C01_ASPNET_WebSite/page-life-cycle.htm" --&gt;

# HTML vs. Web Server Controls

ASP.NET allows you to define actual HTML controls inside your page.   By default, these HTML tags are not connected to any server control.   Therefore, it can be difficult to use them from the server in that there are no instances of related controls with properties and methods.   Instead, you can simply access the tag values through the Page.Request.Form collection.   This model is fine for very simple pages and was the only model for classic ASP.  

Alternatively, you can apply the attribute **runat="server"** to these control tags.   In this case, ASP.NET will wire up the HTML tag to a related, server-side object that provides properties and methods designed to work with the given tag.   This can make your programming experience much easier.   In addition, ASP.NET automatically maintains state for these items between calls for you (recall the view state discussion).

# HTML Server Controls

## Creating HTML Server Controls

Add the **runat="server"** attribute to the Html Control
```xml
<body>
<form id="form1" runat="server">
<div>
<input type="text" name="CustomerName" id="CustomerName" runat="server" />
<input type="submit" id="HtmlSubmitButton" value="Html SubmitButton"  runat="server"  
onclick="return Sure()" 
onserverclick="HtmlSubmitButton_Click" 
/>
</div>
</form>
</body>
```

## Setting HTML Server Control Properties

#### Setting Properties in Source View
```xml
<input type="button" id="myButton" runat="server" value="Click Me"
style="position: absolute; top: 50px; left: 100px; width: 100px;" />
```

#### Setting Properties in Design View

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9q17H8aGE4R2cd1TaEK7d1Ti03RV-SKbD7onCzTfGYTrk92ilcqG60E-HadnyxF-CzCAT4hSsR-X4d9-8CK4-wxxm6f8ryNmZTW24n7wSHK1QglgCgokjd8PmBL78Ei41kJQf8-cYPmA/s581/set-property-in-design-view.png)

#### Setting Properties in Code
```c#
myButton.Disabled = true;
myButton.Style.Add("background-color", "Black");
myButton.Style.Add("color", "Gray");
```

# Web Server Controls

Web server controls are able to provide more functionality because they are not tied to a single HTML tag element.   Instead, they typically render many HTML tags and may also include client-side JavaScript code.   Web server controls also have the ability to detect the Web browser's capabilities and render the appropriate HTML based on those capabilities.  

## Creating Web Server Controls

#### Adding Web Server Controls Using Design View

略

#### Adding Web Server Controls Using Source View
```xml
<asp:TextBox ID="TextBox1" runat="server"></asp:TextBox>
<asp:Button ID="WebSubmitButton" runat="server" Text="Web SubmitButton" 
OnClientClick="return Sure()"
OnClick="WebSubmitButton_Click" />
```

#### Adding Web Server Controls Dynamically Using Code

Recall that the **PreInit** event is typically used to create dynamic controls prior to their initialization inside the **Init** event.  
```c#
protected void Page_PreInit(object sender, EventArgs e)
{
TextBox textBoxUserName = new TextBox();
textBoxUserName.ID = "TextBoxUserName";
form1.Controls.Add(textBoxUserName);
}

protected void Button1_Click(object sender, EventArgs e)
{
TextBox textBoxUserName = (TextBox)form1.FindControl("TextBoxUserName");
myMessage.Show(this, textBoxUserName.Text);
}
```

## Setting Web Server Control Properties

#### Setting Properties in Source View
```xml
<asp:Button ID="ButtonSave" runat="server" Text="Save"
Style="position: absolute; top: 50px; left: 100px; width: 100px;" />
```

#### Setting Properties in Design View

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg05btmZiXFSHMGwkJEillM5h5lHCXd5Eo_K2VAfMKz6wVzFvTEAJLKc5dE-UWejaYGgpC6Fm7FrwOnWCyoKyOcGC_T9a5JtOX7W2-PyZ2XQSUWz6qotz8qhYj6NeaWJYLKv7W3q_VtB5E/s586/set-property-in-design-view-webcontrol.png)

#### Setting Properties in Code

Web server control properties can be set programmatically in the code-behind files of a Web page.   Like all server controls, you reference the Web server controls by an instance variable named after the control's ID property.   The following code was added to a Page\_Load event handler.   It sets the ***Style***, ***Text***, and ***Enabled*** properties of the button that has the ID of ButtonSave.  
```c#
ButtonSave.Enabled = false;
ButtonSave.Text = "Click to Save";
ButtonSave.Style.Add("background-color", "Blue");
```

## Upgrading from Earlier Versions of ASP.NET

通常 .NET 會將 Web Server Control 轉譯成最新的 Html 標準，所以，不同版本的 .Net ，在轉譯伺服器控制項時，有可能會發生不一樣的狀況。  你也可以使用 ControlRenderingCompatibilityVersion 屬性，來指定轉譯 HTML 相容的 ASP.NET 版本。  
```xml
<system.web>
<pages controlRenderingCompatibilityVersion="3.5"/>
</system.web>
```

## Controlling Automatic PostBack

Some Web server controls always cause a ***PostBack***  event when a specific event occurs.   For example, the Button control's ***Click*** event.  
Some Web server controls do **not**, for example, the TextBox control's ***TextChanged*** event.  If required, you can set the ***AutoPostBack*** property as "True" to causes an automatic ***PostBack*** to the server.  
```xml
<asp:TextBox ID="TextBox1" runat="server" AutoPostBack="true"></asp:TextBox>
```

## Working with Naming Containers and Child Controls

#### Searching for Controls
```c#
Control c = FindControl("lblMessage");
```

#### Searching for child controls or controls created dynamically
```c#
Control c = GridView1.FindControl("ctl08");
```