---
title: Specialized Collections
layout: default
parent: 集合與泛型
nav_order: 2
description: "Specialized Collections"
date: 2012-01-16
tags: [DotNetFramework,集合與泛型]
postid: "7540957754363348882"
---
前面介紹的集合，其組成項目都是由 Object 構成，當要擷取資料項目時就會用到cast objects。

在 [System.Collections.Specialized](http://msdn.microsoft.com/zh-tw/library/gg145035.aspx) 命名空間底下，定義了一些僅允許特定型別的集合類別。  例如：  

- [BitArray](http://msdn.microsoft.com/zh-tw/library/x3we7ff2.aspx) ：可調整大小的 boolean 陣列
- [BitVector32](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.bitvector32.aspx) ：用一個 32-bit 的整數值來儲存 32 個 boolean 值的結構
- [StringCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringcollection.aspx) ：可動態調整大小但是只能存放字串的集合
- [StringDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringdictionary.aspx) ：key/value 都必須是字串型別的 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) ，一個 key 只能對應一個 value 。
- [NameValueCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.namevaluecollection.aspx) ：key/value 都必須是字串型別的 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) ，一個 key 只能對應多個 value 。

# Boolean 集合

## BitArray 類別

在 [System.Collections](http://msdn.microsoft.com/zh-tw/library/system.collections) 命名空間下的 [BitArray](http://msdn.microsoft.com/zh-tw/library/system.collections.bitarray.aspx) 是一個可調整大小的 boolean 陣列，有多個建構子可以實體化，包含以下特性。

- 1.集合不能動態調整集合，所以不支援 [Add](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringcollection.add.aspx) 或 Remove 方法。
- 2.集合大小若需要調整，可透過 [Length](http://msdn.microsoft.com/zh-tw/library/system.collections.bitarray.length.aspx) 屬性來變更。
- 3.集合中的索從以零起始。
- 4.集合建立後，便會擁有一個含有 Boolean 值的集合，且每個預設值均為 false。
- 5.若有兩個集合需要運算，其索引長度需相同

#### BitArray 的建構函式
```c#
//指定長度的建構函式
public BitArray(int length);
public BitArray(int length, bool defaultValue);

//依資料值自定長度的建構函式
public BitArray(BitArray bits);
public BitArray(bool[] values);
public BitArray(byte[] bytes);
public BitArray(int[] values);
```

#### 初始化 [BitArray](http://msdn.microsoft.com/zh-tw/library/system.collections.bitarray.aspx) 
```c#
BitArray bArray1 = new BitArray(3);                 //建立一個 length=3 的 BitArray, 預設值為 false
Console.WriteLine(GetBinaryString(bArray1, 1));     //000

BitArray bArray2 = new BitArray(3, true);           //建立 BitArray，同時設定初始值為 true
Console.WriteLine(GetBinaryString(bArray2, 1));     //111

// 使用 bool[] 設定初值
bool[] status3 = new bool[] { true, true, true, false, true, false };
BitArray bArray3 = new BitArray(status3);
Console.WriteLine(bArray3.Length.ToString());       //6
Console.WriteLine(GetBinaryString(bArray3, 1));     //010111

// 使用 byte[] 設定初值，byte[] 中的每個值都表示 8 個 BitArray
byte[] status4 = new byte[] { 7 };
BitArray bArray4 = new BitArray(status4);
Console.WriteLine(bArray4.Length.ToString());       //8
Console.WriteLine(GetBinaryString(bArray4, 8));     //00000111

byte[] status5 = new byte[] { 1, 255, 16 };
BitArray bArray5 = new BitArray(status5);
Console.WriteLine(bArray5.Length.ToString());       //24
Console.WriteLine(GetBinaryString(bArray5, 8));     //00000001\*11111111\*00010000

// 使用 int[] 設定初值，int[] 中的每個值都表示 32 個 BitArray

int[] status6 = new int[] { 1, 255 };
BitArray bArray6 = new BitArray(status6);
Console.WriteLine(bArray6.Length.ToString());       //64
Console.WriteLine(GetBinaryString(bArray6, 32));    //00000000-00000000-00000000-00000001\*00000000-00000000-00000000-11111111
```

#### BitArray 集合運算

BitArray 真正強大的功能在於，它可以對兩個相同大小的 [BitArray](http://msdn.microsoft.com/zh-tw/library/x3we7ff2.aspx) 執行 Boolean 運算。

- And：在目前 [BitArray](http://msdn.microsoft.com/zh-tw/library/x3we7ff2.aspx) 中的元素上，針對指定的 [BitArray](http://msdn.microsoft.com/zh-tw/library/x3we7ff2.aspx) 中的對應元素，執行位元的 AND 運算。
- Or ：在目前 [BitArray](http://msdn.microsoft.com/zh-tw/library/x3we7ff2.aspx) 中的元素上，針對指定的 [BitArray](http://msdn.microsoft.com/zh-tw/library/x3we7ff2.aspx) 中的對應元素，執行位元的 OR 運算。
- Not：反轉目前 [BitArray](http://msdn.microsoft.com/zh-tw/library/x3we7ff2.aspx) 中的位元值，使得設定為 true 的元素變更為 false，並且設定為 false 的元素變更為 true。
- Xor：在目前 [BitArray](http://msdn.microsoft.com/zh-tw/library/x3we7ff2.aspx) 中的元素上，針對指定的 [BitArray](http://msdn.microsoft.com/zh-tw/library/x3we7ff2.aspx) 中的對應元素，執行位元互斥 OR 運算。
```c#
BitArray array1 = new BitArray(3);
BitArray array2 = new BitArray(3);

array1[0] = false;
array1[1] = true;
array1[2] = false;

array2[0] = true;
array2[1] = true;
array2[2] = false;

BitArray array3 = array1.Xor(array2);
foreach (bool bit in array3)
Console.WriteLine(bit);

//True
//False
//False

array1[0] = false;
array1[1] = true;
array1[2] = false;

array2[0] = true;
array2[1] = true;
array2[2] = false;

BitArray array4 = array1.And(array2);
foreach (bool bit in array4)
Console.WriteLine(bit);
```

## BitVector32 結構

BitVector32 結構是用一個 32-bit 的整數值來儲存 32 個 boolean 值的結構，相較於 [BitArray](http://msdn.microsoft.com/zh-tw/library/system.collections.bitarray.aspx) ，它只能固定在 32 個位元的大小。

- 1. [BitVector32](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.bitvector32.aspx) 是用來處理二進位運算的結構。
- 2. 它將所有資料儲存成單一的32 bit整數。
- 3. Data 屬性可以取得整數值。
- 4. [CreateMask](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.bitvector32.createmask) ：用來建立位元遮罩。
- 5. [CreateSection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.bitvector32.createsection) ：用來包裝數值。
```c#
public BitVector32(BitVector32 value);
public BitVector32(int data);
```
BitVector32 的資料就是建構子轉二進位後的值，因為它是 32 bit，所以這個 data 允許的最大值就是 int 型別的最大值。  
#### 初始化 [BitVector32](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.bitvector32.aspx) 
```c#
//建立 BitVector32
BitVector32 bVector1 = new BitVector32(0);              //00000000-00000000-00000000-00000000
BitVector32 bVector2 = new BitVector32(1);              //00000000-00000000-00000000-00000001
BitVector32 bVector3 = new BitVector32(2);              //00000000-00000000-00000000-00000010
BitVector32 bVector4 = new BitVector32(4);              //00000000-00000000-00000000-00000100
BitVector32 bVector5 = new BitVector32(15);             //00000000-00000000-00000000-00001111
BitVector32 bVector6 = new BitVector32(2147483647);     //01111111-11111111-11111111-11111111
BitVector32 bVector7 = new BitVector32(-1);             //11111111-11111111-11111111-11111111
BitVector32 bVector8 = new BitVector32(-2147483648);    //10000000-00000000-00000000-00000000

bVector1[3] = true;
Console.WriteLine(GetBinaryString(bVector1));           //00000000-00000000-00000000-00000011 (3)
bVector1[4] = true;
Console.WriteLine(GetBinaryString(bVector1));           //00000000-00000000-00000000-00000111 (7)
bool bit7 = bVector1[7];                                //true
bool bit8 = bVector1[8];                                //false
```

#### 利用索引值變更位元值

若我們想要 1,3,5 這三個位元設成 1 ，因為 (00010101)&lt;sub&gt;2&lt;/sub&gt; = 21 , 所以那麼我們可以設定 bVector1[21]=true  
若我們想要 3,4 這三個位元設成 0 ，因為 (00001100)&lt;sub&gt;2&lt;/sub&gt; = 12 , 所以那麼我們可以設定 bVector1[12]=false  
```c#
BitVector32 bVector1 = new BitVector32(0);
bVector1[21] = true;                                    //00000000-00000000-00000000-00010101 (21) = 0 | 21
bVector1[12] = false;                                   //00000000-00000000-00000000-00010001 (17) = 21 & 12
```

簡單來看，設成 true ，相當於做 OR 運算；設成 fasle ，相當於做 AND 運算。  
```c#
BitVector32 bVector2 = new BitVector32(255);            //00000000-00000000-00000000-11111111 (255)
bVector2[8] = false;                                    //00000000-00000000-00000000-11110111 (247)
bVector2[32] = false;                                   //00000000-00000000-00000000-11010111 (215)     
bVector2[128] = false;                                  //00000000-00000000-00000000-01010111 (87)
```

#### 使用 [BitVector32](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.bitvector32.aspx) 做位元遮罩

若一次只想變更一個位元的值，那麼這個結構有提供一個方便的方法，那就是使用 CreateMask 建立位元遮罩。
```c#
//建立 BitVector32 遮罩
int mask1 = BitVector32.CreateMask();                   //無參數表是建立第 1 個 bit 的遮罩
int mask2 = BitVector32.CreateMask(mask1);              //建立第 2 個 bit 的遮罩
int mask3 = BitVector32.CreateMask(mask2);              //建立第 3 個 bit 的遮罩
int mask4 = BitVector32.CreateMask(mask3);              //建立第 4 個 bit 的遮罩
int mask5 = BitVector32.CreateMask(mask4);              //建立第 5 個 bit 的遮罩

BitVector32 bVector3 = new BitVector32(0);

bVector3[mask2] = true;                                  //00000000-00000000-00000000-00000010 (2)
Console.WriteLine(GetBinaryString(bVector3));
bVector3[mask5] = true;                                  //00000000-00000000-00000000-00010010 (18)
Console.WriteLine(GetBinaryString(bVector3));
bVector3[mask4] = false;                                 //00000000-00000000-00000000-00010010 (18)
Console.WriteLine(GetBinaryString(bVector3));
bVector3[mask3] = true;                                  //00000000-00000000-00000000-00010110 (22)
```

#### 使用 [BitVector32](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.bitvector32.aspx) 做位元包裝

[BitVector32](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.bitvector32.aspx) 除了可以用來處理個別位元，它也支援「位元包裝（Bit Packing）」。  也就是利用一個 [BitVector32](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.bitvector32.aspx) 型別，同時儲存多個數值小的整數。  建構一個區段，必須指明前一個區段是誰，以及這個區段的最大值。  
```c#
public static BitVector32.Section CreateSection(short maxValue);
public static BitVector32.Section CreateSection(short maxValue, BitVector32.Section previous);
```

假設需求四個整數，每個整數都不超過255，一般必須佔用四個 byte 來儲存，若使用 [BitVector32](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.bitvector32.aspx) ，則可以將其包裝成一個 32-bit 的數值。  
```c#
//建立 BitVector32 區段
BitVector32.Section mySect1 = BitVector32.CreateSection(255);
BitVector32.Section mySect2 = BitVector32.CreateSection(255, mySect1);
BitVector32.Section mySect3 = BitVector32.CreateSection(255, mySect2);
BitVector32.Section mySect4 = BitVector32.CreateSection(255, mySect3);

//初始化 BitVector32
BitVector32 bVector = new BitVector32(0);

//設定每個區段的值都為1
bVector[mySect1] = 1;           //00000000-00000000-00000000-00000001 (1)
bVector[mySect2] = 1;           //00000000-00000000-00000001-00000001 (257)
bVector[mySect3] = 1;           //00000000-00000001-00000001-00000001 (65793)
bVector[mySect4] = 1;           //00000001-00000001-00000001-00000001 (16843009)

//重設初值0，以清除所有位元的值
bVector = new BitVector32(0);

bVector[mySect1] = 8;           //00000000-00000000-00000000-00001000 (8)
bVector[mySect2] = 170;         //00000000-00000000-10101010-00001000 (43528)
bVector[mySect3] = 255;         //00000000-11111111-10101010-00001000 (16755208)
bVector[mySect4] = 192;         //11000000-11111111-10101010-00001000 (-1056986616)
```

上面這個例子，因為需求是四個不超過 255 的整數，所以一個 [BitVector32](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.bitvector32.aspx) 只能表示四個值。  若需求是更小的數值，則一個 [BitVector32](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.bitvector32.aspx) 就能表示更多個值。  
```c#
BitVector32.Section mySect1 = BitVector32.CreateSection(3);
BitVector32.Section mySect2 = BitVector32.CreateSection(3, mySect1);
BitVector32.Section mySect3 = BitVector32.CreateSection(15, mySect2);
BitVector32.Section mySect4 = BitVector32.CreateSection(15, mySect3);
BitVector32.Section mySect5 = BitVector32.CreateSection(255, mySect4);
BitVector32.Section mySect6 = BitVector32.CreateSection(255, mySect5);

BitVector32 bVector = new BitVector32(0);

bVector[mySect1] = 3;           //00000000-00000000-00000000-00000011 (3)
bVector[mySect2] = 3;           //00000000-00000000-00000000-00001111 (15)
bVector[mySect3] = 15;          //00000000-00000000-00000000-11111111 (255)
bVector[mySect4] = 15;          //00000000-00000000-00001111-11111111 (4095)
bVector[mySect5] = 255;         //00000000-00001111-11111111-11111111 (1048575)
bVector[mySect6] = 255;         //00001111-11111111-11111111-11111111 (268435455)
```

# String 集合

.Net支援二個特殊的集合，它們可以用來儲存字串的強型別。

## StringCollection 類別

[StringCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringcollection.aspx) 類似 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) ，可動態調整大小但是只能存放字串的集合。  

- 1.它與 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) 用法很像，一個索引鍵(Key)對應到一個內容(Value)，就跟陣列一樣，初值為0。
- 2.用 [Add](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringcollection.add) 方法，是將資料加到集合的最後一筆。
- 3.用 [Insert](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringcollection.insert) 方法是將資料插到集合的任意處，須選擇插入的位置(索引鍵)。
```c#
StringCollection SC = new System.Collections.Specialized.StringCollection();
SC.Add("First");
SC.Add("Second");
SC.Add("Third");
SC.Add("Fifth");
SC.Insert(3, "Fourth");

string theString = SC[3];    // 不在需要轉型
for (int i = 0; i < 5; i++)
{
Console.WriteLine(SC[i]);
}
//First
//Second
//Third
//Fourth
//Fifth
```

## StringDictionary 類別

[StringDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringdictionary.aspx) 就像是字串型別的 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 

- 1. 它與 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 用法很像，一個索引鍵(Key)對應到一個內容(Value)。
- 2. Key 不能重複(不分大小寫)。
- 3. Key/Value 都必須是字串。
```c#
StringDictionary SD = new StringDictionary();

SD["vito"] = "oldwang@gmail.com";
SD["vito"] = "old.wang@gmail.com";     // 相當於做 modify
SD["VITO"] = "OldWang@gmail.com";      // key 不分大小寫

// SD.Add("ViTo", "OldWang@gmail.com");  // key 不能重複

SD["jeremy lin"] = "jeremy.lin@gmail.com";
SD.Add("tom", "tom@yahoo.com");

if (SD.ContainsKey("vito"))             // 判斷key值是否存在
Console.WriteLine(SD["vito"]);
Console.WriteLine(SD["jeremy lin"]);
Console.WriteLine(SD["tom"]);

//OldWang@gmail.com
//jeremy.lin@gmail.com
//tom@yahoo.com

foreach (DictionaryEntry de in SD)
Console.WriteLine("{0,-25} {1}", de.Key, de.Value);

//jeremy lin                jeremy.lin@gmail.com
//vito                      OldWang@gmail.com
//tom                       tom@yahoo.com
```

## 不分大小寫與文化特性的集合

#### Case-Insensitive Collections

通常我們必須實作 [IComparer](http://msdn.microsoft.com/zh-tw/library/system.collections.icomparer) 和 [IEqualityComparer](http://msdn.microsoft.com/zh-tw/library/system.collections.iequalitycomparer.aspx) 介面以達到物件的比較或相等性的判斷。例如用來建立不區分大小寫的字典集合。  因為這個用法很常用，所以 .NET 特別設計了一個 [CollectionsUtil](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.collectionsutil) 類別，用以建立不分大小寫的 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 和 [SortedList](http://msdn.microsoft.com/zh-tw/library/system.collections.sortedlist.aspx) 。  

- [CreateCaseInsensitiveHashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.collectionsutil.createcaseinsensitivehashtable) ：建立不分大小寫的 Hashtable
- [CreateCaseInsensitiveSortedList](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.collectionsutil.createcaseinsensitivesortedlist) ：建立不分大小寫的 SortedList
```c#
//建立一個不分大小寫的 Hashtable
Hashtable inTable = CollectionsUtil.CreateCaseInsensitiveHashtable();
inTable["hello"] = "Hi";
inTable["HELLO"] = "Heya";
Console.WriteLine(inTable.Count); // 1

//建立一個不分大小寫的 SortedList
SortedList inList = CollectionsUtil.CreateCaseInsensitiveSortedList();
inList["hello"] = "Hi";
inList["HELLO"] = "Heya";
Console.WriteLine(inList.Count); // 1
```

#### Culture-Invariant Collections

[CollectionsUtil](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.collectionsutil) 只能建立不分大小寫的集合，若想建立不分文化特性的集合，可以利用 [StringComparer](http://msdn.microsoft.com/zh-tw/library/system.stringcomparer) 這個類別的執行個體加以指定。
```c#
//建立一個不分文化特性的 Hashtable
Hashtable hash = new Hashtable(StringComparer.InvariantCulture);
hash["hello"] = "Hi";
hash["HELLO"] = "Heya";
Console.WriteLine(hash.Count); // 2

//建立一個不分文化特性且不分大小寫的 SortedList
SortedList list = new SortedList(StringComparer.InvariantCultureIgnoreCase);
list["hello"] = "Hi";
list["HELLO"] = "Heya";
Console.WriteLine(list.Count); // 1
```

# NameValueCollection 類別

[NameValueCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.namevaluecollection.aspx) 類似 [StringDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringdictionary.aspx) 集合，具有以下特性：

- 1. [NameValueCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.namevaluecollection.aspx) 一個 Key 可對應到多個 Value，不同於 [StringDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringdictionary.aspx) 一個 Key 只可對應到一個 Value 。
- 2. 用 [GetValues](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.namevaluecollection.getvalues) 方法回傳的是陣列型態的值。
- 3. Key/Value 都要是字串

[NameValueCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.namevaluecollection.aspx) 類似 [StringDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringdictionary.aspx) 類別，差異在：

#### 1. [NameValueCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.namevaluecollection.aspx) 允許一個索引鍵可以對應到多個值，而這些值也可以透過索引位置進行擷取。
```c#
NameValueCollection nv = new NameValueCollection();
nv.Add("Key", "Some Text");
nv.Add("Key", "More Text");
foreach (string s in nv.GetValues("Key"))
Console.WriteLine(s);
// Some Text
// More Text
```

### 注意  
在操作新增項目到 [NameValueCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.namevaluecollection.aspx) 中的時候，使用[索引子]和[Add]方法，其作用行為會不大一樣；  
若使用索引子，且使用相同的索引鍵時，則只會保留最後加入的一個項目。  
```c#
//1)使用 Add 方法新增：若遇到相同的key值，會形成陣列
NameValueCollection NVC = new NameValueCollection();
NVC.Add("key1", "test 1");
NVC.Add("key1", "test 2");

foreach (string s in NVC.GetValues("key1"))
Console.WriteLine(s);
// test 1
// test 2

foreach (string s in NVC.GetValues("key1"))
Console.WriteLine(s);
// key1 Text 2

//2)使用 索引鍵 新增：若遇到相同的key值，會重新給值
NVC.Clear();
NVC["key2"] = "test 1";
NVC["key2"] = "test 2";
Console.WriteLine("共{0}元素，key2={1}", NVC.GetValues("key2").Length, NVC.GetValues("key2")[0]);
// 共1元素，key2=test 2
```

#### 2. [NameValueCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.namevaluecollection.aspx) 可以利用索引位置擷取項目，如果值有多個，會傳回逗號間隔的一串資訊。
```c#
//使用for列舉
NVC.Clear();
NVC.Add("Key1", "First Text");
NVC.Add("Key2", "Second Text");
NVC.Add("Key2", "More Text");
for (int x = 0; x < NVC.Count; ++x)
{
Console.WriteLine(NVC[x]);
}
//First Text
//Second Text,More Text

//使用foreach列舉 (這個方法若遇到多個值的key時，會有錯)
foreach (string s in NVC.AllKeys)
Console.WriteLine("{0,-10} {1}", s, NVC[s]);
```

透過 index 取得是項目的值，這個 index 其實是指 keys 的 index。  
所以，要求 [NameValueCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.namevaluecollection.aspx) 回傳第 n 個項目時，其實回傳的是第 n 個 key 的項目。