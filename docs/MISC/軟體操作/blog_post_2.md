---
title: SyntaxHighlighter
layout: default
parent: 軟體操作
nav_order: 2
description: "SyntaxHighlighter"
date: 2012-03-08
tags: [MISC,軟體操作]
postid: "5703442946888790777"
---
這是一個網路上免費的樣式修改工具，可以用來將程式碼的文字，做關鍵字的Highlight  
下方為一些展示範例，另外其他的相關設定(如:是否顯示行號，toolbar...)，都可參考下行連結  
[http://alexgorbatchev.com/SyntaxHighlighter/manual/configuration/](http://alexgorbatchev.com/SyntaxHighlighter/manual/configuration/) 

底下是一些使用範例

#### brush:c#
```c#
private int iPageSize
{
get
{
if (BindGridView != null)
return BindGridView.PageSize;
else
return 10;
}
}
```

#### brush:xml
```xml
<body>
<form id="form1" runat="server">
<div class="code">

</div>
</form>
</body>
```

#### brush:css
h1,h2,h3,h4,h5,h6 { color: #384E73 } 
fieldset, img { border: 0 } 
ul { list-style: none } 
ol { list-style-type: decimal } 
caption, 
th { text-align: left } 
q:before, 
q:after { content: '' } 
a{color: #1364c4;text-decoration:none;}
a:hover { text-decoration: underline } 
a:visited { color: #1364c4 } 
a:active { color: #1364c4 } 
article, aside, details, figcaption, figure, footer, header, hgroup, menu, nav, section, summary { display: block }  
#### brush:vb
Dim objSqlConnection As SqlConnection = New SqlConnection(Web.Configuration.WebConfigurationManager.ConnectionStrings(pDBConnect).ToString())
Dim objSqlCommand As SqlCommand = New SqlCommand(pSQL, objSqlConnection)
''Dim myReader As SqlDataReader
Try
objSqlConnection.Open()
Return objSqlCommand.ExecuteReader(CommandBehavior.CloseConnection)
Catch ex As Exception
If (objSqlConnection.State &lt;&gt; ConnectionState.Closed) Then objSqlConnection.Close()
Return Nothing
Finally
objSqlCommand.Dispose()
End Try  
#### brush:sql
```sql
SELECT 
T.object_id table_id, C.column_id as column_id,
T.name TableName,C.column_id ColID,
CASE WHEN ISKCU.COLUMN_NAME IS NOT NULL THEN 'Y' ELSE '' END AS PK, 
CASE WHEN IC.object_id IS NOT NULL THEN 'Y' ELSE '' END AS IndexField,
C.name AS ColName,
C.system_type_id AS ColType,
TY.name AS ColTypeName,
ISC.CHARACTER_MAXIMUM_LENGTH AS ColLen,   
CASE WHEN C.is_nullable=1 THEN 'Y' ELSE '' END AS AllowNull, 
Convert(nvarchar(500),D.definition) AS DefaultValue,
Convert(nvarchar(500),IsNull(E.value,'')) AS Description
FROM         
sys.tables T
INNER JOIN sys.columns C ON C.object_id = T.object_id
INNER JOIN sys.types TY ON TY.user_type_id = C.user_type_id
INNER JOIN INFORMATION_SCHEMA.COLUMNS ISC ON ISC.TABLE_Name=T.name AND ISC.COLUMN_NAME = C.name
LEFT JOIN sys.extended_properties E ON E.major_id = T.object_id AND E.minor_id=C.column_id
LEFT JOIN sys.default_constraints D ON D.parent_object_id=T.object_id AND D.parent_column_id = C.column_id  
LEFT JOIN INFORMATION_SCHEMA.KEY_COLUMN_USAGE ISKCU ON ISKCU.TABLE_Name=T.name AND ISKCU.COLUMN_NAME=C.name AND ISKCU.CONSTRAINT_NAME Like 'PK%'
LEFT JOIN INFORMATION_SCHEMA.TABLE_CONSTRAINTS ISTC ON ISTC.CONSTRAINT_NAME=ISKCU.CONSTRAINT_NAME AND ISTC.CONSTRAINT_TYPE='PRIMARY KEY'
LEFT JOIN sys.index_columns IC ON IC.object_id=T.object_id AND IC.column_id=C.column_id
WHERE 1=1
AND T.name = 'NATURALPERSON'
```

#### brush:javascript
```javascript
XMLRPCMessage.prototype.setMethod = function(methodName){
if (!methodName) return;
this.method = methodName;
}

XMLRPCMessage.prototype.addParameter = function(data){
if (arguments.length==0) return;
this.params[this.params.length] = data;
}
```

#### 2012/8/22 增補

由於 Chrome 不能正確顯示有小數點的 em ，所以 Chrome 瀏覽器在顯示程式碼時會無故多出許多空白行，這個問題可以藉由修改 shCore.css 中的設定值解決。

找出底下這一段 CSS 
.syntaxhighlighter table td.gutter .line {
text-align: right !important;
padding: 0 0.5em 0 1em !important;
}  
修改成底下這一段 CSS 
.syntaxhighlighter table td.gutter .line {
text-align: right !important;
padding: 0 5px  !important;
}    
#### 2012/11/19 增補

如何反白程式碼

使用 highlight 屬性指定要反白的行號即可。
```xml
<pre class="brush: plain; highlight: 2">
1
2
3
</pre>
<pre class="brush: plain; highlight: [1, 3]">
1
2
3
</pre>
```
```c#
//全部平均
var result1 = from employee in employees.AsEnumerable()
group employee by "" into g
select new
{
AvgSalary = g.Average(employee => employee.Field<Int16>("Age"))
};

foreach (var value in result1)
{
myDebug.ResponseBR(value.AvgSalary.ToString());
}

//分組平均
var result2 = from employee in employees.AsEnumerable()
group employee by employee["FirstName"] into g
select new
{
FirstName = g.Key,
AvgSalary = g.Average(emp => emp.Field<Int16>("Age"))
};

foreach (var row in result2)
{
var key = row.FirstName;
var value = row.AvgSalary;
myDebug.ResponseBR("{0}=>{1}", key, value);
}
```