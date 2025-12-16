---
title: 資料繫結控制項(Composite)
layout: default
parent: 資料繫結控制項
nav_order: 2
description: "資料繫結控制項(Composite)"
date: 2012-11-30
tags: [Web,ASPNET,資料繫結控制項]
postid: "3968183062508682624"
---
There are a number of data-bound controls that use other ASP.NET controls to display bound data to the user.   For this reason, these controls are referred to as composite data-bound controls.   These controls inherit from the base class, [CompositeDataBoundControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.compositedataboundcontrol.aspx) .  This class implements the [INamingContainer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.inamingcontainer.aspx) interface, which means that an inheritor of this class is a naming container for child controls.  

組合式的資料繫結控制項包含： [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) 、 [DetailsView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.detailsview.aspx) 、 [FormView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.formview.aspx) 、 [Repeater](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.repeater.aspx) 、 [DataList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datalist.aspx) 、 [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 、 [DataPager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datapager.aspx) 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzatiKiu5y5aY_KzK4rvn3l6lHnqVibOhO-Ja92sL2kR_eBRdpbkoIZ4UZ_gYyA99yGhx1njNAm6hLS14pNxAsWqpqhsGVmzjeXuKZSnNc_3KuSazPhtEnlo6kU6H0BhhtW59iWKuOxmU/s850/composite-data-bound-controls.png)

# GridView

- The [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) is used to display data in a tabular (rows-and-columns) format
- The [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) support paging, sorting, and editing data without having to write much code
- The row objects is a collection of [GridViewRow](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridviewrow.aspx)
- The column objects is a collection of [DataControlField](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datacontrolfield.aspx)

## GridView control structure

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgDBRDuNFNqTfcpna1UnjnJSQTBCysHgQa5r2TLfPXoT9SgQ9_5hYys5cU9_wM0i833xgaywIt7Ej0WUPq8kJCmZgRrauZzhIknyHdJhmjHOWIHwz3Q6y-XL42IZEoggw5Mo_SV96_lPM0/s557/GridViewStructure.png)

## DataControlField class hierarchy

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiAvHpPT__3k5PaquIJsIILzy-RUcxNeIwxS59iYzv29Ik8Ut98ygZhyWxhh7Y2E7Vl5R-lT57ASf6DKTovrxYh-FztSmNPHJDjprQB5QZ9HUKXrfZFrdHvX-HEkDDI1EyL5wMPHnfq7eQ/s548/GridViewRowHierarchy.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh8LWc_EKDYbMzMY33z0aABfQ4K0Y-WEZ6LalYLej5Sb7XMNmQBeaSXVmMzSm5_zW-WbBXEE7m_sviM2CK44NfiN3BaI3BQoN2kfbNYisn3Jko-cbNdi1G3V8I4hesS1u_tC_FubFN_KvQ/s850/DataControlFieldClassHierarchy.png)

## Sample
```xml
<asp:ObjectDataSource ID="ObjectDataSource2" runat="server" 
TypeName="Common.EmployeeLogic"
DataObjectTypeName="Common.Employee" 
DeleteMethod="DeleteEmployes"  
UpdateMethod="UpdateEmployes"
InsertMethod="InsertEmployes" 
SelectMethod="GetAllEmployes" >
</asp:ObjectDataSource>

<asp:GridView ID="GridView2" runat="server" 
DataSourceID="ObjectDataSource2" 
AutoGenerateColumns="False"
DataKeyNames="EmpID"
AllowPaging="True"  >
<Columns>
<asp:CommandField   ShowDeleteButton="True" ShowEditButton="True"    />
<asp:BoundField     DataField="EmpID"       HeaderText="EmpID"  ReadOnly="true" />
<asp:BoundField     DataField="Name"        HeaderText="Name"       />
<asp:BoundField     DataField="Salary"      HeaderText="Salary"     />
<asp:TemplateField  HeaderText="Birthday" >
<EditItemTemplate>
<asp:TextBox ID="txtBirthday" runat="server" Text='<%# Bind(Container.DataItem, "Birthday" , "{0:yyyy/MM/dd}") %>'></asp:TextBox>
<asp:CalendarExtender ID="CalendarExtender1" runat="server" TargetControlID="txtBirthday" Format="yyyy/MM/dd">
</asp:CalendarExtender>
</EditItemTemplate>
<ItemTemplate>
<asp:Label ID="labBirthday" runat="server" Text='<%# Bind(Container.DataItem, "Birthday" , "{0:yyyy/MM/dd}") %>'></asp:Label>
</ItemTemplate>
</asp:TemplateField>
<asp:CheckBoxField  DataField="Merryed"     HeaderText="Merryed"    />
<asp:HyperLinkField DataTextField="ImgUrl" DataNavigateUrlFields="ImgUrl" Target="_blank" HeaderText="ImgUrl" />
<asp:ImageField     DataImageUrlField="ImgUrl"  HeaderText="Img" />
</Columns>
<AlternatingRowStyle BackColor="PaleGoldenrod" />
<FooterStyle BackColor="Tan" />
<HeaderStyle BackColor="Tan" Font-Bold="True" />
<PagerStyle BackColor="PaleGoldenrod" ForeColor="DarkSlateBlue" HorizontalAlign="Center" />
<SelectedRowStyle BackColor="DarkSlateBlue" ForeColor="GhostWhite" />
<SortedAscendingCellStyle BackColor="#FAFAE7" />
<SortedAscendingHeaderStyle BackColor="#DAC09E" />
<SortedDescendingCellStyle BackColor="#E1DB9C" />
<SortedDescendingHeaderStyle BackColor="#C2A47B" />
</asp:GridView>
```
```c#
namespace Common
{
public class EmployeeLogic
{
private static List<Employee> EmpList = new List<Employee>();

static EmployeeLogic()
{
EmpList.Add(new Employee("001", "Chevrolet", 25000, new DateTime(1971, 1, 1), true, "http://127.0.0.1:8080/_images/web/caption_24.png"));
EmpList.Add(new Employee("002", "vito", 30000, new DateTime(1972, 1, 2), false, "http://127.0.0.1:8080/_images/web/head2_r_16.png"));
EmpList.Add(new Employee("003", "shao", 35000, new DateTime(1973, 1, 3), true, "http://127.0.0.1:8080/_images/web/head2_b_16.png"));
EmpList.Add(new Employee("004", "vicky", 40000, new DateTime(1974, 1, 4), false, "http://127.0.0.1:8080/_images/web/title.png"));
EmpList.Add(new Employee("005", "lai", 45000, new DateTime(1975, 1, 5), true, "http://127.0.0.1:8080/_images/web/head3_22.png"));
}

[DataObjectMethod(DataObjectMethodType.Select)]
public static List<Employee> GetAllEmployes()
{
return EmpList;
}

[DataObjectMethod(DataObjectMethodType.Insert)]
public static void InsertEmployes(Employee emp)
{
EmpList.Add(emp);
}

[DataObjectMethod(DataObjectMethodType.Delete)]
public static void DeleteEmployes(Employee employee)
{
//利用迴圈找到相同 EmpID 的項目，然後由集合 Remove 
foreach (Employee emp in EmpList)
{
if (emp.EmpID == employee.EmpID)
{
EmpList.Remove(emp);
break;
}
}
}

[DataObjectMethod(DataObjectMethodType.Update)]
public static void UpdateEmployes(Employee employee)
{
//另一種集合中尋找項目的方法

Employee empFound = EmpList.Find(
delegate(Employee emp) 
{ 
return emp.EmpID == employee.EmpID; 
});

if ( empFound != null )
{
empFound.Name = employee.Name;
empFound.Salary = employee.Salary;
empFound.Birthday = employee.Birthday;
empFound.Merryed = employee.Merryed;
empFound.ImgUrl = employee.ImgUrl;
}
}
}

public class Employee
{
public Employee() { }
public Employee(string empid, string name, Int32 salary, DateTime bithday, bool merryed, string imgurl)
{
EmpID = empid;
Name = name;
Salary = salary;
Birthday = bithday;
Merryed = merryed;
ImgUrl = imgurl;
}

public string EmpID { get; set; }
public string Name { get; set; }
public Int32 Salary { get; set; }
public DateTime Birthday { get; set; }
public bool Merryed { get; set; }
public string ImgUrl { get; set; }
}

}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjPpG3wulWMNzQH56BgsVw9e18xsK50X-0gQlezbOKag6xmBmXVh9V5jMbtOz_KymfpbwKTgdBOQOCRbbLYznHQvF2MWIiM0pPjTlKQgwt7xozHGwiY3AIuonKaccREQ6_vLK3lzEmOi8E/s850/ctl-gridview.png)

## ClientIDMode 屬性

這個 [Control.ClientIDMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.clientidmode.aspx) 屬性是在 ASP.NET 才被加入的。用來設定產生 ClientID 屬性值的演算法。

- [AutoID](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.clientidmode.aspx) ：串連所有上層命名容器的 ID。（ex: ctl00\_GridView1\_TextBox1）
- [Inherit](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.clientidmode.aspx) ：**預設值**，只串連父層命名容器的 ID。（ex: GridView1\_TextBox1）
- [Static](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.clientidmode.aspx) ：使用靜態(固定)值；開發人員必須自行確保控制項的 ID 是否唯一。
- [Predictable](http://msdn.microsoft.com/zh-tw/library/system.web.ui.control.clientidmode.aspx) ：使用指定規則，它一般會搭配 [ClientIDRowSuffix](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.idataboundlistcontrol.clientidrowsuffix.aspx) 屬性一起使用，可以指定某個資料欄位的值，當做特定的後綴詞(Suffix)，  最後子控制項的 ID 格式會結合父層容器的 ID ，並搭配 ClientIDRowSuffix 屬性，以符合 [父控制項ID]\_[子控制項ID]\_[Suffix欄位值] 格式。
```xml
<asp:GridView ID="GridView1" runat="server" DataSourceID="ObjectDataSource1"
ClientIDMode="Predictable" 
ClientIDRowSuffix="EmpID" >
<Columns>
<asp:TemplateField  HeaderText="EmpID" >
<ItemTemplate>
<asp:TextBox ID="EmpID" runat="server"></asp:TextBox>
</ItemTemplate>
</asp:TemplateField>
<asp:TemplateField  HeaderText="Name" >
<ItemTemplate>
<asp:TextBox ID="Name" runat="server"></asp:TextBox>
</ItemTemplate>
</asp:TemplateField>
</Columns>
</asp:GridView>
```

上面這個範例，以 EmpID 檔做 Suffix ，所以其子控制項的 ID 會包含：  GridView1\_EmpID\_E001, GridView1\_EmpID\_E002, ...GridView1\_Name\_E001, GridView1\_Name\_E002, ...，其中 E001, E002 為員工的ID值）  

若 ClientIDRowSuffix 屬性為空白，則會使用以 0 為開始的數字串取代。

# DetailsView

- The [DetailsView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.detailsview.aspx) is used to       **display the values of a single record**
- The [DetailsView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.detailsview.aspx) allows you to edit, delete, and insert records.
- The [DetailsView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.detailsview.aspx) can be used by itself to navigate the data source if the [AllowPaging](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.detailsview.allowpaging.aspx) property is set to True.
- The [DetailsView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.detailsview.aspx) can be used to display a master-detail form by combining with other controls such as the GridView, ListBox, or DropDownList.
- The [DetailsView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.detailsview.aspx) does not directly support sorting , however, you can use the data source.
- The [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) does not automatically support inserting new records, whereas the [DetailsView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.detailsview.aspx) does support this feature.
- The [DetailsView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.detailsview.aspx) supports the same formatting options that are available with the [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) control.
```xml
<asp:DetailsView ID="DetailsView2" runat="server"
AllowPaging="True"
AutoGenerateEditButton = "true" 
AutoGenerateInsertButton="true" 
AutoGenerateDeleteButton="false"
DataKeyNames="EmpID" 
AutoGenerateRows="False"
DataSourceID="ObjectDataSource2">
<Fields>
<asp:BoundField     DataField="EmpID"       HeaderText="EmpID"  ReadOnly="true" />
<asp:BoundField     DataField="Name"        HeaderText="Name"       />
<asp:BoundField     DataField="Salary"      HeaderText="Salary"     />
<asp:TemplateField  HeaderText="Birthday" >
<EditItemTemplate>
<asp:TextBox ID="txtBirthday" runat="server" Text='<%# Bind(Container.DataItem, "Birthday" , "{0:yyyy/MM/dd}") %>'></asp:TextBox>
<asp:CalendarExtender ID="CalendarExtender1" runat="server" TargetControlID="txtBirthday" Format="yyyy/MM/dd">
</asp:CalendarExtender>
</EditItemTemplate>
<ItemTemplate>
<asp:Label ID="labBirthday" runat="server" Text='<%# Bind(Container.DataItem, "Birthday" , "{0:yyyy/MM/dd}") %>'></asp:Label>
</ItemTemplate>
</asp:TemplateField>
<asp:CheckBoxField  DataField="Merryed"     HeaderText="Merryed"    />
<asp:HyperLinkField DataTextField="ImgUrl" DataNavigateUrlFields="ImgUrl" Target="_blank" HeaderText="ImgUrl" />
<asp:ImageField     DataImageUrlField="ImgUrl"  HeaderText="Img" />
<asp:CommandField 
ShowDeleteButton="True" 
ShowEditButton="True" 
ShowInsertButton="True" />  
</Fields>
</asp:DetailsView>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgxRvrRcYXjMoyrZzVwttJFRHC-L9hyphenhyphen3pgYr77ZkH_6JUlKiQR8a_igwwkeiF1caHuYmEmJYfMvGEV7OqbD0_xv1zKr6YJ-ZYBGz313NIGWa8DNyiZeOQToszVPNxlxvVS7cvDAP24zc6o/s381/ctl-DetailsView.png)

# FormView

The [FormView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.formview.aspx) is like the [DetailsView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.detailsview.aspx) , it is used to       **display a single record from a data source**.   
However, the [FormView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.formview.aspx) control       **does not automatically output the data in a predefined HTML table**.   
Instead, it allows developers to create templates that define how the data should be displayed.   
You can define different templates for viewing, editing, and updating records.   
Creating your own templates gives you the greatest flexibility in controlling how data is displayed.  
```xml
<asp:FormView ID="FormView1" runat="server" 
AllowPaging="True" 
DataSourceID="ObjectDataSource1">

<HeaderTemplate><span>Detail Data</span></HeaderTemplate>

<ItemTemplate>
<table>
<tr><td align="right">Vin：</td><td><asp:Literal ID="Literal2" runat="server" Text='<%# Bind("Vin") %>' ></asp:Literal></td></tr>
<tr><td align="right">Make：</td><td><asp:Literal ID="Literal1" runat="server" Text='<%# Bind("Make") %>' ></asp:Literal></td></tr>
<tr><td align="right">Model：</td><td><asp:Literal ID="ModelTextBox" runat="server" Text='<%# Bind("Model") %>' ></asp:Literal></td></tr>
<tr><td align="right">Year：</td><td><asp:Literal ID="YearTextBox" runat="server" Text='<%# Bind("Year") %>' ></asp:Literal></td></tr>
<tr><td align="right">Price：</td><td><asp:Literal ID="PriceTextBox" runat="server" Text='<%# Bind("Price") %>' ></asp:Literal></td></tr>
</table>
<br />
<asp:LinkButton ID="EditButton" runat="server" CausesValidation="False" CommandName="Edit" Text="編輯" />&nbsp;
<asp:LinkButton ID="DeleteButton" runat="server" CausesValidation="False" CommandName="Delete" Text="刪除" />&nbsp;
<asp:LinkButton ID="NewButton" runat="server" CausesValidation="False" CommandName="New" Text="新增" />
</ItemTemplate>
<EditItemTemplate>
<table cellspacing="5" cellpadding="5">
<tr><td align="right">Vin：</td><td><asp:TextBox ID="Vin" runat="server" Text='<%# Bind("Vin") %>' ></asp:TextBox></td></tr>
<tr><td align="right">Make：</td><td><asp:TextBox ID="Make" runat="server" Text='<%# Bind("Make") %>' ></asp:TextBox></td></tr>
<tr><td align="right">Model：</td><td><asp:TextBox ID="Model" runat="server" Text='<%# Bind("Model") %>' ></asp:TextBox></td></tr>
<tr><td align="right">Year：</td><td><asp:TextBox ID="Year" runat="server" Text='<%# Bind("Year") %>' ></asp:TextBox></td></tr>
<tr><td align="right">Price：</td><td><asp:TextBox ID="Price" runat="server" Text='<%# Bind("Price") %>' ></asp:TextBox></td></tr>
</table>
<br />
<asp:LinkButton ID="UpdateButton" runat="server" CausesValidation="True" CommandName="Update" Text="更新" />&nbsp;
<asp:LinkButton ID="UpdateCancelButton" runat="server" CausesValidation="False" CommandName="Cancel" Text="取消" />
</EditItemTemplate>
<InsertItemTemplate>
<table cellspacing="5" cellpadding="5">
<tr><td align="right">Vin：</td><td><asp:TextBox ID="VinTextBox" runat="server" Text='' /></td></tr>
<tr><td align="right">Make：</td><td><asp:TextBox ID="MakeTextBox" runat="server" Text='' /></td></tr>
<tr><td align="right">Model：</td><td><asp:TextBox ID="ModelTextBox" runat="server" Text='' /></td></tr>
<tr><td align="right">Year：</td><td><asp:TextBox ID="YearTextBox" runat="server" Text='' /></td></tr>
<tr><td align="right">Price：</td><td><asp:TextBox ID="PriceTextBox" runat="server" Text='' /></td></tr>
</table>
<br />
<asp:LinkButton ID="InsertButton" runat="server" CausesValidation="True" CommandName="Insert" Text="插入" /> &nbsp;
<asp:LinkButton ID="InsertCancelButton" runat="server" CausesValidation="False" CommandName="Cancel" Text="取消" />
</InsertItemTemplate>

<FooterTemplate><span><%# DateTime.Now.ToShortDateString() %></span></FooterTemplate>

</asp:FormView>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh6zLp4fsLvRTunWM79ju6RnPLjGKVeJdrz-351BapLacASRLLkukfIR3giWzU86Rpv7LL-QdSVYinKA24YPHClGrq2_0luWr7wN3r3b5JwWjdBi0YJzY_kSjJoCmLiOZXbiz6IbN6aKQw/s193/ctl-FormView.png)

# Repeater

The [Repeater](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.repeater.aspx) control also uses templates to define custom binding.    
However, it does not show data as individual records.    
Instead, it repeats the data rows as you define in your template.    
This allows you to create a single row of data and have it repeat across your page.  

The [Repeater](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.repeater.aspx) control is a **read-only** template.   That is, it only supports the ItemTemplate.   It does not implicitly support editing, insertion, and deletion.  

在 [Repeater](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.repeater.aspx) 裡的 Template 控制項，其 render 優先順序：  

&lt;HeaderTemplate&gt;
→FirstItem
&lt;SeparatorTemplate&gt;
&lt;ItemTemplate&gt;
&lt;SeparatorTemplate&gt;

→SecondItem
&lt;SeparatorTemplate&gt;
&lt;AlternatingItemTemplate&gt;
&lt;SeparatorTemplate&gt;

...
&lt;FooterTemplate&gt;
```xml
<asp:Repeater ID="Repeater1" runat="server" DataSourceID="ObjectDataSource2">
<HeaderTemplate>HeaderTemplate Aread</HeaderTemplate>
<ItemTemplate>
Name：<asp:Label ID="Label2" runat="server" Text='<%# Eval("Name") %>'></asp:Label><br />
Birthday：<asp:Label ID="Label3" runat="server" Text='<%# Eval("Birthday") %>'></asp:Label><br />
Merryed：<asp:CheckBox ID="CheckBox1" runat="server" Checked='<%# Convert.ToBoolean(Eval("Merryed"))  %>' /><br />

</ItemTemplate>
<SeparatorTemplate>
<hr style="border-bottom:1px dotted red; height:0;" />
</SeparatorTemplate>
<FooterTemplate>FooterTemplate Aread</FooterTemplate>
</asp:Repeater>
```
```xml
HeaderTemplate Aread
Name：<span id="Repeater1_Label2_0">Chevrolet</span><br />
Birthday：<span id="Repeater1_Label3_0">1971/1/1 上午 12:00:00</span><br />
Merryed：<input id="Repeater1_CheckBox1_0" type="checkbox" name="Repeater1$ctl01$CheckBox1" checked="checked" /><br />

<hr style="border-bottom:1px dotted red; height:0;" />

Name：<span id="Repeater1_Label2_1">vito</span><br />
Birthday：<span id="Repeater1_Label3_1">1972/1/2 上午 12:00:00</span><br />
Merryed：<input id="Repeater1_CheckBox1_1" type="checkbox" name="Repeater1$ctl03$CheckBox1" /><br />

<hr style="border-bottom:1px dotted red; height:0;" />

Name：<span id="Repeater1_Label2_4">lai</span><br />
Birthday：<span id="Repeater1_Label3_4">1975/1/5 上午 12:00:00</span><br />
Merryed：<input id="Repeater1_CheckBox1_4" type="checkbox" name="Repeater1$ctl09$CheckBox1" checked="checked" /><br />

FooterTemplate Aread
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhtlmOBl-c41k3sOQ6BRrK42NRCLKoEoBPb1ZKW5E6rOmBAVwYTG9yvEG9fdMDb72TY2_3LK9HKCWcz7Cu4iE19UH0ATDsXUVCsAvsZk-WGW-zyw_2YCsXX59zDVvIOofogm2HMlLBSvR4/s297/ctl-Repeater.png)

## Nested Repeater

如果要設計一個巢狀的 Repeater 結構，你必須利用上層 Repeater 的 ItemDataBound 事件中，將資料來源繫結給內層的 Repeater 。  
```xml
<asp:Repeater ID="rptEmployees" runat="server" DataSourceID="ObjectDataSource3" OnItemDataBound="rptEmployees_ItemDataBound">
<ItemTemplate>
<%#Eval("EmpNo")%>,<%#Eval("Name")%>,<%#Eval("BirthDate")%><br />
<asp:Repeater ID="rptFamilies" runat="server">
<ItemTemplate>
&nbsp;&nbsp;Families: <%#Eval("Relation")%>,<%#Eval("Name")%>,<%#Eval("BirthDate")%><br />
</ItemTemplate>
</asp:Repeater>
</ItemTemplate>
</asp:Repeater>
```
```c#
protected void rptEmployees_ItemDataBound(object sender, RepeaterItemEventArgs e)
{
if (e.Item.ItemType == ListItemType.Item || e.Item.ItemType == ListItemType.AlternatingItem)
{
EmployeeEntity emp = (EmployeeEntity)e.Item.DataItem;

Repeater rptFamilies = e.Item.FindControl("rptFamilies") as Repeater;
rptFamilies.DataSource = emp.Families;
rptFamilies.DataBind();
}
}
```

如果使用 ObjectDataSource 繫結，且在一關始就已經取有內層的資料集合，也可以直接設定內層 Repeater 的 DataSource 屬性。如下範例：  
```xml
<asp:Repeater ID="rptEmployees" runat="server" DataSourceID="ObjectDataSource3">
<ItemTemplate>
<%#Eval("EmpNo")%>,<%#Eval("Name")%>,<%#Eval("BirthDate")%><br />
<asp:Repeater ID="rptFamilies" runat="server" DataSource='<%# Eval("Families")%>' >
<ItemTemplate>
&nbsp;&nbsp;Families: <%#Eval("Relation")%>,<%#Eval("Name")%>,<%#Eval("BirthDate")%><br />
</ItemTemplate>
</asp:Repeater>
</ItemTemplate>
</asp:Repeater>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh6kFA2eY6HIeDq-2r_PX_TzVsq9xSy2N0Kop8pjEYIBwAvs-yaqwtKQqBQZbW8jUtl9n6_XeqY9kYC4ikO5Vgm2HpzxVxTDADpu2e5IJuRyPF3IreLSeCVcBpKl6_ILd6fcRQlWRM_vHY/s385/ctl-repeater-nested.png)

# DataList

[DataList](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datalist.aspx) 控制項和 [Repeater](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.repeater.aspx) 控制項很像，但是它會將每筆資料用一個 &lt;td&gt;&lt;/td&gt; 包起來。  你可以透過 [RepeatColumns](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkboxlist.repeatcolumns.aspx) 、 [RepeatDirection](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.checkboxlist.repeatdirection.aspx) 、 [RepeatLayout](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datalist.repeatlayout.aspx) 屬性，自訂每行的樣式。  
```xml
<asp:DataList ID="DataList2" runat="server" DataSourceID="ObjectDataSource2"
RepeatColumns="2" RepeatDirection="Horizontal"
>
<ItemTemplate>
EmpID:<asp:Label ID="EmpIDLabel" runat="server" Text='<%# Eval("EmpID") %>' /><br />
Name:<asp:Label ID="NameLabel" runat="server" Text='<%# Eval("Name") %>' /><br />
Salary:<asp:Label ID="SalaryLabel" runat="server" Text='<%# Eval("Salary") %>' /><br />
Birthday:<asp:Label ID="BirthdayLabel" runat="server" Text='<%# Eval("Birthday") %>' /><br />
Merryed:<asp:Label ID="MerryedLabel" runat="server" Text='<%# Eval("Merryed") %>' /><br />
ImgUrl:<asp:Label ID="ImgUrlLabel" runat="server" Text='<%# Eval("ImgUrl") %>' />
<br /><br />
</ItemTemplate>
</asp:DataList>
```
```xml
<table id="DataList1" cellspacing="0" style="border-collapse:collapse;">
<tr>
<td>
EmpID:<span id="DataList1_EmpIDLabel_0">001</span><br />
Name:<span id="DataList1_NameLabel_0">Chevrolet</span><br />
Salary:<span id="DataList1_SalaryLabel_0">25000</span><br />
Birthday:<span id="DataList1_BirthdayLabel_0">1971/1/1 上午 12:00:00</span><br />
Merryed:<span id="DataList1_MerryedLabel_0">True</span><br />
ImgUrl:<span id="DataList1_ImgUrlLabel_0">http://127.0.0.1:8080/_images/web/caption_24.png</span>
</td>
<td>
EmpID:<span id="DataList1_EmpIDLabel_1">002</span><br />
Name:<span id="DataList1_NameLabel_1">vito</span><br />
Salary:<span id="DataList1_SalaryLabel_1">30000</span><br />
Birthday:<span id="DataList1_BirthdayLabel_1">1972/1/2 上午 12:00:00</span><br />
Merryed:<span id="DataList1_MerryedLabel_1">False</span><br />
ImgUrl:<span id="DataList1_ImgUrlLabel_1">http://127.0.0.1:8080/_images/web/head2_r_16.png</span>
</td>
</tr>
<tr>
<td>
EmpID:<span id="DataList1_EmpIDLabel_2">003</span><br />
Name:<span id="DataList1_NameLabel_2">shao</span><br />
Salary:<span id="DataList1_SalaryLabel_2">35000</span><br />
Birthday:<span id="DataList1_BirthdayLabel_2">1973/1/3 上午 12:00:00</span><br />
Merryed:<span id="DataList1_MerryedLabel_2">True</span><br />
ImgUrl:<span id="DataList1_ImgUrlLabel_2">http://127.0.0.1:8080/_images/web/head2_b_16.png</span>
</td>
<td>
EmpID:<span id="DataList1_EmpIDLabel_3">004</span><br />
Name:<span id="DataList1_NameLabel_3">vicky</span><br />
Salary:<span id="DataList1_SalaryLabel_3">40000</span><br />
Birthday:<span id="DataList1_BirthdayLabel_3">1974/1/4 上午 12:00:00</span><br />
Merryed:<span id="DataList1_MerryedLabel_3">False</span><br />
ImgUrl:<span id="DataList1_ImgUrlLabel_3">http://127.0.0.1:8080/_images/web/title.png</span>
</td>
</tr>
<tr>
<td>
EmpID:<span id="DataList1_EmpIDLabel_4">005</span><br />
Name:<span id="DataList1_NameLabel_4">lai</span><br />
Salary:<span id="DataList1_SalaryLabel_4">45000</span><br />
Birthday:<span id="DataList1_BirthdayLabel_4">1975/1/5 上午 12:00:00</span><br />
Merryed:<span id="DataList1_MerryedLabel_4">True</span><br />
ImgUrl:<span id="DataList1_ImgUrlLabel_4">http://127.0.0.1:8080/_images/web/head3_22.png</span>
</td>
<td></td>
</tr>
</table>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEibdHxLhtvz5YvFSpyS79oUli1ak7UBa9dxJPiD4PIv2E73ASd0R85LZCk8fbwdC7Ad4wi0Xok00Efc97FAEcwsBycSUKkN-fGnB77c8Bt1X2tdGLD6TzhzSJuqElFlE8QKeUb_gpfF1ng/s659/ctl-DataList.png)

# ListView

[ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 和 [DataPager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datapager.aspx) 控制項都是 ASP.NET 3.5 才引進的新的資料繫結控制項。  它是一個以樣板導向的控制項，這表示在預設狀況下，它並不會呈現任何東西，必須加入樣板項目，才能產出 HTML 標籤。  它可以簡化數個資料繫結工作，包括 CSS 樣式、彈性分頁，以及排序、插入、刪除及更新等輔助功能。  

The [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) control also uses templates for the display of data.   However, it supports many additional templates that allow for more scenarios when working with your data.   This includes the LayoutTemplate.  

The [LayoutTemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) allows you to indicate an overall layout inside which rows of your data should be displayed.   The rows themselves are defined using the [ItemTemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.templatefield.itemtemplate.aspx) . At run time, rows are placed within the [LayoutTemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) placeholder as identified by a  control that has its ID attribute set to itemPlaceholder.  

The GorupTemplate of [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) allows you to define groups of data.  The ItemSeparatorTemplate of [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) allows you to define content that should be placed in between rows of items.  

The [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) control also implicitly supports the ability to edit, insert, and delete data using a data source control.   You can define individual templates for each of these scenarios.   You can then change the mode of the [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) control through a server-side call and thus invoke the template for the user.  

## Using LayoutTemplate and ItemTemplate

使用 [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 必須設計一個 [LayoutTemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 和 [ItemTemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.templatefield.itemtemplate.aspx) 。  
前者是整體外觀的樣式，後者是資料項生成的樣式。  

你必須在 [LayoutTemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 置放一個名為 itemPlaceholder 的預留位置控制項，讓 [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 知道要將資料項填塞在什麼地方。  
這個預留位置控制項的名稱，可以由 [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 的 [ItemPlaceholderID](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.itemplaceholderid.aspx) 屬性設定變更，若不指定，其預設值就是 itemPlaceholder。  
這個預留位置控制項並沒有限制是什麼控制項，它只是用來給 [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 知道資料放置的位置。  

你也可以在 [LayoutTemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 放置一個 [DataPager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datapager.aspx) 控制項，它就會自動幫該 [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 達到分頁功能。  
```xml
<asp:ListView ID="ListView1" runat="server" DataSourceID="SqlDataSource1">
<LayoutTemplate>
<table>
<tr>
<th>EmployeeID</th>
<th>FirstName</th>
<th>LastName</th>
</tr>
<asp:PlaceHolder runat="server" ID="itemPlaceholder" />
</table>
<div>
<asp:DataPager ID="DataPager2" runat="server" PageSize="3">
<Fields>
<asp:NextPreviousPagerField ButtonType="Button" />
</Fields>
</asp:DataPager>
</div>
</LayoutTemplate>
<ItemTemplate>
<tr>
<td><%# Eval("EmployeeID")%></td>
<td><%# Eval("FirstName")%></td>
<td><%# Eval("LastName")%></td>
</tr>
</ItemTemplate>
</asp:ListView>
```

## 範例２

下面這個例子，注意二點：  

- 1. 第 46 行，必須在 [LayoutTemplate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 中指定一個命名為 itemPlaceholder 的容器，以建立項目資料。
- 2. 第 52~59 行， [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 會使用 [DataPager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datapager.aspx) 控制項來設計分頁的設定。
```xml
<asp:ListView ID="ListView1" runat="server" DataSourceID="ObjectDataSource2">
<AlternatingItemTemplate></AlternatingItemTemplate>
<EditItemTemplate></EditItemTemplate>
<EmptyDataTemplate></EmptyDataTemplate>
<InsertItemTemplate></InsertItemTemplate>
<ItemTemplate>
<tr style="background-color:#DCDCDC;color: #000000;">
<td>
<asp:Button ID="DeleteButton" runat="server" CommandName="Delete" Text="刪除" />
<asp:Button ID="EditButton" runat="server" CommandName="Edit" Text="編輯" />
</td>
<td>
<asp:Label ID="EmpIDLabel" runat="server" Text='<%# Eval("EmpID") %>' />
</td>
<td>
<asp:Label ID="NameLabel" runat="server" Text='<%# Eval("Name") %>' />
</td>
<td>
<asp:Label ID="SalaryLabel" runat="server" Text='<%# Eval("Salary") %>' />
</td>
<td>
<asp:Label ID="BirthdayLabel" runat="server" Text='<%# Bind(Container.DataItem, "Birthday" , "{0:yyyy/MM/dd}") %>' />
</td>
<td>
<asp:CheckBox ID="MerryedCheckBox" runat="server" Checked='<%# Eval("Merryed") %>' Enabled="false" />
</td>
<td>
<asp:Image ID="Image1" runat="server" ImageUrl='<%# Eval("ImgUrl") %>' />
</td>
</tr>
</ItemTemplate>
<LayoutTemplate>
<table id="Table2" runat="server">
<tr id="Tr1" runat="server">
<td id="Td1" runat="server">
<table ID="itemPlaceholderContainer" runat="server">
<tr id="Tr2" runat="server">
<th id="Th1" runat="server"></th>
<th id="Th2" runat="server">EmpID</th>
<th id="Th3" runat="server">Name</th>
<th id="Th4" runat="server">Salary</th>
<th id="Th5" runat="server">Birthday</th>
<th id="Th6" runat="server">Merryed</th>
<th id="Th7" runat="server">ImgUrl</th>
</tr>
<tr ID="itemPlaceholder" runat="server"></tr>
</table>
</td>
</tr>
<tr id="Tr3" runat="server">
<td id="Td2" runat="server" >
<asp:DataPager ID="DataPager1" runat="server" PageSize="2">
<Fields>
<asp:NextPreviousPagerField
ButtonType="Button" 
ShowFirstPageButton="True" 
ShowLastPageButton="True" />
</Fields>
</asp:DataPager>
</td>
</tr>
</table>
</LayoutTemplate>
<SelectedItemTemplate></SelectedItemTemplate>
</asp:ListView>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjveBilDeUoX0_4iPm4LGF_RihRhOQmMMGzxrrFMaCPbozSLa0e_y_gWmmECyOaVCt0kt7ZtZeiUyhw7h7fg8GCDz-OCLvWJUuppP7jZBJ8apgvOqNr_DqGPaXTPxZSsqTmMHBXq2y1Uwo/s598/ctl-ListView.png)

# DataPager

任何實作 [IPageableItemContainer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.ipageableitemcontainer.aspx) 介面的資料繫結控制項（例如 [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 控制項），  都可以使用 [DataPager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datapager.aspx) 來達到資料的分頁功能。  

目前也只有 [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 控制項有實作 [IPageableItemContainer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.ipageableitemcontainer.aspx) 介面，如果你想用在 [GridView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.gridview.aspx) 的話，你可以自訂一個控制項，並且實作 [IPageableItemContainer](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.ipageableitemcontainer.aspx) 介面即可。  你可以參考底下這個範例 [http://www.c-sharpcorner.com/uploadfile/nipuntomar/gridview-with-datapager-in-Asp-Net-3-5/](http://www.c-sharpcorner.com/uploadfile/nipuntomar/gridview-with-datapager-in-Asp-Net-3-5/)  

你可以把這個控制項放在網頁中的任何位置，然後透過 [PagedControlID](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datapager.pagedcontrolid.aspx) 屬性與資料繫結控制項建立關聯。  你也可以同時使用多個 [DataPager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datapager.aspx) ，以達到多重分頁的功能，例如在 DataGrid 頂端和底部皆放置一個 [DataPager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datapager.aspx) ，以提供不同的分頁介面。  

如果將 [DataPager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datapager.aspx) 放置在特定的 [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) 內部，它們就會隱含地建立關聯，無須再特別使用 [PagedControlID](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datapager.pagedcontrolid.aspx) 指定關聯；反倒是，若你關聯到外部的 [ListView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listview.aspx) ，這個 [DataPager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datapager.aspx) 就會失效。  

####  [DataPager](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.datapager.aspx) 能提供三種不同型式的分頁欄位：

- [NumericPagerField](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.numericpagerfield.aspx) ：會顯示 1 2 3...分頁介面。
- [NextPreviousPagerField](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.nextpreviouspagerfield.aspx) ：會顯示「下一頁」、「上一頁」、「第一頁」及「最後一頁」按鈕。
- [TemplatePagerField](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.templatepagerfield.aspx) ：自定分頁介面設計。
```xml
<div>
<asp:DataPager ID="DataPager2" runat="server" PageSize="3" PagedControlID="ListView2">
<Fields>
<asp:NextPreviousPagerField ButtonType="Button" ShowFirstPageButton="true" ShowPreviousPageButton="true" ShowNextPageButton="false" />
<asp:NumericPagerField  />
<asp:NextPreviousPagerField ButtonType="Button" ShowNextPageButton="true" ShowLastPageButton="true" ShowPreviousPageButton="false" />
</Fields>
</asp:DataPager>
</div>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg44iiaLreYTSMHeIgcKUEXkDTWngPNAs2BuZn-XOwzKG-HSOD3wq6e-QvNNbedUC7sCu_8X322LGnO1ve2rwyjIgvYHLeqmokfCWf948ysidBrm_5R39o17Li_2c87CZZd1Cy2wa8fHVQ/s381/ctl-DataPager.png)
## 參考資料  

- [GridView with DataPager in ASP.NET 3.5](http://www.c-sharpcorner.com/uploadfile/nipuntomar/gridview-with-datapager-in-Asp-Net-3-5/)
- [Pager Control for ASP.NET](http://www.codeproject.com/Articles/11418/Pager-Control-for-ASP-NET)
-