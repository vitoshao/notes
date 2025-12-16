---
title: DataGridView 控制項(1)
layout: default
parent: WinForm
nav_order: 2
description: "DataGridView 控制項(1)"
date: 2014-06-16
tags: [DotNetFramework,WinForm]
postid: "2129080907655184803"
---
DataGridView 是一種表格樣式的控制項，專門用來顯示資料用的。  不論有沒有基礎資料來源，您都可以使用 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 控制項來顯示資料。   若沒有指定資料來源，你可以先建立包含資料的資料行和資料列，再將它們加入 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 。  若使用資料來源，你可以設定 [DataSource](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.datasource.aspx) 和 DataMember 屬性，讓 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 與資料建立繫結，就可以自動填入資料。  

# DataGridView 控制項架構

構成 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 控制項的相關類別，都包含在 System.Windows.Forms 命名空間中，而且全部都以 "DataGridView" 前置詞命名。  DataGridView 控制項是由兩個主要物件所組成：儲存格（Cells）和群組列（Bands），這些物件都是由 DataGridViewElement 類別衍生出來的。  下圖為 DataGridViewElement 類別的物件模型：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhdEv6X4w9cdMorCEpZMwUrlhNF3SsRxxrrtnhUz2WlVA9BBpbtwps7PZKe9vjzVVCPnmbBuJbwMtK2_DWX-k4pgihINamuSNi4ANo5s1F4jyIhfrwDfeaE3lE_kxgWV5O4pVI7jbrlL2k/s178/datagridview-03.gif)

所以說，最主要用來與 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 控制項互動的類別，就是是底下三個：

- [DataGridViewCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcell.aspx)
- [DataGridViewColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcolumn.aspx)
- [DataGridViewRow](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewrow.aspx)

## 儲存格

儲存格是最基本的互動單位，每一個儲存格都必須是一個 [DataGridViewCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcell.aspx) 型別，但是 [DataGridViewCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcell.aspx) 是一個抽象基底類別，所以實際的儲存格型別都是從它衍生出來的。

下列清單是 [DataGridViewCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcell.aspx) 的衍生類別：

- DataGridViewButtonCell
- DataGridViewTextBoxCell
- DataGridViewCheckBoxCell
- DataGridViewComboBoxCell
- DataGridViewImageCell
- DataGridViewLinkCell
- DataGridViewHeaderCell
- DataGridViewRowHeaderCell
- DataGridViewColumnHeaderCell
- DataGridViewTopLeftHeaderCell
- 自訂型別的儲存格

#### 儲存格樣式

你可以透過以下屬性來設定儲存格的樣式：

- DefaultCellStyle
- ColumnHeadersDefaultCellStyle
- CellBorderStyle
- GridColor
```c#
//若要讓資料列和資料行行首使用自訂的視覺化樣式
//必須停止應用程式啟用的視覺化樣式.
dgView2.EnableHeadersVisualStyles = false;

//設定儲存格樣式,
//只有在使用單一框線時，才可以將 GridColor 屬性設定為任何色彩；
//若使用其他類型的框線，則是由作業系統指定色彩。
dgView2.CellBorderStyle = DataGridViewCellBorderStyle.Single;
dgView2.GridColor = Color.Blue ;   

//定義一個樣式
DataGridViewCellStyle headStyle = new DataGridViewCellStyle();
headStyle.BackColor = Color.Yellow;
headStyle.ForeColor = Color.Red;
headStyle.Font = new Font("Verdana", 12, FontStyle.Bold);

//定義一個樣式
DataGridViewCellStyle cellStyle = new DataGridViewCellStyle();
cellStyle.Font = new Font("Ariel", 12, FontStyle.Regular);

//分配樣式給 ColumnHeader
dgView2.ColumnHeadersDefaultCellStyle = headStyle;

//分配樣式給 Columns
dgView2.Columns.Add("col1", "col1_head");
dgView2.Columns[0].DefaultCellStyle = cellStyle;
```

## 群組列

[DataGridViewColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcolumn.aspx)  和 [DataGridViewRow](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewrow.aspx) 都算是一種群組列（Band）結構，二者都是衍生自 DataGridViewBand 基底類別。  

### 資料行

[DataGridViewColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcolumn.aspx)  類別是用來代表 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 控制項中的資料行，它主要是用來控制 Grid 的外觀和行為，也就是和 UI 相關的設定。  例如欄位寛度或儲存格的樣式等。  

下圖為 [DataGridViewColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcolumn.aspx) 類別的物件模型：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhBXEhkMAR6JoKSVz7l6PAjJkMJ6MA5dEYy80jVnE4u3CL40zUu3ZzpEJ6ic1WXw24My4uulf6Q1xRv14PBCCN_15K8dVBxFp-Dela625BAxzHl9CFOXth2IND3lDUjKKEEeWdXBVOep1Y/s302/datagridview-04.png)

下面程式碼，簡單示範如何加入一個資料行到 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 控制項中
```c#
//定義一個資料行的樣式
DataGridViewCellStyle cellStyle = new DataGridViewCellStyle();
cellStyle.BackColor = Color.Aqua;
cellStyle.Font = new Font("Verdana", 10, FontStyle.Bold);

//定義一個用來放置TextBox型別的資料行
DataGridViewCell Cell1 = new DataGridViewTextBoxCell();
DataGridViewColumn Column1 = new DataGridViewColumn(Cell1);
Column1.HeaderText = "Head1";
Column1.Name = "Column1";

//定義另一個用來放置ComboBox型別的資料行
DataGridViewComboBoxCell Cell2 = new DataGridViewComboBoxCell();
Cell2.Items.Add("111");
Cell2.Items.Add("222");
DataGridViewColumn Column2 = new DataGridViewColumn(Cell2);
Column2.HeaderText = "Head2";
Column2.Name = "Column2";

//在 DataGridView 中加入資料行
dgView2.Columns.Add(Column1);
dgView2.Columns["Column1"].DefaultCellStyle = cellStyle;

//在 DataGridView 中加入資料行
dgView2.Columns.Add(Column2);
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj27D8tI6MSaSjAlbAmmxDyaeHtLPGhxbhP-D4dfIRQiPSyzaV5gq4fXEHF5QZNbxjP0tVFAVlWT5DbTyGA7nQkDT68uccHkG8WZX1etnmjoATtBJC8aS3NGoZ_ReRDefK_MG8qWqTUamU/s311/datagridview-06.png)

由上面例子可以知道，要用什麼型別的欄位，就必須在 [DataGridViewColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcolumn.aspx) 中裝載什麼型別的 [DataGridViewCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcell.aspx) 。  在 .NET 中，已經事先定義好下列幾個常用的儲存格型別，它們都是 [DataGridViewColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcolumn.aspx) 的衍生類別：  

- [DataGridViewButtonColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewbuttoncolumn.aspx)
- [DataGridViewTextBoxColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewtextboxcolumn.aspx)
- [DataGridViewCheckBoxColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcheckboxcolumn.aspx)
- [DataGridViewComboBoxColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcomboboxcolumn.aspx)
- [DataGridViewImageColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewimagecolumn.aspx)
- [DataGridViewLinkColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewlinkcolumn.aspx)

你就可以直接使用這些衍生類別來建立資料行。  
```c#
//定義一個 DataGridViewTextBoxColumn
DataGridViewTextBoxColumn Column3 = new DataGridViewTextBoxColumn();
Column3.HeaderText = "Head3";
Column3.Name = "Column3";
dgView2.Columns.Add(Column3);

//定義一個 DataGridViewCheckBoxColumn 
DataGridViewCheckBoxColumn Column4 = new DataGridViewCheckBoxColumn();
Column4.HeaderText = "Head4";
Column4.Name = "Column4";
dgView2.Columns.Add(Column4);
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg7PcBdSuXtf9xutN169UQgL1QFlXAfifLltqrf5B_kv-szuX1IORv71m1XEW1dhumxFZ9rK_jZjqg6CwRxMR6A98jY_uAwoqn_YI9sRDriSzF6OjX0_i6iDFmwf75BZYwEVmJCT0OTMkA/s471/datagridview-07.png)

### 資料列

如果說 [DataGridViewColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcolumn.aspx) 是用來設定 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 的欄位型態，那麼 [DataGridViewRow](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewrow.aspx) 則是用來表示要顯示到 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 上的資料欄位。  您可以使用 Rows 集合，存取 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 控制項的資料列。 另外，可以使用 SelectedRows 集合，存取選取的資料列。  
```c#
DataGridViewRow row = dgv.Rows[index];

row.Cells[0].Value = trade.TradeNum;
row.Cells[1].Value = trade.TradeDate;
row.Cells[2].Value = trade.TradeType;
row.Cells[3].Value = trade.StockID;
row.Cells[4].Value = trade.StockName;
```

# 不使用繫結

若要使用 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 控制項來顯示資料，可分成三種模式：繫結、未繫結和虛擬。

未繫結模式適用於顯示少量的資料，由其對於靜態、唯讀的資料特別有用。  未繫結模式通常會藉由程式直接將資料填入控制項之中，  

## 編輯資料行

### 如何加入資料行

構成 DataGridView 資料行的物件是 **DataGridViewColumnCollection**，它提供２個 [Add](https://msdn.microsoft.com/zh-tw/library/System.Windows.Forms.DataGridViewColumnCollection.Add%28v=vs.100%29.aspx) 方法，可以用來加入資料行。  
```c#
public virtual int Add(DataGridViewColumn dataGridViewColumn);
public virtual int Add(string columnName, string headerText);
```

若要建立一個 [DataGridViewColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcolumn.aspx) ，  必需提供一個 [DataGridViewCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcell.aspx) 當做參數。  不過由於 [DataGridViewCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcell.aspx) 是一個抽象類別，  所以直接提供一個 [DataGridViewCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcell.aspx) 的衍生類別：  

#### DataGridViewCell 的衍生類別

- [DataGridViewButtonCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewbuttoncell.aspx)
- [DataGridViewCheckBoxCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcheckboxcell.aspx)
- [DataGridViewComboBoxCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcomboboxcell.aspx)
- [DataGridViewImageCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewimagecell.aspx)
- [DataGridViewLinkCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewlinkcell.aspx)
- [DataGridViewTextBoxCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewtextboxcell.aspx)

#### DataGridViewColumn 的衍生類別

與 [DataGridViewCell](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcell.aspx) 相對應的， [DataGridViewColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcolumn.aspx) 也有下列幾個衍生類別。

- [DataGridViewButtonColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewbuttoncolumn.aspx)
- [DataGridViewCheckBoxColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcheckboxcolumn.aspx)
- [DataGridViewComboBoxColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcomboboxcolumn.aspx)
- [DataGridViewImageColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewimagecolumn.aspx)
- [DataGridViewLinkColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewlinkcolumn.aspx)
- [DataGridViewTextBoxColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewtextboxcolumn.aspx)

加入一個 [DataGridViewColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcolumn.aspx) 
```c#
DataGridViewCell cell = new DataGridViewTextBoxCell();          // 注意： DataGridViewCell 是個 virutal class
DataGridViewColumn dgvCol = new DataGridViewColumn(cell);
col1.HeaderText = "col1";
col1.Name = "col1";
dgv.Columns.Add(dgvCol);
```

加入一個 [DataGridViewColumn](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcolumn.aspx) 的衍生類別
```c#
// Add DataGridViewTextBoxColumn

DataGridViewTextBoxColumn col2 = new DataGridViewTextBoxColumn();
col2.HeaderText = "col2";
col2.Name = "col2";
dgv.Columns.Add(col2);

// Add DataGridViewComboBoxColumn

DataGridViewComboBoxColumn col4 = new DataGridViewComboBoxColumn();
col4.Items.Add("item1");
col4.Items.Add("item2");
col4.Items.Add("item3");
dgv.EditMode = DataGridViewEditMode.EditOnEnter;  //DataGridViewEditMode 預設值是 EditOnKeystrokeOrF2 ，改成 EditOnEnter 才不用點二次，即可進入編輯。

col4.HeaderText = "col4";
col4.Name = "col4";
dgv.Columns.Add(col4);
```

直接加入資料行，並指定資料行行首文字
```c#
dgv.Columns.Add("col3", "col3_head");
```

### 資料行的屬性設定

資料行有多個屬性可以用來控制其外觀或表現行為，比較重要的有：

- ReadOnly：是否唯讀。
- Resizable：欄寛是否調整。
- AutoSizeMode：寬度要以何種方式自動調整。
- Frozen：設定某欄是否為凍結資料行。
```c#
DataGridViewColumn col2 = dgv.Columns[1];
col2.Name = "col2";

// 與[外觀]相關的屬性
col2.HeaderText = "col2_head";
col2.ToolTipText = "this is column 2";
col2.Visible = true;

// 設定 cell 樣式
DataGridViewCellStyle style = new DataGridViewCellStyle();
style.Font = new Font("細明體", 12);
style.BackColor = Color.Yellow;
col2.DefaultCellStyle = style;

// 與[行為]相關的屬性
col2.ReadOnly = false;
col2.Resizable = DataGridViewTriState.True;
col2.SortMode = DataGridViewColumnSortMode.Automatic;

// 與[配置]相關的屬性
dgv.Columns[2].Frozen = true;    // 設定凍結資料行
dgv.Columns[0].Frozen = true;    // 因為Columns[2]為凍結資料行，２以前都算凍結
col2.DividerWidth = 0;                // 設定資料行分割線的寬度(凍結欄與非凍結欄的間距).

// 調整資料行寬度的方式
col2.AutoSizeMode = DataGridViewAutoSizeColumnMode.AllCellsExceptHeader;

// 指定欄寛
col2.Width = 200;            // 欄位寛度
col2.MinimumWidth = 50;      // 欄位最小寛度
col2.FillWeight = 200;       // 輸入盒寛度，必須大於 0
//col.MaxInputLength = 5;     // 最大可輸入字元數

dgv.Columns[3].AutoSizeMode = DataGridViewAutoSizeColumnMode.Fill;
//dgv.Columns[1].AutoSizeMode = DataGridViewAutoSizeColumnMode.Fill;  
//凍結資料行不可以使用 Fill 屬性值
```

## 編輯資料列

### 如何加入資料列

構成 DataGridView 資料列的物件是 **DataGridViewRowCollection**，它提供４個 [Add](https://msdn.microsoft.com/zh-tw/library/System.Windows.Forms.DataGridViewRowCollection.Add%28v=vs.100%29.aspx) 方法，可以用來加入資料列。  
```c#
public virtual int Add();
public virtual int Add(DataGridViewRow dataGridViewRow);
public virtual int Add(int count);
public virtual int Add(params object[] values);
```
```c#
//1. 加入一個空白列
var index = dgv.Rows.Add();
dgv.Rows[index].Cells["Column1"].Value = "Column1";
dgv.Rows[index].Cells["Column1"].Value = "Column1";

//2. 加入一個 DataGridViewRow
DataGridViewRow row2 = (DataGridViewRow)dgv.Rows[0].Clone();
row2.Cells[0].Value = "row2_c1";
row2.Cells[1].Value = "row2_c2";
row2.Cells[2].Value = "row2_c3";
row2.Cells[3].Value = "item2";
dgv.Rows.Add(row2);

//3. 一次加入多列
dgv.Rows.Add(2);

//4. 加入一個指定內容的列
string[] row = new string[] { "row3_c1", "row3_c2", "row3_c3", "item3"};
dgv.Rows.Add(row);
dgv.Rows.Add("row4_c1", "row4_c2", "row4_c3", "item4");
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiuhRVz3k1Z0vvI-jz3zZ7nyYnfADZ88bOOPJDJAb5kjKEnr01iYqwXEoiRl9lRJIrPtQNCPELk3aTR6LM_n24h5XIIn-fLVrcwUdPsH5lWDCh8Wg8QJTtzOsKjfVSRGfA_p12dRvFa1-Y/s464/datagridview-03.png)

# 使用資料繫結

使用繫結方式，主要就是希望不要人工進行管理，希望 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 可以自動管理資料。  當 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 繫結資料時，控制項便會自動完成推入(pushed)和提取(pulled)資料列的操作。  

[DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx)  控制項支援標準的 Windows Form 資料繫結模型，因此它可以繫結至下列清單中所描述的類別執行個體。  

- 實作 IList 介面的任何類別，例如一維陣列。
- 實作 IListSource 介面的任何類別，例如 DataTable 和 DataSet 類別。
- 實作 IBindingList 介面的任何類別，例如 [BindingList＜T＞](http://msdn.microsoft.com/zh-tw/library/ms132679%28v=vs.110%29.aspx) 類別。
- 實作 IBindingListView 介面的任何類別，例如 [BindingSource](http://msdn.microsoft.com/zh-tw/library/vstudio/system.windows.forms.bindingsource%28v=vs.110%29.aspx) 類別。

## 顯示資料

當 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 的 [AutoGenerateColumns](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.autogeneratecolumns.aspx) 屬性為 true 時，在資料來源中的每個資料行都會自動在 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 控制項中產生對應的資料行。  如果偏好建立自己的資料行，您可以將 [AutoGenerateColumns](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.autogeneratecolumns.aspx) 屬性設定為 false，然後透過 [DataPropertyName](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridviewcolumn.datapropertyname.aspx) 屬性來繫結每個資料行。  
```c#
private SqlDataAdapter objAdapter;

private void GetData()
{
String strConn = ConfigurationManager.ConnectionStrings["EmpConnectionString"].ConnectionString;
SqlConnection objConn = new SqlConnection(strConn);

SqlCommand objCmd = new SqlCommand();
objCmd.Connection = objConn;
objCmd.CommandText = "SELECT ID, Name, Age FROM Emp";

DataTable dt = new DataTable();

objAdapter.SelectCommand = objCmd;
objAdapter.Fill(dt);

dataGridView1.DataSource = dt;
}
```

## 加入資料

當 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 繫結到資料來源時，你就不允許對它的 DataGridViewRow 或 DataGridViewColumn 進行操作。  若要變更 DataGridView 上的內容，你可以重新繫結到新的資料來源，或者對原始的資料來源做調整。  
```c#
tblPhoto photo = new tblPhoto();
photo.Title =  Path.GetFileNameWithoutExtension(file.FullName);
photo.Summary = photo.Title;
photo.FileName = file.FullName;
photo.MD5 = md5;
photo.SyncState = SyncStateEnum.New;

bsDetail.Add(photo);　　//在 bind source 中加入一筆新的資料。
```

## 更新資料庫

要將資料更新回資料庫，可以直接叫用 DataAdapter 的 [Update](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.update.aspx) 方法，不過在叫用 [Update](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.update.aspx) 方法。  同樣的，在叫用前當然給先給定一個 UpdateCommand 物件，不過，如果要更改的對向是單一資料表，可以直接簡單的指定一個 [SqlCommandBuilder](http://msdn.microsoft.com/zh-tw/library/system.data.sqlclient.sqlcommandbuilder.aspx) 物件， [SqlCommandBuilder](http://msdn.microsoft.com/zh-tw/library/system.data.sqlclient.sqlcommandbuilder.aspx) 物件，它就會依據 SelectCommand 內容，自動產生更新命令，這樣叫用 [Update](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.update.aspx) 方法才會有效。  否則會產生錯誤訊息：「當傳遞擁有已修改資料列的 DataRow 集合時，更新需要有效的 UpdateCommand」。  
```c#
private void btnUpdate_Click(object sender, EventArgs e)
{
//使用 CommandBuilder 來產生相對應的 Insert, Delete, Update SQL語法
var cmdBuilder = new SqlCommandBuilder();
cmdBuilder.DataAdapter = objAdapter;  
objAdapter.Update(((DataTable)dataGridView1.DataSource));
}
```
## 參考資料  

- [DataGridView 控制項 (Windows Form)](http://msdn.microsoft.com/zh-tw/library/vstudio/e0ywh3cz.aspx)
- [DataGridView 控制項概觀 (Windows Form)](http://msdn.microsoft.com/zh-tw/library/vstudio/k39d6s23%28v=vs.110%29.aspx)
- [DataGridView 控制項架構 (Windows Form)](http://msdn.microsoft.com/zh-tw/library/vstudio/dkdz8z4e.aspx)
- [如何：建立未繫結的 Windows Form DataGridView 控制項](http://msdn.microsoft.com/zh-tw/library/vstudio/5s3ce6k8%28v=vs.110%29.aspx)
- [如何：將資料繫結至 Windows Form DataGridView 控制項](http://msdn.microsoft.com/zh-tw/library/vstudio/fbk67b6z%28v=vs.110%29.aspx)
- [如何：自訂 DataGridView 控制項中的儲存格和資料行](http://msdn.microsoft.com/zh-tw/library/vstudio/7fb61s43%28v=vs.110%29.aspx)
- [如何：自訂裝載在 DataGridView 儲存格中的控制項](http://msdn.microsoft.com/zh-tw/library/vstudio/7tas5c80%28v=vs.110%29.aspx)
- [Windows Form DataGridView 控制項的資料顯示模式](http://technet.microsoft.com/zh-tw/subscriptions/cd28yf6d%28v=vs.90%29.aspx)
-