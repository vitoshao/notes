---
title: 使用 ADO.NET Connected 類別
layout: default
parent: ADO.NET and XML
nav_order: 2
description: "使用 ADO.NET Connected 類別"
date: 2012-09-16
tags: [Web,ASPNET,ADO.NET and XML]
postid: "401918190423614055"
---
# DBConnection

- 與 Connecton 相關的物件，最主要的功能就是移動「Disconnected 相關物件」與「資料儲存區」間的資料。
- 不同的資料儲存區，會使用不同的資料提供者，如 SqlClientDataProvider 、 OleDbDataProvider 、 OracleClientDataProvider 。
- 要建立一個連線，必須要有一個有效的連線字串。
- 傳送指令到資料儲存區前，必須開啟連線。
- 使用 Using 區塊可以在程式碼結束時，自動處置連線。
- 若使用同一個連線字串建立連線時，系統會使用 ThreadPool 中的連線物件，減少另外建立連線的資源消耗。
```c#
protected SqlConnection CreateConnection()
{
SqlConnection connection = new SqlConnection();
connection.ConnectionString =
@"
Data Source = VITO-2011W7\W7_SQLSVR_2008;
Initial Catalog = TestDB; 
User ID=vito; 
Password=vito1234;
";
return connection;
}

protected void btnCreateConnection_Click(object sender, EventArgs e)
{
SqlConnection connection = CreateConnection();
try
{
connection.Open();
}
catch (Exception ex)
{
myDebug.Write(ex.ToString());
}
finally
{
connection.Close();
}
}
```

# DbCommand

- [DbCommand](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbcommand.aspx) 可以傳送一個或多個結構化的查詢語言。
- [DbCommand](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbcommand.aspx) 必須透過一個開啟的連線，才能連結到資料儲存區。
- [DbCommand](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbcommand.aspx) 必須指明 CommandText 及 CommandType 屬性， CommandType 屬性預設為 Text 。
- [DbCommand](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbcommand.aspx) 可以透過 DbParameter 物件傳遞參數資料。

## 建立 DbCommand
```c#
SqlCommand oCmd = oConn.CreateCommand();
oCmd.CommandType = CommandType.Text;
oCmd.CommandText = "SELECT top 100 \* From tblUser";
```

## 執行 DbCommand

- [ExecuteNonQuery](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbcommand.executenonquery.aspx) ：執行 SQL 陳述式，不傳回值。
- [ExecuteScalar](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbcommand.executescalar.aspx) ：執行 SQL查詢陳述式，並傳回查詢結果集中第一個資料列的第一個資料行。
- [ExecuteReader](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbcommand.executereader.aspx) ：執行 SQL 陳述式，並傳回 DbDataReader。
```c#
using (SqlConnection oConn = CreateTestDBConnection())
{
oConn.Open();

//===========================================================
// ExecuteNonQuery
//===========================================================
DbCommandoCmd1 = oConn.CreateCommand();
oCmd1.CommandType = CommandType.Text;
oCmd1.CommandText = "Insert Employee(Name,Salary) Values ('vito','1000')";
oCmd1.ExecuteNonQuery();

//===========================================================
// ExecuteScalar
//===========================================================
DbCommandoCmd2 = oConn.CreateCommand();
oCmd2.CommandType = CommandType.StoredProcedure;
oCmd2.CommandText = "usp_InsertEmployee";
int id = Convert.ToInt32( oCmd2.ExecuteScalar() );

//===========================================================
// ExecuteReader
//===========================================================
DbCommandoCmd3 = oConn.CreateCommand();
oCmd3.CommandType = CommandType.Text;
oCmd3.CommandText = "SELECT top 100 \* From tblUser";
DbDataReader oReader = oCmd3.ExecuteReader();
}
}
```

## 使用 DbDataReader 物件

- 表示伺服器端(server-side)資料指標。
- 必須由 [ExecuteReader](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbcommand.executereader.aspx) 回傳，不能自已建立。
- 只能順向唯讀。(forward-only & read-only)
- 透過 [Read](http://msdn.microsoft.com/zh-tw/library/system.xml.xmltextreader.read.aspx) 方法，可以擷取資料至緩衝區。但一次僅能處理一個資料列。
- 使用 DataReader 時，相關聯的 Connection 會忙於服務 DataReader，所以無法在相同 Connection 上再執行其他作業，直到 DataReader 關閉為止。
```c#
SqlCommand oCmd = oConn.CreateCommand();
oCmd.CommandType = CommandType.Text;
oCmd.CommandText = "SELECT top 10000 \* From tblUser";

oCmd.Connection = oConn;
oConn.Open();
oReader = oCmd.ExecuteReader();

//順向逐筆讀取
while (oReader.Read())
{
if (oReader["UserName"].ToString() == "vito")
{
myDebug.WriteLine("Find");
break;
}
}

//將 DataReader 轉成 DataTable 
DataTable dTable = new DataTable();
dTable.Load(oReader);
```

## 使用多重作用結果集 ( Multiple Active Result Sets , MARS )

- 通常在建立伺服器指標之後，若這個指標沒有關閉，在同一個連線之下，就無法再執行任何 Command 。
- 也就是若建立的 SqlDataReader 沒有關閉前，就無法再對資料庫下命令。
- 不過，從 Server2005 之後，開始支援一個連線多個指令，搭配連線字串的設定，即可啟用就 MARS。
- 但是 MARS 僅能簡化設計的工作量，並不能增加系統效能，反而會增加系統的負載量，所以請勿隨意使用。
```c#
protected SqlConnection CreateMARSConnection()
{
SqlConnection connection = new SqlConnection();
connection.ConnectionString =
@"
Data Source = VITO-2011W7\W7_SQLSVR_2008;
Initial Catalog = TestDB; 
User ID = vito; 
Password = vito1234;
MultipleActiveResultSets = True;
";
return connection;
}
protected void btnMARS_Click(object sender, EventArgs e)
{
string UserID = "";
string UserName = "";
using (SqlConnection oConn = CreateMARSConnection())
{
oConn.Open();

SqlCommand oCmd1 = new SqlCommand("SELECT top 10 UserID From tblUser", oConn);
SqlDataReader Reader1 = oCmd1.ExecuteReader();

SqlCommand oCmd2 = new SqlCommand();
oCmd2.Connection = oConn;
oCmd2.CommandText = "SELECT UserName From tblUser Where UserID = @UserID";

while (Reader1.Read())
{
UserID = Reader1["UserID"].ToString();
oCmd2.Parameters.Clear();
oCmd2.Parameters.Add(new SqlParameter("@UserID", UserID));
UserName = oCmd2.ExecuteScalar().ToString();
//若沒有啟用 MARS ，會產生「已經開啟一個與這個 Command 相關的 DataReader，必須先將它關閉。」
}
}
}
```

## 使用 SqlBulkCopy 物件執行大量複製作業

[SqlBulkCopy](http://msdn.microsoft.com/zh-tw/library/system.data.sqlclient.sqlbulkcopy.aspx) 這個類別提供多個 [WriteToServer](http://msdn.microsoft.com/zh-tw/library/system.data.sqlclient.sqlbulkcopy.writetoserver.aspx) 多載方法，分別是針對不同型態的資料進行複製作業。它是一個高效率的資料匯入方法，但匯入對象僅限 SQL Server 。
```c#
public void WriteToServer(DataRow[] rows);
public void WriteToServer(DataTable table);
public void WriteToServer(IDataReader reader);
public void WriteToServer(DataTable table, DataRowState rowState);
```
```c#
protected void btnSqlBulkCopy_Click(object sender, EventArgs e)
{
using (SqlConnection oConn = CreateTestDBConnection())
{
oConn.Open();

SqlCommand oCmd = oConn.CreateCommand();
oCmd.CommandType = CommandType.Text;
oCmd.CommandText = "SELECT top 10000 UserName From tblUser";

//建立目標資料庫的連線
SqlConnection bulkCopyConnection = CreateTestDBConnection();
bulkCopyConnection.Open();

//執行 BulkCopy
SqlDataReader rdr = oCmd.ExecuteReader();
SqlBulkCopy bc = new SqlBulkCopy(bulkCopyConnection);
bc.DestinationTableName = "tblUserName";
bc.WriteToServer(rdr);

//關閉連線
bulkCopyConnection.Close();
}
}
```

# DbDataAdapter

若要執行 SQL 指令，你只要建立 [DbCommand](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbcommand.aspx) 物件即可。  若想取得執行結果，也可以叫用 [DbCommand](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbcommand.aspx) 的 [ExecuteReader](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbcommand.executereader.aspx) 方法，  但這個方法回傳的資料是一個 [DbDataReader](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdatareader.aspx) 物件，它是一個「連線狀態」的物件，也就是在讀取資料時，必須與資料來源一直保持連接的狀態。  

若想要更進一步的操作資料庫，就必須藉由 [DataAdapter](http://msdn.microsoft.com/zh-tw/library/system.data.common.dataadapter.aspx) 來幫助。  [DataAdapter](http://msdn.microsoft.com/zh-tw/library/system.data.common.dataadapter.aspx) 是由一個 Connection 和數個 SQL Commnad 所組合成的物件，  [DataAdapter](http://msdn.microsoft.com/zh-tw/library/system.data.common.dataadapter.aspx) 是採用離線存取的方式，  在設定好想關資訊後，就可以透過 Fill 方法去取得資料，它會自動開啟連線，並且在撈完資料後關閉連線。  你可以藉由它將查詢結果填入 [DataSet](http://msdn.microsoft.com/zh-tw/library/system.data.dataset.aspx) 或 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 等離線狀態的物件。  也可以利用它將異動後的資料更新回資料庫去。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgTg0PIpqvHF62zPSD6wzr55vPkQNs-qvfzBPXXJ0Y_yiQhO8DzUK5-ySJ529FsTGhr_IEfAUQccFP0GCTbTbgy46wzs-TTlMaOdu9z4sOi_1kVFqM0W449AtySzJgvxFZq6QM_FV5AhyphenhyphenM/s578/IC30745.gif)

##  SqlDataAdapter 

#### 建構子
```c#
public SqlDataAdapter();
public SqlDataAdapter(SqlCommand selectCommand);
public SqlDataAdapter(string selectCommandText, SqlConnection selectConnection);
public SqlDataAdapter(string selectCommandText, string selectConnectionString);
```

#### 屬性

[DbDataAdapter](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.aspx) 物件中包含了 SelectCommand、InsertCommand、UpdateCommand、DeleteCommand 四種屬性，用來擷取或更新資料庫由的資料。

- [SelectCommand](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.selectcommand.aspx) ：a command used to select records in the data source.
- [InsertCommand](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.insertcommand.aspx) ：a command used to insert new records into the data source.
- [UpdateCommand](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.updatecommand.aspx) ：a command used to insert update records into the data source.
- [DeleteCommand](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.deletecommand.aspx) ：a command used to insert delete records into the data source.
- [UpdateBatchSize](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.updatebatchsize.aspx)：啟用或停用批次處理。
- 1：停用批次。此為預設值，將變更的資料列一列一列的回傳處理。
- 0：沒有限制批次大小。
- n：每次以 n 筆資料批次傳送處理。 (n&gt;1)

## Fill 方法

[Fill](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.fill.aspx) 方法是用來將資料由資料庫移到指定的物件。  它會依據 [DbDataAdapter](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.aspx) 中的 [SelectCommand](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.selectcommand.aspx) 中的指令，將資料是由資料庫載入到 [DataTable](http://msdn.microsoft.com/zh-tw/library/system.data.datatable.aspx) 之中。  
```c#
public override int Fill(DataSet dataSet)；
public int Fill(DataTable dataTable);
public int Fill(DataSet dataSet, string srcTable);
protected virtual int Fill(DataTable dataTable, IDbCommandcommand, CommandBehavior behavior);
......
```
```c#
protected void btnFill_Click(object sender, EventArgs e)
{
using (SqlConnection connection = CreateTestDBConnection())
{
connection.Open();

SqlCommand cmd = connection.CreateCommand();
cmd.CommandText = "SELECT UserID, UserName FROM tblUser";

SqlDataAdapter adapter = new SqlDataAdapter(cmd);
DataSet dsUsers = new DataSet("Users");
adapter.Fill(dsUsers, "Users");  //將查詢結果填到DataSet

Tools.bindDataToGridView(gv1, dsUsers, "Users");
}
}
```

## Update 方法
```c#
public int Update(DataRow[] dataRows);
public override int Update(DataSet dataSet);
public int Update(DataTable dataTable);
protected virtual int Update(DataRow[] dataRows, DataTableMapping tableMapping);
public int Update(DataSet dataSet, string srcTable);
```

[Update](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.update.aspx) 方法是用來將變更的資料回存到資料庫。  它會依據 [DataRow](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.aspx) 的狀態值判斷，只要 [DataRow.RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) 值不是 [Unchanged](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) ，才會進行傳送。  也就是它只傳送異動過的資料回資料庫進行更新處理。  

使用 [Update](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.update.aspx) 方法時，原則上必須先設定好 [DbDataAdapter](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.aspx) 物件的四個指令屬性 (SelectCommand、InsertCommand、UpdateCommand、DeleteCommand)，系統才會知道應該如何回存資料。  不過，若針對單一資料表，可以使用 [DbCommandBuilder](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbcommandbuilder.aspx) 物件，它為根據 [SelectCommand](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.selectcommand.aspx) 指令物件內容，快速的自動建立好其他三種指令物件。  
```c#
protected void btnUpdate_Click(object sender, EventArgs e)
{
using (SqlConnection connection = CreateTestDBConnection())
{
connection.Open();

SqlCommand cmdSelect = connection.CreateCommand();
cmdSelect.CommandText = "SELECT Top 10 UserID, UserName FROM tblUser";

//設定 SelectCommand
SqlDataAdapter adapter = new SqlDataAdapter(cmdSelect);

//建立 SqlCommandBuilder
SqlCommandBuilder cmdBuilder = new SqlCommandBuilder(adapter);

//將查詢結果填到DataSet
DataSet dsUsers = new DataSet("Users");
adapter.Fill(dsUsers, "Users");

Tools.bindDataToGridView(gv1, dsUsers, "Users");

//測試更新資料
dsUsers.Tables[0].Rows[0]["UserName"] = dsUsers.Tables[0].Rows[0]["UserName"].ToString() + "1";
adapter.Update(dsUsers, "Users");

//原本應該提供一個 DbCommand給 UpdateCommand 屬性
//不過這個簡單例子，我們可以使用 SqlCommandBuilder 幫忙即可
//底下檢視一下，這些自動建立的 DbCommand的樣子

myDebug.WriteLine(cmdBuilder.GetInsertCommand().CommandText);   //該內容是在第14行指令後才建出來的
myDebug.WriteLine(cmdBuilder.GetUpdateCommand().CommandText);
myDebug.WriteLine(cmdBuilder.GetDeleteCommand().CommandText);

//INSERT INTO [Products] ([ProductName]) VALUES (@p1)
//UPDATE [Products] SET [ProductName] = @p1 WHERE (([ProductID] = @p2) AND ([ProductName] = @p3))
//DELETE FROM [Products] WHERE (([ProductID] = @p1) AND ([ProductName] = @p2))
}
}
```

## 批次更新

當執行 [Update](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.update.aspx) 方法，系統是將已變更的資料列，一列一列 (row-by-row) 的傳回資料庫以進行更新，若想增加效能，可以設定 [UpdateBatchSize](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.updatebatchsize.aspx) 屬性，將傳送筆數增加。  UpdateBatchSize 屬性預設值為１，若設成 0 表示沒有限制。  

If the [RowState](http://msdn.microsoft.com/zh-tw/library/system.data.datarow.rowstate.aspx) is anything but [Unchanged](http://msdn.microsoft.com/zh-tw/library/system.data.datarowstate.aspx) , the [Update](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.update.aspx) method sends the change to the database.

#### RowUpdateEvent 事件

這個事件發生在 [Update](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.update.aspx) 方法執行之後，我們可以訂閱這個事件，來檢測批次更新是否有一次傳送多筆資料。  
```c#
protected void btnBatch_Click(object sender, EventArgs e)
{
using (SqlConnection connection = CreateTestDBConnection())
{
SqlCommand cmdSelect = connection.CreateCommand();
cmdSelect.CommandType = CommandType.Text;
cmdSelect.CommandText = "SELECT Top 10 UserID, UserName FROM tblUser";

SqlDataAdapter da = new SqlDataAdapter(cmdSelect);
SqlCommandBuilder bldr = new SqlCommandBuilder(da);
DataSet dsProducts = new DataSet();

connection.Open();

//==========================================================
// Fill Data
//==========================================================
da.Fill(dsProducts);
Tools.bindDataToGridView(gv1, dsProducts);

//==========================================================
// Update Data  (共３筆)
//==========================================================
dsProducts.Tables[0].Rows[0]["UserName"] = System.DateTime.Now.ToString();
dsProducts.Tables[0].Rows[1]["UserName"] = System.DateTime.Now.ToString();
dsProducts.Tables[0].Rows[2]["UserName"] = System.DateTime.Now.ToString();

// handle RowUpdated Event
da.RowUpdated += SqlRowUpdated;

da.UpdateBatchSize = 2; 
da.Update(dsProducts);
}
}

//這個事件會發生二次
//第一次 RecordsAffected=2
//第一次 RecordsAffected=1

protected void SqlRowUpdated(object sender, SqlRowUpdatedEventArgs e)
{
DataRow dr = e.Row;
myDebug.WriteLineBR(e.RecordsAffected.ToString());
}
```

# DbProviderFactory

## 什麼是 DbProviderFactory 類別

[DbProviderFactory](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbproviderfactory.aspx) 類別是建立 DataProvider 的實作，透過它可以用來動態指定資料庫提供者。

[DbProviderFactory](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbproviderfactory.aspx) 類是 ADO.NET 中的一個 DBProvider 的基底類別，各種特定的 DBProvider 都繼承它，它的架構就像 Design Pattern 中的 Simple Factory 。  我們可以利用這個類別，在程式中不直接指定使用哪一個 DBProvider ，再利用設定檔中設定，然後於執行階段時再決定。  
```c#
public abstract class DbProviderFactory
{
protected DbProviderFactory();

public virtual DbConnection CreateConnection();
public virtual DbDataAdapter CreateDataAdapter();
public virtual DbCommandCreateCommand();
public virtual DbCommandBuilder CreateCommandBuilder();
public virtual DbParameter CreateParameter();

public virtual DbConnectionStringBuilder CreateConnectionStringBuilder();
public virtual DbDataSourceEnumerator CreateDataSourceEnumerator();
public virtual CodeAccessPermission CreatePermission(PermissionState state);
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjRg6wuXpO6nbAXi5iKeH414wODAFzYY0DUnevoSgUUUB2y1S4vZ1S8XiV8l0phC9NsiDr0-qWZmlViDW4uwfAsOL7zsNTs7wkQoS3-thj1FgrPTmlRnNSpxptVWjXhfyTnQx5pM2ToWF0/s562/DBProviderFactory.png)

## 建立 DbProviderFactory 類別
```c#
SqlConnection connection = new SqlConnection();
connection.ConnectionString = ConfigurationManager.ConnectionStrings["NorthWindConnectionString"].ConnectionString;
connection.Open();
connection.Close();
```
```c#
string InvariantName = "System.Data.SqlClient";

DbProviderFactory dbFactory = DbProviderFactories.GetFactory(InvariantName);
DbConnection connection = dbFactory.CreateConnection();
connection.ConnectionString = ConfigurationManager.ConnectionStrings["NorthWindConnectionString"].ConnectionString;
connection.Open();
connection.Close();
```

# 例外狀況的處理
```c#
protected void btnExceptionHandle_Click(object sender, EventArgs e)
{
using (SqlConnection oConn = CreateTestDBConnection())
{
try
{
SqlCommand oCmd = oConn.CreateCommand();
oCmd.CommandText = "RaisError('Custom Error',18,1)";

oConn.Open();
oCmd.ExecuteNonQuery();
}
catch (DbException ex)
{
foreach (DictionaryEntry item in ex.Data)
{
myDebug.WriteLine(item.Value.ToString());
}
}
}
}
```

# ADO.NET 常見的 DB 存取錯誤訊息

下列幾項是程式更新資料庫時常會出現的錯誤訊息。

## 1. 當傳遞擁有已修改資料列的 DataRow 集合時，更新需要有效的 UpdateCommand。

當呼叫 DataAdapter 的 [Update](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.update.aspx) 方法時，如果沒有指定適當的 [UpdateCommand](http://msdn.microsoft.com/zh-tw/library/system.data.sqlclient.sqldataadapter.updatecommand.aspx) 物件給 DataAdapter 的話就會發生這個錯誤。  最簡單的解法就是使用 CommandBuilder 來自動產生相對應的 Insert, Delete, [Update](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.update.aspx) SQL 語法。  
```c#
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

private void btnUpdate_Click(object sender, EventArgs e)
{
//使用 CommandBuilder 來產生相對應的 Insert, Delete, Update SQL語法
var cmdBuilder = new SqlCommandBuilder();
cmdBuilder.DataAdapter = objAdapter;  
objAdapter.Update(((DataTable)dataGridView1.DataSource));
}
```

## 2. 並行違規: UpdateCommand 已經影響必須是 1 記錄的 0。

這個錯誤會發生在同時有兩個人在編輯一筆資料的時候，若第一個人先叫用了 [Update](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.update.aspx) ，第二個人若再叫用 [Update](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.update.aspx) 就會產生「並行違規: [UpdateCommand](http://msdn.microsoft.com/zh-tw/library/system.data.sqlclient.sqldataadapter.updatecommand.aspx) 已經影響必須是 1 記錄的 0。」的錯誤。  （Concurrency violation: the [UpdateCommand](http://msdn.microsoft.com/zh-tw/library/system.data.sqlclient.sqldataadapter.updatecommand.aspx) affected 0 of the expected 1 records.）  也就是說，程式碼預期資料庫應該會回應 1 筆資料被更新，但是卻回應 0 筆資料被更新，這是不對的，所以產生該項錯誤。  

之所以會這個樣子，是因為使用了 [SqlCommandBuilder](http://msdn.microsoft.com/zh-tw/library/system.data.sqlclient.sqlcommandbuilder.aspx) 自動產生的更新命令，不管資料是否包含鍵值，這個更新命令都會更據最後一次 AcceptChanges 時的**所有欄位的資料**當做 Where 條件去執行變更。例如：  
```c#
private void btnUpdate2_Click(object sender, EventArgs e)
{
int idx = dt.Rows.Count - 1;

// 若該筆資料一開始為 (ID,Name,Age) = (1, "AAA", 20)

dt.Rows[idx]["Name"] = "BBB";   
dt.AcceptChanges();                 // 叫用 AcceptChanges 後，此時資料變更為 (ID,Name,Age) = (1, "BBB", 20)

dt.Rows[idx]["Name"] = "CCC";

objAdapter.Update(((DataTable)dataGridView1.DataSource));

// 當叫用 Update 時，SqlCommandBuilder 自動產生的更新語法為
// UPDATE EMP SET Name='CCC' WHERE ID=1 AND NAME='BBB' AND Age=20 （更據所有欄位值去當成 where 條件）
// 這個語法實際對資料庫操作時，會回傳 0 筆被更新，這與預期不符，所以就產生錯誤。
// 如果資料庫中剛好有一筆符合的資料，那麼就會更新到非預期的資料，但不會產生錯誤。
}
```

## 3. 不會傳回任何重要資料行資訊的 SelectCommand 不支援 UpdateCommand 動態 SQL 的產生。

Error: Dynamic sql generation for the update command is not supported against a select command that does not return any key column information  

這個錯誤發生在 SELECT 出來的資料沒有包含主鍵，所以無法動態產生 [Update](http://msdn.microsoft.com/zh-tw/library/system.data.common.dbdataadapter.update.aspx) 指令。加入Primary Key 就可以了。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8sU8GpkENGfUw-3dp0GTehhGARp0y99QU2ozXjpRaU9fpLXZtvLRskRvPynHqY9CBODN9lu-6m_acemmpBKmaHpU9le8t3OPL_AbkJFkAsz-W2xfu33xuCMeJdnlIwj2dEggNMPImPgU/s560/adonet-update-err-msg.png)

# 偵測連接事件的資訊

Connection 物件包含一個 InfoMessage 事件，可以用來擷取由資料庫回應出來的一般資訊或錯誤資料。
```c#
protected void ConnectionInfoMessage(object sender, SqlInfoMessageEventArgs e)
{
foreach (SqlError err in e.Errors)
{
if (err.Class > 9) // Severity
{
Console.WriteLine("Error Message : {0} : State : {1}", err.Message, err.State);
}
else
{
Console.WriteLine("Info Message : {0} : State : {1}", err.Message, err.State);
}
}
}

protected void btnInfoMessage_Click(object sender, EventArgs e)
{
using (SqlConnection connection = CreateTestDBConnection())
{
connection.InfoMessage +=
new SqlInfoMessageEventHandler(ConnectionInfoMessage);

connection.Open();

DbCommandoCmd1 = connection.CreateCommand();
oCmd1.CommandType = CommandType.StoredProcedure;
oCmd1.CommandText = "usp_Test";
oCmd1.ExecuteNonQuery();
}
}
```

# 使用 Transaction 物件
```c#
protected void btnTransation_Click(object sender, EventArgs e)
{
using (SqlConnection connection = CreateTestDBConnection())
{
connection.Open();

SqlTransaction transaction;
transaction = connection.BeginTransaction();

try
{
SqlCommand cmd = new SqlCommand();

//必須將 Connection 和 Transaction 二個物件都指定給 Command 物件
cmd.Connection = connection;
cmd.Transaction = transaction;

cmd.CommandType = CommandType.StoredProcedure;
cmd.CommandText = "usp_Test";
cmd.ExecuteNonQuery();
cmd.CommandText = "usp_Test2";
cmd.ExecuteNonQuery();

transaction.Commit();       //commit the transaction
}
catch
{
transaction.Rollback();     //rollback the transaction
}
}
}
```

# 非同步資料存取 ( Asynchronous Data Access) 

使用非同步資料存取功能時，必須在連線字串中，設定 Asynchronous Processing = true ，並在程式中叫用以下非同的執行方法。
```c#
public IAsyncResult BeginExecuteNonQuery();
public IAsyncResult BeginExecuteNonQuery(AsyncCallback callback, object stateObject);

public IAsyncResult BeginExecuteReader();
public IAsyncResult BeginExecuteReader(AsyncCallback callback, object stateObject);

public IAsyncResult BeginExecuteXmlReader();
public IAsyncResult BeginExecuteXmlReader(AsyncCallback callback, object stateObject);
```
```c#
protected void btnAsynchronous_Click(object sender, EventArgs e)
{
// 必須在連線設定中指定 Asynchronous Processing = true 

string connString = @"
Data Source = VITO-2011W7\W7_SQLSVR_2008; Initial Catalog = Vito2012; User ID=vito; Password=vito1234;
Asynchronous Processing = true;
MultipleActiveResultSets = true";

SqlConnection conn1 = new SqlConnection(connString);
SqlConnection conn2 = new SqlConnection(connString);
SqlConnection conn3 = new SqlConnection(connString);

SqlCommand cmd1 = conn1.CreateCommand();
cmd1.CommandType = CommandType.StoredProcedure;
cmd1.CommandText = "usp_AsysTest";

SqlCommand cmd2 = conn2.CreateCommand();
cmd2.CommandType = CommandType.StoredProcedure;
cmd2.CommandText = "usp_AsysTest";

SqlCommand cmd3 = conn3.CreateCommand();
cmd3.CommandType = CommandType.StoredProcedure;
cmd3.CommandText = "usp_AsysTest";

DateTime dt1 = DateTime.Now;

conn1.Open();
conn2.Open();
conn3.Open();

IAsyncResult result1 = cmd1.BeginExecuteNonQuery();
IAsyncResult result2 = cmd2.BeginExecuteNonQuery();
IAsyncResult result3 = cmd3.BeginExecuteNonQuery();

//result1.AsyncWaitHandle.WaitOne();
//result2.AsyncWaitHandle.WaitOne();
//result3.AsyncWaitHandle.WaitOne();

cmd1.EndExecuteNonQuery(result1);
cmd2.EndExecuteNonQuery(result2);
cmd3.EndExecuteNonQuery(result3);

DateTime dt2 = DateTime.Now;
TimeSpan timespan1 = dt2 - dt1;
myDebug.WriteLineBR(timespan1.ToString());  // 00:00:05.0012860

conn1.Close();
conn2.Close();
conn3.Close();
}
```

# 如何使用大型二進位物件 (Binary Large OBject, BLOB)

## 什麼是大型二進位物件

在 SQL 中，有以下幾種比較大型的欄位型態：

| 型別 | Unicode | 長度 | 最大長度 |
| --- | --- | --- | --- |
| image |  | 可變 | 2^31-1 |
| binary |  | 固定 | 8,000 |
| varbinary |  | 可變 | 2^31-1 |
| text |  | 可變 | 2^31-1 |
| ntext | Unicode | 可變 | 2^30-1 |
| varchar |  | 固定 | 8,000 |
| nvarchar | Unicode | 可變 | 2^31-1 (2GB) |

因為 MS 將在未來的版本中，移除 ntext、text 和 image 等資料類型。  所以請避免在新的開發工作中使用這些資料類型。  可改用 nvarchar(max)、varchar(max) 和 varbinary(max) 取代。  

更多說明，可參考MSDN：

- [從檔案插入影像 (ADO.NET)](http://msdn.microsoft.com/zh-tw/library/4f5s1we0%28v=vs.100%29.aspx)
- [使用 UPDATETEXT 搭配二進位資料 (ADO.NET)](http://msdn.microsoft.com/zh-tw/library/3517w44b%28v=vs.100%29.aspx)

## 寫入 BLOG 資料
```c#
protected void btnWriteBLOB_Click(object sender, EventArgs e)
{
string sFilePath = @"D:\VitoBlogPic\test1.jpg";
byte[] photo = GetPhoto(sFilePath);

using (SqlConnection oConn = CreateTestDBConnection())
{
SqlCommand oCmd = oConn.CreateCommand();
oCmd.CommandType = CommandType.Text;
oCmd.CommandText = "INSERT Employee(Name,Picture) Values (@Name, @Picture)";

oCmd.Parameters.Add("@Name", SqlDbType.NVarChar, 150).Value = "vito";
oCmd.Parameters.Add("@Picture", SqlDbType.VarBinary, photo.Length).Value = photo;

try
{
oConn.Open();
oCmd.ExecuteNonQuery();
}
catch (Exception ex)
{
myDebug.WriteLine(ex.Message);
}
}
}
public static byte[] GetPhoto(string filePath)
{
FileStream stream = new FileStream(filePath, FileMode.Open, FileAccess.Read);
BinaryReader reader = new BinaryReader(stream);
byte[] photo = reader.ReadBytes((int)stream.Length);
reader.Close();
stream.Close();
return photo;
}
```

## 讀取 BLOG 資料
```c#
protected void btnReadBLOB_Click(object sender, EventArgs e)
{
using (SqlConnection oConn = CreateTestDBConnection())
{
SqlCommand oCmd = oConn.CreateCommand();
oCmd.CommandType = CommandType.Text;
oCmd.CommandText = "Select \* from Employee where EmployeeID='9411993'";

try
{
oConn.Open();
SqlDataReader reader = oCmd.ExecuteReader();

if (reader.Read())
{
byte[] photo = reader["Picture"] as byte[];

string sFilePath = @"C:\EmployeePic.jpg";
FileStream fs = new FileStream(sFilePath, FileMode.OpenOrCreate, FileAccess.Write);
fs.Write(photo, 0, photo.Length);
fs.Close();
}
}
catch (Exception ex)
{
myDebug.WriteLine(ex.Message);
}
}
}
```

上面例子會將資料庫中的資料讀取後儲存成檔案，若我們處理的 BLOB 是圖檔資料，我們可以建立一個 「[泛型處理常式](http://msdn.microsoft.com/zh-tw/library/bb398986.aspx)」來處理網頁讀取圖檔的需求，以方便將圖型資料顯示在網頁上。  

1) 假設我們在網頁上放置了一個 Image 控制項
```xml
<asp:Image ID="Image1" runat="server" />
```

2) 我們可以在 Code Behind 中，指定要顯示的資料
```c#
Image1.ImageUrl = @"../Common/Image.ashx?EmpID=9411993";
```

3) 然後撰寫一個泛型處理常式
```c#
public class Image : IHttpHandler
{
public void ProcessRequest(HttpContext context)
{
string EmpID = context.Request.QueryString["EmpID"];
string ConStr = WebConfigurationManager.ConnectionStrings["TestDBConnectionString"].ConnectionString;
using (SqlConnection oConn = new SqlConnection(ConStr))
{
SqlCommand oCmd = oConn.CreateCommand();
oCmd.CommandType = CommandType.Text;
oCmd.CommandText = "Select Picture from Employee where EmployeeID=@EmpID";
oCmd.Parameters.Add(new SqlParameter("@EmpID", EmpID));
try
{
oConn.Open();
SqlDataReader reader = oCmd.ExecuteReader();

if (reader.Read())
{
if (!Convert.IsDBNull(reader["Picture"]))
{
byte[] photo = reader["Picture"] as byte[];
context.Response.ContentType = "image/jpeg";
context.Response.BinaryWrite(photo);
}
else
context.Response.Write("./images/noimage.gif");
}
}
catch (Exception ex)
{
myDebug.WriteLine(ex.Message);
}
}

}

public bool IsReusable
{
get
{
return false;
}
}
}
```