---
title: 編碼與解碼
layout: default
parent: 文字編碼與正規化
nav_order: 1
description: "編碼與解碼"
date: 2012-01-16
tags: [DotNetFramework,文字編碼與正規化]
postid: "7921356303589731473"
---
# 什麼是編碼與解碼

「碼」：將原本的資訊，以特別的代號來表示，只要知道每種代號的意義，就能以代號來獲知原本的資訊。  例如：.\_  \_... \_.\_. 就是摩斯電碼。還有像 ASCII, BIG5, UTF8 ... 都是一種「碼」。  

- 編碼(encoding)：將一組 Unicode 字元轉換成位元組序列的處理程序。( string to byte[] )
- 解碼(decoding)：將已編碼的位元組序列轉換成一組 Unicode 字元的處理程序。( byte[] to string)

我們都知道，電腦中的文字，都必須使用某種編碼標準(encoding standard)進行編碼(encoding)後，才可以存放在記憶體或檔案中。只是這個編碼與解碼的過程，通常系統都會自動幫我們處理。  不過，有時候我們還是必須手動處理這個編碼與解碼的過程，例如：要將資料和Unix系統交換時，或者要以指定的編碼標準處理文字檔案等等。  其實，每一種編碼標準都是為了轉換 byte value 與 character。  就像 [ASCII](http://zh.wikipedia.org/wiki/ASCII) 就是一種老舊但也是最廣泛使用的編碼標準之一。  例如其中 0x41 = A， 0x61 = a ...。  不過，因為這個編碼標準對非英語系的支援非常有限，所以也就延伸出各種 [Unicode](http://zh.wikipedia.org/wiki/Unicode) 的編碼標準以支援各種語言。  

每一種特定的語言，都存在一種或數種的字集碼，每一種字集碼就是一個代碼頁 ( code page )。  例如：big5 的 code page = 950，提供給繁體中文語系使用的編碼；Windows-1252 的 code page = 1252，提供給西歐的英語系使用；shift\_jis 的 code page = 932，提供給日語中的漢字使用。  不過，這些表示 code page 的數字，是由不同的系統廠商自行定義的。例如：UTF-8 在 IBM 稱作代碼頁1208，在微軟稱作代碼頁65001，在 SAP 稱作代碼頁4110。  更詳細的字元編碼概念可參閱這篇說明：[.NET Framework 中的字元編碼方式](http://msdn.microsoft.com/zh-tw/library/ms404377.aspx)。  

有了各種字集碼，讓電腦系統有方法可以處理不同語系的文字，但卻無法在同一個文件裏面同時支援多個語系。  [Unicode](http://zh.wikipedia.org/wiki/Unicode) 是為了解決傳統的字元編碼方案的侷限所產生的一項業界標準。  它對世界上大部分的文字系統進行了整理、編碼，使得電腦可以用更為簡化的方式來呈現和處理文字。  

#### Unicode

Unicode 本身並不具任何編碼型態，它可以說是所有 code page 的合成，包含大部分語言的字元。這張表含蓋的字集範圍介於 0 ~ 1114111 (0x10FFFF) 之間。   不過在現實生活中，沒有人會使用 Unicode 儲存資料，通常是是使用 UTF-8 或者 UTF-16 儲存資料。  在表示一個 Unicode 的字元時，通常會用「U+」然後緊接著一組十六進位的數字來表示這一個字元。例：U+0041表示「A」，U+0042表示「B」，U+E999表示「陳」。  

#### UCS-2

UCS-2 使用16位的編碼空間，也就是每個字元佔用2個位元組。  這樣理論上一共最多可以表示2^16^（即65536）個字元。基本滿足各種語言的使用。實際上目前版本的統一碼並未完全使用這16位編碼，而是保留了大量空間以作為特殊使用或將來擴展。  如果想查詢 Unicode 文字的編碼，可以利用 [Unicode lookup](http://unicodelookup.com/) 網站來查詢，一次可以輸入多個字並且可以一次幫你產生多種 Unicode 的表達形式。  

#### UTF-8

UTF-8（8-bit Unicode Transformation Format）是一種針對 Unicode 的可變長度字元編碼。也就是每個字元的編碼至少一個byte，最多四個byte。  可以用來表示 Unicode 標準中的任何字元，且其編碼中的第一個位元組仍與ASCII相容，這使得原來處理ASCII字元的軟體無須或只須做少部分修改，即可繼續使用。  因此，它逐漸成為電子郵件、網頁及其他儲存或傳送文字的應用中，優先採用的編碼。  
0~127：使用8-bit編碼，對應到 ASCII 。  
128~2047：使用16-bit編碼，可支援 Latin, Greek, Cyrillic, Hebrew 等語言。  
2048~65535：使用24-bit編碼，可支援 Chinese, Japanese,Korean 等語言。  
65536~：使用32-bit編碼，其他需求語言。  

#### BIG5

如果第一個 byte 是正數或零，那麼就確定是 ASCII 的字元。如果第一個 byte 是負數，就再讀一個 byte ，兩個 byte 當作為一個字元。  

.Net Framework 系統中，預設使用 UTF-16 編碼型態來表示字元，在 [System.Text](http://msdn.microsoft.com/zh-tw/library/system.text.aspx) 命名空間底下，同時還提供了幾種編碼與解碼的相關類別：  

- [Unicode UTF-32](http://msdn.microsoft.com/zh-tw/library/system.text.utf32encoding.aspx)：UTF32Encoding 類別，將每個字碼指標表示成 32 位元整數。
- [Unicode UTF-16](http://msdn.microsoft.com/zh-tw/library/system.text.unicodeencoding.aspx)：UnicodeEncoding 類別，將每個字碼指標表示成 16 位元整數。
- [Unicode UTF-8](http://msdn.microsoft.com/zh-tw/library/system.text.utf8encoding.aspx)：UTF8Encoding 類別，將每個字碼指標表示成 1 到 4 個位元組，對應到 windows 的 code page = 65001。
- [UTF-7](http://msdn.microsoft.com/zh-tw/library/system.text.utf7encoding.aspx)：UTF7Encoding 類別，將 Unicode 字元表示為 7 位元 ASCII 字元序列。
- [ASCII](http://msdn.microsoft.com/zh-tw/library/system.text.asciiencoding.aspx)：ASCIIEncoding 類別，僅支援有限的字元集(U+0000~U+007F)，對應到 Windows 字碼頁 20127。

任何經過 UTF-X 編碼的文字，最後都得解碼以還原到 Unicode 所對應數字，才知道它是哪一個字。

# 檢視編碼類型

.NET Framework 提供了 [Encoding](http://msdn.microsoft.com/zh-tw/library/86hf4sb8.aspx) 類別，可以用來取得 Unicode 字元的編碼規則，或者將不同的編碼與 Unicode 進行轉換。   除了上述提及的幾種方法可以取得特定的編碼物件，另外，透過 [Encoding.GetEncoding](http://msdn.microsoft.com/zh-tw/library/httb1hft.aspx) 則可以用來取得指定編碼方式的編碼物件。  

- [Encoding.GetBytes](http://msdn.microsoft.com/zh-tw/library/system.text.encoding.getbytes.aspx) ：將一組字元編碼成位元組序列。
- [Encoding.GetString](http://msdn.microsoft.com/zh-tw/library/system.text.encoding.getstring.aspx) ：將位元組序列解碼成字串。
- [Encoding.Default](http://msdn.microsoft.com/zh-tw/library/system.text.encoding.default) ：取得作業系統目前 ANSI 字碼頁的編碼方式。。
- [Encoding.Convert](http://msdn.microsoft.com/zh-tw/library/system.text.encoding.convert) ：將整個位元組陣列從一種編碼方式轉換成另一種編碼方式。
```c#
Encoding e1 = Encoding.Default;                 // get default encoding
Encoding e2 = Encoding.GetEncoding(950);        // get big5 encoding
Encoding e3 = Encoding.GetEncoding("Korean");   // get koren encoding
Encoding e4 = Encoding.GetEncoding(65001);      // get utf-8 encoding

Console.WriteLine("CodePage : {0}", e1.CodePage);
Console.WriteLine("EncodingName : {0}", e1.EncodingName);
//CodePage : 950
//EncodingName : 繁體中文 (Big5)

Console.WriteLine("CodePage : {0}", e4.CodePage);
Console.WriteLine("EncodingName : {0}", e4.EncodingName);
//CodePage : 65001
//EncodingName : Unicode (UTF-8)
}```
```c#
private void button10_Click(object sender, EventArgs e)
{
string strContent = "ABCabc012你我他";

//Default
ShowCode(Encoding.Default.GetBytes(strContent));            //x41 x42 x43 x61 x62 x63 x30 x31 x32 xA7 x41 xA7 xDA xA5 x4C 

//Big5 = Default
ShowCode(Encoding.GetEncoding(950).GetBytes(strContent));   //x41 x42 x43 x61 x62 x63 x30 x31 x32 xA7 x41 xA7 xDA xA5 x4C   '繁體中文 (Big5)

//GB2312
ShowCode(Encoding.GetEncoding(936).GetBytes(strContent));   //x41 x42 x43 x61 x62 x63 x30 x31 x32 xC4 xE3 xCE xD2 xCB xFB   '簡體中文 (GB2312)

//28591-ISO
ShowCode(Encoding.GetEncoding(28591).GetBytes(strContent)); //x41 x42 x43 x61 x62 x63 x30 x31 x32 x3F x3F x3F               '西歐語系 (ISO)

//Unicode = 1200
ShowCode(Encoding.Unicode.GetBytes(strContent));            //x41 x00 x42 x00 x43 x00 x61 x00 x62 x00 x63 x00 x30 x00 x31 x00 x32 x00 x60 x4F x11 x62 xD6 x4E
ShowCode(Encoding.GetEncoding(1200).GetBytes(strContent));  //x41 x00 x42 x00 x43 x00 x61 x00 x62 x00 x63 x00 x30 x00 x31 x00 x32 x00 x60 x4F x11 x62 xD6 x4E 

//UTF8 = 65001
ShowCode(Encoding.UTF8.GetBytes(strContent));               //x41 x42 x43 x61 x62 x63 x30 x31 x32 xE4 xBD xA0 xE6 x88 x91 xE4 xBB x96 
ShowCode(Encoding.GetEncoding(65001).GetBytes(strContent)); //x41 x42 x43 x61 x62 x63 x30 x31 x32 xE4 xBD xA0 xE6 x88 x91 xE4 xBB x96 

//UTF32
ShowCode(Encoding.UTF32.GetBytes(strContent));              //x41 x00 x00 x00 x42 x00 x00 x00 x43 x00 x00 x00 x61 x00 x00 x00 x62 x00 x00 x00 x63 x00 x00 x00 x30 x00 x00 x00 x31 x00 x00 x00 x32 x00 x00 x00 x60 x4F x00 x00 x11 x62 x00 x00 xD6 x4E x00 x00 
}
private void ShowCode(byte[] Content)
{
for (int i = 0; i < Content.Length; i++)
Console.Write("x" + Content[i].ToString("X2") + " ");
Console.Write("\n\r");
}
```

使用 [Encoding.Convert](http://msdn.microsoft.com/zh-tw/library/system.text.encoding.convert) 方法，可以將整個位元組陣列從一種編碼方式轉換成另一種編碼方式。  
```c#
byte[] byteDefault = Encoding.Default.GetBytes(strContent);                             // 使用 Default 的編碼方式, 將 Unicode 文字內容轉 byte[] 

byte[] tmpUTF8 = Encoding.Convert(Encoding.Default, Encoding.UTF8, byteDefault);        // 將 byte[] 中的資料, 轉成 UTF8 編碼格式的 byte[]

byte[] tmpUTF32 = Encoding.Convert(Encoding.UTF8, Encoding.UTF32, tmpUTF8);             // 再轉成 UTF32 編碼格式的 byte[]

byte[] tmpBig5 = Encoding.Convert(Encoding.UTF32, Encoding.GetEncoding(950), tmpUTF32); // 再轉成 950 編碼格式的 byte[]

Console.WriteLine(Encoding.GetEncoding(950).GetString(tmpBig5));                        // ABCabc012你我他
```

底下這個範例中的文字，原本是使用 950 編碼的格式，卻使用 28591 編碼格式輸出，所以變成亂碼。用下面方法可以轉回正確的文字
```c#
string sSource = "ABCabc012§A§Ú¥L";                              //這原本是使用 950 編碼的文字，卻使用 28591 編碼輸出
byte[] byteLatin1 = Encoding.GetEncoding(28591).GetBytes(sSource);  //先用 28591 取回 byte[]
string sBig5 = Encoding.GetEncoding(950).GetString(byteLatin1);     //再用 950 解碼成字串
Console.Write(sBig5);                                               // ABCabc012你我他
}
```

底下這個範例示範不同編碼規則轉換後的情況：
```c#
string sDecoded;
byte[] bSource;

strContent = "ABCabc012你我他";

//下面例子：因為韓文字集與中文字集無法相對應，轉換過程資料丟失
bSource = Encoding.GetEncoding(950).GetBytes(strContent);
bSource = Encoding.Convert(Encoding.GetEncoding(950), Encoding.GetEncoding(50225), bSource);   // 將中文編碼的 byte[], 轉成韓文編碼的 byte[]
bSource = Encoding.Convert(Encoding.GetEncoding(50225), Encoding.GetEncoding(950), bSource);   // 再轉回中文編碼的 byte[]
sDecoded = Encoding.GetEncoding(950).GetString(bSource);        //ABCabc012?我他

strContent = "ABCabc012你我他堃鴴";

//下面例子：將 Unicode 文字轉換成 BIG5 編碼方式的字元陣列
//因為 BIG5 字集比 Unicode 字集少，轉換過程資料丟失
bSource = Encoding.GetEncoding(950).GetBytes(strContent);
sDecoded = Encoding.GetEncoding(950).GetString(bSource);        //ABCabc012你我他??

//下面例子：將 Unicode 文字轉換成 UTF8 編碼方式的字元陣列
//可正常轉換
bSource = Encoding.UTF8.GetBytes(strContent);
sDecoded = Encoding.UTF8.GetString(bSource);                    //ABCabc012你我他堃鴴

//下面例子：將 Unicode 文字轉換成 Unicode 編碼方式的字元陣列
//可正常轉換
bSource = Encoding.Unicode.GetBytes(strContent);
sDecoded = Encoding.Unicode.GetString(bSource);                 //ABCabc012你我他堃鴴

//下面例子：將 UTF8 -> BIG5 -> UTF8
//轉換過程會造成資料丟失，即使再轉回原來的編碼方式也無法完全。
bSource = Encoding.UTF8.GetBytes(strContent);
bSource = Encoding.Convert(Encoding.UTF8, Encoding.GetEncoding(950), bSource); 
bSource = Encoding.Convert(Encoding.GetEncoding(950), Encoding.UTF8, bSource);   
sDecoded = Encoding.UTF8.GetString(bSource);                    //ABCabc012你我他??

//下面例子：先將 UTF8 -> UTF32 -> UTF8
//資料不丟失
bSource = Encoding.UTF8.GetBytes(strContent);
bSource = Encoding.Convert(Encoding.UTF8, Encoding.UTF32, bSource);   
bSource = Encoding.Convert(Encoding.UTF32, Encoding.UTF8, bSource);  
sDecoded = Encoding.UTF8.GetString(bSource);                    //ABCabc012你我他堃鴴
```

底下這個範例可取得系統所有可支援的 code page ，結果列於附註
```c#
foreach (EncodingInfo ei in Encoding.GetEncodings())    // 取得所有可支援的 code page
{
Encoding code = ei.GetEncoding();

Console.Write("{0,-6} {1,-50} ", ei.CodePage, ei.Name);
Console.Write("{0,-8} {1,-8} ", code.IsBrowserDisplay, code.IsBrowserSave);
Console.Write("{0,-8} {1,-8} ", code.IsMailNewsDisplay, code.IsMailNewsSave);
Console.Write("{0,-8} {1,-8} ", code.IsSingleByte, code.IsReadOnly);
Console.WriteLine("{0}", ei.DisplayName);
}```

# 指定檔案的編碼格式

#### 如何在寫入檔案時指定編碼類型

透過指定 StreamWriter 的編碼類型，可以在寫入檔案時指定檔案的編碼方式。  若沒有指明編碼方式，.Net Framework 會使用 UTF-8 編碼方式建立 StreamWriter  
```c#
StreamWriter swUtf7 = new StreamWriter("utf7.txt", false, Encoding.UTF7);
swUtf7.WriteLine("Hello, World!");
swUtf7.Close();

StreamWriter swUtf8 = new StreamWriter("utf8.txt", false, Encoding.UTF8);
StreamWriter swUtf8 = new StreamWriter("utf8.txt", false); //與上一行同意
swUtf8.WriteLine("Hello, World!");
swUtf8.Close();

StreamWriter swUtf16 = new StreamWriter("utf16.txt", false, Encoding.Unicode);
swUtf16.WriteLine("Hello, World!");
swUtf16.Close();

StreamWriter swUtf32 = new StreamWriter("utf32.txt", false, Encoding.UTF32);
swUtf32.WriteLine("Hello, World!");
swUtf32.Close();```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjXiljmec1VcDbZn5Tttku1HLTYwEZhsf1-0n3_vYA-bu6CHXjpXPhWoeLuTObtRlWMAcSr0PqxgUKV-fKn2GCGKOEjuEnnyXGVC9RYvvflQTLBegmq1e9iYAorE22S8v1T94AELwEINFE/s677/streamwrite_encoding.png)

#### 如何在讀取檔案時指定編碼類型

一般讀取檔案也是不用指定編碼類型的，.Net Framework會自動對常用的編碼方式進行解碼。  不過，你也可以在建立 Stream 建構子時，直接指明編碼的類型。  
```c#
string fn;
StreamReader sr;

//不指定編碼方式,.Net自動對常用編碼方式進行解碼,所以程式不會出錯
fn = "utf8.txt";
sr = new StreamReader(fn);
Console.WriteLine(sr.ReadToEnd());
sr.Close();

//指定編碼方式UTF7進行解碼
fn = "utf7.txt";
sr = new StreamReader(fn, Encoding.UTF7);
Console.WriteLine(sr.ReadToEnd());
sr.Close();

//不指定編碼方式,因為UTF7不是常用編碼,.Net無法自動進行解碼,所以輸出會有亂碼
fn = "utf7.txt";
sr = new StreamReader(fn);
Console.WriteLine(sr.ReadToEnd());
sr.Close();```

請注意， [Encoding](http://msdn.microsoft.com/zh-tw/library/86hf4sb8.aspx) 主要是處理 Unicode 字元，而不是任意的二進位資料，例如位元組陣列。  如果應用程式必須將任意二進位資料編碼成文字，應該使用諸如 uuencode 的通訊協定；該通訊協定是由 Convert.ToBase64CharArray 之類的方法所實作。


- [代碼頁](http://zh.wikipedia.org/wiki/%E5%AD%97%E7%A2%BC%E9%A0%81)
- [Unicode](http://zh.wikipedia.org/wiki/Unicode)
- [NET Framework 中的字元編碼方式](http://msdn.microsoft.com/zh-tw/library/ms404377.aspx)
- [使用 Unicode 編碼方式](http://msdn.microsoft.com/zh-tw/library/zs0350fy.aspx)

**附註：使用 GetEncodings 取得的系統編碼類型**  

| CodePage | Name | BrDisp | BrSave | MNDisp | MNSave | 1-Byte | ReadOnly | Display |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 37 | IBM037 | False | False | False | False | True | True | IBM EBCDIC (美國-加拿大) |
| 437 | IBM437 | False | False | False | False | True | True | OEM 美國 |
| 500 | IBM500 | False | False | False | False | True | True | IBM EBCDIC (國際) |
| 708 | ASMO-708 | True | True | False | False | True | True | 阿拉伯文 (ASMO 708) |
| 720 | DOS-720 | True | True | False | False | True | True | 阿拉伯文 (DOS) |
| 737 | ibm737 | False | False | False | False | True | True | 希臘文 (DOS) |
| 775 | ibm775 | False | False | False | False | True | True | 波羅的海文 (DOS) |
| 850 | ibm850 | False | False | False | False | True | True | 西歐語系 (DOS) |
| 852 | ibm852 | True | True | False | False | True | True | 中歐語系 (DOS) |
| 855 | IBM855 | False | False | False | False | True | True | OEM 斯拉夫文 |
| 857 | ibm857 | False | False | False | False | True | True | 土耳其文 (DOS) |
| 858 | IBM00858 | False | False | False | False | True | True | OEM 多語系拉丁文 I |
| 860 | IBM860 | False | False | False | False | True | True | 葡萄牙文 (DOS) |
| 861 | ibm861 | False | False | False | False | True | True | 冰島文 (DOS) |
| 862 | DOS-862 | True | True | False | False | True | True | 希伯來文 (DOS) |
| 863 | IBM863 | False | False | False | False | True | True | 加拿大法文 (DOS) |
| 864 | IBM864 | False | False | False | False | True | True | 阿拉伯文 (864) |
| 865 | IBM865 | False | False | False | False | True | True | 北歐字母 (DOS) |
| 866 | cp866 | True | True | False | False | True | True | 斯拉夫文 (DOS) |
| 869 | ibm869 | False | False | False | False | True | True | 希臘文，現代 (DOS) |
| 870 | IBM870 | False | False | False | False | True | True | IBM EBCDIC (多語系拉丁文 2) |
| 874 | windows-874 | True | True | True | True | True | True | 泰文 (Windows) |
| 875 | cp875 | False | False | False | False | True | True | IBM EBCDIC (希臘現代) |
| 932 | shift\_jis | True | True | True | True | False | True | 日文 (Shift-JIS) |
| 936 | gb2312 | True | True | True | True | False | True | 簡體中文 (GB2312) |
| 949 | ks\_c\_5601-1987 | True | True | True | True | False | True | 韓文 |
| 950 | big5 | True | True | True | True | False | True | 繁體中文 (Big5) |
| 1026 | IBM1026 | False | False | False | False | True | True | IBM EBCDIC (土耳其拉丁文 5) |
| 1047 | IBM01047 | False | False | False | False | True | True | IBM 拉丁文 1 |
| 1140 | IBM01140 | False | False | False | False | True | True | IBM EBCDIC (美國-加拿大-歐洲) |
| 1141 | IBM01141 | False | False | False | False | True | True | IBM EBCDIC (德國-歐洲) |
| 1142 | IBM01142 | False | False | False | False | True | True | IBM EBCDIC (丹麥-挪威-歐洲) |
| 1143 | IBM01143 | False | False | False | False | True | True | IBM EBCDIC (芬蘭-瑞典-歐洲) |
| 1144 | IBM01144 | False | False | False | False | True | True | IBM EBCDIC (義大利-歐洲) |
| 1145 | IBM01145 | False | False | False | False | True | True | IBM EBCDIC (?霂Z牙-歐洲) |
| 1146 | IBM01146 | False | False | False | False | True | True | IBM EBCDIC (英國-歐洲) |
| 1147 | IBM01147 | False | False | False | False | True | True | IBM EBCDIC (法國-歐洲) |
| 1148 | IBM01148 | False | False | False | False | True | True | IBM EBCDIC (國際-歐洲) |
| 1149 | IBM01149 | False | False | False | False | True | True | IBM EBCDIC (冰島-歐洲) |
| 1200 | utf-16 | False | True | False | False | False | True | Unicode |
| 1201 | utf-16BE | False | False | False | False | False | True | Unicode (位元組由大到小) |
| 1250 | windows-1250 | True | True | True | True | True | True | 中歐語系 (Windows) |
| 1251 | windows-1251 | True | True | True | True | True | True | 斯拉夫文 (Windows) |
| 1252 | Windows-1252 | True | True | True | True | True | True | 西歐語系 (Windows) |
| 1253 | windows-1253 | True | True | True | True | True | True | 希臘文 (Windows) |
| 1254 | windows-1254 | True | True | True | True | True | True | 土耳其文 (Windows) |
| 1255 | windows-1255 | True | True | True | True | True | True | 希伯來文 (Windows) |
| 1256 | windows-1256 | True | True | True | True | True | True | 阿拉伯文 (Windows) |
| 1257 | windows-1257 | True | True | True | True | True | True | 波羅的海文 (Windows) |
| 1258 | windows-1258 | True | True | True | True | True | True | 越南文 (Windows) |
| 1361 | Johab | False | False | False | False | False | True | 韓文 (Johab) |
| 10000 | macintosh | False | False | False | False | True | True | 西歐語系 (Mac) |
| 10001 | x-mac-japanese | False | False | False | False | False | True | 日文 (Mac) |
| 10002 | x-mac-chinesetrad | False | False | False | False | False | True | 繁體中文 (Mac) |
| 10003 | x-mac-korean | False | False | False | False | False | True | 韓文 (Mac) |
| 10004 | x-mac-arabic | False | False | False | False | True | True | 阿拉伯文 (Mac) |
| 10005 | x-mac-hebrew | False | False | False | False | True | True | 希伯來文 (Mac) |
| 10006 | x-mac-greek | False | False | False | False | True | True | 希臘文 (Mac) |
| 10007 | x-mac-cyrillic | False | False | False | False | True | True | 斯拉夫文 (Mac) |
| 10008 | x-mac-chinesesimp | False | False | False | False | False | True | 簡體中文 (Mac) |
| 10010 | x-mac-romanian | False | False | False | False | True | True | 羅馬尼亞文 (Mac) |
| 10017 | x-mac-ukrainian | False | False | False | False | True | True | 烏克蘭文 (Mac) |
| 10021 | x-mac-thai | False | False | False | False | True | True | 泰文 (Mac) |
| 10029 | x-mac-ce | False | False | False | False | True | True | 中歐語系 (Mac) |
| 10079 | x-mac-icelandic | False | False | False | False | True | True | 冰島文 (Mac) |
| 10081 | x-mac-turkish | False | False | False | False | True | True | 土耳其文 (Mac) |
| 10082 | x-mac-croatian | False | False | False | False | True | True | 克羅埃西亞文 (Mac) |
| 12000 | utf-32 | False | False | False | False | False | True | Unicode (UTF-32) |
| 12001 | utf-32BE | False | False | False | False | False | True | Unicode (UTF-32 位元組由大到小) |
| 20000 | x-Chinese-CNS | False | False | False | False | False | True | 繁體中文 (CNS) |
| 20001 | x-cp20001 | False | False | False | False | False | True | TCA 台灣 |
| 20002 | x-Chinese-Eten | False | False | False | False | False | True | 繁體中文 (Eten) |
| 20003 | x-cp20003 | False | False | False | False | False | True | IBM5550 台灣 |
| 20004 | x-cp20004 | False | False | False | False | False | True | TeleText 台灣 |
| 20005 | x-cp20005 | False | False | False | False | False | True | Wang 台灣 |
| 20105 | x-IA5 | False | False | False | False | True | True | 西歐語系 (IA5) |
| 20106 | x-IA5-German | False | False | False | False | True | True | 德文 (IA5) |
| 20107 | x-IA5-Swedish | False | False | False | False | True | True | 瑞典文 (IA5) |
| 20108 | x-IA5-Norwegian | False | False | False | False | True | True | 挪威文 (IA5) |
| 20127 | us-ascii | False | False | True | True | True | True | US-ASCII |
| 20261 | x-cp20261 | False | False | False | False | False | True | T.61 |
| 20269 | x-cp20269 | False | False | False | False | True | True | ISO-6937 |
| 20273 | IBM273 | False | False | False | False | True | True | IBM EBCDIC (德國) |
| 20277 | IBM277 | False | False | False | False | True | True | IBM EBCDIC (丹麥-挪威) |
| 20278 | IBM278 | False | False | False | False | True | True | IBM EBCDIC (芬蘭-瑞典) |
| 20280 | IBM280 | False | False | False | False | True | True | IBM EBCDIC (義大利) |
| 20284 | IBM284 | False | False | False | False | True | True | IBM EBCDIC (西班牙) |
| 20285 | IBM285 | False | False | False | False | True | True | IBM EBCDIC (UK) |
| 20290 | IBM290 | False | False | False | False | True | True | IBM EBCDIC (日文片假名) |
| 20297 | IBM297 | False | False | False | False | True | True | IBM EBCDIC (法國) |
| 20420 | IBM420 | False | False | False | False | True | True | IBM EBCDIC (阿拉伯文) |
| 20423 | IBM423 | False | False | False | False | True | True | IBM EBCDIC (希臘文) |
| 20424 | IBM424 | False | False | False | False | True | True | IBM EBCDIC (希伯來文) |
| 20833 | x-EBCDIC-KoreanExtended | False | False | False | False | True | True | IBM EBCDIC (韓文擴充) |
| 20838 | IBM-Thai | False | False | False | False | True | True | IBM EBCDIC (泰國) |
| 20866 | koi8-r | True | True | True | True | True | True | 斯拉夫文 (KOI8-R) |
| 20871 | IBM871 | False | False | False | False | True | True | IBM EBCDIC (冰島) |
| 20880 | IBM880 | False | False | False | False | True | True | IBM EBCDIC (斯拉夫俄文) |
| 20905 | IBM905 | False | False | False | False | True | True | IBM EBCDIC (土耳其) |
| 20924 | IBM00924 | False | False | False | False | True | True | IBM 拉丁文 1 |
| 20932 | EUC-JP | False | False | False | False | False | True | 日文 (JIS 0208-1990 和 0212-1990) |
| 20936 | x-cp20936 | False | False | False | False | False | True | 簡體中文 (GB2312-80) |
| 20949 | x-cp20949 | False | False | False | False | False | True | 韓文 Wansung |
| 21025 | cp1025 | False | False | False | False | True | True | IBM EBCDIC (斯拉夫塞爾維亞文-保加利亞文) |
| 21866 | koi8-u | True | True | True | True | True | True | 斯拉夫文 (KOI8-U) |
| 28591 | iso-8859-1 | True | True | True | True | True | True | 西歐語系 (ISO) |
| 28592 | iso-8859-2 | True | True | True | True | True | True | 中歐語系 (ISO) |
| 28593 | iso-8859-3 | False | False | True | True | True | True | 拉丁文 3 (ISO) |
| 28594 | iso-8859-4 | True | True | True | True | True | True | 波羅的海文 (ISO) |
| 28595 | iso-8859-5 | True | True | True | True | True | True | 斯拉夫文 (ISO) |
| 28596 | iso-8859-6 | True | True | True | True | True | True | 阿拉伯文 (ISO) |
| 28597 | iso-8859-7 | True | True | True | True | True | True | 希臘文 (ISO) |
| 28598 | iso-8859-8 | True | True | False | False | True | True | 希伯來文 (ISO-Visual) |
| 28599 | iso-8859-9 | True | True | True | True | True | True | 土耳其文 (ISO) |
| 28603 | iso-8859-13 | False | False | True | True | True | True | 愛沙尼亞文 (ISO) |
| 28605 | iso-8859-15 | False | True | True | True | True | True | 拉丁文 9 (ISO) |
| 29001 | x-Europa | False | False | False | False | True | True | 歐洲 |
| 38598 | iso-8859-8-i | True | True | True | True | True | True | 希伯來文 (ISO-Logical) |
| 50220 | iso-2022-jp | False | False | True | True | False | True | 日文 (JIS) |
| 50221 | csISO2022JP | False | True | True | True | False | True | 日文 (JIS-Allow 1 byte Kana) |
| 50222 | iso-2022-jp | False | False | False | False | False | True | 日文 (JIS-Allow 1 byte Kana - SO/SI) |
| 50225 | iso-2022-kr | False | False | True | False | False | True | 韓文 (ISO) |
| 50227 | x-cp50227 | False | False | False | False | False | True | 簡體中文 (ISO-2022) |
| 51932 | euc-jp | True | True | True | True | False | True | 日文 (EUC) |
| 51936 | EUC-CN | False | False | False | False | False | True | 簡體中文 (EUC) |
| 51949 | euc-kr | False | False | True | True | False | True | 韓文 (EUC) |
| 52936 | hz-gb-2312 | True | True | True | True | False | True | 簡體中文 (HZ) |
| 54936 | GB18030 | True | True | True | True | False | True | 簡體中文 (GB18030) |
| 57002 | x-iscii-de | False | False | False | False | False | True | ISCII 梵文語系 |
| 57003 | x-iscii-be | False | False | False | False | False | True | ISCII 孟加拉文 |
| 57004 | x-iscii-ta | False | False | False | False | False | True | ISCII 坦米爾文 |
| 57005 | x-iscii-te | False | False | False | False | False | True | ISCII 特拉古文 |
| 57006 | x-iscii-as | False | False | False | False | False | True | ISCII 阿薩姆文 |
| 57007 | x-iscii-or | False | False | False | False | False | True | ISCII 歐利亞文 |
| 57008 | x-iscii-ka | False | False | False | False | False | True | ISCII 坎那達文 |
| 57009 | x-iscii-ma | False | False | False | False | False | True | ISCII 馬來亞拉姆文 |
| 57010 | x-iscii-gu | False | False | False | False | False | True | ISCII 古吉拉特文 |
| 57011 | x-iscii-pa | False | False | False | False | False | True | ISCII 旁遮普語 |
| 65000 | utf-7 | False | False | True | True | False | True | Unicode (UTF-7) |
| 65001 | utf-8 | True | True | True | True | False | True | Unicode (UTF-8) |