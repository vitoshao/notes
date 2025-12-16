---
title: DataGridView 控制項(2)
layout: default
parent: WinForm
nav_order: 3
description: "DataGridView 控制項(2)"
date: 2014-06-16
tags: [DotNetFramework,WinForm]
postid: "6547614841023351209"
---
## 如何使用自動完成功能

若資料欄位為 ComboBox 或 TextBox 控制項型態， DataGridView 有提供自動完成輸入的功能，如下圖。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj3miMv5TDSD1H5XZMVjqSMu7goygD6Jcx903sIsexdiY2mryapps6Ir66iAn_TNHwRFGq_BX-2nakwq-viPnhUBn-W0HmDKzlR6yq-cUebGjOcsxD7jZabY-Z9gIyIItZ4adZYPBlLlis/s344/datagridview-auto-complete.png)
```c#
AutoCompleteStringCollection DataCollection = new AutoCompleteStringCollection();

private void frmMaintain_Load(object sender, EventArgs e)
{
using (StockDataContext db = new StockDataContext())
{
//載入資料到 datagridview
var qry = db.Stocks.Select(stock => stock);
dataGridView3.DataSource = qry.ToArray();

//建立資料清單
foreach (var stock in qry)
DataCollection.Add(stock.StockName);
}
}

private void dataGridView3_EditingControlShowing(object sender, DataGridViewEditingControlShowingEventArgs e)
{
if (dataGridView3.CurrentCell.ColumnIndex==3)
{
TextBox autoText = e.Control as TextBox;
if (autoText != null)
{
autoText.AutoCompleteSource = AutoCompleteSource.CustomSource;  //設定自動完成功能的資料來源
autoText.AutoCompleteMode = AutoCompleteMode.Suggest;           //設定自動完成功能模式（Suggest => 顯示與輸入內容相關的輔助下拉式清單）
autoText.AutoCompleteCustomSource = DataCollection;             //設定自動完成功能的資料清單
}
}
}        
```

## 如何繫結 DataGridView 與 LINQ Query Result

因為 DataGridView 支援以下資料來源，所以可以將 LinQ Query 的結果轉成 List 即可。   

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhQvjB5GgqJ7uFVIU2rh8NV5AA-TyGzyNn8y6Vb2Xn9LdA5nrTQ7Hcv6ihoO7JW9oBGZSGm78hW3gJG5iiBLCy_DJ0cYp10jtolWsLOJkfOl802-nUnokaFiXX8hMWzTeeuPWfSIxDSwk4/s362/dgv2-binding-type.png)

### 繫結
```c#
using (StockDataContext db = new StockDataContext())
{
var qry = from Ob in db.Observe
join S in db.Stocks on Ob.StockID equals S.StockID
where Ob.ObserveID == "A2"
select new
{
Ob.StockID,
Ob.OrderNum,
Ob.ObserveID,
S.StockName
};
dataGridView1.DataSource = qry.ToList();
}
```

### 使用自訂類別繫結

上面這個例子，當資料繫結到 DataGridVIew 之後，你會發覺控制項會自動呈現唯讀的狀態。  這是因為使用 select new 所回傳的是一個匿名物件，匿名物件的 property 都是唯讀的，所以 DataGridVIew 也就變成唯讀狀態。  如果要讓 DataGridVIew 處於可被修改的狀態，你只要先自訂一個類別來轉換即可。例如：  
```c#
private class MyObserve
{
public string StockID { get; set; }
public int OrderNum { get; set; }
public string ObserveID { get; set; }
public string StockName { get; set; }
}

using (StockDataContext db = new StockDataContext())
{
var qry = from Ob in dc.Observe
join S in dc.Stocks on Ob.StockID equals S.StockID
where Ob.ObserveID == "A2"
select new MyObserve
{
ObserveID = Ob.ObserveID,
OrderNum = Ob.OrderNum,
StockID = Ob.StockID,
StockName = S.StockName
};
dataGridView1.DataSource = qry.ToList();
｝
```

### 更新資料庫

如果使用 DataTable 繫結 DataGridView ，當在 DataGridView 做完異動後，你可以將該 DataTable 丟給 SqlDataAdapter.Update() 方法，它就會檢查有異動過的資料，自動產生更新的命令進行資料庫更新。  可是當繋結的對象是 Linq Result ，它是轉成陣列或清單資料後，再顯示到 DataGridView 上，所以你沒有簡單的方法將異動過的資料，直接更新回資料庫端。  所以新增、修改、刪除等操作，都必須額外的步驟來處理。  例如若要更新，就必須先透過查詢取得資料，將資料變更後，再呼叫 SubmitChanges 回存到資料庫。  
```c#
private void dataGridView2_RowValidated(object sender, DataGridViewCellEventArgs e)
{
dataGridView2.Rows[e.RowIndex].Cells[0].Selected = true;
string StockID = dataGridView2.Rows[e.RowIndex].Cells["StockID"].Value.ToString();

var qry = from Ob in dc.Observe
where Ob.ObserveID == "A2" && Ob.StockID == StockID
select Ob;

var observe = qry.FirstOrDefault();
if (observe != null)
{
observe.OrderNum = (int)dataGridView2.Rows[e.RowIndex].Cells["OrderNum"].Value;
}
dc.SubmitChanges();
}
```

## DataGridView 事件的先後順序

DataGridView 事件非常多，了解這些事件發生的先後順序，對寫程式上還是必須的。  例如上面一個例子，原本是希望在游標在發生 RowLeave 時，去更新資料庫，不過當 RowLeave 發生時， Cell EndEdit 還沒發生，所以會無法取得的使用者更新後的資料。  

##### 當同一列的儲存格發生游標變動時

- Cell Leave (old cell)
- Cell Validating/ed (old cell)
- Cell EndEdit (old cell)
- Cell Enter (new cell)
- Cell BeginEdit (new cell)

##### 當游標變動發生在不同列時

- Cell Leave (old cell), Row leave (old row)
- Cell Validating/ed (old cell)
- Cell EndEdit (old cell)
- Row Validating/ed (old row)
- Row Enter (new row)
- Cell Enter (new cell)
- Cell BeginEdit (new cell)

## 如何使用 Button 型別的欄位

## 如何使用 DateTimePicker 型別的欄位

## 如何使用 Image 型別的欄位

## DataGridView 欄寬設定

### 調整欄位寬度

DataGridView.AutoResizeColumns = DataGridViewAutoSizeColumnsMode.AllCells    AutoResizeColumns()  AllowUserToResizeColumns

### 如何設定列行首（Row Head）寬度

### 如何在 RowHeader 顯示序號

DataGridView.Rows.HeaderCell 是用來表示首行的儲存格，所以只要利用 CellFormatting 事件發生時，給定欄位值即可。  
```c#
private void dgvMyTradeRecords_CellFormatting(object sender, DataGridViewCellFormattingEventArgs e)
{
if (e.ColumnIndex == 0) 
{
dgvMyTradeRecords.Rows[e.RowIndex].HeaderCell.Value = Convert.ToString(e.RowIndex + 1);
}
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjG5WGHhG7YEBKtJBtAU7_YrklBHxlXN5e5_PZObMazhfufNdOJQrHttxG0syh79S2Vt6vGVd5Z4QVJyTP-di_IlPt2y5vF6DiLtDhkB7UCiIccEM-NVu6kGpnafy9M-bpbvEJpTkp-460/s228/dgv-HeaderCell.png)

### 調整資料列高度

DataGridView 有個樣版物件 RowTmplate ，可以用來管理 Row 的樣式，包含 Row 的高度等屬性。  不過，要注意的是，這個物件就只是一個樣版，是給新增加的 Row 做為參考用的樣版，所以若程式執行中才變更這個樣版，對原先已存在的資料列是無效的。  

若要在程式執行中列資料列高度的調整，則必須針對每一個 Row 重新設定 Height 屬性。  另外，若調整列高的目的在於配合資料內容的多寡，那麼可以將 AutoSizeRowsMode 屬性設為 DisplayedCells 或 AllCells 等屬性值。