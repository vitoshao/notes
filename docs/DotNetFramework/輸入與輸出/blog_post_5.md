---
title: 資料流
layout: default
parent: 輸入與輸出
nav_order: 5
description: "資料流"
date: 2012-08-14
tags: [DotNetFramework,輸入與輸出]
postid: "7396751190288957306"
---
Stream 物件是由 byte 所組成的序列(sequence)。  它本身是一個抽象類別，底下圖表中的實作類別，可以使用在各種不同的場景下。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjMIQwRMr3hiu34DHfKFC4RAztn02lwJpaPmtijoe7453K5mP44j9QrBEexGc7WtWFF9xfUsQNReoB_-FWWDuuY3TRfU68YQ91ugofoJ_iI59y3Ok0C89y2rNZLBMlomJCfRRLgNxVhv7o/s850/dotnet-streams.png)

# 1.讀寫檔案
```c#
// FileStream
public FileStream(string path, FileMode mode, FileAccess access, FileShare share);
```

#### 寫入檔案
```c#
string tmp = "test1\r\ntest2\r\ntest3\r\n" + System.DateTime.Now.ToString();
byte[] data = Encoding.Default.GetBytes(tmp);
int numBytes = data.Length;

FileStream fileStream = File.Open(@"C:\\temp\\Log.txt", FileMode.Create, FileAccess.Write);
fileStream.Write(data, 0, numBytes);            //第一個參數是資料來源，準備寫入檔案
fileStream.Close();
```

#### 讀取檔案
```c#
FileStream fileStream = File.Open(@"C:\\temp\\Log.txt", FileMode.Open, FileAccess.Read);
int numBytes = (int)fileStream.Length;
byte[] data = new byte[numBytes];

int n = fileStream.Read(data, 0, numBytes);     //第一個參數是用來存放讀取的檔案資料
fileStream.Close();

string tmp = Encoding.Default.GetString(data);
```

# 2.讀寫隔離儲存區
```c#
// IsolatedStorageFileStream
public IsolatedStorageFileStream(string path, FileMode mode, FileAccess access, FileShare share);
```

#### 寫入隔離儲存區
```c#
//建立一個隔離儲存區
IsolatedStorageFile isoFile = IsolatedStorageFile.GetUserStoreForAssembly();

//在隔離儲存區中，建立一個檔案
IsolatedStorageFileStream isoStream = 
new IsolatedStorageFileStream("myfile.txt", FileMode.Create, isoFile);

//使用 StreamWriter 包裹 IsolatedStorageFileStream，方便寫入資料
StreamWriter streamWriter = new StreamWriter(isoStream);
streamWriter.WriteLine("這是寫入隔離區檔案的小範例");
streamWriter.WriteLine("IsolatedStorageFile：表示隔離儲存區");
streamWriter.WriteLine("IsolatedStorageFileStream：用來讀寫隔離儲存區");

//釋放資源
streamWriter.Dispose();
isoStream.Dispose();
isoFile.Dispose();
```

#### 讀取隔離儲存區
```c#
//建立一個隔離儲存區
IsolatedStorageFile isoFile = IsolatedStorageFile.GetUserStoreForAssembly();

if (isoFile.GetFileNames("myfile.txt").Length == 0)
{
Console.WriteLine("找不到檔案");
}
else
{
//開啟在隔離儲存區中的檔案
IsolatedStorageFileStream isoStream = 
new IsolatedStorageFileStream("myfile.txt", FileMode.Open, isoFile);

//使用 StreamReader 包裹 IsolatedStorageFileStream，方便讀取資料　
StreamReader streamReader = new StreamReader(isoStream);
string data = streamReader.ReadToEnd();

//釋放資源
streamReader.Dispose();
isoStream.Dispose();
isoFile.Dispose();
}
```

# 3.壓縮資料 & 解壓縮
```c#
// GZipStream
public GZipStream(Stream stream, CompressionMode mode);
```

#### 資料壓縮
```c#
StringBuilder tmp = new StringBuilder();
for (int i = 1; i <= 20000; i++)
tmp.AppendLine(i.ToString() + 
@" Hello World Hello World Hello World Hello World Hello World 
Hello World Hello World Hello World Hello World Hello World");
byte[] data = Encoding.Default.GetBytes(tmp.ToString());

//建立 FileStream （存放壓縮後資料用）
FileStream fileStream = File.Open("HelloWorld.gzip", FileMode.Create);

//建立 GZipStream
GZipStream gzipStream = new GZipStream(fileStream, CompressionMode.Compress);

//壓縮資料
gzipStream.Write(data, 0, data.Length);     // data中的資料將被壓縮至指定的 stream

//關閉物件
gzipStream.Close();
fileStream.Close();
```

#### 資料解壓縮
```c#
//建立 FileStream （開啟壓縮檔）
FileStream compress_data = File.Open("HelloWorld.gzip", FileMode.Open);

//建立 GZipStream
GZipStream gzipStream = new GZipStream(compress_data, CompressionMode.Decompress, true);

//解壓縮  (建立一個 filestream ，用來存放解壓縮後資料)
FileStream decompress_data = File.Create("HelloWorld.txt");     
gzipStream.CopyTo(decompress_data);    //將資料由 GZipStream, CopyTo filestream

//關閉物件
gzipStream.Close();
compress_data.Close();
decompress_data.Close();
```

# 4.序列化 & 還原序列化
```c#
// BinaryFormatter
public void Serialize(Stream serializationStream, object graph);
public object Deserialize(Stream serializationStream);
```

#### 序列化
```c#
[Serializable]
public class Grade
{
public string name;
public int math;
public int english;

public Grade(string _name, int _math, int _english)
{
name = _name;
math = _math;
english = _english;
}
}
```
```c#
Grade grade = new Grade("vito", 80, 90);

FileStream fileStream = new FileStream("Grade.BIN", FileMode.Create);
BinaryFormatter bFormatter = new BinaryFormatter();
bFormatter.Serialize(fileStream, grade);
fileStream.Close();
```

#### 還原序列化
```c#
FileStream fileStream = new FileStream("Grade.BIN", FileMode.Open);
BinaryFormatter bFormatter = new BinaryFormatter();
Grade grade = (Grade) bFormatter.Deserialize(fileStream);
fileStream.Close();
```

# 5.資料加密 & 解密
```c#
// CryptoStream
public CryptoStream(Stream stream, ICryptoTransform transform, CryptoStreamMode mode);

AesCryptoServiceProvider.CreateDecryptor
AesCryptoServiceProvider.CreateEncryptor
```

#### 資料加密
```c#
// Plain Data
byte[] bPlainText = Encoding.UTF8.GetBytes("this is data");

// KEY & IV
byte[] byteKey = Convert.FromBase64String("LuJ+rn5JBVI8YGM1wgCn6TLDqnpD4qhA8KflTP0m/fQ=");
byte[] byteIV = Convert.FromBase64String("3JqD0OWQRF3UuyVZvCdMEA==");

//1. 建立 AES 對稱加解密演算法物件
AesCryptoServiceProvider aesAlgrithm = new AesCryptoServiceProvider();

//2. 建立加密子物件。
ICryptoTransform transform = aesAlgrithm.CreateEncryptor(byteKey, byteIV);

//3. 建立一個 Stream , 用來存放加密資料用的資料流
MemoryStream memory_stream = new MemoryStream();

//4. 建立 CryptoStream 物件
CryptoStream crypto_stream = new CryptoStream(memory_stream, transform, CryptoStreamMode.Write);

//5. 將資料由 buffer 寫到 CryptoStream 資料流
crypto_stream.Write(bPlainText, 0, bPlainText.Length);      //將資料加密後，透過CryptoStraem，寫到MemoryStream
crypto_stream.FlushFinalBlock();

memory_stream.Close();
crypto_stream.Close();

byte[] bCipherText = memory_stream.ToArray();
sCipherText = Convert.ToBase64String(bCipherText);
```

#### 資料解密
```c#
// Cipher Data
byte[] bCipherText = Convert.FromBase64String(sCipherText);

// KEY & IV
byte[] byteKey = Convert.FromBase64String("LuJ+rn5JBVI8YGM1wgCn6TLDqnpD4qhA8KflTP0m/fQ=");
byte[] byteIV = Convert.FromBase64String("3JqD0OWQRF3UuyVZvCdMEA==");

//1. 建立 AES 對稱加解密演算法物件
AesCryptoServiceProvider aesAlgrithm = new AesCryptoServiceProvider();

//2. 建立解密子物件。
ICryptoTransform transform = aesAlgrithm.CreateDecryptor(byteKey, byteIV);

//3. 建立一個 Stream , 用來存放加密資料用的資料流
MemoryStream memory_stream = new MemoryStream();

//4. 建立 CryptoStream 物件
CryptoStream crypto_stream = new CryptoStream(memory_stream, transform, CryptoStreamMode.Write);

//5. 將資料由 buffer 寫到 CryptoStream 資料流
crypto_stream.Write(bCipherText, 0, bCipherText.Length);      //將資料解密後，透過CryptoStraem，寫到MemoryStream
crypto_stream.FlushFinalBlock();

crypto_stream.Close();
memory_stream.Close();

string sPlainText = Encoding.UTF8.GetString(memory_stream.ToArray());
```

#### 資料加密後存至檔案
```c#
// Plain Data
byte[] bPlainText = Encoding.UTF8.GetBytes("使用 AES 對稱加解密演算法物件");

// KEY & IV
byte[] byteKey = Convert.FromBase64String("LuJ+rn5JBVI8YGM1wgCn6TLDqnpD4qhA8KflTP0m/fQ=");
byte[] byteIV = Convert.FromBase64String("3JqD0OWQRF3UuyVZvCdMEA==");

//1. 建立 AES 對稱加解密演算法物件
AesCryptoServiceProvider aesAlgrithm = new AesCryptoServiceProvider();

//2. 建立一個 Stream , 用來存放加密資料用的資料流
FileStream fileStream = new FileStream("Encrypt.dat", FileMode.Create, FileAccess.Write);

//3. 建立加密子物件。
ICryptoTransform transform = aesAlgrithm.CreateEncryptor(byteKey, byteIV);

//4. 建立 CryptoStream 物件
CryptoStream crypto_stream = new CryptoStream(fileStream, transform, CryptoStreamMode.Write);

//5. 將資料由 buffer 寫到 CryptoStream 資料流
crypto_stream.Write(bPlainText, 0, bPlainText.Length);      //將資料加密後，透過 CryptoStraem，寫到 stream
crypto_stream.FlushFinalBlock();

fileStream.Close();
crypto_stream.Close();
```

#### 讀取檔案中的密文進行解密
```c#
// Cipher Data
byte[] bCipherText = Convert.FromBase64String(sCipherText);

// KEY & IV
byte[] byteKey = Convert.FromBase64String("LuJ+rn5JBVI8YGM1wgCn6TLDqnpD4qhA8KflTP0m/fQ=");
byte[] byteIV = Convert.FromBase64String("3JqD0OWQRF3UuyVZvCdMEA==");

//1. 建立 AES 對稱加解密演算法物件
AesCryptoServiceProvider aesAlgrithm = new AesCryptoServiceProvider();

//2. 建立一個 Stream , 用來存放加密資料用的資料流
FileStream fileStream = new FileStream("Encrypt.dat", FileMode.Open, FileAccess.Read);

//3. 建立解密子物件。
ICryptoTransform transform = aesAlgrithm.CreateDecryptor(byteKey, byteIV);

//4. 建立 CryptoStream 物件
CryptoStream crypto_stream = new CryptoStream(fileStream, transform, CryptoStreamMode.Read);

//5. 從 Stream 讀取資料以執行解密
StreamReader sReader = new StreamReader(crypto_stream);
string sPlainText = sReader.ReadToEnd();

sReader.Close();
crypto_stream.Close();
fileStream.Close();
```

# 6.讀寫 MemoryStream
```c#
public MemoryStream();
public MemoryStream(byte[] buffer);
...
```

#### 寫入 MemoryStream
```c#
MemoryStream memStream1;

string tmp = "test1\r\ntest2\r\ntest3\r\n" + System.DateTime.Now.ToString();
byte[] data = Encoding.Default.GetBytes(tmp);
int numBytes = data.Length;

//根據指定的位元組陣列，初始化 MemoryStream 執行個體。
memStream1 = new MemoryStream(data);
memStream1.Close();

//透過 StreamWriter 將資料寫入 MemoryStream 
var memoryStream = new MemoryStream();
StreamWriter streamWriter = new StreamWriter(memoryStream);
streamWriter.WriteLine("Hello World");

//The string is currently stored in the  
// StreamWriters buffer. Flushing the stream will  
// force the string into the MemoryStream. 
streamWriter.Flush(); 
```

#### 讀取 MemoryStream
```c#
string tmp = "test1\r\ntest2\r\ntest3\r\n" + System.DateTime.Now.ToString();
byte[] data = Encoding.Default.GetBytes(tmp);
MemoryStream memoryStream = new MemoryStream(data);

string text = "";

// The StreamReader will read from the current position of the MemoryStream 

StreamReader streamReader = new StreamReader(memoryStream, Encoding.Default);
text = streamReader.ReadToEnd();
Console.Write(text);
//test1
//test2
//test3
//2012/8/31 下午 07:47:07test2

memoryStream.Position = 7;
text = streamReader.ReadToEnd();
Console.Write(text);
//test2
//test3
//2012/8/31 下午 07:47:0

memoryStream.Seek(0, SeekOrigin.Begin);
text = streamReader.ReadLine();
Console.Write(text);
//test1

streamReader.Close();
```

# ７.讀寫 BufferedStream

使用 BufferedStream ，必須指定 catch 的大小，它有以下二個建構子：
```c#
public BufferedStream(Stream stream);                   //使用 4096 位元組的預設緩衝區大小，來初始化 System.IO.BufferedStream 類別的新執行個體。
public BufferedStream(Stream stream, int bufferSize);   //使用指定的緩衝區大小來初始化 System.IO.BufferedStream 類別的新執行個體。
```

#### 寫入 BufferedStream
```c#
byte[] data = Encoding.UTF8.GetBytes("使用 NetworkStream 傳送資料到遠端");

Socket clientSocket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
int BuffreSize = 4096;

NetworkStream netStream= new NetworkStream(clientSocket,true);
BufferedStream bufStream = new BufferedStream(netStream, BuffreSize);

//netStream.Write(data, 0, BuffreSize);
bufStream.Write(data, 0, BuffreSize);  //加入緩衝層
```