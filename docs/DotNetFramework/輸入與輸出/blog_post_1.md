---
title: 隔離儲存區
layout: default
parent: 輸入與輸出
nav_order: 1
description: "隔離儲存區"
date: 2012-01-07
tags: [DotNetFramework,輸入與輸出]
postid: "8247669068840180709"
---
# 什麼是隔離儲存區 (Isolated Storage)

隔離儲存區是為了考量系統安全性，將應用程式限定在少許權限的區域中運作，又可以滿足程式儲存資料的需求。  意思就是該區域中的資料只有該 App 能夠存取，其他的 App 無法存取。  .NET Framework 提供了這樣子的一個儲存機制，用以將應用程式和資料產生關聯，藉以提供資料隔離和安全。  有了隔離儲存區，程式碼不再需要使用唯一的路徑去存取檔案系統中的檔案，而且資料也可進一步受到保護以避免遭受其他應用程式的影響。  

隔離儲存區中的資料是依照 user 、 domain 、 assembly 來隔離的。  例如，可依使用者資訊和程式碼組件將資料隔離，限定該隔離區內資料的讀取權限僅限該程式碼，以提升這些資料的安全性，以免受到其他應用程式的影響。  這個機制常應用在ClickOnce相關程式中。  

#### 隔離類型

詳細內容請參考 MSDN『[隔離類型](http://msdn.microsoft.com/zh-tw/library/eh5d60e1%28VS.80%29.aspx)』。

![](http://127.0.0.1:8080/_images/cs/isolated-storage.png)

#### 命名空間

命名空間 [System.IO.IsolatedStorage](http://msdn.microsoft.com/zh-tw/library/x7dzh4ws.aspx) 提供以下物件，可以用來實作隔離儲存區的機制：

- [IsolatedStorageFile 類別](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.aspx)：提供隔離儲存區大部分的必要功能，可取得、刪除和管理隔離儲存區。
- [IsolatedStorageFileStream 類別](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefilestream.aspx)：處理存放區的檔案讀取和寫入。這個類別繼承自 [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 。
- [IsolatedStorageScope 列舉](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragescope.aspx)：能夠建立和選取適當隔離類型的存放區。
```c#
public IsolatedStorageFileStream(string path, FileMode mode);
public IsolatedStorageFileStream(string path, FileMode mode, FileAccess access);
public IsolatedStorageFileStream(string path, FileMode mode, FileAccess access, FileShare share);
public IsolatedStorageFileStream(string path, FileMode mode, IsolatedStorageFile isf);
......
```

# 如何寫入隔離儲存區

在寫入檔案前，必需先決定隔離儲存區的範圍。可透過以下二個方式取得：

- 1) 機器範圍隔離儲存區 **Assembly/Machine**：適合用來存放 application-level data          ```c#
//為目前使用者建立一個隔離區
IsolatedStorageFile machineStorage = IsolatedStorageFile.GetMachineStoreForAssembly();
//等同下行程式碼
IsolatedStorageFile machineStorage = IsolatedStorageFile.GetStore(IsolatedStorageScope.Assembly | IsolatedStorageScope.Machine ， null ， null);
```
- 2) 使用者範圍隔離儲存區 **Assembly/User**：適合用來存放 user-level data          ```c#
//為目前使用者建立一個隔離區
IsolatedStorageFile isoSotrage = IsolatedStorageFile.GetUserStoreForAssembly();
//等同下行程式碼
IsolatedStorageFile isoSotrage = IsolatedStorageFile.GetStore(IsolatedStorageScope.Assembly | IsolatedStorageScope.User ， null ， null);
```

隔離儲存區的存取範圍是依據 User 和 Machine 來區分，再配合應用程式識別 Application 、 Domain 、 Assembly ，所以建立隔離儲存區共有以下的方法

- [GetMachineStoreForApplication](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.getmachinestoreforapplication.aspx) ：取得對應於呼叫程式碼的應用程式識別之機器範圍隔離儲存區。
- [GetUserStoreForApplication](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.getuserstoreforapplication.aspx)       ：取得對應於呼叫程式碼的應用程式識別之使用者範圍隔離儲存區。
- [GetUserStoreForAssembly](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.getuserstoreforassembly.aspx)       ：儲存區限本人本程式使用。
- [GetMachineStoreForAssembly](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.getmachinestoreforassembly.aspx) ：儲存區限本程式使用，不限使用者。
- [GetMachineStoreForDomain](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.getmachinestorefordomain.aspx) ：取得對應於應用程式定義域識別和組件識別的機器範圍隔離儲存區。
- [GetUserStoreForDomain](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.getuserstorefordomain.aspx)       ：取得對應於應用程式定義域識別和組件識別的使用者範圍隔離儲存區。
- [GetStore](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.getstore.aspx) ：

#### 寫入隔離儲存區的步驟如下：

1. 建立隔離儲存區類型。
2. 建立 [IsolatedStorageFileStream](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefilestream.aspx) ，並指明隔離類型。
3. 建立 [StreamWriter](http://msdn.microsoft.com/zh-tw/library/system.io.streamwriter.aspx) ，包裹 IsolatedSteram 。
4. 藉由 [StreamWriter](http://msdn.microsoft.com/zh-tw/library/system.io.streamwriter.aspx) 將資料寫入 IsolatedSteram 。

#### 程式碼範例如下：
```c#
//為目前使用者建立一個隔離區
IsolatedStorageFile isoSotrage = IsolatedStorageFile.GetUserStoreForAssembly();

//為目前使用者建立一個檔案
IsolatedStorageFileStream isoStream = new IsolatedStorageFileStream("myfile.txt", FileMode.Create, isoSotrage);

//建立 StreamWriter ，包裹 IsolatedSteram
StreamWriter streamWriter = new StreamWriter(isoStream);

//藉由 StreamWriter 將資料寫入 IsolatedSteram 
streamWriter.WriteLine("這是寫入隔離區檔案的小範例");
streamWriter.WriteLine("IsolatedStorageFile：表示隔離儲存區");
streamWriter.WriteLine("IsolatedStorageFileStream：用來讀寫隔離儲存區");

//釋放資源
userWriter.Dispose();
isoStream.Dispose();
isoSotrage.Dispose();
```

# 如何讀取隔離儲存區

#### 讀取隔離儲存區的步驟如下：

1. 建立隔離儲存區類型。
2. 建立 IsolatedSteram，並指明隔離類型以及欲讀取的檔案名稱。
3. 建立 [StreamReader](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.aspx) ，包裹 IsolatedSteram 。
4. 藉由 [StreamReader](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.aspx) 由 IsolatedSteram 讀取資料。

#### 程式碼範例如下：
```c#
//為目前使用者建立一個隔離區
IsolatedStorageFile isoSotrage = IsolatedStorageFile.GetUserStoreForAssembly();

//判斷檔案是否存在
string[] files = isoSotrage.GetFileNames("myfile.txt");
if (files.Length == 0)
{
Console.WriteLine("找不到檔案");
}
else
{ 
//讀取隔離區的檔案
IsolatedStorageFileStream isoStream = new IsolatedStorageFileStream("myfile.txt", FileMode.Open, isoSotrage);

//建立 StreamReader ，包裹 IsolatedSteram
StreamReader streamReader = new StreamReader(isoStream);

//藉由 StreamReader 由 IsolatedSteram 讀取資料  
string contents = streamReader.ReadToEnd();
Console.WriteLine(contents);

//釋放資源
streamReader.Dispose();
isoStream.Dispose();
isoSotrage.Dispose();
}
```

精簡一下，比較好記
```c#
//取得隔離儲存區
IsolatedStorageFile isoSotrage = IsolatedStorageFile.GetUserStoreForAssembly();

//讀取隔離儲存區的檔案
IsolatedStorageFileStream isoStream = new IsolatedStorageFileStream("myfile.txt", FileMode.Open, isoSotrage);

//藉由 StreamReader 讀取 IsolatedSteram 資料  
string contents = new StreamReader(isoStream).ReadToEnd();
```

當需要在隔離儲存區尋找檔案或目錄時，無法像一般檔案系統使用 File.Exist() 方法來判斷。  必須透過 [GetFileNames](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.getfilenames) 、 [GetDirectoryNames](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.getdirectorynames) 方法向儲存區尋問以取得檔案清單  
```c#
//判斷目錄是否存在
string[] directories = isoSotrage.GetDirectoryNames("SomeDir");
if (directories.Length == 0)
{
....
}
```

# 如何刪除隔離儲存區

- [GetFileNames](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.getfilenames.aspx) ：Gets a list of file names
- [DeleteFile](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.deletefile.aspx) ：Deletes a file within the store
```c#
//為目前使用者建立一個隔離區
IsolatedStorageFile isoSotrage = IsolatedStorageFile.GetUserStoreForAssembly();

//判斷檔案是否存在
string[] files = isoSotrage.GetFileNames("myfile.txt");
if (files.Length == 0)
{
Console.WriteLine("找不到檔案");
}
else
{
//刪除隔離區的檔案                    
isoSotrage.DeleteFile("myfile.txt");

//釋放資源                    
isoSotrage.Dispose();
}```

# 如何在隔離儲存區內建立目錄

- [GetDirectoryNames](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.getdirectorynames.aspx) ：Gets a list of directory names
- [CreateDirectory](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.createdirectory.aspx) ：Creates a directory within the store
- [DeleteDirectory](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefile.deletedirectory.aspx) ：Deletes a directory within the store
```c#
//為目前使用者建立一個隔離區
IsolatedStorageFile isoSotrage = IsolatedStorageFile.GetUserStoreForAssembly();
IsolatedStorageFileStream isoStream;
StreamWriter userWriter;
StreamReader userReader;

//判斷目錄是否存在
string[] directories = isoSotrage.GetDirectoryNames("SomeDir");
if (directories.Length == 0)
{
//建立目錄
isoSotrage.CreateDirectory("SomeDir");

//建立檔案
isoStream = new IsolatedStorageFileStream(@"SomeDir\myfile.txt" ， FileMode.Create ， isoSotrage);

//寫資料至隔離檔
userWriter = new StreamWriter(isoStream);
userWriter.WriteLine("這是TEST範例");
userWriter.Close();

isoStream.Close();
}
else
{ 
Console.WriteLine("目錄已存在");
}

//判斷檔案是否存在
string[] files = isoSotrage.GetFileNames(@"SomeDir\myfile.txt");
if (files.Length == 0)
{
Console.WriteLine("找不到檔案");
}
else
{ 
//讀取檔案
isoStream = new IsolatedStorageFileStream(@"SomeDir\myfile.txt" ， FileMode.Open ， isoSotrage);
userReader = new StreamReader(isoStream);
string contents = userReader.ReadToEnd();
userReader.Close();

Console.WriteLine(contents);

//刪除檔案
isoSotrage.DeleteFile(@"SomeDir\myfile.txt");
isoSotrage.DeleteDirectory("SomeDir");   //若目錄下還有檔案 ，不允許刪除
isoStream.Close();
}

//釋放資源    
isoSotrage.Close();
```

# 隔離儲存區的許可權限

當部份受信任的組件或應用程式要存取隔離儲存區時，該程式碼必須滿足存取控制清單 (ACL) ，以控制哪一個使用者擁有使用檔案系統的權限。   [IsolatedStorageFilePermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.isolatedstoragefilepermission) 類別可用來受與程式碼存取隔離儲存區的權限。  
```c#
[IsolatedStorageFilePermission(SecurityAction.Demand ，
UserQuota = 1024 ，
UsageAllowed = IsolatedStorageContainment.AssemblyIsolationByUser)]
class Program
{
// ...
}
```
## 參考資料  
![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [隔離儲存區的簡介](http://msdn.microsoft.com/zh-tw/library/3ak841sy%28VS.80%29.aspx)