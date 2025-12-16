---
title: 檔案系統
layout: default
parent: 輸入與輸出
nav_order: 4
description: "檔案系統"
date: 2012-01-07
tags: [DotNetFramework,輸入與輸出]
postid: "2510092029951046963"
---
# 何謂 File System Classes

[System.IO](http://msdn.microsoft.com/zh-tw/library/system.io.aspx) 命名空間包含 [File](http://msdn.microsoft.com/zh-tw/library/system.io.file.aspx) 和 [Directory](http://msdn.microsoft.com/zh-tw/library/system.io.directory.aspx) 等類別，提供管理檔案和目錄的功能。  
因為這些物件的方法是靜態 (Static) 或共用成員，所以可以直接使用它們，而不需要先建立類別的執行個體。  

與檔案相關的類別，大至可分成二類：

#### 資訊提示(Information)：

資訊提示類別大都繼承自[FileSystemInfo](http://msdn.microsoft.com/zh-tw/library/system.io.filesysteminfo.aspx) 基底類別。提供與檔案系統相關的資訊，如 [FileInfo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.aspx) 與 [DirectoryInfo](http://msdn.microsoft.com/zh-tw/library/system.io.directoryinfo.aspx) 。

#### 公用程式(Utility)。

公用程式類別則提供對檔案系統的操作功能，如 [File](http://msdn.microsoft.com/zh-tw/library/system.io.file.aspx) 、 [Directory](http://msdn.microsoft.com/zh-tw/library/system.io.directory.aspx) 、 [Path](http://msdn.microsoft.com/zh-tw/library/system.io.path.aspx) 等類別。

#### 下表列出並描述用於檔案 I/O 的主要類別。

| 類別 | 描述 |
| --- | --- |
| [Directory](http://msdn.microsoft.com/zh-tw/library/system.io.directory.aspx) | 提供在所有目錄和子目錄中建立、移動和列舉的靜態方法。 |
| [DirectoryInfo](http://msdn.microsoft.com/zh-tw/library/system.io.directoryinfo.aspx) | 提供在所有目錄和子目錄中建立、移動和列舉的執行個體方法 (Instance Method)。 |
| [DriveInfo](http://msdn.microsoft.com/zh-tw/library/driveinfo.aspx) | 提供在所有磁碟機中建立、移動和列舉的執行個體方法。 |
| [File](http://msdn.microsoft.com/zh-tw/library/system.io.file.aspx) | 提供建立、複製、刪除、移動和開啟檔案的靜態方法，並協助建立 [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 。 |
| [FileAttributes](http://msdn.microsoft.com/zh-tw/library/system.io.fileattributes.aspx) | 提供檔案和目錄的屬性 (Attribute)，例如 Archive、Hidden 和 ReadOnly。 |
| [FileInfo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.aspx) | 提供建立、複製、刪除、移動和開啟檔案的靜態方法，並協助建立 FileStream。 |
| [Path](http://msdn.microsoft.com/zh-tw/library/system.io.path.aspx) | 提供用於處理目錄字串的方法和屬性。 |
| [FileAccess](http://msdn.microsoft.com/zh-tw/library/4z36sx0f.aspx) | 定義存取檔案的讀取、寫入或讀取/寫入常數 |
| [FileMode](http://msdn.microsoft.com/zh-tw/library/system.io.filemode.aspx) | 指定作業系統應該如何開啟檔案。在 [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 和 [IsolatedStorageFileStream](http://msdn.microsoft.com/zh-tw/library/system.io.isolatedstorage.isolatedstoragefilestream.aspx) 以及 [File](http://msdn.microsoft.com/zh-tw/library/system.io.file.aspx) 和 [FileInfo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.aspx) 之 [Open](http://msdn.microsoft.com/zh-tw/library/system.io.file.open.aspx) 方法的許多建構函式 (Constructor) 中指定此參數。 |
| [FileShare](http://msdn.microsoft.com/zh-tw/library/system.io.fileshare.aspx) | 定義常數，用以控制其他檔案資料流可以有相同檔案之存取權的類型。 |
| [System.Security.Permissions.FileIOPermission](http://msdn.microsoft.com/zh-tw/library/system.security.permissions.fileiopermission.aspx) | 定義 Read、Write、Append 和 PathDiscovery 存取權限，藉以控制檔案與資料夾的存取。 |

## FileInfo 物件

####  [FileInfo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.aspx) 建構子
```c#
public FileInfo(string fileName);
```

####  [FileInfo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.aspx) 屬性

- [FileInfo.Directory](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.directory.aspx) ：取得父代 (Parent) 目錄的執行個體。
- [FileInfo.DirectoryName](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.directoryname.aspx) ：取得表示目錄完整路徑的字串。
- [FileInfo.Exists](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.exists.aspx) ：判斷檔案是否存在。
- [FileInfo.IsReadOnly](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.isreadonly.aspx) ：判斷檔案是否唯讀。
- [FileInfo.Length](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.length.aspx) ：取得目前檔案的大小，以位元組為單位。
- [FileInfo.Name](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.name.aspx) ：取得檔案的名稱。

####  [FileInfo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.aspx) 方法

- [FileInfo.Open](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.open.aspx) ：開啟檔案。
- [FileInfo.Create](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.create.aspx) ：建立檔案。
- [FileInfo.Delete](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.delete.aspx) ：刪除檔案。
- [FileInfo.CopyTo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.copyto.aspx) ：複製現有的檔案到新的檔案。
- [FileInfo.MoveTo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.moveto.aspx) ：移動指定的檔案至新的位置，提供指定新檔名的選項。
- [FileInfo.AppendText](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.appendtext.aspx) ：建立將文字附加在 [FileInfo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.aspx) 的這個執行個體所表示之檔案的 StreamWriter。
- [FileInfo.Encrypt](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.encrypt.aspx) ：加密檔案，只有使用加密檔案的帳戶才能將其解密。
- [FileInfo.Decrypt](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.decrypt.aspx) ：解密目前帳戶使用 [Encrypt](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.encrypt.aspx) 方法加密的檔案。

## File 物件

####  [File](http://msdn.microsoft.com/zh-tw/library/system.io.file.aspx) 物件的方法

- [Open](http://msdn.microsoft.com/zh-tw/library/system.io.file.open.aspx) ：Open a file
- [Create](http://msdn.microsoft.com/zh-tw/library/system.io.file.create.aspx) ：Creates a new file. (overwrite existing)
- [Delete](http://msdn.microsoft.com/zh-tw/library/system.io.file.delete.aspx) ：Delete an existing file
- [Copy](http://msdn.microsoft.com/zh-tw/library/system.io.file.copy.aspx) ：Copies a file to a new file.
- [Move](http://msdn.microsoft.com/zh-tw/library/system.io.file.move.aspx) ：Moves a file from one place to another.
- [OpenText](http://msdn.microsoft.com/zh-tw/library/system.io.file.opentext.aspx) ：開啟現有 UTF-8 編碼的文字檔來讀取。
- [OpenRead](http://msdn.microsoft.com/zh-tw/library/system.io.file.openread.aspx) ：開啟現有檔案來讀取。
- [OpenWrite](http://msdn.microsoft.com/zh-tw/library/system.io.file.openwrite.aspx) ：開啟現有檔案或建立新檔案，以進行寫入。
- [CreateText](http://msdn.microsoft.com/zh-tw/library/system.io.file.createtext.aspx) ：建立或開啟用以寫入 UTF-8 編碼文字的檔案。(overwrite existing)
- [ReadAllText](http://msdn.microsoft.com/zh-tw/library/system.io.file.readalltext.aspx) ：Opens a file, reads the contents of it into a string.
- [WriteAllText](http://msdn.microsoft.com/zh-tw/library/system.io.file.writealltext.aspx) ：Opens a file, writes the contents of a string into it. (overwrite existing)
- [AppendAllText](http://msdn.microsoft.com/zh-tw/library/system.io.file.appendalltext.aspx) ：Opens a file which is prepared to allow text to be appended.

####  [File.Open](http://msdn.microsoft.com/zh-tw/library/system.io.file.open.aspx) 的多載方法
```c#
public static FileStream Open(string path, FileMode mode);
public static FileStream Open(string path, FileMode mode, FileAccess access);
public static FileStream Open(string path, FileMode mode, FileAccess access, FileShare share)
```

## 檔案的開啟模式、存取方式、共用模式

####  [FileMode](http://msdn.microsoft.com/zh-tw/library/system.io.filemode.aspx) ：定義檔案的開啟模式。

1. Create          ：建立新檔案，如果檔案已經存在，則覆寫。
2. CreateNew       ：建立新檔案，如果檔案已經存在，將會擲回 IOException。
3. Open            ：開啟現有的檔案，如果檔案不存在，會擲回 System.IO.FileNotFoundException。
4. OpenOrCreate    ：開啟現有的檔案，如果檔案不存在，則建立新的檔案。
5. Append          ：開啟現有的檔案，並搜尋至檔案末端，如果檔案不存在，則建立新的檔案。
6. Truncate        ：開啟現有的檔案，並清空

####  [FileAccess](http://msdn.microsoft.com/zh-tw/library/4z36sx0f.aspx) ：定義檔案的存取方式。

1. Read        ：讀取檔案的存取權限
2. Write       ：寫入檔案的存取權限
3. ReadWrite   ：讀取和寫入檔案的存取權限

####  [FileShare](http://msdn.microsoft.com/zh-tw/library/system.io.fileshare.aspx) ：控制其他 [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 物件是否可同時存取。

1. None        ：拒絕共用目前檔案。
2. Read        ：允許讀取。
3. Write       ：允許寫入。
4. ReadWrite   ：允許讀取與寫入。
5. Delete      ：允許刪除
6. Inheritable ：

# 如何取得檔案資訊

[FileInfo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.aspx) 類別提供建立、複製、刪除、移動和開啟檔案的屬性和執行個體方法，並協助建立 [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 物件。

- 1.使用檔案路徑建立新的 [FileInfo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.aspx) 物件。
- 2.存取 [FileInfo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.aspx) 物件的屬性
```c#
FileInfo ourFile = new FileInfo(@"C:\Windows\ASUS\PANELID.INI");
if (ourFile.Exists)
{
Console.WriteLine("檔案路徑 : {0}", ourFile.FullName);
Console.WriteLine("檔案目錄 : {0}", ourFile.DirectoryName);
Console.WriteLine("檔案名稱 : {0}", ourFile.Name);
Console.WriteLine("檔案大小 : {0}", ourFile.Length);
Console.WriteLine("檔案主檔名 : {0}", Path.GetFileNameWithoutExtension(ourFile.FullName));
Console.WriteLine("檔案副檔名 : {0}", ourFile.Extension);

Console.WriteLine("CreationTime : {0}", ourFile.CreationTime.ToString("yyyy/MM/dd HH:mm:ss"));
Console.WriteLine("LastAccessTime : {0}", ourFile.LastAccessTime.ToString("yyyy/MM/dd HH:mm:ss"));
Console.WriteLine("LastWriteTime : {0}", ourFile.LastWriteTime.ToString("yyyy/MM/dd HH:mm:ss"));

//檔案路徑 : C:\Windows\ASUS\PANELID.INI
//檔案目錄 : C:\Windows\ASUS
//檔案名稱 : PANELID.INI
//檔案大小 : 32
//檔案主檔名 : PANELID
//檔案副檔名 : .INI
//CreationTime : 2011/04/22 15:39:47
//LastAccessTime : 2011/04/22 15:39:47
//LastWriteTime : 2011/07/18 16:44:50
}
```

# 如何開啟檔案
```c#
// 使用 File 物件開檔
FileStream fs1 = File.Open(@"c:\VITO\log.txt", FileMode.Create);                             //若檔案存在則覆寫
FileStream fs2 = File.Open(@"c:\VITO\log.txt", FileMode.OpenOrCreate, FileAccess.ReadWrite); //若檔案存在則開啟,不存在則新建
fs1.Close();
fs2.Close();

// 使用 FileInfo 物件開檔
FileInfo fileinfo = new FileInfo(@"c:\VITO\log.txt");
FileStream fs3 = fileinfo.Open(FileMode.Create);
fs3.Close();
```

# 如何讀寫檔案

## 使用 FileStream 物件

要操作檔案，可以使用 [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 類別輕鬆完成，即然它是一個 [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) ，所以資料流必定是 Byte[] 形式，操作時要記得轉換。  底下是幾個常用到的方法：  

- [Read](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.read.aspx) ：從資料流讀取資料，並寫入指定的緩衝區。
- [ReadByte](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.readbyte.aspx) ：從檔案讀取一個位元組，並將讀取位置前移一個位元組。
- [Write](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.write.aspx) ：將緩衝區的資料寫入這個資料流。
- [WriteByte](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.writebyte.aspx) ：寫入一個位元組到檔案資料流中的目前位置。
- [Seek](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.seek.aspx) ：Sets the current position of this stream to the given value.
- [Flush](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.flush.aspx) ：清除這個資料流的緩衝區，並讓所有緩衝區中的資料全部寫入檔案。
```c#
FileStream.Write(byte[] bytearray, int offset, int count) 
```

- bytearray ：來源資料。
- offset ：指定要由 bytearray 的哪個 byte 開始讀取。
- count ：指定一次最大可讀取的位元組數。

當叫用 [Write](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.write.aspx) 時，實際上資料是以一個block接著一個block方式寫入，block最大值由 count 參數指定。  每次寫入一個 block ，offset 就會自動累加，並移到下一個 block 繼續寫入。  
```c#
// 使用 File 物件開啟檔案，若檔案存在則覆寫
FileStream fs = File.Open(@"d:\log.txt", FileMode.Create);

// 資料
string sData = "test content";

// 對資料進行編碼，並取得位元組序列
var encoding = Encoding.GetEncoding(65001);      // get utf-8 encoding
byte[] byteArray = encoding.GetBytes(sData);

// 將位元組序列寫入檔案。
fs.Write(byteArray, 0, byteArray.Length);
fs.Close();
```

當叫用 [Read](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.read.aspx) 時，實際上資料是以一個block接著一個block方式讀取，每次讀取會回傳讀取大小，判斷該回傳值即可知道是否整個檔案已讀取結束。  
```c#
// 使用 File 物件開啟檔案，若檔案不存在，擲回 System.IO.FileNotFoundException。
FileStream fs = File.Open(@"d:\log.txt", FileMode.Open, FileAccess.Read);

byte[] byteArray = new byte[fs.Length];

int iToReadLength = byteArray.Length;
int iReadLenght = 0;

// 由資料流讀取資料，直到資料流未端才結束（回傳值零）。
while (iToReadLength > 0)
{
int n = fs.Read(byteArray, iReadLenght, iToReadLength);

if (n == 0)
break;

iReadLenght += n;
iToReadLength -= n;
}
fs.Close();

string result = System.Text.Encoding.UTF8.GetString(byteArray);
Console.WriteLine(result);
```

## 使用 File 物件
```c#
string file_name = @"d:\log.txt";
string text = "";

bool exist = File.Exists(file_name);            // 判定檔案是否存在
if (exist)
{
text = File.ReadAllText(file_name, Encoding.UTF8);  // 讀取檔案內所有文字
}
Console.WriteLine(text);
```
```c#
string file_name = @"d:\log.txt";
string text = "test content";

File.WriteAllText(file_name, text, Encoding.UTF8);  // 將 text 寫入檔案
```

叫用 [WriteAllText](http://msdn.microsoft.com/zh-tw/library/system.io.file.writealltext.aspx) 方法時，會自動判斷檔案是否存在，若不存在會自動建立檔案；若檔案存在，會覆寫內容。

# 如何複製檔案

- 一旦取得 [FileInfo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.aspx) 物件,只需要呼叫方法便可複製該檔案。
```c#
string sourceFileName = @"c:\testfile.txt";
string destFileName = @"c:\testfile.bak";

//使用 FileInfo.CopyTo 
FileInfo ourFile = new FileInfo(sourceFileName);
ourFile.CopyTo(destFileName);
ourFile.CopyTo(destFileName, true);   //允許覆寫

//使用 File.Copy
File.Copy(sourceFileName, destFileName);
File.Copy(sourceFileName, destFileName, true);   //允許覆寫
```

更詳細內容，可參考MSDN:

- [HOW TO：複製、刪除和移動檔案和資料夾](http://msdn.microsoft.com/zh-tw/library/cc148994.aspx)
- [HOW TO：複製目錄](http://msdn.microsoft.com/zh-tw/library/bb762914.aspx)

# 如何列舉目錄中的檔案

- 1.使用目錄路徑建立新的 [DirectoryInfo](http://msdn.microsoft.com/zh-tw/library/system.io.directoryinfo.aspx) 物件。
- 2.呼叫 [GetFiles](http://msdn.microsoft.com/zh-tw/library/ms143327.aspx) 方法列舉目錄中的所屬檔案。
```c#
DirectoryInfo ourDir = new DirectoryInfo(@"c:\windows");
Console.WriteLine("Directory: {0}", ourDir.FullName);
Console.WriteLine("Root: {0}", ourDir.Root);

//列出所有檔案
foreach (FileInfo file in ourDir.GetFiles())
{
Console.WriteLine("File: {0}", file.Name);
}

//列出所有tsst開頭，且副檔名為txt的檔案
foreach (FileInfo file in ourDir.GetFiles("tsst\*.txt"))
{
Console.WriteLine("File: {0}", file.Name);
}
```

更詳細內容，可參考MSDN:

- [HOW TO：列舉目錄和檔案](http://msdn.microsoft.com/zh-tw/library/dd997370.aspx)

# 如何列舉磁碟機

- [DriveInfo](http://msdn.microsoft.com/zh-tw/library/system.io.driveinfo.aspx) 類別,可以用來表示某個磁碟，並且提查詢該磁碟的方法與屬性。例如磁碟名稱,空間可用量,磁碟機大小等等。
- [DriveType](http://msdn.microsoft.com/zh-tw/library/system.io.drivetype.aspx) 列舉,這個列舉包含可種磁碟機可能的型態。
```c#
DriveInfo[] drives = DriveInfo.GetDrives();
foreach (DriveInfo drive in drives)
{
Console.WriteLine("Drive: {0}", drive.Name);
Console.WriteLine("Type: {0}", drive.DriveType);
}

DriveInfo driveC = new DriveInfo(@"C:\");
Console.WriteLine("DriveName: {0}", driveC.Name);
Console.WriteLine("DriveType: {0}", driveC.DriveType);
Console.WriteLine("TotalSize: {0}", driveC.TotalSize);
Console.WriteLine("TotalFreeSpace: {0}", driveC.TotalFreeSpace);
Console.WriteLine("VolumeLabel: {0}", driveC.VolumeLabel);
```

# Path物件

[Path](http://msdn.microsoft.com/zh-tw/library/system.io.path.aspx) 類別提供了可操作檔案系統路徑的多個方法。
```c#
string ourPath = @"C:\Windows\ASUS\PANELID.INI";

Console.WriteLine(Path.HasExtension(ourPath));                  
Console.WriteLine(Path.GetExtension(ourPath));                  
Console.WriteLine(Path.GetFileName(ourPath));                   
Console.WriteLine(Path.GetFileNameWithoutExtension(ourPath));
Console.WriteLine(Path.IsPathRooted(ourPath));
Console.WriteLine(Path.GetDirectoryName(ourPath));
Console.WriteLine(Path.GetFileName(Path.GetDirectoryName(ourPath)));    

//True
//.INI
//PANELID.INI
//PANELID
//True
//C:\Windows\ASUS
//ASUS

Console.WriteLine(Path.GetPathRoot(ourPath));                   
Console.WriteLine(Path.GetDirectoryName(ourPath));             
Console.WriteLine(Path.GetFullPath(ourPath));                   
Console.WriteLine(Path.Combine(Path.GetDirectoryName(ourPath), "Log.txt"));     
Console.WriteLine(Path.ChangeExtension(ourPath, "tmp"));                        //注意:這個操作僅僅是變更變數值,並非實際變更檔案副檔名

//C:\
//C:\Windows\ASUS
//C:\Windows\ASUS\PANELID.INI
//C:\Windows\ASUS\Log.txt
//C:\Windows\ASUS\PANELID.tmp
```

# 如何監看目錄的變更

[FileSystemWatcher](http://msdn.microsoft.com/zh-tw/library/system.io.filesystemwatcher.aspx) 這個類別提供當目錄或目錄內的檔案變更時，可監聽 (Listen) 檔案系統的變更，並引發告知事件。

監聽除了監看指定目錄內檔案也可監聽子目錄的變更。
```c#
private void button6_Click(object sender, EventArgs e)
{
FileSystemWatcher watcher = new FileSystemWatcher();

// 設定監看位置
watcher.Path = @"c:\";

// 設定監看的變更類型
watcher.NotifyFilter = NotifyFilters.LastAccess | NotifyFilters.LastWrite
| NotifyFilters.FileName | NotifyFilters.DirectoryName;

// 設定僅監看.txt檔.
watcher.Filter = "\*.txt";

// 設定同時監看子目錄中的檔案
watcher.IncludeSubdirectories = true;

// Add Changed event handlers.
watcher.Created += new FileSystemEventHandler(watcher_Changed);
watcher.Deleted += new FileSystemEventHandler(watcher_Changed);
watcher.Changed += new FileSystemEventHandler(watcher_Changed);

// Add Renamed event handlers.
watcher.Renamed += new RenamedEventHandler(watcher_Renamed);

// Add Error event handlers.
watcher.Error += watcher_Error;

// Begin  watching
watcher.EnableRaisingEvents = true;

}

// Changed Event Handler
static void watcher_Changed(object sender,FileSystemEventArgs e)
{
Console.WriteLine("Directory changed({0}): {1}", e.ChangeType, e.FullPath);
}

// Renamed Event Handler
static void watcher_Renamed(object sender, RenamedEventArgs e)
{
Console.WriteLine("Renamed from {0} to {1}", e.OldFullPath, e.FullPath);
}

// Error Event Handler
static void watcher_Error(object sender, ErrorEventArgs e)
{
Console.WriteLine("Error: {0}", e.GetException());
}
```

請注意，超過緩衝區大小時，FileSystemWatcher可能會失去事件。為了避免遺漏事件，請遵循下列方針：

- 設定 InternalBufferSize屬性以增加緩衝區大小。
- 避免監看具有長檔名的檔案，因為長檔名會造成緩衝區填滿。考慮使用較短名稱重新命名這些檔案。
- 盡可能讓事件處理程式碼越短越好。

# 資料夾

## 資料夾選取元件

#### FolderBrowserDialog 類別

[FolderBrowserDialog](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.folderbrowserdialog.aspx) 元件可以用來瀏覽、建立和選擇資料夾。  

- [RootFolder](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.folderbrowserdialog.rootfolder.aspx) ：設定瀏覽的啟始資料夾，這個屬性只接受特定資料夾（ Environment.SpecialFolde ，預設值為桌面 DesktopDirectory ）
- [SelectedPath](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.folderbrowserdialog.selectedpath.aspx) ：設定瀏覽的啟始資料夾，可以指定任何路徑。
- ShowDialog ：顯示的對話方塊。
```c#
FolderBrowserDialog fbDialgo = new FolderBrowserDialog();
fbDialgo.SelectedPath = @"D:\Download\Google";
DialogResult result = fbDialgo.ShowDialog();

if (result == DialogResult.OK)
{
string folderName = fbDialgo.SelectedPath;
}
```

## 如何列舉子資料夾

要取得子資料夾清單，只要透過 [DirectoryInfo.GetDirectories](http://msdn.microsoft.com/zh-tw/library/system.io.directoryinfo.getdirectories.aspx) 方法即可，它有以下二個參數：  

- searchPattern ：指定要搜尋的特定字串
- searchOption ：指定搜尋作業應該只包含目前目錄還是包含所有子目錄。
```c#
string path = @"D:\Download\";
DirectoryInfo dir = new DirectoryInfo(path);

string searchPattern = "\*";
SearchOption serrchOption = SearchOption.TopDirectoryOnly;
DirectoryInfo[] folders = dir.GetDirectories(searchPattern, serrchOption);
```

## 如何列舉特定資料夾下的檔案

要取得子資料夾清單，只要透過 [DirectoryInfo.GetFiles](http://msdn.microsoft.com/zh-tw/library/system.io.directoryinfo.getfiles.aspx) 方法即可，它也支援上述的二個參數：  
```c#
string path = @"D:\Download\";
DirectoryInfo dir = new DirectoryInfo(path);

string searchPattern = "\*.jpg";
SearchOption serrchOption = SearchOption.AllDirectories;
FileInfo[] files = dir.GetFiles(searchPattern, serrchOption);
```

## 如何建立資料夾

使用 [Directory.CreateDirectory](http://msdn.microsoft.com/zh-tw/library/system.io.directory.createdirectory.aspx) 方法，可以在指定的路徑中建立所有目錄。
```c#
string dir = @"C:\test\test1";

if (!Directory.Exists(dir))             //判斷資料夾 C:\test\test1 是否存在
{
Directory.CreateDirectory(dir);     //建立資料夾：若目錄存在，則不會再建立
}
```