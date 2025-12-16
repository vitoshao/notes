---
title: 讀寫檔案
layout: default
parent: 輸入與輸出
nav_order: 3
description: "讀寫檔案"
date: 2012-01-07
tags: [DotNetFramework,輸入與輸出]
postid: "6357712307524322626"
---
# 何謂 Stream

## 資料流基本概念

資料流是一種循序或隨機存取資料的方法。也就是針對 byte[] 中的資料做循序或隨機的存取。

#### 與 [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) 相關的三項基本作業：

- 1. 讀取資料流
- 2. 寫入資料流
- 3. 搜尋資料流

#### 緩衝處理

資料流的寫入作業會先將資料寫在緩衝區中，遇到以下狀況才會將緩衝區中的資料寫入到基礎裝置。

- [StreamWriter.Flush](http://msdn.microsoft.com/zh-tw/library/system.io.streamwriter.flush.aspx) ：立即將緩衝區清除到基礎資料流。
- [StreamWriter.AutoFlush](http://msdn.microsoft.com/zh-tw/library/system.io.streamwriter.autoflush.aspx) ：指出 [StreamWriter](http://msdn.microsoft.com/zh-tw/library/system.io.streamwriter.aspx) 在每次呼叫 StreamWriter.Write 之後，立即將緩衝區清除到基礎資料流。
- [StreamWriter.Close](http://msdn.microsoft.com/zh-tw/library/system.io.streamwriter.close.aspx) ：關閉資料流。

## Stream 物件

Stream 是所有資料流的抽象基底類別。它提供以下屬性及方法

####  [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) 屬性

- [Position](http://msdn.microsoft.com/zh-tw/library/system.io.stream.position.aspx) ：資料流的目前位置。
- [Length](http://msdn.microsoft.com/zh-tw/library/system.io.stream.length.aspx) ：資料流的長度。
- [CanRead](http://msdn.microsoft.com/zh-tw/library/system.io.stream.canread.aspx) ：目前資料流是否支援讀取。
- [CanSeek](http://msdn.microsoft.com/zh-tw/library/system.io.stream.canseek.aspx) ：目前資料流是否支援搜尋。
- [CanWrite](http://msdn.microsoft.com/zh-tw/library/system.io.stream.canwrite.aspx) ：目前資料流是否支援寫入。
- [IsAsync](http://msdn.microsoft.com/zh-tw/library/system.io.stream.isasync.aspx) ：目前是非同步或同步開啟。

####  [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) 方法

- [Seek](http://msdn.microsoft.com/zh-tw/library/system.io.stream.seek.aspx) ：設定目前資料流到指定的 [Position](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.position.aspx) 。
- [SetLength](http://msdn.microsoft.com/zh-tw/library/system.io.stream.setlength.aspx) ：設定目前資料流的 [Length](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.length.aspx) 。
- [Read](http://msdn.microsoft.com/zh-tw/library/system.io.stream.read.aspx) ：從資料流讀取位元組區塊，並將資料寫入指定緩衝區。
- [Write](http://msdn.microsoft.com/zh-tw/library/system.io.stream.write.aspx) ：使用緩衝區的資料，將位元組區塊寫入這個資料流。
- [ReadByte](http://msdn.microsoft.com/zh-tw/library/system.io.stream.readbyte.aspx) ：從檔案讀取一個位元組，並將讀取位置前移一個位元組。
- [WriteByte](http://msdn.microsoft.com/zh-tw/library/system.io.stream.writebyte.aspx) ：寫入一個位元組到檔案資料流中的目前位置。
- [BeginRead](http://msdn.microsoft.com/zh-tw/library/system.io.stream.beginread.aspx) ：開始非同步的讀取。
- [EndRead](http://msdn.microsoft.com/zh-tw/library/system.io.stream.endread.aspx) ：等候暫止的非同步讀取來完成。
- [BeginWrite](http://msdn.microsoft.com/zh-tw/library/system.io.stream.beginwrite.aspx) ：開始非同步的寫入。
- [EndWrite](http://msdn.microsoft.com/zh-tw/library/system.io.stream.endwrite.aspx) ：結束非同步寫入，並封鎖直到 I/O 作業完成。
- [Flush](http://msdn.microsoft.com/zh-tw/library/system.io.stream.flush.aspx) ：清除這個資料流的緩衝區，讓所有緩衝資料全部寫入檔案。

## Stream 的衍生類別：

由 [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) 衍生類別有：

| [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) | 命名空間：**System.IO**  &lt;br&gt;用來處理檔案的資料流。 |
| --- | --- |
| [BufferedStream](http://msdn.microsoft.com/zh-tw/library/system.io.bufferedstream.aspx) | 命名空間：**System.IO.BufferedStream**  &lt;br&gt;BufferedStream 是用來加入另一個 [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) 中(例如 NetworkStream)，以改善讀取和寫入效能。  &lt;br&gt;緩衝區是記憶體中用以快取資料的位元組區塊，因此能減少呼叫作業系統的次數。( [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 內部已經有緩衝，而 [MemoryStream](http://msdn.microsoft.com/zh-tw/library/system.io.memorystream.aspx) 則不需要緩衝) |
| [CryptoStream](http://msdn.microsoft.com/zh-tw/library/k1f992c1.aspx) | 命名空間：**System.Security.Cryptography**  &lt;br&gt;會將資料流連結至密碼編譯轉換。 |
| [MemoryStream](http://msdn.microsoft.com/zh-tw/library/system.io.memorystream.aspx) | 命名空間：**System.IO**  &lt;br&gt;是非緩衝資料流，其封裝資料可在記憶體中直接存取。這個資料流沒有支援存放區，若當做暫存緩衝區可能很有用。 |
| [NetworkStream](http://msdn.microsoft.com/zh-tw/library/system.net.sockets.networkstream.aspx) | 命名空間：**System.Net.Sockets**  &lt;br&gt;代表網路連線上的 Stream。 |
| [GZipStream](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.aspx) | 命名空間：**System.Compression**  &lt;br&gt;用於壓縮和解壓縮資料流。 |

不同的衍生資料流類別，其基礎資料來源會有所不同，某些資料流可能只支援某些功能或屬性。  例如， [NetworkStream](http://msdn.microsoft.com/zh-tw/library/system.net.sockets.networkstream.aspx) 沒有目前位置的概念，因而基本上就不會支援搜尋功能。  通常應用程式可以使用 [CanRead](http://msdn.microsoft.com/zh-tw/library/system.io.stream.canread.aspx)、[CanWrite](http://msdn.microsoft.com/zh-tw/library/system.io.stream.canwrite.aspx) 以及 [CanSeek](http://msdn.microsoft.com/zh-tw/library/system.io.stream.canseek.aspx) 等屬性來判斷目前資料流可支援的操作。
&lt;!-- more --&gt;    
# 檔案相別類別

## 用於檔案 I/O 的類別

| 類別/列舉 | 描述 |
| --- | --- |
| [File](http://msdn.microsoft.com/zh-tw/library/system.io.file.aspx) | 提供建立、複製、刪除、移動和開啟檔案的靜態方法，並協助建立 [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 物件。 |
| [FileInfo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.aspx) | 提供建立、複製、刪除、移動和開啟檔案的執行個體方法，並協助建立 [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 物件。File 類別提供靜態方法。 |
| [Directory](http://msdn.microsoft.com/zh-tw/library/system.io.directory.aspx) | 提供建立、移動和列舉目錄和子目錄的靜態方法。 |
| [DirectoryInfo](http://msdn.microsoft.com/zh-tw/library/system.io.directoryinfo.aspx) | 提供建立、移動和列舉目錄和子目錄的執行個體方法。 |
| [FileSystemInfo](http://msdn.microsoft.com/zh-tw/library/system.io.filesysteminfo.aspx) | FileSystemInfo 是 [FileInfo](http://msdn.microsoft.com/zh-tw/library/system.io.fileinfo.aspx) 和 [DirectoryInfo](http://msdn.microsoft.com/zh-tw/library/system.io.directoryinfo.aspx) 的抽象基底類別。 |
| [Path](http://msdn.microsoft.com/zh-tw/library/system.io.path.aspx) | 以跨平台方式提供處理目錄字串的方法和屬性。 |
| [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) | 透過它的 [Seek](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.seek.aspx) 方法支援對檔案的隨機存取。FileStream 預設為同步開啟檔案，但也支援非同步作業。 |
| [DeflateStream](http://msdn.microsoft.com/zh-tw/library/0y917bht.aspx) | 提供了方法和屬性，以透過 Deflate 演算法來壓縮和解壓縮資料流。 |
| [GZipStream](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.aspx) | 提供了方法和屬性來壓縮和解壓縮資料流。根據預設，這個類別會使用與 [DeflateStream](http://msdn.microsoft.com/zh-tw/library/0y917bht.aspx) 類別相同的演算法，但是可加以擴充來使用其他壓縮格式。 |
| [SerialPort](http://msdn.microsoft.com/zh-tw/library/30swa673.aspx) | 提供了方法和屬性來控制序列埠檔案資源。 |

## 用於讀寫資料流的類別

| [BinaryReader](http://msdn.microsoft.com/zh-tw/library/system.io.binaryreader.aspx) / [BinaryWriter](http://msdn.microsoft.com/zh-tw/library/system.io.binarywriter.aspx) | 以二進位方式讀寫檔案。 |
| --- | --- |
| [TextReader](http://msdn.microsoft.com/zh-tw/library/system.io.textreader.aspx) / [TextWriter](http://msdn.microsoft.com/zh-tw/library/system.io.textwriter.aspx) | 以文字方式讀寫檔案料。 |
| [StreamReader](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.aspx) / [StreamWriter](http://msdn.microsoft.com/zh-tw/library/system.io.streamwriter.aspx) | 自資料流中讀寫資料。 |
| [StringReader](http://msdn.microsoft.com/zh-tw/library/system.io.stringreader.aspx) / [StringWriter](http://msdn.microsoft.com/zh-tw/library/system.io.stringwriter.aspx) | 自記憶體中的字串讀寫資料。 |

## FileStream 物件

####  [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 建構子
```c#
public FileStream(string path, FileMode mode);
public FileStream(string path, FileMode mode, FileAccess access);
public FileStream(string path, FileMode mode, FileAccess access, FileShare share);
......
```

####  [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 屬性和方法

[FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 是由 [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) 繼承來的，其屬性和方法也都大至相同，可參考 [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) 的列表即可。

## StreamReader 物件

####  [StreamReader](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.aspx) 建構子
```c#
public StreamReader(Stream stream);
public StreamReader(string path);
public StreamReader(Stream stream, Encoding encoding);
public StreamReader(string path, Encoding encoding);
......
```

####  [StreamReader](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.aspx) 方法

- [Peek](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.peek.aspx) ：傳回下一個可供使用的字元，若目前位置已至檔案結尾，則回傳-1。使用 Peek 不會變更目前位置。
- [Read](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.read.aspx) ：自輸入資料流讀取下一個字元或下一組字元。
- [ReadLine](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.readline.aspx) ：自目前資料流讀取一行字元，並將資料以字串傳回。
- [ReadToEnd](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.readtoend.aspx) ：從目前位置到資料流末端來讀取資料流。

# 如何讀取檔案

## 如何開啟檔案

要開啟檔案，主要有以下幾個方式：

1. 使用 [File](http://msdn.microsoft.com/zh-tw/library/system.io.file.aspx)物件
- [File.Open](http://msdn.microsoft.com/zh-tw/library/system.io.file.open.aspx) ：可開啟任意型態的檔案，預設以 UTF-8 編碼方式開啟，若不是 Unicode 的檔案，必需指明以何種編碼方式開啟。
- [File.OpenText](http://msdn.microsoft.com/zh-tw/library/system.io.file.opentext.aspx) ：只可開啟文字檔案。
2. 使用 [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx)物件
- [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 提供讀取、寫入、開啟和關閉檔案等功能。可以指定讀取和寫入作業為同步或非同步。
- [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 會緩衝輸入和輸出，以獲得較佳的效能。
3. 使用 [StreamReader](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.aspx)物件
- [StreamReader](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.aspx) 提供讀取資料流的相關功能。
- [StreamReader](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.aspx) 不是安全執行緒，如需安全執行緒之包裝函式 (Wrapper) 的詳細資訊，可參閱 TextReader.Synchronized。

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

## 範例
```c#
//==============================================================
//１）使用 File.Open 方法，直接開啟檔案
//==============================================================
FileStream fileStream1 = File.Open(@"C:\\temp\\Log.txt", FileMode.Open, FileAccess.Read);
fileStream1.Position = 0;
while (fileStream1.Position != fileStream1.Length)
{
Console.WriteLine("{0:x2}", fileStream1.ReadByte());
}
fileStream1.Close();

//==============================================================
//２）使用 File.OpenText 方法, 以 UTF-8 編碼方式開啟文字檔
//==============================================================
StreamReader fileStream2 = File.OpenText(@"C:\\temp\\Log.txt");
while (!fileStream2.EndOfStream)
Console.WriteLine(fileStream2.ReadLine());
fileStream2.Close();

//==============================================================
//３）直接使用 FileStream 開啟檔案讀取
//==============================================================

FileStream fileSream3 = new FileStream(@"C:\\temp\\Log.txt", FileMode.Open);
int buffer_size = 4096;
byte[] buffer = new byte[buffer_size];
int num = fileSream3.Read(buffer, 0, buffer_size);
fileSream3.Close();

//==============================================================
//４）使用 StreamReader 物件開啟檔案，預設以 UTF-8 編碼方式開啟
//==============================================================

StreamReader streamReader3 = new StreamReader(@"C:\\temp\\Log.txt");
while (!streamReader3.EndOfStream)
Console.WriteLine(streamReader3.ReadLine());
streamReader3.Close();

//==============================================================
// 4.2 指定特定的編碼方式開啟檔案
//==============================================================

StreamReader streamReader4 = new StreamReader(@"C:\testfile.txt", System.Text.Encoding.GetEncoding(950));
DumpStream(streamReader4);
while (!streamReader4.EndOfStream)
Console.WriteLine(streamReader3.ReadLine());
streamReader4.Close();

//==============================================================
// 4.3 讀取整個檔案
//==============================================================

StreamReader streamReader5 = new StreamReader(@"C:\\temp\\Log.txt");
string Content = streamReader5.ReadToEnd();
streamReader5.Close();
```

[ReadLines](http://msdn.microsoft.com/zh-tw/library/system.io.file.readlines.aspx) 是.Net4才提供的方法,可以一次讀取檔案的所有行。  [ReadLines](http://msdn.microsoft.com/zh-tw/library/system.io.file.readlines.aspx) 和 [ReadAllLines](http://msdn.microsoft.com/zh-tw/library/system.io.file.readalllines.aspx) 方法不同之處在於： 當您使用 ReadLines 時，可以在傳回整個集合之前，就開始列舉字串集合。而使用 ReadAllLines 時，則必須等到整個字串陣列載入結束後，才能存取該陣列。  因此，當讀取非常大的檔案時， ReadLines 可以更有效率。
```c#
foreach (string line in File.ReadLines(@"C:\testfile.txt", System.Text.Encoding.Default))
{
Console.WriteLine(line);
}
```

# 如何寫入檔案
```c#
//使用 File 物件,直接開啟檔案寫入

FileStream theFile1 = File.Create(@"c:\test1.txt");
theFile1.WriteByte(88);
theFile1.WriteByte(89);
theFile1.WriteByte(90);
theFile1.Close();

//使用 StreamWriter 物件，直接開啟檔案寫入

string mydocpath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);  //取得系統資料夾:我的文件
using (StreamWriter outfile = new StreamWriter(mydocpath + @"\AllTxtFiles.txt"))
{
outfile.Write("使用 StreamWriter 物件,直接開啟檔案寫入");
}

//使用 StreamWriter 物件，開啟檔案後，將資料Append在檔案後頭

StreamWriter writer = File.AppendText(@"C:\test1.txt"):
writer.WriteLine("Some data");
writer.Close();
```

# 認識 Readers & Writers

Readers 和 Writer 的作用就是要讓資料流的讀取或寫入步驟變的簡單。例如：

- [StreamReader](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.aspx) / [StreamWriter](http://msdn.microsoft.com/zh-tw/library/system.io.streamwriter.aspx) ：以特定的編碼方式，讀取文字檔中的資料到Stream，或者將資料流中的資料寫入檔案。
- [StringReader](http://msdn.microsoft.com/zh-tw/library/system.io.stringreader.aspx) / [StringWriter](http://msdn.microsoft.com/zh-tw/library/system.io.stringwriter.aspx) ：用來從記憶體讀取字串，或將字串寫入記憶體。
- [BinaryReader](http://msdn.microsoft.com/zh-tw/library/system.io.binaryreader.aspx) / [BinaryWriter](http://msdn.microsoft.com/zh-tw/library/system.io.binarywriter.aspx) ：用來處理二進位檔案。

## StreamReader 和 StreamWriter
```c#
//Constructor
public StreamReader(Stream stream);
public StreamReader(string path);
public StreamReader(Stream stream, Encoding encoding);
public StreamReader(string path, Encoding encoding);
...
```

- [Peek](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.peek.aspx) ：傳回下一個可供使用的字元。不變更 position 。若無可用字元回傳 -1 。
- [Read](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.read.aspx) ：從目前位置讀取下一個字元。
- [ReadLine](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.readline.aspx) ：從目前位置讀取一行字元。
- [ReadToEnd](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.readtoend.aspx) ：從目前位置讀取到資料流末端。
- [Close](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.close.aspx) ：關閉 [StreamReader](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.aspx) 物件。

下列範例將示範如何將文字寫入文字檔。它會利用 "\*.txt" 搜尋模式，從使用者的 [我的文件] 資料夾讀取所有文字檔，並將這些檔案寫入大型文字檔中。
```c#
//取得我的文件資料夾
string mydocpath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);  
StringBuilder strBuilder = new StringBuilder();

//在指定的目錄底下，列舉檔案 EnumerateFiles
foreach (string txtName in Directory.EnumerateFiles(mydocpath, "\*.txt"))
{
using (StreamReader reader = new StreamReader(txtName))
{
strBuilder.AppendLine(txtName.ToString());
strBuilder.AppendLine("= = = = = =");
strBuilder.Append( reader.ReadToEnd() );
strBuilder.AppendLine();
strBuilder.AppendLine();
}
}

StreamWriter outfile = new StreamWriter(mydocpath + @"\AllTxtFiles.txt")
outfile.Write(strBuilder.ToString());
outfile.Close();
```

## StringReader 和 StringReader

下列範例將示範如何使用 [StringReader](http://msdn.microsoft.com/zh-tw/library/system.io.stringreader.aspx) / [StringReader](http://msdn.microsoft.com/zh-tw/library/system.io.stringreader.aspx) 讀寫檔案
```c#
string s = @"Hello all
This is a multi-line
text string";

StringReader rdr = new StringReader(s);
StringWriter wdr = new StringWriter();

// See if there are more characters 。Peek：傳回下一個字元, -1表示無資料
while (rdr.Peek() != -1)
{
string line = rdr.ReadLine();   //由 StringReader 讀取資料到 line
Console.WriteLine(line);

wdr.WriteLine(line);            //將 line 的資料寫入到 StringWriter
}
Console.WriteLine(wdr.ToString());  //傳回寫入StringWriter中的資料
```

## BinaryReader 和 BinaryWriter

下列範例將示範如何使用 [BinaryReader](http://msdn.microsoft.com/zh-tw/library/system.io.binaryreader.aspx) 讀取檔案
```c#
FileStream file = new FileStream(@"C:\\temp\\Log.txt", FileMode.Open, FileAccess.Read);
BinaryReader reader = new BinaryReader(file);

byte a = reader.ReadByte();
byte[] a5 = reader.ReadBytes((int)file.Length);

reader.Close();
file.Close();
```

# Memory Stream

記憶體資料流( [MemoryStream](http://msdn.microsoft.com/zh-tw/library/system.io.memorystream.aspx) )提供在記憶體中建立資料流。資料流的目前位置是下一個讀取或寫入作業所會進行的位置。目前的位置可以經由 [Seek](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.seek.aspx) 方法來加以擷取或設定。  
例如有一工作需陸續使用 [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 將資料寫入，為了減少檔案被鎖定的時間，我們可以先將資料寫入 [MemoryStream](http://msdn.microsoft.com/zh-tw/library/system.io.memorystream.aspx) ，再將資料由 [MemoryStream](http://msdn.microsoft.com/zh-tw/library/system.io.memorystream.aspx) 寫到 [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) ，如下列程式所示：  
```c#
MemoryStream memStrm = new MemoryStream();

StreamWriter writer = new StreamWriter(memStrm);
writer.WriteLine("Hello");
writer.WriteLine("Goodbye");

// Force the writer to push the data into the underlying stream
writer.Flush();  // Flush : 清除writer，並且將緩衝資料都寫入基礎資料流。

// Create a file stream
FileStream filStrm = File.Create(@"c:\inmemory.txt");

// Write the entire Memory stream to the file
memStrm.WriteTo(filStrm);

// Clean up
writer.Close();
filStrm.Close();
memStrm.Close();```

# Bufferd Stream

替一個Stream物件加入緩衝層，使讀取與寫入作業作用在Bufferd Stream。  緩衝區是記憶體中用以快取資料的位元組區塊，因此能減少呼叫作業系統的次數。  緩衝區會改善讀取和寫入的效能。  緩衝區可用於讀取或者寫入，但絕不能同時使用。   [BufferedStream](http://msdn.microsoft.com/zh-tw/library/system.io.bufferedstream.aspx) 的 [Read](http://msdn.microsoft.com/zh-tw/library/system.io.bufferedstream.read.aspx) 和 [Write](http://msdn.microsoft.com/zh-tw/library/system.io.bufferedstream.write.aspx) 方法自動維護緩衝區。  
[BufferedStream](http://msdn.microsoft.com/zh-tw/library/system.io.bufferedstream.aspx) 需包裹另外一個Stream物件，如下列程式所示：  

- 1. [Create](http://msdn.microsoft.com/zh-tw/library/system.io.file.create.aspx) a new [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) object, using the [File](http://msdn.microsoft.com/zh-tw/library/system.io.file.aspx) class to specify a new file.
- 2. [Create](http://msdn.microsoft.com/zh-tw/library/system.io.file.create.aspx) a new [BufferedStream](http://msdn.microsoft.com/zh-tw/library/system.io.bufferedstream.aspx) object, specifying the file stream as the underlying stream.
- 3. Use a [StreamWriter](http://msdn.microsoft.com/zh-tw/library/system.io.streamwriter.aspx) to write data into the buffered stream.
```c#
FileStream newFile = File.Create(@"c:\test.txt");
BufferedStream buffered = new BufferedStream(newFile);
StreamWriter writer = new StreamWriter(buffered);
writer.WriteLine("Some data");
writer.Close();
```

# 讀寫檔案總結

## 使用 File 物件

使用 [File](http://msdn.microsoft.com/zh-tw/library/system.io.file.aspx) 物件讀取檔案，必須整個檔案一次讀取
```c#
string sFilepath = @"C:\\temp\\Log.txt";
string sContent = "";
byte[] bArray = new byte[100];
int iNum = 0;
int iData = 0;

//===================================================================================
//0) File
//===================================================================================
string alltext                  = File.ReadAllText(sFilepath);
IEnumerable<string> lines    = File.ReadLines(sFilepath);        //還沒全部載入就可以讀取
string[] allLines               = File.ReadAllLines(sFilepath);     //必須全部載入後，才就可以讀取
byte[] bytes                    = File.ReadAllBytes(sFilepath);
```

## 使用 FileStream 物件

[FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 提供更多方法來讀取檔案資料。
```c#
//1) 由 File.Open 建立
FileStream filestream1 = File.Open( sFilepath, FileMode.Open, FileAccess.Read);
filestream1.Close();

//2) 由 FileStream Constructor 建立
FileStream filestream2 = new FileStream(sFilepath, FileMode.Open, FileAccess.Read);
filestream2.Close();

FileStream filestream = new FileStream(sFilepath, FileMode.Open, FileAccess.Read);
filestream.Position = 0;                    //變更指標位置
iNum  = filestream.Read(bArray, 0, 10);     //讀取一段
iData = filestream.ReadByte();              //讀取一個byte
IAsyncResult asyncResult = filestream.BeginRead(bArray, 0, 10, new AsyncCallback(EndReadCallback), filestream); //非同步讀取
```

## 使用 TextReader 物件

[TextReader](http://msdn.microsoft.com/zh-tw/library/system.io.textreader.aspx) 是文字的讀取器。
```c#
//1) StreamReader
TextReader textReader1 = File.OpenText(sFilepath);
iData       = textReader1.Read();                //一次讀取一個byte
sContent    = textReader1.ReadLine();            //一次讀取一行
sContent    = textReader1.ReadToEnd();           //一次讀取全部
textReader1.Close();

//2) StringReader
TextReader textReader2 = new StringReader("test text");
iData       = textReader2.Read();                //一次讀取一個byte
sContent    = textReader2.ReadLine();            //一次讀取一行
sContent    = textReader2.ReadToEnd();           //一次讀取全部
textReader2.Close();
```

## 使用 StreamReader 物件

[StreamReader](http://msdn.microsoft.com/zh-tw/library/system.io.streamreader.aspx) 是 [TextReader](http://msdn.microsoft.com/zh-tw/library/system.io.textreader.aspx) 的實作，可以特定的編碼方式讀取資料。
```c#
//1) 由 File.OpenText 建立
StreamReader streamReader1 = File.OpenText(sFilepath);
sContent = streamReader1.ReadToEnd();
streamReader1.Close();

//2) 由 StreamReader Constructor 建立
StreamReader streamReader2 = new StreamReader(sFilepath);
sContent = streamReader2.ReadToEnd();
streamReader2.Close();
```

## 使用 StringReader 物件

[StringReader](http://msdn.microsoft.com/zh-tw/library/system.io.stringreader.aspx) 也是 [TextReader](http://msdn.microsoft.com/zh-tw/library/system.io.textreader.aspx) 的實作，專門用來從記憶體讀取字串。
```c#
string s = @"Hello all
This is a multi-line
text string";

StringReader stringReader = new StringReader(s);
iData       = stringReader.Read();              //一次讀取一個byte
sContent    = stringReader.ReadLine();          //一次讀取一行
sContent    = stringReader.ReadToEnd();         //一次讀取全部
```