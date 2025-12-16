---
title: TreeView 控制項
layout: default
parent: 資料繫結控制項
nav_order: 4
description: "TreeView 控制項"
date: 2013-03-29
tags: [Web,ASPNET,資料繫結控制項]
postid: "3758907750000788250"
---
# 使用靜態資料建構 TreeView

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

# 使用遞迴動態建構 TreeView

靜態資料必須一開始就知道資料，才做的出來，如果 Tree 的資料是存在資料庫中，你也可以使用遞迴由程式動態建構出所有的樹狀節點。  
```c#
protected void Page_Load(object sender, EventArgs e)
{
if (!IsPostBack)
{
//取得資料，回傳格式 ( OrgID, OrgName, ID, ParentID )
DataTable dtOrg = RjCommonDB.GetOrg();

//DataTable 必須放在 DataSet 中，才可以建立關聯
DataSet dsOrg = new DataSet();
dsOrg.Tables.Add(dtOrg);

//將 DataRelation 加進 DataSet 中
DataRelation drOrg = new DataRelation("OrgRelation", dsOrg.Tables[0].Columns["ID"], dsOrg.Tables[0].Columns["ParentId"]);
dsOrg.Relations.Add(drOrg);

foreach (DataRow row in dsOrg.Tables[0].Rows)
{
if (row["ParentID"].ToString() == "")
{
TreeNode treeNode = CreateNode(row["OrgID"].ToString(), row["OrgName"].ToString(), int.Parse(row["ChildCount"].ToString()) > 0);
TreeView4.Nodes.Add(treeNode);
PopulateTree(row, treeNode);
}
}
}
}

// 建立子節點
private void PopulateTree(DataRow dataRow, TreeNode parentNode)
{
foreach (DataRow row in dataRow.GetChildRows("OrgRelation"))
{
TreeNode treeNode = CreateNode(row["OrgID"].ToString(), row["OrgName"].ToString(), int.Parse(row["ChildCount"].ToString()) > 0);
parentNode.ChildNodes.Add(treeNode);
PopulateTree(row, treeNode);  //Recursively build the tree
}
}
///節點屬性設定
private TreeNode CreateNode(string nodeValue, string nodeText, bool expanded)
{
TreeNode node = new TreeNode(); ;
node.Text = nodeText;
node.Value = nodeValue;
node.Expanded = expanded;
return node;
}
```

# 繫結到 HierarchicalDataSourceControl

[TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) 控制項是一個**階層資料繫結控制項** ( [HierarchicalDataBoundControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.hierarchicaldataboundcontrol.aspx) )，若要繫結資料，則必須透過**階層資料來源控制項** ([HierarchicalDataSourceControl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.hierarchicaldatasourcecontrol.aspx))。  例如： [XmlDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.aspx) 和 [SiteMapDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.sitemapdatasource.aspx) 控制項。  下面範例使用 [XmlDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xmldatasource.aspx) 將資料繫結到 [TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) 控制項。  

假設下面 XML 文件是我們要繫結的資料，首先要知道的是，每一個標籤都表示一個節點，所以共有９個節點。
```xml
<?xml version="1.0" encoding="utf-8"?>
<總公司>
<Company CompanyId="C1" CompanyName="Microsof Inc.">
<Employee CompanyId="C1" EmployeeId="E01" FirstName="Joe" >Lin</Employee>
<Employee CompanyId="C1" EmployeeId="E02" FirstName="Mary">Huang</Employee>
<Employee CompanyId="C1" EmployeeId="E03" FirstName="Sam">Huang</Employee>
</Company>
<Company CompanyId="C2" CompanyName="Apple Inc.">
<Employee CompanyId="C2" EmployeeId="E04" FirstName="Sue">Chen</Employee>
<Employee CompanyId="C2" EmployeeId="E05" FirstName="Tom">Li</Employee>
<Employee CompanyId="C2" EmployeeId="E06" FirstName="Mike">Wang</Employee>
</Company>
</總公司>
```

再來要知道的是，假設有個標籤的樣式如下：
```xml
<Item FieldA='ＡＢＣ' FieldB='ＸＹＺ'>甲乙丙</Item>
```

1) 若希望在節點上顯示 ＡＢＣ ，則必須設定 [TextField](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenodebinding.textfield.aspx) 或 [Text](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.text.aspx) 屬性。例如： TextField="FieldA" 或 Text="FieldA" 。  
2) 若希望在節點上顯示 甲乙丙 ，則必須將 [TextField](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenodebinding.textfield.aspx) 設定成 **#InnerText** 。例如： TextField="#InnerText" 。  
3) 若必須將 ＸＹＺ 回傳，則必須設定 [ValueField](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenodebinding.valuefield.aspx) 或 [Value](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listitem.value.aspx) 屬性 屬性。例如： ValueField="FieldB" 或 Value="FieldB" 。  
4) 要載入 [TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) 的項目必須使用 [DataMember](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.listcontrol.datamember.aspx) 屬性指定，例如：DataMember="Company" 。  
```xml
<asp:XmlDataSource ID="XmlDataSource3" runat="server" DataFile="CompanyEmployee2.xml" />
<asp:TreeView ID="TreeView1" runat="server" 
DataSourceID="XmlDataSource3" 
OnSelectedNodeChanged="TreeView1_SelectedNodeChanged">
<DataBindings>
<asp:TreeNodeBinding DataMember="Company" TextField="CompanyName" ValueField="CompanyId" />
<asp:TreeNodeBinding DataMember="Employee" TextField="#InnerText" ValueField="EmployeeId" />
</DataBindings>
</asp:TreeView>
</ContentTemplate>
```
```c#
protected void TreeView1_SelectedNodeChanged(object sender, EventArgs e)
{
//取得 SelectdNode 的 ParentNode
TreeNode node = TreeView1.SelectedNode;
int level = node.Depth;
TreeNode PNode = node.Parent;

myMessage.ShowAjax(this, 
"You clicked tree node \r\n" 
+ TreeView1.SelectedNode.Text + "\r\n" 
+ TreeView1.SelectedNode.Value + "\r\n\"
+ "Its parent node is " + PNode.Text 
);
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBhL10bqMrPLrXpXYFdunDJQuyBPI2ZIiASoD9Mco4LPZKzq42onip3OV38Ap-aBjbP2Xz52x8lq5JnX6ImOfHyGUqzvMARz1AtBACpojYqUwdrgCuEqZpfVps-JEMiSMzCH2gCET5skc/s444/ctl-TreeView.png)

# 繫結到實作 IHierarchicalDataSource 的自訂物件

若要將某個物件資料直接丟給 [TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) 繫結，則該資料必須實作 [IHierarchicalDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.ihierarchicaldatasource.aspx) 介面。  底下自訂類別就是一個實作 [IHierarchicalDataSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.ihierarchicaldatasource.aspx) 的範例。  使用時只要將該物件資料指定給 [DataSource](hhttp://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basedataboundcontrol.datasource.aspx) 屬性，再叫用 DataBind 方法即可。  
```xml
<asp:TreeView ID="TreeView3" runat="server" OnSelectedNodeChanged="TreeView3_SelectedNodeChanged">
<DataBindings>
<asp:TreeNodeBinding 
DataMember = "System.Data.DataRowView" 
TextField  = "OrgName" 
ValueField = "OrgID" />
</DataBindings>
</asp:TreeView>
```
```c#
protected void Page_Load(object sender, EventArgs e)
{
if (!IsPostBack)
{
//這個 DataTable 至少要包含三個欄位， ID, ParentID, Text
// ID : 節點唯一識別碼
// ParentID : 父層節點識別碼
// Text : 節點名稱
// 至於節點的Value值，可以使用另外一個欄位，也可以使用ID欄位
DataTable dtOrg = RjCommonDB.GetOrg();

TreeView3.DataSource = new MyHierarchicalDataSource(dtOrg, "ID", "ParentID", "DE9C5DF3-48D4-4D81-8A8F-320B94DEEB04");
TreeView3.DataBind();
}
}
```
```c#
/// <summary>
/// 實作階層式資料來源
/// </summary>
public class MyHierarchicalDataSource : IHierarchicalDataSource
{
//實作事件
public event EventHandler DataSourceChanged;

//實作方法
/// <summary>
/// 依路徑，回傳該路徑下節點的 View
/// </summary>
/// <param name="viewPath">階層式路徑</param>
/// <returns></returns>
public HierarchicalDataSourceView GetHierarchicalView(string viewPath)
{
return new MyHierarchicalDataSourceView(this, viewPath);
}

readonly DataView dvOriginal;    // 將傳進來的 dataset 轉成 dataview
public string idColumnName { get; private set; }
public string pidColumnName { get; private set; }
public string rootValue { get; private set; }

/// <summary>
/// 初始化 MyHierarchicalDataSource 類別的新執行個體
/// </summary>
/// <param name="dataSet"></param>
/// <param name="idColumnName">識別碼欄位名稱</param>
/// <param name="pidColumnName">父層欄位名稱</param>
/// <param name="rootValue">根的值</param>
public MyHierarchicalDataSource(DataTable dtOriginal, string idColumnName, string pidColumnName, string rootValue)
{
this.dvOriginal = dtOriginal.DefaultView;
this.idColumnName = idColumnName;
this.pidColumnName = pidColumnName;
this.rootValue = rootValue;
}

// 取得 childNode 的 viewPath
internal string GetChildrenViewPath(string viewPath, DataRowView row)
{
return viewPath + "\\" + row[idColumnName].ToString();
}

// 取得 parentNode 的 viewPath
internal string GetParentViewPath(string viewPath)
{
return viewPath.Substring(0, viewPath.LastIndexOf("\\"));
}

// 取得某一節點的 Parent 節點
internal DataRowView GetParentRow(DataRowView row)
{
dvOriginal.RowFilter = GetFilter(idColumnName, row[pidColumnName].ToString()); 
DataRowView parentRow = dvOriginal[0];
dvOriginal.RowFilter = "";
return parentRow;
}

// 判斷某一節點是否有子節點
internal bool HasChildren(DataRowView row)
{
dvOriginal.RowFilter = GetFilter(idColumnName, row[pidColumnName].ToString());
bool hasChildren = dvOriginal.Count > 0;
dvOriginal.RowFilter = "";
return hasChildren;
}

internal string GetFilter(string columnName, object columnValue)
{
if (columnValue is string)
return String.Format("[{0}] = '{1}'", columnName, columnValue.ToString().Replace("'", "''"));
else
return String.Format("[{0}] = {1}", columnName, columnValue);
}

#region private classes that implement further interfaces

/// <summary>
/// 以階層式資料結構，表示 System.Web.UI.HierarchicalDataSourceControl 控制項的節點或節點集合的資料檢視
/// </summary>
private class MyHierarchicalDataSourceView : HierarchicalDataSourceView
{
public string viewPath { get; private set; }
public MyHierarchicalDataSource hiDataSource { get; private set; }
public MyHierarchicalDataSourceView(MyHierarchicalDataSource hiDataSource, string viewPath)
{
this.hiDataSource = hiDataSource;
this.viewPath = viewPath;
}

/// <summary>
/// 取得檢視中的所有資料項目清單。
/// </summary>
/// <returns>可列舉階層式集合的介面</returns>
public override IHierarchicalEnumerable Select()
{
return new HierarchicalEnumerable(hiDataSource, viewPath);
}
}

/// <summary>
/// 可列舉的階層式集合
/// </summary>
private class HierarchicalEnumerable : IHierarchicalEnumerable
{
public string viewPath { get; private set; }
public MyHierarchicalDataSource hiDataSource { get; private set; }
public HierarchicalEnumerable(MyHierarchicalDataSource hiDataSource, string viewPath)
{
this.hiDataSource = hiDataSource;
this.viewPath = viewPath;
}

//傳回指定列舉項目的階層式結構的資料項目
public IHierarchyData GetHierarchyData(object enumeratedItem)
{
DataRowView row = (DataRowView)enumeratedItem;
return new HierarchyData(hiDataSource, viewPath, row);
}

//傳回會逐一查看集合的列舉程式
public IEnumerator GetEnumerator()
{
if (viewPath == "") // find root
{
hiDataSource.dvOriginal.RowFilter = hiDataSource.GetFilter(hiDataSource.pidColumnName, hiDataSource.rootValue);
}
else
{
string lastID = viewPath.Substring(viewPath.LastIndexOf("\\") + 1);
hiDataSource.dvOriginal.RowFilter = hiDataSource.GetFilter(hiDataSource.pidColumnName, lastID);
}

IEnumerator enumerator = hiDataSource.dvOriginal.ToTable().DefaultView.GetEnumerator();
hiDataSource.dvOriginal.RowFilter = "";

return enumerator;
}
}

/// <summary>
/// 階層式結構的資料項目
/// </summary>
private class HierarchyData : IHierarchyData
{
public string viewPath { get; private set; }
public MyHierarchicalDataSource hiDataSource { get; private set; }
public DataRowView row { get; private set; }

//Constructor
public HierarchyData(MyHierarchicalDataSource hiDataSource, string viewPath, DataRowView row)
{
this.hiDataSource = hiDataSource;
this.viewPath = viewPath;
this.row = row;
}

#region 必要實作內容

public IHierarchicalEnumerable GetChildren()
{
return new HierarchicalEnumerable(hiDataSource, hiDataSource.GetChildrenViewPath(viewPath, row));
}

public IHierarchyData GetParent()
{
return new HierarchyData(hiDataSource, hiDataSource.GetParentViewPath(viewPath), hiDataSource.GetParentRow(row));
}

public bool HasChildren
{
get { return hiDataSource.HasChildren(row); }
}

public object Item
{
get { return row; }
}

public string Path
{
get { return viewPath; }
}

public string Type
{
get { return typeof(DataRowView).ToString(); }
}

#endregion
}

#endregion
}
```

# 動態載入 TreeView 節點資料

上面介紹的方法，不管是使用靜態資料或者使用繫結資料庫，該 [TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) 中的資料都會在一開始就全部載入到控制項中，如果資料量較大的話，效能可能不會太好，這時就可以考量採用動態載入節點資料的方式。  

動態載入節點的方法在一開始時只載入根節點的資料，  同時將 [TreeNode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenode.aspx) 的 [PopulateOnDemand](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treenode.populateondemand.aspx) 屬性和 [TreeView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.aspx) 的 [PopulateNodesFromClient](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.populatenodesfromclient.aspx) 屬性都設定為 **true** ，  表示將等到 [TreeView.TreeNodePopulate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.treeview.treenodepopulate.aspx) 事件發生時，才動態載入子節點。  

PopulateOnDemand 預設值為 false ，PopulateNodesFromClient 預設值為 true 。
```xml
<asp:TreeView ID="TreeView1" runat="server" 
PopulateNodesFromClient="true"
OnTreeNodePopulate="TreeView1_TreeNodePopulate" >
</asp:TreeView>
```
```c#
protected void Page_Load(object sender, EventArgs e)
{
if (!IsPostBack)
{
//此處只取得根節點資料，回傳格式 ( OrgName, ID, ParentID, ChildCount )
DataTable dtOrg = RjCommonDB.GetChildOrg("00000000-0000-0000-0000-000000000000");

foreach (DataRow row in dtOrg.Rows)
{
TreeNode treeNode = CreateNode(row["ID"].ToString(), row["OrgName"].ToString(), int.Parse(row["ChildCount"].ToString()));
TreeView1.Nodes.Add(treeNode);
}
}
}

// 建立子節點
private void PopulateChildNode(TreeNode ParentNode)
{
DataTable dtOrg = RjCommonDB.GetChildOrg(ParentNode.Value);
foreach (DataRow row in dtOrg.Rows)
{
TreeNode treeNode = CreateNode(row["ID"].ToString(), row["OrgName"].ToString(), int.Parse(row["ChildCount"].ToString()));
ParentNode.ChildNodes.Add(treeNode);
}
}
// 節點屬性設定
private TreeNode CreateNode(string nodeValue, string nodeText, int ChildCount)
{
TreeNode node = new TreeNode(); ;
node.Text = nodeText;
node.Value = nodeValue;
node.Expanded = false;
node.PopulateOnDemand = ChildCount > 0; //若有子節點，才設定成 PopulateOnDemand
return node;
}
// 當發生 NodePopulate 事件時，才由資料庫載入子節點
protected void TreeView1_TreeNodePopulate(object sender, TreeNodeEventArgs e)
{
TreeNode node = e.Node;
PopulateChildNode(node);
}
```
## 參考資料  

- [Binding ASP.NET TreeView to a DataSet or an ObjectDataSource](http://varjabedian.net/archive/2008/04/22/binding-asp.net-treeview-to-a-dataset-or-an-objectdatasource.aspx)
- [動態TreeView 結合資料庫產生節點](http://www.dotblogs.com.tw/evan/archive/2008/12/03/6170.aspx)
-