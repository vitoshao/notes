---
title: 資料繫結控制項(Simple)
layout: default
parent: 資料繫結控制項
nav_order: 1
description: "資料繫結控制項(Simple)"
date: 2012-10-26
tags: [Web,ASPNET,資料繫結控制項]
postid: "2038442633524045314"
---
簡單的資料繫結控制項包含： [DropDownList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.dropdownlist.aspx) 、 [ListBox](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listbox.aspx) 、 [CheckBoxList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkboxlist.aspx) 、 [RadioButtonList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.radiobuttonlist.aspx) 、 [BulletedList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.bulletedlist.aspx) 

# Introducing Data-Bound Controls

## Introducing

1. Simple data-bound controls ： the controls that inherit from the [ListControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.aspx)
2. Composite data-bound controls ： the controls that inherit from the [CompositeDataBoundControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.compositedataboundcontrol.aspx) .
3. Hierarchical data-bound controls ： the controls that inherit from the [HierarchicalDataBoundControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.hierarchicaldataboundcontrol.aspx) , such as [Menu](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.menu.aspx) and [TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) controls.

只要是實作 [IEnumerable](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable.aspx) 、 [IListSource](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.ilistsource.aspx) 、 [IDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.idatasource.aspx) 、 [IHierarchicalDatasource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.ihierarchicaldatasource.aspx) 的資料都可以繫結(bind)到資料繫結控制項(data-bound web control)。  資料繫結控制項在執行階段會自動連結到資料來源，並執行 DataBind 方法，將資料載入到控制項上。
&lt;!-- more --&gt;    
## Mapping Fields to Templates

A template control is a control that has no default UI. The control simply provides the mechanism for binding to data.  The developer supplies the UI in the form of inline templates.  The template can contain declarative elements such as HTML and DHTML.  The template can also contain ASP.NET data-binding syntax to insert data from the data source.  

支援樣板的繫結控制項包括 [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) 、 [DetailsView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.detailsview.aspx) 、 [FormView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.formview.aspx) 控制項。  這類控制項大都提供以下樣板設計：  

- HeaderTemplate ：rendered at the top of the control.
- FooterTemplate ：rendered at the bottom of the control.
- [ItemTemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.templatefield.itemtemplate.aspx) ：rendered for each row in the data source.
- AlternatingItemTemplate ：alternating item template;.
- SelectedItemTemplate ：render a row that has been selected.
- SeparatorTemplate ：separator template that defines the separation of each item and alternate item.
- EditItemTemplate ：render a row that is in edit mode.

## Using the DataBinder Class

The [DataBinder](http://msdn.microsoft.com/zh-tw/library/system.web.ui.databinder.aspx) class can be used to define code inside your script that controls how a given data source is bound.  It provides the static [Eval](http://msdn.microsoft.com/zh-tw/library/system.web.ui.databinder.eval.aspx) method to parse data-binding expression syntax.  

#### Eval

Eval method uses reflection to perform a lookup of a DataItem property's underlying type by looking at the type metadata that is stored in the type's assembly.   After the metadata is retrieved, the [Eval](http://msdn.microsoft.com/zh-tw/library/system.web.ui.databinder.eval.aspx) method determines how to connect to the given field.   This makes writing data binding syntax on your page an easy task. For example, the following shows binding to the Vin property of a Car object:  
```xml
<%# Eval("Vin") %>
```

The [Eval](http://msdn.microsoft.com/zh-tw/library/system.web.ui.databinder.eval.aspx) method also provides format string : 
```xml
<%# Eval("Price", "{0:C}") %>
```

#### Bind

The [Eval](http://msdn.microsoft.com/zh-tw/library/system.web.ui.databinder.eval.aspx) method is great for one-way (or read-only) data binding. However, it does not support read-write data binding and thus cannot be used for insert and edit scenarios.   The **Bind** method of the [DataBinder](http://msdn.microsoft.com/zh-tw/library/system.web.ui.databinder.aspx) class, however, can be used for two-way data binding.   This makes **Bind** desirable when editing or inserting records.  
```xml
<asp:TextBox ID="txtTextBox" RunAt="Server" Text='<%# Bind("FirstName") %>' />
```

Imagine for example a [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) with a [ItemTemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.templatefield.itemtemplate.aspx) and EditItemTemplate.   If you use **Bind** or [Eval](http://msdn.microsoft.com/zh-tw/library/system.web.ui.databinder.eval.aspx) in the ItemTemplate, there will be no difference.   If you use [Eval](http://msdn.microsoft.com/zh-tw/library/system.web.ui.databinder.eval.aspx) in the EditItemTemplate, the value will not be able to be passed to the Update method of the [DataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.datasource.aspx) that the grid is bound to.

## Hierarchy of Data-Bound Controls

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgN9VsZvS4426U1hKTgXf5Vuj-VfDASIx3GRbb3sTL7qLVnzXb5lZ52DwoxeuRJ7xk1U4MN5AVdroXfNO9rqbjP7x6kfJ8RHobzmF9OF0DCRY98MeCM_-xPkb0ih1KyXfeXoIZnv_Fy88g/s850/data-bound-controls.png)

# Simple Data-Bound Controls

這一類型的繫結控制項都是繼承 [ListControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.aspx) 這個抽象的類別。  
[ListControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.aspx) 類別包含一個 [Items](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.items.aspx) 集合屬性，存放項目集合 (集合由 [ListItem](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.aspx) 組成)。  
每個 [ListItem](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.aspx) 包含一個顯示用的 [Text](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.text.aspx) 屬性，和一個 postback 到主機端的 [Value](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.value.aspx) 屬性。  

集合中的項目可以透過 Add 方法加入，也可以繫結到資料來源取得。  要繫結到資料來源必須指定 [DataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.datasource.aspx) 屬性，若資料來源包含一個以上的表格，則必須再使用 [DataMember](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.datamember.aspx) 屬性指明繫結的表格。  

#### ListControl 屬性

- [Items](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.items.aspx) ：取得清單控制項中項目的集合。   
集合項目由 [ListItem](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.aspx) 物件組成，該物件包含以下屬性：  

- [Text](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.text.aspx) ：顯示用。
- [Value](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.value.aspx) ：回傳用。
- [DataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.datasource.aspx) ：設定繫結的資料來源。
- [DataMember](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.datamember.aspx) ：如果資料來源包含多個不同資料項目清單，必須指明資料清單名稱。
- [SelectedIndex](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.selectedindex.aspx) ：get or set the index of the selected item
- [SelectedItem](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.selecteditem.aspx) ：access the selected ListItem
- [SelectedValue](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.selectedvalue.aspx) ：access the value of the selected ListItem
- [AppendDataBoundItems](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.appenddatabounditems.aspx)：是否在資料繫結之前清除清單項目。
- True ：keep all items that are currently in the [ListControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.aspx) in addition to appending the items from the data binding.
- False ：clears the [Items](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.items.aspx) property prior to binding the data.
- [SelectedIndexChanged](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.selectedindexchanged.aspx) ：which is raised when the selection in the list control changes between posts to the server.   
set a control's [AutoPostback](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.autopostback.aspx) property to True if you intend that it post back to the server

# DropDownList

The [DropDownList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.dropdownlist.aspx) control is used to display a list of items to users to allow them to make a single selection.  

這個控制項有幾點要注意：

- 其轉譯的 Html 標籤為 &lt;select&gt;
- 若要在 Server 端處理使用者選定資料，可以透過 OnSelectedIndexChanged 屬性指定。
- 若希望使用者一改變選項立刻回傳，必須啟用 AutoPostBack 屬性。
- 若要在 Client 端處理使用者選定資料，可以撰寫物件的 onchange 事件"。
```xml
Select Territory : 
<asp:DropDownList ID="DropDownList1" runat="server" 
DataSourceID = "SqlDataSource1" 
DataTextField = "TerritoryDescription" 
DataValueField = "TerritoryID"
OnSelectedIndexChanged = "DropDownList1_SelectedIndexChanged" 
AutoPostBack = "true">
</asp:DropDownList>
```
```c#
protected void DropDownList1_SelectedIndexChanged(object sender, EventArgs e)
{
sBuilder.Clear();
sBuilder.AppendLine("===DropDownList1===" + System.DateTime.Now.ToString());
sBuilder.AppendLine("Select Text  ：" + DropDownList1.SelectedItem.Text);
sBuilder.AppendLine("Select Value ：" + DropDownList1.SelectedItem.Value);
sBuilder.AppendLine("Select Index ：" + DropDownList1.SelectedIndex);

labMessage.Text += sBuilder.ToString().Replace(Environment.NewLine,"<br>");
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiShhUqf0adaFDwlE4XwbopEiRDXFQJS2IMQkBX0KHfcPHvU-kWf2CJz22YFa7bAro6__EAAlMI4F8wCQBhHMk2RPa_DDuJrZmx6SIvqnX1iVfg9b-VavMk9LW7tsPfNj0bl7jtuRILp3s/s308/ctl-dropdownlist.png)

若要由 Client 端先行處理 Change 事件，再 Postback 回 Servert 端。如果同時設定 OnSelectedIndexChanged 和 Change 事件，則回導至 Postback 失效。此時可以這麼做
```javascript
<script language="javascript" type="text/javascript">
function testFunction(obj) {
var control = document.getElementById(obj.id);
if (control.value == "02116") {
//return false; 
}
else {
//return true; 
__doPostBack("DropDownList1", "OnSelectedIndexChanged");
}
}
</script>

<asp:DropDownList ID="DropDownList1" runat="server"
AutoPostBack="true"
OnSelectedIndexChanged="DropDownList1_SelectedIndexChanged"
onchange = "javascript:testFunction(this);" 
>
</asp:DropDownList>
```

# ListBox

The [ListBox](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listbox.aspx) control is used to display items in a longer list rather than one at a time like the DropDownList.   Users can see more data at a given time. The control can also be configured to allow the selection of a single item or multiple items.  

- [SelectionMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listbox.selectionmode.aspx) ：設定選取模式 {Single | Multiple }。
- [ListItem.Selected](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.selected.aspx) ：判斷是否已選取的項目。
```xml
<asp:ListBox ID="ListBox1" runat="server" 
DataSourceID="SqlDataSource1" 
DataTextField="TerritoryDescription" 
DataValueField="TerritoryID"
SelectionMode="Multiple">
</asp:ListBox>
<asp:Button ID="btnListBoxSubmit" runat="server" Text="Submit" onclick="btnListBoxSubmit_Click" />
```
```c#
protected void btnListBoxSubmit_Click(object sender, EventArgs e)
{
foreach (ListItem i in ListBox1.Items)
{
if (i.Selected)
labMessage.Text = labMessage.Text + "You selected TerritoryID: " + i.Value + "<br />";
}
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgEDjBpgAT1wAqbXuwaBUNxwD0C-UceKlHUBc8qEA3EvyQbVq0NE3EsxofowgmxzSxovvnWDSgSf2DLB__g7XGBX80a0nfOrJkDWW26Rs5LCdN_ECzWPod6q2rQRv7YpysymQPL3SzDck4/s263/ctl-listbox.png)

#### 繫結集合項目範例
```xml
<asp:ListBox ID="ListBox4" runat="server"
SelectionMode="Multiple"
onselectedindexchanged="ListBox4_SelectedIndexChanged"></asp:ListBox>

<asp:Button ID="Button1" runat="server" Text="Button" onclick="Button1_Click" />
<asp:Label ID="Label1" runat="server" Text="Label"></asp:Label>
```
```c#
public partial class WebForm2 : System.Web.UI.Page
{
public WebForm2()
{
this.Init += new EventHandler(BindData);
}

void BindData(object sender, EventArgs e)
{
if (!IsPostBack)
{
ListBox4.DataSource = CultureInfo.GetCultures(CultureTypes.AllCultures);
ListBox4.DataTextField = "DisplayName";
ListBox4.DataValueField = "EnglishName";
ListBox4.DataBind();
}
}

protected void Button1_Click(object sender, EventArgs e)
{
Label1.Text = "";
foreach (ListItem item in ListBox4.Items)
{
if (item.Selected)
{
Label1.Text += item.Value + item.Text + "<br>";
}
}
}
}
```

# CheckBoxList

[CheckBoxList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkboxlist.aspx) 和 [RadioButtonList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.radiobuttonlist.aspx) 是類似的控制項，一個允許多選，一個只能單選。

要取得 [CheckBoxList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkboxlist.aspx) 選取的項目，可以透過 [Items](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.items.aspx) 集合項目。  要取得 [RadioButtonList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.radiobuttonlist.aspx) 選取的項目，直接使用 [SelectedValue](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.selectedvalue.aspx) 即可。  

- [RepeatColumns](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkboxlist.repeatcolumns.aspx) ：Gets or sets the number of columns to display in the [CheckBoxList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkboxlist.aspx) control。
- [RepeatDirection](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkboxlist.repeatdirection.aspx) ：Gets or sets a value that indicates whether the control displays vertically or horizontally.
```xml
<asp:CheckBoxList ID="CheckBoxList1" runat="server" 
DataSourceID    = "SqlDataSource1" 
DataTextField  = "TerritoryDescription" 
DataValueField  = "TerritoryID" 
RepeatColumns   = "5"
RepeatDirection = "Vertical" >
</asp:CheckBoxList>
<asp:Button ID="btnCheckBoxListSubmit" runat="server" Text="Submit" 
onclick="btnCheckBoxListSubmit_Click" />
```
```c#
protected void btnCheckBoxListSubmit_Click(object sender, EventArgs e)
{
foreach (ListItem item in CheckBoxList1.Items)
{
if (item.Selected)
labMessage.Text = labMessage.Text + "You selected TerritoryID: " + item.Value + "<br />";
}
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEilZjhF7Vef1KIFc8Uxh2dJd0MDhRUsLa29Ti-WpPJ2F60D5qom5U3IvFbsF_HoXqboTUzAMADvdFpSk6YzQJuNDUf2RqCo-wDhKn8W_lKzyIKc4mgaORXfS2J_UIMdyEPfj9fpSWbfJbw/s626/ctl-CheckBoxList.png)

# RadioButtonList
```xml
<asp:RadioButtonList ID="RadioButtonList1" runat="server" 
DataSourceID="SqlDataSource1" 
DataTextField="TerritoryDescription" 
DataValueField="TerritoryID" 
RepeatColumns="5"
RepeatDirection="Vertical"
>
</asp:RadioButtonList>
<asp:Button ID="btnRadioButtonListSubmit" runat="server" Text="Submit" 
onclick="btnRadioButtonListSubmit_Click" />
```
```c#
protected void btnRadioButtonListSubmit_Click(object sender, EventArgs e)
{
labMessage.Text = RadioButtonList1.SelectedValue;
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5-KwZ9jPFCip7ZnfKg7NDkaBU1q9zaXcDMxwR2dn6HhYAgc8T8w8RE-6Uy-hU7pVzxPabtY6ztytUkS6zG9iQ4k8jFzaR-07kDfluDWQQu3-8WHkmLfWpipLtiCWTQpZ8MA3j_C1aRQE/s634/ctl-RadioButtonList.png)

# BulletedList

The [BulletedList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.bulletedlist.aspx) control displays an unordered or ordered list of items that renders as HTML ul or ol elements, respectively.  This control renders as either bullets or numbers based on the [BulletStyle](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.bulletedlist.bulletstyle.aspx) property.  

- [BulletStyle](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.bulletedlist.bulletstyle.aspx) ：設定項目符號樣式 { Numbered | LowerAlpha | Circle | Disc | ... }
- [DisplayMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.bulletedlist.displaymode.aspx) ：設定顯示模式 {Text | HyperLink | LinkButton}
```xml
<asp:BulletedList runat="server" ID="BulletedList1"
DataSourceID="SqlDataSource1"
DataTextField="TerritoryDescription"
DataValueField="TerritoryID" 
BulletStyle="Circle"
DisplayMode = "Text" >
</asp:BulletedList>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiBysJev_dUGNl9w3JMSyWhvu3TLGwHJiVLlwhnCF9-pDU43YeODr0USQO9qfk-GxfKbfUsQOM886JMnr_jaGuN2Glgrzm_OxztN1VtOMVRB_r8Fwyg8BSrSyy8VRznBOoeMoQN19-ORlE/s275/ctl-BulletedList.png)