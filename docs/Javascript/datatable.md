---
title: DataTables介紹
layout: default
parent: Javascript
nav_order: 1
description: "DataTables介紹。"
date: 2025-06-12
tags:
  - DataTables
---
相關套件

## 初始化設定

![Datatable Style 1](images/datatable-style-1.png)

```js
var myTable = $("#MyTable").DataTable({
    ordering : true,
    lengthChange: true,
    searching: true,
    info: true,
    paging: true
});
```

## ＤataTable 常手設定

下方內容說明，使用這個DataTable設定當作範本。
```js
myTable = $("#MyTable").DataTable({
    ajax: {
        url: '@Url.Action("GetAnnualRevenue", "Dashboard")',
        type: "POST",
        data: {'companyid':237, 'groupby' : 'm'},
    },
    //ordering : false,        //設定整個Table不使用排序功能
    lengthChange: false,
    searching: false,
    info: false,
    paging: true,

    responsive: true,
    deferRender: true,      //第2頁以後的資料會廷遲載入, 適合大量資料載入, 2.X版預設值為true, 1.X版預設值為false
    destroy: false,

    //凍結設定
    fixedColumns: true,     //設定凍結, 預設標題列和第一欄 (paging必須為true)
    scrollCollapse: true,
    scroller: true,
    scrollY: 200,           //Y捲軸高度200, 超過才顯示Y捲軸

    columns: [
        {
            "sTitle": "年度",
            "mData": "Year",
            "sClass": "text-center", //這個舊式寫法,class只會作用在資料列,標題列沒有效果
        },
        {
            title: "一月",
            data: "M1",
            className: "text-end",  //這個寫法,class同時作用在資料列和標題列
            orderable: false,       //設定這個Column不使用排序功能
            searchable: false,      //這個Column的資料不列入搜尋範圍
            width: 100
        },
        {
            title: '二月',
            data: 'M2',
            className: "text-end",
            render: function (data, type, row) {
                var number = DataTable.render.number(',', '.', 0, '').display(data);
                if (data == null)
                    return '';
                else if (row.M2>=row.M1)
                    return `<span style="color:blue">${number}</span>`;
                else
                    return `<span style="color:green">${number}</span>`;
            }
        },
        {
            title: '三月',
            data: 'M3',
            className: "text-end",
            render: function (data, type, row) {
                var number = DataTable.render.number(',', '.', 0, '').display(data);
                if (data == null)
                    return '';
                else if (data>1000)
                    return `<span style="color:green">${number}</span>`;
                else
                    return `<span style="color:darkgray">${number}</span>`;
            }
        },
        { data: 'M4',   title: '四月',　className: "text-end", render: $.fn.dataTable.render.number(',', '.', 0, '') },
        { data: 'M5',   title: '五月',　className: "text-end", render: $.fn.dataTable.render.number(',', '.', 0, '') },
        { data: 'M6',   title: '六月',　className: "text-end", render: $.fn.dataTable.render.number(',', '.', 0, '') },
        { data: 'M7',   title: '七月',　className: "text-end", render: $.fn.dataTable.render.number(',', '.', 0, '') },
        { data: 'M8',   title: '八月',　className: "text-end", render: $.fn.dataTable.render.number(',', '.', 0, '') },
        { data: 'M9',   title: '九月',　className: "text-end", render: $.fn.dataTable.render.number(',', '.', 0, '') },
        { data: 'M10',  title: '十月',　className: "text-end", render: $.fn.dataTable.render.number(',', '.', 0, '') },
        { data: 'M11',  title: '11月',　className: "text-end", render: $.fn.dataTable.render.number(',', '.', 0, '') },
        { data: 'M12',  title: '12月',　className: "text-end", render: $.fn.dataTable.render.number(',', '.', 0, '') },
        { data:'Total', title: '合計',　className: "text-end", render: $.fn.dataTable.render.number(',', '.', 0, '') },
        {
            title : "功能", data: null,
            render: function (data, type, row) {
                var link = "";
                link += `<button class='btn btnUpdate btn-success btn-xs' type='button'>&nbsp修改</button>`;
                link += `<button class='btn btnDelete btn-danger btn-xs' type='button'>&nbsp刪除</button>`;
                return link;
            }
        }
    ],

    columnDefs: [
        {
            targets: 0,
            width: 100
        },
        {
            targets: 'nosort',
            orderable: false
            // class有nosort欄位, 不排序
            // PS.這個方法只適用HTML中有直接設定標題列,並指定class，若是由 Datatable 產生標題列, 則無效.
        },
        {
            targets: [ 2, 3 ],
            orderable: false   //class有Revenue的欄位, 不排序
        }
    ],

});
```

## 排序

### 整個Table的排序
```js
ordering : false
```
### 單一欄位的排序
```js
columns[
	{
		orderable : false
	}
]
```
```js
columnDefs: [
	{
		// 找尋class有nosort欄位, 不排序 
		targets: 'nosort',
		orderable: false   			
		// PS.這個方法只適用HTML中有直接設定標題列,並指定class，若是由 Datatable 產生標題列, 則無效.
	},
	{
		// 第 3,4 欄不排序 
		targets: [ 2, 3 ],
		orderable: false   //class有Revenue的欄位, 不排序
	}
```
### 中文排序問題:	
DataTables 中文排序需要使用自訂排序規則來處理中文字元的比較。 可以透過 $.fn.dataTable.ext.oSort 來自訂排序方法，並使用 localeCompare() 來進行中文比較。 此外，還需要在 columnDefs 中指定該列使用自訂排序器。
```js

{ 
    data : 'KeyWord', 
    type : 'chinese' 
},

jQuery.extend(jQuery.fn.dataTableExt.oSort, {
	"chinese-asc": function (a, b) {
		return a.localeCompare(b, 'zh-Hant'); // 使用繁體中文排序
	},
	"chinese-desc": function (a, b) {
		return b.localeCompare(a, 'zh-Hant');
	}
});
```

## 設定資料型態
```js
$('#myTable').DataTable({
    columns: [
        { data: 'name', type: 'string' }, // Explicitly set as string
        { data: 'age', type: 'num' },    // Explicitly set as number
        { data: 'dob', type: 'date' }    // Explicitly set as date
    ]
});
```

## 設定格式
### 日期
```js
render: function (data, type, row) {
    return moment(row.OrderDate).format('YYYY/MM/DD');
}
```
### 數值
```js
render: function (data, type, row) {
    var number = DataTable.render.number(',', '.', 0, '').display(data);
    if (data == null)
        return '';
    else
        return number;
}
```

## 凍結設定
```js
fixedColumns: true,     // 設定凍結, 預設凍結標題列和第一欄 (paging必須為true)
scrollCollapse: true,   // 預設為false 是否開始滾軸功能控制X、Y軸
scroller: true,
scrollY: 200,           // Y捲軸高度200, 超過才顯示Y捲軸
```

## 廷遲載入
第2頁以後的資料會廷遲載入, 適合大量資料載入, 2.X版預設值為true, 1.X版預設值為false
```js
deferRender: true,
```

## 重載

1. 直接以 DataTable 裡頭的 ajax 設定，當做資料來源重新載入。
```js
myTable.ajax.reload();
myTable.page(0);  //重載後回到第一頁
```
重載後，若有需要也可以加入callback function.
```js
myTable.ajax.reload(function (json) {
	//callback function
});
```
2. 使用外部 ajax, 取得資料後, 餵給 DataTable.
```js
$.ajax({
	url: '@Url.Action("action", "controller")',
	type: 'POST',
	data: {'companyid':279, 'groupby' : 'm'},
	success: function (result, textStatus, jqXHR) {
		myTable.clear();
		myTable.rows.add(result.data);
		myTable.draw();
	},
});
```

## 破壞

設定 Datatable 重新載入時，必須整個重新建立。預設值：false。

```js
destroy: true,
```

## 事件(Events)	

### 搜尋事件
```js
mytable.on('search',function(){ 
    alert('開始搜尋');
});
```
### Click事件
```js
//Row Click Event
myTable.on('click','tr',function(){
    console.log('tr click, this =>' ,this);
    var data = myTable.row(this).data();
    console.log('tr click, data =>' , data);
});

//Button Click Event
myTable.on('click', '.btnUpdate', function () {
    var data = myTable.row($(this).closest('tr')).data();
    console.log('btnUpdate, data =>', data);
});

myTable.on('click', '.btnDelete', function () {
    var data = myTable.row($(this).closest('tr')).data();
    console.log('btnDelete, data =>',data);
});
```
![Datatable Click Tr](images/datatable-click-tr.png)
![Datatable Click Class](images/datatable-click-class.png)

### 載入完成事件
```js
"initComplete": function () {
    $("#row-count").html("(共" + this.api().data().length + "筆)");
    if (this.api().data().length < 11) {
        //筆數判斷
        ...
    }
    else {
        ...
    }
}
```

## 動作(Actions)
```js
myTable.clear().draw();   //清空
```
```js
$('#ProductName').keyup(function () {
    myTable.column(5).search($(this).val()).draw();  //顯示篩選後的結果
})
```

## 選取行
```js
myTable.on('click', 'tbody tr', function () {
    if (!$(this).hasClass('selected')) {
        myTable.$('tr.selected').removeClass('selected');
        $(this).addClass('selected');
        var rowIndex = $(this).closest('tr');
        var rowdata = myTable.row(rowIndex).data();
        ...
    }
});
```

