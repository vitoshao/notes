---
title: DataGridView 控制項(3)
layout: default
parent: WinForm
nav_order: 1
description: "DataGridView 控制項(3)"
date: 2016-01-15
tags: [WinForm]
postid: "7646746792411204734"
---
## 如何在設定 DataSource 屬性時，不要觸發 SelectionChanged 事件

當 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 設定 [DataSource](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.datasource.aspx) 屬性時，會引發 SelectionChanged 事件，而且會有二次。  一次是原本的選取列失效了，另一次是 Binding 後，會自動選取第一列，所以總共會發生次 SelectionChanged 事件。  若要停止這個事件，可以在 Binding 前先移除事件的訂閱或者使用一個 flag 讓程式識別。  
```c#
gv.SelectionChanged -= this.gv_SelectionChanged;
gv.DataSource = list;
gv.SelectionChanged += this.gv_SelectionChanged;
```

## 如何以程式選取指定的 Row

通常我們會點選 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 來操作變更選取資料列，如何想要透過程式碼來完成這個操作，可以參考以下做法。

#### highlight 選取列

想要 highlight 特定的資料列只要將該資料列的 Selected 設成 true 即可。
```c#
Grid.Rows[i].Selected = True
```

#### highlight 選取列，並且變更 CurrentRow 屬性

上面程式碼只會變更 highlight 的資料列，並不會變更 CurrentRow 屬性，除非你指定 CurrentCell 屬性。
```c#
Grid.CurrentCell = Grid.Rows[i].Cells[j];
```

所以完整模擬 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 取得 focus ，並選取一資料列，必須這麼做
```c#
Grid.CurrentCell = Grid.Rows[i].Cells[j]Grid.Rows[i].Selected = True
```

不過底下這一點很重

通常我們藉由 UI 上 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 的操作，可以在 SelectionChanged 事件裡透過 DataGridView.CurrentRow 屬性取得游標所在的資料列。  但是，若是透過程式碼變更 CurrentCell 屬性來移動游標，雖然也會引發 SelectionChanged 事件，但是它是發生在 CurrentCellChanged 事件之前。  所以，若你在 SelectionChanged 事件中讀取 DataGridView.CurrentRow 屬性，取得的是未變更前的資料。  

資料來源：https://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.currentcell(v=vs.100).aspx

## 如何不要有 Selection 產生的 highlight

當滑鼠點到 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 時，它就會產生一個 highlight ，可能是一整個列或者是一個儲存格。  有時為了畫面美觀，要如何不顯示這個 highlight 呢。最簡單的方法就是把 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 的 readonly 屬性設成 true ，不過這麼做使用者將無法執行 copy 等操作。  下面方法將 highlight 的背景色設定成 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 的背色是不錯的技巧，可以參考使用。  
```c#
gv.DefaultCellStyle.SelectionBackColor = gv.DefaultCellStyle.BackColor;
gv.DefaultCellStyle.SelectionForeColor = gv.DefaultCellStyle.ForeColor;
```

## DataGridView.DataSource 屬性

[DataGridView.DataSource](https://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.datasource%28v=vs.100%29.aspx)屬性，可用來取得或設定 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 的資料來源。它支援以下資料繫結模型：  

- [IList](https://msdn.microsoft.com/zh-tw/library/system.collections.ilist%28v=vs.100%29.aspx)介面，例：一維陣列。
- [IListSource](https://msdn.microsoft.com/zh-tw/library/system.componentmodel.ilistsource%28v=vs.100%29.aspx)介面，例：DataTable、DataSet類別。
- [IBindingList](https://msdn.microsoft.com/zh-tw/library/system.componentmodel.ibindinglist%28v=vs.100%29.aspx)介面，例：[BindingList&lt;T&gt;](https://msdn.microsoft.com/zh-tw/library/ms132679%28v=vs.100%29.aspx)類別。
- [IBindingListView](https://msdn.microsoft.com/zh-tw/library/system.componentmodel.ibindinglistview%28v=vs.100%29.aspx)介面，例： [BindingSource](https://msdn.microsoft.com/zh-tw/library/system.windows.forms.bindingsource%28v=vs.100%29.aspx) 類別。

只要實作這四種介面的實體，都可以當做 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 的資料來源，它們之間有一項很重要的差異就是前二種資料類型是一次性繫結，後二種資料類型是雙向繫結，也就是資料來源若發生變更時，UI上的資料也會自動變更，反之亦然。  
```c#
List<Catalog> list = null;

list = dbContext.Catalogs.Where(c => c.TweOtc == 1).ToList();
dataGridView1.DataSource = list;

// 若資料來源發生變更，必須重新繫結，資料才會顯示到 DataGridView
list.Add(new Catalog());
dataGridView1.DataSource = null;
dataGridView1.DataSource = list;
```
```c#
BindingList<Catalog> bsCatalog = null; 

var query = dbContext.Catalogs.Where(c => c.TweOtc == 1);
bsCatalog = new BindingListt<Catalog>(query.ToList());
dataGridView1.DataSource = null;
dataGridView1.DataSource = bsCatalog;

// 若資料來源發生變更，資料會自動顯示到 DataGridView ，不用再重新繫結
bsCatalog.Add(new Catalog());
```

## Binding EF entities to a DataGridView

如果要將 EF 的實體繫結到 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) ，你可以像上面的例子，直接將 LINQ 查詢指定給 [DataSource](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.datasource.aspx) 屬性。  之後若在 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 上進行變更，只要叫用 context.SaveChanges() 方法即可存回資料庫。  
```c#
private void bnGetData_Click(object sender, EventArgs e)
{
var query = from c in dbContext.Catalogs
where c.TweOtc == 1
select c;

dataGridView1.DataSource = null;
dataGridView1.DataSource = query.ToList();
}

private void bnSave_Click(object sender, EventArgs e)
{
dbContext.SaveChanges();
}
```

但是，在下面例子中，因為不想要顯示所有欄位，所以我們透過 new 關鍵字，回傳我們需要的欄位名稱，這時你會發現繫結後的 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 會是**唯讀**的。  這是因為 new 會建立一個**匿名型別**的物件，當 DataGridVidw 的資料來源是匿名型別串列時，其屬性會自動設定唯讀無法變更。  
```c#
var query = from c in dbContext.Catalogs
where c.TweOtc == 1
select new { c.CatalogID, c.CatalogName };

dataGridView1.DataSource = null;
dataGridView1.DataSource = query.ToList();
```

要處理這個問題，可以用以下幾種方式解決：  

##### 隱藏不要的欄位

你可以將 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 的 Column 屬性設為唯讀或者隱藏不要的欄位。  
```c#
dataGridView1.Columns[0].Visible = false;
```

##### 在 EF 中

##### 使用自訂類別對應

將 LINQ 篩選出來的結果，轉成我們自訂的類別，只要是具名型別，繫結到 [DataGridView](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.datagridview.aspx) 就沒有唯讀限制了。  
```c#
private class MyCatalog
{
public string CatalogID { get; set; }
public string CatalogName { get; set; }
}

List<MyCatalog> myCatalogList = null;
private void bnGetData_Click(object sender, EventArgs e)
{
var query = from c in dbContext.Catalogs
where c.TweOtc == 1
select new MyCatalog { CatalogID = c.CatalogID, CatalogName=c.CatalogName };
myCatalogList = query.ToList();

dataGridView1.DataSource = null;
dataGridView1.DataSource = myCatalogList;
}

private void bnSave_Click(object sender, EventArgs e)
{
// 這個方法，必須自行判斷 DataGridView 中的內容，並自行變更 context 中的相對應資料。

foreach (var myCatalog in myCatalogList)
{
var catalog = dbContext.Catalogs.Where(c => c.CatalogID == myCatalog.CatalogID).FirstOrDefault();
if (catalog != null)
{
catalog.CatalogName = myCatalog.CatalogName;
}
}

dbContext.SaveChanges();
}
```

## 使用 DataSource 的 DataGridView 如何手動增加一列

像下面這段 Code ，當 DataGridView 的 DataSource 繫結到某個資料來源時，如果你想手動增加一個資料列，那是不允許的。  
```c#
List<Catalog> listCatalogs = new List<Catalog>();
dataGridView1.DataSource = listCatalogs;

//無效，必須重新再 binding 一次可行
listCatalogs.Add(new Catalog());

//無效，會產生 error ，因為 DataGridView 已繫結，無法手動加入新的資料列。
dataGridView1.Rows.Add();
```

通常你必須重新繫結， DataGridView 才會產生變畫。
```c#
listCatalogs.Add(new Catalog());
dataGridView1.DataSource = null;
dataGridView1.DataSource = listCatalogs;
```

不過你可以改用 BindingSource 繫結，然後對 BindingSource 操作，來變更 DataGridView 。
```c#
BindingSource bs = new BindingSource();

dataGridView1.DataSource = bs;

bs.DataSource = listCatalogs;

bs.Add(new Catalog()); 　//新資料列會自動產生。
```

## 使用雙向繫結

List 只可以做到單向繫結，也就是若你對 List 清單異動，這個結果會反應到 UI 上。  但是反過來，若你在 UI 上新增一筆資料，該結果並不會反應到 List 清單。