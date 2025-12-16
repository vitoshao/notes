---
title: 壓縮資料流
layout: default
parent: 輸入與輸出
nav_order: 2
description: "壓縮資料流"
date: 2012-01-07
tags: [DotNetFramework,輸入與輸出]
postid: "1909832344197656738"
---
# 認識 Compress Stream

.NET Framework 提供二個類別，用來處理壓縮資料。

- GZIP：使用 [GZipStream](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.aspx) 類別表示 GZIP 資料格式，它無失真檔案壓縮和解壓縮的工業標準演算法。
- Deflate：使用 [DeflateStream](http://msdn.microsoft.com/zh-tw/library/0y917bht.aspx) 類別表示結實 (Deflate) 演算法，該演算法是無失真檔案壓縮和解壓縮的工業標準演算法。它使用 LZ77 演算法和 Huffman編碼的組合。

關於這二個壓縮物件

1. 最大只能對4GB的資料進行壓縮。
2. GZIP 壓縮後的資料包含一些檔頭資訊，Deflate 則無。
3. GZipStream 類別使用 GZIP 資料格式，這種格式包含用於偵測資料損毀的循環冗餘檢查值 (Cyclic Redundancy Check value, CRC)。
4. 若要自用的話，使用 [DeflateStream](http://msdn.microsoft.com/zh-tw/library/0y917bht.aspx) 方法會壓的小一些；若要分發出去給其他人使用，則使用 [GZipStream](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.aspx) 。

.Net4.0已經沒有4G的限制了，另外在 [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) 類別中提供新的 [CopyTo](http://msdn.microsoft.com/zh-tw/library/dd992542.aspx) 方法，可快速方便複製目前資料流的內容到另一個資料流。
&lt;!-- more --&gt;  
# 如何壓縮資料流

一般資料流執行寫入動作時，會將資料輸出到某個資源檔中。  	例如： [FileStream](http://msdn.microsoft.com/zh-tw/library/system.io.filestream.aspx) 會輸出到檔案，而 [MemoryStream](http://msdn.microsoft.com/zh-tw/library/system.io.memorystream.aspx) 則是輸出到記憶體。  	而壓縮資料流則是輸出另一個資料流 (FileStream、MemoryStrem...)。  	所以，當建立 [GZipStream](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.aspx) 的執行個體時，必須提供一個 [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) 當參數，然後再呼叫執行個體的 [GZipStream.Write](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.write.aspx) 方法，資料就會寫入到該 [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) 。  

####  [GZipStream](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.aspx) 建構子：
```c#
//參數 stream
//壓縮時，做為輸出的資料流
//解壓縮時，做為來源的資料流
public GZipStream(Stream stream, CompressionMode mode);                     
public GZipStream(Stream stream, CompressionMode mode, bool leaveOpen);     //leaveOpen : true 表示在處置 GZipStream 物件之後，將資料流保持開啟，否則為 false。
```

####  [GZipStream.Write](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.write.aspx) ：

Writes compressed bytes to the underlying stream from the specified byte array.
```c#
public override void Write(
byte[] array,   //將 array 中的資料，壓縮之後，寫到執行個體指定的那個 stream。
int offset,
int count
)
```

####  [GZipStream.Read](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.read.aspx) ：

Reads a number of decompressed bytes into the specified byte array.
```c#
public override int Read(
byte[] array,   //由執行個體指定的那個 stream 讀取資料，解壓縮後，寫到 array 陣列。
int offset,
int count
)
```

#### 壓縮資料的簡單步驟：

- 建立一個輸出資料流 [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) ，用來存放壓縮後的資料。
- 建立 [GZipStream](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.aspx) 物件。指定輸出資料流，並將 [CompressionMode](http://msdn.microsoft.com/zh-tw/library/system.io.compression.compressionmode.aspx) 設定為 **Compress**。
- 讀取資料。
- 將讀取的資料，透過 [GZipStream.Write](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.write.aspx) 方法，執行壓縮，並將壓縮過的資料寫入到 [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) 。
```c#
public static void GZipCompress1(string inFilename)
{
// 建立一個輸出資料流 Stream ，用來存放壓縮後的資料。
string outFilename = inFilename + ".gz";
FileStream fs_Dest = File.Create(outFilename);

// 建立 GZipStream 物件。指定輸出資料流，並將 CompressionMode 設為 Compress。
GZipStream gzipStream = new GZipStream(fs_Dest, CompressionMode.Compress);

// 讀取來源檔的資料
FileStream fs_Source = File.OpenRead(inFilename);
const int buf_size = 4096;
byte[] buffer = new byte[buf_size];
int bytes_read = 0;
do
{
bytes_read = fs_Source.Read(buffer, 0, buf_size);
//寫入壓縮資料流 compStream
gzipStream.Write(buffer, 0, bytes_read);        
} while (bytes_read != 0);

gzipStream.Close();
fs_Dest.Close();
fs_Source.Close();
}
```
```c#
public static void GZipCompress2(string inFilename)
{
// 建立一個輸出資料流 Stream ，用來存放壓縮後的資料。
string outFilename = inFilename + ".gz";
FileStream fs_Dest = File.Create(outFilename);

// 建立 GZipStream 物件。指定輸出資料流，並將 CompressionMode 設為 Compress。
GZipStream compStream = new GZipStream(fs_Dest, CompressionMode.Compress);

// 讀取來源檔的資料
FileStream fs_Source = File.OpenRead(inFilename);

// 將資料由 filestream CopyTo GZipStream 完成壓縮
fs_Source.CopyTo(compStream);

compStream.Close();
fs_Dest.Close();
fs_Source.Close();
}
```
```c#
public static void GZipCompress3(string inFilename)
{
string outFilename = inFilename + ".gz";

// 目的檔
using (FileStream fs_Dest = File.Create(outFilename))
{
// 壓縮資料流
using (GZipStream gzipStream = new GZipStream(fs_Dest, CompressionMode.Compress))
{
// 來源檔
using (FileStream fs_Source = File.OpenRead(inFilename))
{
// 來源檔 -> 壓縮資料流
fs_Source.CopyTo(gzipStream);
fs_Source.Flush();
gzipStream.Flush();
}
}
}
}
```

# 如何解壓縮資料流

解壓縮和壓縮的方法大至相同，只是對象不太一樣。   
進行壓縮的時候， GZipStream 執行個體所包裹的資料流，是一個空的資料流，目的是要用來存放壓縮後的資料。  
進行解壓縮的時候， GZipStream 執行個體所包裹的資料流，是一個包含原壓縮資料的資料流，通常就是指向壓縮檔的檔案資料流。  

#### 解壓縮資料的簡單步驟：

- 建立一個資料流 [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) ，包裹要被解壓縮的資料。
- 建立 [GZipStream](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.aspx) 物件。指定來源資料流，並將 [CompressionMode](http://msdn.microsoft.com/zh-tw/library/system.io.compression.compressionmode.aspx) 設定為 **Decompress**。
- 建立一個 byte[] 的緩衝區，用來暫存解壓縮後的資料。
- 叫用 [GZipStream.Read](http://msdn.microsoft.com/zh-tw/library/system.io.compression.gzipstream.read.aspx) 方法，由 [Stream](http://msdn.microsoft.com/zh-tw/library/system.io.stream.aspx) 讀取資料，以執行解壓縮，並將解壓縮的資料存放在步驟３的緩衝區。
```c#
// 建立一個 DeflateStream
FileStream fs = new FileStream("testdata.def", FileMode.Open);  //開啟 Deflate 格式的壓縮檔
DeflateStream cs = new DeflateStream(fs, CompressionMode.Decompress);

MemoryStream memStream = new MemoryStream();
byte[] buffer = new byte[4096];
int offset = 0;
int count = 0;
while (true)
{
count = cs.Read(buffer, 0, 4096);   //讀取解壓縮資料
if (count > 0)
{
memStream.Write(buffer, 0, count);  //將 buffer 先丟到 memory stream
offset += count;
}
else
break;
}
byte[] data = memStream.ToArray();

cs.Close();
fs.Close();

string sContent = Encoding.Default.GetString(data);
```
```c#
public static void GZipDecompress2(string inFilename)
{
FileInfo fi = new FileInfo(inFilename);

//取得來源檔的資料流
FileStream fs_Source = fi.OpenRead();

// 建立 GZipStream 物件。指定來源資料流，並將 CompressionMode 設為 Decompress。
GZipStream gzipStream = new GZipStream(fs_Source, CompressionMode.Decompress);

//建立目的檔資料流
string outFilename = inFilename.Remove(inFilename.Length - fi.Extension.Length);  //移除壓縮檔副檔名
FileStream fs_Dest = File.Create(outFilename);

// 將資料由 GZipStream CopyTo filestream 完成解壓縮
gzipStream.CopyTo(fs_Dest);

gzipStream.Close();
fs_Source.Close();
fs_Dest.Close();
}
```
```c#
public static void GZipDecompress3(string inFilename)
{
FileInfo fi = new FileInfo(inFilename);
string outFilename = inFilename.Remove(inFilename.Length - fi.Extension.Length);  //移除壓縮檔副檔名

// 來源檔
using (FileStream fs_Source = File.OpenRead(inFilename))
{
// 解壓縮資料流
using (GZipStream gzipStream = new GZipStream(fs_Source, CompressionMode.Decompress))
{
// 目的檔
using (FileStream fs_Dest = File.Create(outFilename))
{
// 解壓縮資料流 -> 目的檔
gzipStream.CopyTo(fs_Dest);
fs_Source.Flush();
gzipStream.Flush();
}
}
}
}
```

# 範例：利用 MemoryStream 壓縮與解壓縮資料
```c#
//=================================================================================
// 產生測試資料
//=================================================================================
StringBuilder tmp = new StringBuilder();
for (int i = 1; i <= 20000; i++)
tmp.AppendLine(i.ToString() + " Hello World Hello World Hello World Hello World Hello World Hello World Hello World Hello World Hello World Hello World");

byte[] source_data = Encoding.Default.GetBytes(tmp.ToString());

//=================================================================================
// 壓縮 byte[] 資料至 MemoryStream 中
//=================================================================================

// 建立一個 ZipStream
MemoryStream dest_stream = new MemoryStream();
GZipStream zipStream1 = new GZipStream(dest_stream, CompressionMode.Compress);

// 輸出壓縮資料
zipStream1.Write(source_data, 0, source_data.Length);
zipStream1.Close();

// 將 MemoryStream 裡的資料轉到 byte[]
byte[] compress_data = dest_stream.ToArray();

//=================================================================================
// 將資料載入到 MemoryStream 中，再解壓縮
//=================================================================================

// 將資料載入到 MemoryStream 中
MemoryStream source_stream = new MemoryStream(compress_data);

// 建立一個 ZipStream 
GZipStream zipStream2 = new GZipStream(source_stream, CompressionMode.Decompress);

// 輸出解壓縮後資料
MemoryStream tmpMemoryStream = new MemoryStream();
int theByte = zipStream1.ReadByte();
while (theByte != -1)
{
tmpMemoryStream.WriteByte((byte)theByte);
theByte = zipStream2.ReadByte();
}

// 將 tmpMemoryStream 裡的資料轉到 byte[]
byte[] decompress_data = tmpMemoryStream.ToArray();
zipStream2.Close();

string sContent = Encoding.Default.GetString(decompress_data);
```