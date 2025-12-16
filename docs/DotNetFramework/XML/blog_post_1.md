---
title: XML Menu
layout: default
parent: XML
nav_order: 1
description: "XML Menu"
date: 2012-03-08
tags: [DotNetFramework,XML]
postid: "4513334152612903589"
---
在設計視窗程式時，常常會使用到 MenuStrip 控制項來設計功能選單。  不過有時候選單項目無法在設計階段就決定，必須等到執行階段才能決定，這時候就必須利用到動態載入選單的技巧。  

這篇文章，將使用 XML 文件來規範選單的內容，再透過反射的技巧，開啟要執行的表單。  

# 建立一個 Windows Application 專案

這個專案包含以下幾個部份：

1. 新增一個「Windows Form 應用程式」專案，取名為 WindowsFormsApplication1。
2. 加入一個「Windows Form」,取名為 MainForm，並將表單的 IsMdiContainer 屬性設成 true ，同時將該表單設定為應用程式的啟動表單。
3. 加入一個 XML 檔案，取名為 XMLFile1.xml。
4. 最後加入４個「Windows Form」，分別取名為 frmA1、frmA2、frmB1、frmB2 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgC-V9IQuOCz5cA-wupmZadr4hxodawZcdjK98DD6LR6vda4EBtYM4x9f7wV5e1U61Og9N-EUNfck0KtPFS-JNJU7UOjboMQVH5_E0c8oco4h5NYdgU954W96J7b7b3DEGjUxn4z4709kg/s256/xml-menu-1.png)
```c#
static class Program
{
/// <summary>
/// 應用程式的主要進入點。
/// </summary>
[STAThread]
static void Main()
{
Application.EnableVisualStyles();
Application.SetCompatibleTextRenderingDefault(false);
Application.Run(new MainForm());
}
}
```

# 定義 XML 文件的格式

在 XML 檔案中，鍵入你要載入的內容，如下面範例。其中項目的屬性定義如下：  

- id：選單識別碼。
- text：選單的顯示文字。
- tip：選單的tip文字。
- form：選單點選時，要開啟的表單名稱，必須是完整的名稱，也就是包含命名空間的描述。
```xml
<?xml version="1.0" encoding="utf-8" ?>
<MainMenu>
<MenuItem id="mGroupA" text="表單群組A" tip="表單群組A">
<MenuItem id="mFormA1" text="表單A1" tip="表單A1" form="WindowsFormsApplication1.frmA1" ></MenuItem>
<MenuItem id="mFormA2" text="表單A2" tip="表單A2" form="WindowsFormsApplication1.frmA2" ></MenuItem>
</MenuItem>
<MenuItem id="mGroupB" text="表單群組B" tip="表單群組B">
<MenuItem id="mFormB1" text="表單B1" tip="表單B1" form="WindowsFormsApplication1.frmB1" ></MenuItem>
<MenuItem id="mFormB2" text="表單B2" tip="表單B2" form="WindowsFormsApplication1.frmB2" ></MenuItem>
</MenuItem>
</MainMenu>
```

# 動態建立表單

接下來，我們在 MainFrom 中加入一個 MenuStrip 控制項（取名：MainMenu），用來建立相關的選單內容。程式碼如下：  

這裡要特別注意的，程式碼會使用到 System.Xml 和 System.Reflection 命名空間。
```c#
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;
using System.Xml;
using System.Reflection;

namespace WindowsFormsApplication1
{
public partial class MainForm : Form
{
public MainForm()
{
InitializeComponent();
LoadMenu(@"..\..\XMLFile1.xml");
}

private void Form1_Load(object sender, EventArgs e)
{
}

private void LoadMenu(string fileName)
{
XmlReaderSettings setting = new XmlReaderSettings();
setting.IgnoreComments = true;
setting.IgnoreWhitespace = true;
setting.ValidationType = ValidationType.None;
try
{
XmlDocument xmlDoc = new XmlDocument();
xmlDoc.Load(fileName);
foreach (XmlNode node in xmlDoc.DocumentElement.ChildNodes)
{
CreateMenuItem(node, null);
}
}
catch (XmlException xmlex)
{
throw xmlex;
}
}

private void CreateMenuItem(XmlNode node, ToolStripMenuItem PMenuItem)
{
ToolStripMenuItem MenuItem = new ToolStripMenuItem();
MenuItem.TextAlign = ContentAlignment.MiddleLeft;
foreach (XmlAttribute att in node.Attributes)
{
switch (att.Name)
{
case "id":
MenuItem.Name = att.Value;
break;
case "text":
MenuItem.Text = att.Value;
break;
case "tip":
MenuItem.ToolTipText = att.Value;
break;
case "form":
MenuItem.Tag = att.Value;  //利用ToolStripMenuItem的tag屬性,暫存表單名稱
break;
}
}
if (PMenuItem == null)
MainMenu.Items.Add(MenuItem);
else
PMenuItem.DropDownItems.Add(MenuItem);

if (MenuItem.Tag != null)
{
//註冊Click事件
MenuItem.Click += new EventHandler(MenuItem_Click);
}

foreach (XmlNode child_node in node.ChildNodes)
{
CreateMenu(child_node, MenuItem);
}
}

private void MenuItem_Click(object sender, EventArgs e)
{
string FormName = ((ToolStripMenuItem)sender).Tag.ToString();
Form childForm = ((Form)Assembly.GetExecutingAssembly().CreateInstance(FormName));

if (childForm != null)
{
childForm.MdiParent = this;
childForm.WindowState = FormWindowState.Maximized;
childForm.Show();
}
else
{
MessageBox.Show("該視窗不存在！");
}
}
}
}
```

這個範例中，我們透過反射（reflection）技巧來動態取得表單的類別。  而要取得表單的類別，則必須先取得含有該類別的組件，所以我們可以使用 [Assembly.GetExecutingAssembly](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.getexecutingassembly.aspx) 方法，以取得含有該表單的組件。  

底下是執行的結果，這個表單，會依據 XML 內容建立功能選單。  每個功能選單，在點繫後也會開啟在 XML 中設定的表單。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjsnxUKot_h0CAGUFbTiMv7cub6AjhhgNgCkmclIxqWzNPm06-Ldfg8BPDGgBs4kKdhSy0Mgl_kaHPVPKl097PP4CaxLS4i6sFUyHufOh6F2khUbvSlZNm2iCw-eda3oe-2c7XMvDcgHkY/s334/xml-menu-2.png)

# 建立一個 XmlMenu 自訂控制項

為了日後方便使用，我們也可以將上面功能，打包到一個自訂控制項，讓其他專案可以直接加入參考使用。操作步驟如下：

1. 新增一個「類別庫」專案。（MyControls）
2. 在類別庫專案中，加入「自訂控制項」項目。（XmlMenu.cs）。這個自訂控制項，繼承 MenuStrip 類別。  程式碼如下：  ```c#
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Windows.Forms;
using System.Xml;
using System.Reflection;

namespace MyControls
{
public partial class XmlMenu : MenuStrip
{
public XmlMenu()
{
InitializeComponent();
}

protected override void OnPaint(PaintEventArgs pe)
{
base.OnPaint(pe);
}

public  void LoadMenu(string fileName)
{
XmlReaderSettings setting = new XmlReaderSettings();
setting.IgnoreComments = true;
setting.IgnoreWhitespace = true;
setting.ValidationType = ValidationType.None;
try
{
XmlDocument xmlDoc = new XmlDocument();
xmlDoc.Load(fileName);
foreach (XmlNode node in xmlDoc.DocumentElement.ChildNodes)
{
CreateMenuItem(node, null);
}
}
catch (XmlException xmlex)
{
throw xmlex;
}
}

private void CreateMenuItem(XmlNode node, ToolStripMenuItem PMenuItem)
{
ToolStripMenuItem MenuItem = new ToolStripMenuItem();
MenuItem.TextAlign = ContentAlignment.MiddleLeft;
foreach (XmlAttribute att in node.Attributes)
{
switch (att.Name)
{
case "id":
MenuItem.Name = att.Value;
break;
case "text":
MenuItem.Text = att.Value;
break;
case "tip":
MenuItem.ToolTipText = att.Value;
break;
case "form":
MenuItem.Tag = att.Value;  //利用ToolStripMenuItem的tag屬性,暫存表單名稱
break;
}
}
if (PMenuItem == null)
this.Items.Add(MenuItem);
else
PMenuItem.DropDownItems.Add(MenuItem);

if (MenuItem.Tag != null)
{
//註冊Click事件
MenuItem.Click += new EventHandler(MenuItem_Click);
}

foreach (XmlNode child_node in node.ChildNodes)
{
CreateMenuItem(child_node, MenuItem);
}
}

private void MenuItem_Click(object sender, EventArgs e)
{
string FormName = ((ToolStripMenuItem)sender).Tag.ToString();
Form childForm = ((Form)Assembly.GetEntryAssembly().CreateInstance(FormName));

if (childForm != null)
{
childForm.MdiParent = (Form)this.Parent;
childForm.WindowState = FormWindowState.Maximized;
childForm.Show();
}
else
{
MessageBox.Show("該視窗不存在！");
}
}
}
}
```

上面程式碼與先前的程式碼有少許的不同，其中有幾行程式碼補充說明如下：

1. 行14：這個自訂控制項繼承 MenuStrip 類別。
2. 行26：LoadMenu 方法必須是 public ，因為會由外部叫用。
3. 行70：由於這個自訂控制項，本身就是一個 MenuStrip ，所以用 this 來表示自已。
4. 行89：  在先前的範例中，我們使用 [Assembly.GetExecutingAssembly](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.getexecutingassembly.aspx) 方法取得含有該表單的組件，  那時候是因為反射程式碼和 MDI 表單是在同一個組件。不過，在現在的這個類別庫專案中，因為這些程式碼與應用程式是位於不相同的組件，  所以必須使用 [Assembly.GetEntryAssembly](http://msdn.microsoft.com/zh-tw/library/system.reflection.assembly.getentryassembly.aspx) 方法，才可以取得叫用這個類別庫的組件。
5. 行93：子表單的 Parent 改成和 xmlMenu 控制項的 Parent 相同。
3. 使用時，你只要在應用程式中加入類別庫的參考，然後在 MDI 表單中，加入 XmlMenu 自訂控制項，最後在表單的 Form\_Load 事件中，叫用 XmlMenu.LoadMenu() 方法，並傳入 XML 檔案路徑即可。  ```c#
namespace WindowsFormsApplication1
{
public partial class MainForm : Form
{
public MainForm()
{
InitializeComponent();
}

private void Form1_Load(object sender, EventArgs e)
{
xmlMenu1.LoadMenu(@"..\..\XMLFile1.xml");
}
}
}
```