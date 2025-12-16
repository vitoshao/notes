---
title: 資料繫結控制項(Hierarchical)
layout: default
parent: 資料繫結控制項
nav_order: 3
description: "資料繫結控制項(Hierarchical)"
date: 2012-11-30
tags: [Web,ASPNET,資料繫結控制項]
postid: "4086353772820221758"
---
The [HierarchicalDataBoundControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.hierarchicaldataboundcontrol.aspx) control serves as a base class for controls that render data in a hierarchical fashion.   The classes that inherit from [HierarchicalDataBoundControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.hierarchicaldataboundcontrol.aspx) are [TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) and [Menu](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.menu.aspx) Control.  

# TreeView

#### TreeView 控制項的功用

- TreeView Control is used to display hierarchical data
- The nodes of this control can be bound to XML, tabular, or relational data.
- nodes can be displayed as either plaintext or hyperlinks
- you can optionally display a check box next to each node.
- you can display a tree hierarchy without being forced to have a single root node.
- The [TreeNode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenode.aspx) has a [Text](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.text.aspx) property that is populated with the data that is to be displayed.
- The [TreeNode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenode.aspx) also has a [Value](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.value.aspx) property that is used to store the data that is posted back to the Web server.
- You can set a node to be a navigation node by setting the [NavigateUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenode.navigateurl.aspx) property.

#### TreeView 控制項的組成元素

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiktfiCle20OiDODeWkLkp7mSooPDO3qo8tQhWMQPnp0VOfue1NSPG1YEFRvbeGjf56NxL9_K3F_fch5vTAkkQpm9cEidaiJJvfdt9uDQFB0UVqd1sL1P8TfwErrkxFXujUlnMoK1nUtrs/s497/ctl-TreeView-Parts.png)

## Populating the TreeView Control

要建構 [TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) 資料，有二種方式：（１）使用靜態資料　（２）透過資料繫結。

### Populating the TreeView control with **static data**

使用靜態資料，只要在 &lt;Nodes&gt; 標籤中加入 &lt;asp:TreeNode&gt; 項目即可。  
```xml
<asp:TreeView ID="TreeView2" runat="server" ShowLines="True" >
<Nodes>
<asp:TreeNode Text="1" Value="1">
<asp:TreeNode Text="1.1" Value="1.1">
<asp:TreeNode Text="1.1.1" Value="1.1.1"></asp:TreeNode>
<asp:TreeNode Text="1.1.2" Value="1.1.2"></asp:TreeNode>
</asp:TreeNode>
<asp:TreeNode Text="1.2" Value="1.2">
<asp:TreeNode Text="1.2.1" Value="1.2.1"></asp:TreeNode>
<asp:TreeNode Text="1.2.2" Value="1.2.2">
<asp:TreeNode Text="1.2.2.1" Value="1.2.2.1"></asp:TreeNode>
</asp:TreeNode>
<asp:TreeNode Text="1.2.3" Value="1.2.3"></asp:TreeNode>
</asp:TreeNode>
</asp:TreeNode>
</Nodes>
</asp:TreeView>
```

### Populating the TreeView control with **data binding**

[TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) 控制項是由 [HierarchicalDataBoundControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.hierarchicaldataboundcontrol.aspx) 控制項繼承而來，所以要透過資料繫結的方式來建構 [TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) 的話，則必須使用有實作 [IHierarchicalDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.ihierarchicaldatasource.aspx) 介面的資料來源。  例如 [XmlDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.aspx) 和 [SiteMapDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.aspx) 控制項，它與一般資料來源的差別就在於其資料結構是階層式的，而不是表格式(tabular)或清單式(list-style)。  

####  [TreeNodeBinding](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenodebinding.aspx) 屬性：

- [DataMember](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.datamember.aspx) ：設定資料來源的資料項目名稱，以決定是否要繫結到 [TreeNode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenode.aspx) 。
- [TextField](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenodebinding.textfield.aspx) ：設定資料來源的欄位，以繫結到 [TreeNode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenode.aspx) 的 [Text](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.text.aspx) 欄位
- [ValueField](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenodebinding.valuefield.aspx) ：設定資料來源的欄位，以繫結到 [TreeNode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenode.aspx) 的 [Value](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.value.aspx) 欄位
- [Text](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenodebinding.text.aspx) ：設定資料來源的欄位，以套用到節點的顯示文字。若和 [TextField](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenodebinding.textfield.aspx) 同時設定，會以 [TextField](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenodebinding.textfield.aspx) 的設定為主。
- [Value](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenodebinding.value.aspx) ：設定資料來源的欄位，以套用到節點的值，該資訊不會顯示出來，但可由 postback 後取得。

[範例請參考這裡](http://vito-note.blogspot.com/2013/03/treeview.html)

# Menu

- The [Menu](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.menu.aspx) control is a data-bound control that is used to display hierarchical data in the form of a menu system.
- The [Menu](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.menu.aspx) control is often used in combination with a [SiteMapDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.aspx) control for navigating a Web site.
- The [Menu](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.menu.aspx) control can be populated using static data or by data binding to the control.

#### Populating the [TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) control with **static data**

使用靜態資料，只要在 &lt;Items&gt; 標籤中加入 &lt;asp:MenuItem&gt; 項目即可。  
```xml
<asp:Menu ID="Menu2" runat="server">
<Items>
<asp:MenuItem Text="新增項目" Value="新增項目">
<asp:MenuItem Text="新增項目" Value="新增項目">
<asp:MenuItem Text="新增項目" Value="新增項目"></asp:MenuItem>
<asp:MenuItem Text="新增項目" Value="新增項目"></asp:MenuItem>
</asp:MenuItem>
</asp:MenuItem>
<asp:MenuItem Text="新增項目" Value="新增項目">
<asp:MenuItem Text="新增項目" Value="新增項目"></asp:MenuItem>
</asp:MenuItem>
<asp:MenuItem Text="新增項目" Value="新增項目">
<asp:MenuItem Text="新增項目" Value="新增項目"></asp:MenuItem>
</asp:MenuItem>
</Items>
</asp:Menu>
```

也可以透過程式碼，動態加入，如下示範。  
```c#
Menu3.Items.Add(new MenuItem("A", "A"));
Menu3.Items.Add(new MenuItem("B", "B"));
Menu3.Items.Add(new MenuItem("C", "C"));

Menu3.Items[0].ChildItems.Add(new MenuItem("A1", "A1"));
Menu3.Items[0].ChildItems.Add(new MenuItem("A2", "A2"));

Menu3.Items[2].ChildItems.Add(new MenuItem("C1", "C1"));
Menu3.Items[2].ChildItems.Add(new MenuItem("C2", "C2"));
```

#### Populating the [TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) control with **data binding**

To use data binding to populate the [Menu](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.menu.aspx) control,   you can use any data source that implements the **IHierarchicalDataSource** interface,   such as an [XmlDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.aspx) control or a [SiteMapDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.aspx) control.  
```xml
<?xml version="1.0" encoding="utf-8" ?>
<MenuItems> 
<Home display="Home" url="~/" /> 
<Products display="Products" url="~/products/">
<SmallWidgets display="Small Widgets" url="~/products/smallwidgets.aspx" />
<MediumWidgets display="Medium Widgets" url="~/products/mediumwidgets.aspx" />
<BigWidgets display="Big Widgets" url="~/products/bigwidgets.aspx" />
</Products>
<Support display="Support" url="~/Support/">
<Downloads display="Downloads" url="~/support/downloads.aspx" />
<FAQs display="FAQs" url="~/support/faqs.aspx" />
</Support>
<AboutUs display="About Us" url="~/aboutus/">
<Company display="Company" url="~/aboutus/company.aspx" /> 
<Locations display="Location" url="~/aboutus/locations.aspx" />
</AboutUs> 
</MenuItems>
```
```xml
<asp:XmlDataSource ID="XmlDataSource2" runat="server"  
DataFile="~/_Uploads/MenuItems.xml"
XPath="/MenuItems/\*">
</asp:XmlDataSource>

<asp:Menu ID="Menu1" runat="server" 
DataSourceID="XmlDataSource2" 
OnMenuItemClick="Menu1_MenuItemClick">
<DataBindings>
<asp:MenuItemBinding DataMember="Products" NavigateUrlField="url" TextField="display" />
<asp:MenuItemBinding DataMember="Support" NavigateUrlField="url" TextField="display" />
<asp:MenuItemBinding DataMember="AboutUs" NavigateUrlField="url" TextField="display" />
</DataBindings>
</asp:Menu>
```
```c#
protected void Menu1_MenuItemClick(object sender, MenuEventArgs e)
{
//取得 Clicked Item 的相關資料
MenuItem menuitem = e.Item;

myMessage.ShowAjax(this, 
"You clicked menu \r\n"
+ "Text = " + menuitem.Text + "\r\n"
+ "Path = " + menuitem.ValuePath + "\r\n"
+ "Url = " + menuitem.NavigateUrl + "\r\n"
+ "Level = " + menuitem.Depth + "\r\n" 
);
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvLgrFsXokhGDtrKLm_8AgJtTrMY-YQszEkJU7tVY7f93VkfhsaRk507jpuzXSX5-60mIYDyybX7nbo5eFO3t0UoNpwqoBQsjB2gnCOUpwNgi_d9Xwnffzk99WvQuzcO-ivnSFhG_9cwU/s180/ctl-Menu.png)