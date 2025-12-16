---
title: 通用 Server Controls
layout: default
parent: 網頁伺服器控制項
nav_order: 2
description: "通用 Server Controls"
date: 2012-10-26
tags: [Web,ASPNET,網頁伺服器控制項]
postid: "1397899848218542620"
---
通用型(common)的伺服器控制項，指的是 [Label](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.label.aspx) 、 [TextBox](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.textbox.aspx) 、 [Button](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.button.aspx) 、 [CheckBox](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkbox.aspx) 、 [RadioButton](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.radiobutton.aspx) 這類較基本功能的控制項。  

# The Label Control

這個控制項比較沒什麼好講的，值得一提的是，若由後置程式碼填資料到 [Label](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.label.aspx) 控制項，必項注意安全性問題。  因為它可能引發 XSS 資安弱點。  若要避免這個問題，可以使用 [HttpUtility.HtmlEncode](http://msdn.microsoft.com/zh-tw/library/system.web.httputility.htmlencode.aspx)   或 [Server.HtmlEncode](http://msdn.microsoft.com/zh-tw/library/system.web.httpserverutility.htmlencode.aspx) 方法，在資料填入前先行編碼。  
```c#
string script = @"<script>alert('test1');</script>";
Label1.Text = HttpUtility.HtmlEncode(script);

//碥碼後的文字： &amp;lt;script&amp;gt;alert(&amp;#39;test&amp;#39;);&amp;lt;/script&amp;gt;
```

# The TextBox Control

- [TextMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.textbox.textmode.aspx) ：設定的行為模式 (SingleLine / MultiLine / Password)。
- [MaxLength](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.textbox.maxlength.aspx) ：設定文字方塊中所允許的字元數目上限。
- [Wrap](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.textbox.wrap.aspx) ：設定多行文字方塊中的文字內容是否換行。(default=true)。

# The Button Control

A [Button](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.button.aspx) control can also be used as a command button,   which is one of a set of buttons that work together as a group, such as a toolbar.  You define a button as a command button by assigning a value to its [CommandName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.button.commandname.aspx) property.   When a user clicks one of the command buttons, its [Command](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.button.command.aspx) event is called on the server.   This event is passed an instance of [CommandEventArgs](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.commandeventargs.aspx) as a parameter.  
```xml
<asp:Button ID="Button4" runat="server" Text="<<" CommandName="previous"  oncommand="Play_Command" />
<asp:Button ID="Button5" runat="server" Text="●" CommandName="play"  oncommand="Play_Command" />
<asp:Button ID="Button6" runat="server" Text=">>" CommandName="next"  oncommand="Play_Command" />
```
```c#
protected void Play_Command(object sender, CommandEventArgs e)
{
switch (e.CommandName)
{
case "previous":
myMessage.Show(this, "Previous");
break;
case "play":
myMessage.Show(this, "Play");
break;
case "next":
myMessage.Show(this, "Next");
break;
}
}
```

# The CheckBox Control

The [CheckBox](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkbox.aspx) control gives the user the ability to select between true and false.

- [Text](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkbox.text.aspx) ：
- [TextAlign](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkbox.textalign.aspx) ：
- [Checked](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkbox.checked.aspx) ：
- [AutoPostBack](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkbox.autopostback.aspx) ：是否自動回傳。
- [CheckedChanged](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkbox.checkedchanged.aspx) ：Event 發生於 [Checked](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkbox.checked.aspx) 屬性的值變更時。  
若 [AutoPostBack](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkbox.autopostback.aspx) = false ，這個 [CheckedChanged](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkbox.checkedchanged.aspx) 事件就不會引發 PostBack ；      但是若其它控制項發生 PostBack 事件時， CheckChanged 事件還是會被執行。
```xml
<asp:CheckBox ID="CheckBox1" runat="server"
Text="是/否" 
TextAlign="Left" 
Checked="true"
AutoPostBack="false" 
onCheckedChanged="CheckBox1_CheckedChanged" 
/>
```

# The RadioButton Control

The [RadioButton](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.radiobutton.aspx) control gives the user the ability to select between mutually exclusive [RadioButton](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.radiobutton.aspx) controls in a group.   This is useful when you are asking a user to select a single item from a group of items.   To group multiple [RadioButton](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.radiobutton.aspx) controls together, specify the same [GroupName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.radiobutton.groupname.aspx) for each [RadioButton](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.radiobutton.aspx) control in the group.  
```xml
<asp:RadioButton ID="RadioButton1" runat="server" GroupName="Fruit" Text="Apple" /><br />
<asp:RadioButton ID="RadioButton2" runat="server" GroupName="Fruit" Text="Melon" /><br />
<asp:RadioButton ID="RadioButton3" runat="server" GroupName="Fruit" 
Text="Tangerine" 
TextAlign="Right" 
Checked="true"
AutoPostBack="true" 
oncheckedchanged="RadioButton1_CheckedChanged"
/>
```

# ListBox

# DropDownList

# CheckBoxList

# BulletedList

# RadioButtonList

# AdRotator