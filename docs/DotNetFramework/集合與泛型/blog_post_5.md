---
title: ArrayList 集合
layout: default
parent: 集合與泛型
nav_order: 5
description: "ArrayList 集合"
date: 2012-01-16
tags: [DotNetFramework,集合與泛型]
postid: "630048496486003860"
---
集合物件的特性：允許資料項目的增減，可以巡覽集合中的資料項目。

# 集合的種類

清單、佇列、位元陣列、雜湊表和字典等等都是.NET底下的一種集合，所有集合類別都由以下二個命名空間提供：

## 1. System.Collections 命名空間所提供的類別

| 類別 | 說明 |
| --- | --- |
| [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) | 使用大小會視需要動態增加的陣列，實作  [IList](http://msdn.microsoft.com/zh-tw/library/system.collections.ilist.aspx) 介面。 |
| [CaseInsensitiveComparer](http://msdn.microsoft.com/zh-tw/library/system.collections.caseinsensitivecomparer.aspx) | 比較兩個物件是否相等，忽略字串的大小寫。 |
| [CaseInsensitiveHashCodeProvider](http://msdn.microsoft.com/zh-tw/library/system.collections.caseinsensitivehashcodeprovider.aspx) | **已過時。**使用忽略字串大小寫的雜湊演算法，提供物件的雜湊程式碼。 |
| [CollectionBase](http://msdn.microsoft.com/zh-tw/library/system.collections.collectionbase.aspx) | 提供強式型別集合的 abstract 基底類別。 |
| [ReadOnlyCollectionBase](http://msdn.microsoft.com/zh-tw/library/system.collections.readonlycollectionbase.aspx) | 提供強式非泛型唯讀集合的 abstract 基底類別。 |
| [Comparer](http://msdn.microsoft.com/zh-tw/library/system.collections.comparer.aspx) | 比較兩個物件是否相等，其中字串比較是區分大小寫的。 |
| [DictionaryBase](http://msdn.microsoft.com/zh-tw/library/system.collections.dictionarybase.aspx) | 提供索引鍵/值組配對強式集合的 abstract 基底類別。 |
| [Stack](http://msdn.microsoft.com/zh-tw/library/system.collections.stack.aspx) | 表示簡單之物件的後進先出 (Last-In First-Out，LIFO) 非泛型集合。 |
| [Queue](http://msdn.microsoft.com/zh-tw/library/system.collections.queue.aspx) | 表示物件的先進先出 (FIFO) 集合。 |
| [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) | 表示根據索引鍵的雜湊程式碼，所整理的索引鍵/值組集合。 |
| [SortedList](http://msdn.microsoft.com/zh-tw/library/system.collections.sortedlist.aspx) | 表示索引鍵/值組配對的集合，這個集合按索引鍵排序，而且可以按索引鍵和索引存取。 |
| [BitArray](http://msdn.microsoft.com/zh-tw/library/system.collections.bitarray.aspx) | 管理以布林 (Boolean) 表示的位元值之精簡陣列，其中 true 表示位元為開啟 (1)，而 false 表示位元為關閉 (0)。 |
| [StructuralComparisons](http://msdn.microsoft.com/zh-tw/library/system.collections.structuralcomparisons.aspx) | 提供物件，用以執行兩個集合物件的結構比較。 |
&lt;!-- more --&gt;  
## 2. System.Collections.Specializeds 命名空間所提供的類別

| 類別 | 說明 |
| --- | --- |
| [CollectionsUtil](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.collectionsutil.aspx) | 建立忽略字串大小寫的集合。 |
| [HybridDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.hybriddictionary.aspx) | 在集合很小時，使用               [ListDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.listdictionary.aspx) 來實作 **IDictionary**，然後在集合變大時，切換至               [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx)。 |
| [ListDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.listdictionary.aspx) | 使用單向連結串列來實作 [IDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.idictionary.aspx) 。建議用於通常包含 10 個或更少項目的集合。 |
| [NameObjectCollectionBase](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.nameobjectcollectionbase.aspx) | 提供 **abstract** 基底類別給可以用索引鍵或索引來存取之關聯               [String](http://msdn.microsoft.com/zh-tw/library/system.string.aspx) 索引鍵和               [Object](http://msdn.microsoft.com/zh-tw/library/system.object.aspx) 值的集合。 |
| [NameObjectCollectionBase.KeysCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.nameobjectcollectionbase.keyscollection.aspx) | 表示集合索引鍵 **String** 的集合。 |
| [NameValueCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.namevaluecollection.aspx) | 表示相關 **String** 索引鍵和 **String** 值的集合，而這些可以利用索引鍵或索引來存取。 |
| [OrderedDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.ordereddictionary.aspx) | 表示根據索引鍵/索引排序的索引鍵/值組集合。 |
| [StringCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringcollection.aspx) | 表示字串的集合。 |
| [StringDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringdictionary.aspx) | 實作雜湊資料表，其中的索引鍵與資料值均採用強式型別宣告為字串，而不是物件。 |
| [StringEnumerator](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringenumerator.aspx) | 支援在               [StringCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringcollection.aspx) 上的簡易反覆運算。 |

# ArrayList 類別

[ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) 是一種基本的集合物件，有著以下特性：

- 隨著元素逐漸加入，會視需要透過重新配置的方式自動增加容量。
- 在這個集合的項目是沒有經過排序的，可以儲存任何型別(Object)，也接受 null 值，也允許重複的項目。
- 如果置放實質型別，則會被 Boxing。

#### ArrayList 方法

- [Add](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringcollection.add.aspx) ：加入一個項目至最後頭
- [AddRange](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.addrange.aspx) ：加入一個陣列或集合
- [Insert](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringcollection.insert.aspx) ：插入一個陣列
- [InsertRange](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.insertrange.aspx) ：插入一個陣列或集合
- [Remove](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.remove.aspx) ：移除特定物件之第一個符合的元素。
- [RemoveAt](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.removeat.aspx) ：移除第 n 個項目
- [RemoveRange](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.removerange.aspx) ：移除第 n~m 個項目
- [Clear](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.clear.aspx) ：清除所有項目
- [GetEnumerator](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.getenumerator.aspx) ：傳回會逐一查看 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) 的列舉程式。
- [IndexOf](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.indexof.aspx) ：傳回 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) 或其中一部分中值的第一個項目之以零起始的索引。
- [LastIndexOf](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.lastindexof.aspx) ：
- [Contains](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.contains.aspx) ：判斷某元素是否在 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) 中。
- [BinarySearch](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.binarysearch.aspx) ：使用二進位搜尋演算法來尋找在排序的 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) 中或在其中一部分中的特定元素。
- [Reverse](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.reverse.aspx) ：反轉 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) 或其中一部分中元素的順序。
- [Sort](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.sort.aspx) ：排序 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) 或其中一部分的項目。
- [Synchronized](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.synchronized.aspx) ：傳回同步 (安全執行緒) 的清單包裝函式。
- [ToArray](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.toarray.aspx) ：將 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) 的元素複製到新的陣列。
- [CopyTo](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.copyto.aspx) ：複製至一維陣列。

底下內容，皆以 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) 來說明集合的基本操作功能。

## 加入或移除資料項目
```c#
ArrayList alCol = new ArrayList();

// 集合項目可以是 value type 或 reference type

// 加入一個項目
string s = "Hello";
alCol.Add(s);
alCol.Add("hi");
alCol.Add(50);
alCol.Add(new object());

// 加入一個陣列
string[] anArray = new string[] { "more", "or", "less" };
alCol.AddRange(anArray);

// 加入一個集合
object[] anotherArray = new object[] { new object(), new ArrayList() };
alCol.AddRange(anotherArray);

// 插入一個項目
alCol.Insert(3, "Hey All");

// 插入一個陣列
string[] moreStrings =  new string[] { "goodnight", "see ya" };
alCol.InsertRange(4, moreStrings);

// 更新一個項目
alCol[3] = "Hi all";

// 移除一個項目
alCol.Add("Hello");
alCol.Remove("Hello");

// 移除第１個項目
alCol.RemoveAt(0);

// 移除前４個項目
alCol.RemoveRange(0, 4);

//判斷是否存在某個項目
if (alCol.Contains("Hello"))
{
//找出某個item的Index
int index2 = alCol.IndexOf("Helll");   //non match retrun -1
int index = alCol.IndexOf("Hello");
alCol.RemoveAt(index);
}

//清除所有項目
alCol.Clear();
```

## 巡訪資料項目(Iterate)

要取得 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) 中的項目，有以下幾種方法：

- 1. **利用 indexer**      ```c#
for (int x = 0; x < coll.Count; ++x)
{
Console.WriteLine(coll[x]);
}```
- 2. **利用 foreach** ：      只要支援 [IEnumerable](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable.aspx) 介面的物件，就可以使用 foreach 語法，簡單地達到巡訪所有 item 的操作      ```c#
foreach (object item in coll)
{
Console.WriteLine(item);
}

//若已知集合中的項目皆為同一型別，可以在foreach指定型別，以節省物件轉型的時間
foreach (string item in coll)
{
Console.WriteLine(item);
}
```
- 3. **利用 IEnumerator** ： [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) 支援 [IEnumerable](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable.aspx) 介面，它允許使用**列舉值** ([IEnumerator](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerator%28v=VS.100%29.aspx)) 來存取串列。      ```c#
IEnumerator enumerator = coll.GetEnumerator();  //透過 GetEnumerator 方法，取得 IEnumerator

while (enumerator.MoveNext())                   //列舉值一開始會位於集合中的第一個元素之前
{
Console.WriteLine(enumerator.Current);      //Current:取得目前項目
}

enumerator.Reset();                             //利用Reset, 將 enumerator 重新指向第一個
```

#### 備註

列舉集合中的元素時，並不會獨佔集合的存取權，因此在集合中逐一列舉，本質上不是執行緒安全的程序。  若要確保列舉期間的執行緒安全，必須在整個列舉期間鎖定該集合，也就是必須實作自己的同步處理機制。

## 集合介面的一致性

在處理集合項目的操作時, .Net 定義了幾組介面,以確保實作時可以呼叫的共通方法。  例如:上面提到的 [IEnumerable](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable)，該介面提供了巡訪資料的共通方法。  

.Net 定義了幾組介面,以確保實作時可以呼叫的共通方法

- [IEnumerable](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable) ：提供了巡訪資料的共通方法
- [IEnumerator](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerator) ：提供了巡訪資料的共通方法
- [ICollection](http://msdn.microsoft.com/zh-tw/library/system.collections.icollection.aspx) ：確保每個集合都支援取得集合項目的共通方法，以及將集合複製到一個Array物件的共通方法。
- [IList](http://msdn.microsoft.com/zh-tw/library/system.collections.ilist.aspx) ：表示可以個別由索引存取之物件的非泛型集合。
- [IComparer](http://msdn.microsoft.com/zh-tw/library/system.collections.icomparer) ：公開比較兩個物件的方法。
```c#
//IEnumerable 介面的組成成員
public interface IEnumerable
{  
IEnumerator GetEnumerator();
}```
```c#
//IEnumerator 介面的組成成員
public interface IEnumerator
{  
object Current { get; }
bool MoveNext();
void Reset();
}```
```c#
//ICollection 介面的組成成員
public interface ICollection
{  
int Count { get; }                   //取得在 ICollection 中所包含的元素數。
bool IsSynchronized { get; }         //取得值，指出對 ICollection 的存取是否為同步 (安全執行緒)。

void CopyTo(Ａrray array,int index)  //從特定的 Array 索引開始，複製 ICollection 項目至 Array。
IEnumerator GetEnumerator()          //傳回會逐一查看集合的列舉程式。 (繼承自 IEnumerable)。
}```
```c#
//IList 介面的組成成員
public interface IList
{  
Object this[int index { get; set; }
bool IsFixedSize { get; }
bool IsReadOnly { get; }

int Add (Object value)
void Clear ()
bool Contains (Object value)
int IndexOf (Object value)
void Insert (int index,	Object value)
void Remove (Object value)
void RemoveAt (int index)
}```
```c#
//IComparer 介面的組成成員
public interface IComparer
{  
int Compare (Object x,Object y)
}```

## 排序

[ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) 類別支援對集合進行排序，只要呼叫它的 [Sort](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.sort) 方法即可，例如：  
```c#
coll.Sort();                            //以預設方法排序
coll.Reverse();                         //反向排序
```

#### [ArrayList.Sort](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.sort) 共有三個覆載方法。
```c#
public virtual void Sort();
public virtual void Sort(IComparer comparer);
public virtual void Sort(int index, int count, IComparer comparer);
```

不帶參數的 [Sort](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.sort.aspx) 會依照集合元素本身的比較原則進行排序，也就是集合元素必須實作 [IComparable](http://msdn.microsoft.com/zh-tw/library/system.icomparable.aspx) 介面才可以使用這個排序方法。  

帶 [IComparer](http://msdn.microsoft.com/zh-tw/library/system.collections.icomparer) 型別參數的 [Sort](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.sort.aspx) ，就不會根據 item 本身的比較原則，而是依據這個參數當做比較原則。  

#### 使用比較子進行比較

若要支援 [IComparer](http://msdn.microsoft.com/zh-tw/library/system.collections.icomparer) 介面，則必須實作 [Compare](http://msdn.microsoft.com/zh-tw/library/system.collections.icomparer.compare.aspx) 方法：
```c#
int Compare(object x, object y);
```

#### [Compare](http://msdn.microsoft.com/zh-tw/library/system.collections.icomparer.compare.aspx) 是用來比較兩個物件的大小。其回傳值意義如下：

- 負值： x 小於 y。
- 零值： x 等於 y。
- 正值： x 大於 y。

**範例**
```c#
//定義一個比較子
public class DescendingComparer : IComparer
{
CaseInsensitiveComparer _comparer = new CaseInsensitiveComparer();
public int Compare(object x, object y)
{
//將二值對調,呼叫CaseInsensitiveComparer.Compare,以比較大小
return _comparer.Compare(y, x);
}
}

private void button3_Click(object sender, EventArgs e)
{
ArrayList coll = new ArrayList();
coll.Add("First");
coll.Add("Second");
coll.Add("Third");
coll.Add("Fourth");

coll.Sort();                            //以預設方法排序
coll.Sort(new DescendingComparer());    //自訂 compare 方法排序
}
```

#### 實作類別的 [IComparable](http://msdn.microsoft.com/zh-tw/library/system.icomparable.aspx) 介面，以支援比較

若要支援 [IComparable](http://msdn.microsoft.com/zh-tw/library/system.icomparable.aspx) 介面，則必須實作 [CompareTo](http://msdn.microsoft.com/zh-tw/library/system.icomparable.compareto.aspx) 方法。
```c#
int CompareTo(object obj);
```

#### [CompareTo](http://msdn.microsoft.com/zh-tw/library/system.icomparable.compareto.aspx) 是用來表示目前的執行個體應該排序在比較物件的前面、後面或相同位置。其回傳值意義如下：

- 負值： 執行個體排序在 obj 前面。
- 零值： 執行個體與 obj 排序在相同位置。
- 正值： 執行個體排序在 obj 後面。

**範例**
```c#
public class myAge: IComparable
{
private int _value;

public myAge(int age)
{
_value = age;
}
public int Value()
{
return _value;
}
public override string ToString()
{
return _value.ToString();
}
public int CompareTo(object obj)
{
if (obj is myAge)
{
myAge age = (myAge)obj;
return _value.CompareTo(age.Value());
}
else
throw new ArgumentException("object is not an Age");
}
}
private void button5_Click(object sender, EventArgs e)
{
ArrayList ages = new ArrayList();
ages.Add(new myAge(10));
ages.Add(new myAge(40));
ages.Add(new myAge(30));
ages.Add(new myAge(20));
foreach (myAge age in ages)
{
Console.WriteLine(age);
}
//10 40 30 20
ages.Sort();
foreach (myAge age in ages)
{
Console.WriteLine(age);
}
//10 20 30 40
}
```

## 搜尋

下面三種方法都可以用來搜尋 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) 中是否存在某個項目：  

- [IndexOf](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.IndexOf) ：傳回 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) 中第一個符合搜尋項目的索引。這個方法採用線性搜尋，是一種 O(n) 運算。
- [Contains](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.contains) ：判斷某元素是否在 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) 中，此方法會呼叫 Object.Equals 來判斷是否相等。這個方法採用線性搜尋，是一種 O(n) 運算。
- [BinarySearch](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.binarysearch) ：使用二進位搜尋演算法來尋找在排序的 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) 中的元素。這個方法是 O(log n) 運算。
```c#
ArrayList myAL = new ArrayList();
for (int i = 0; i <= 4; i++)    // 0 2 4 6 8
myAL.Add(i \* 2);

int obj = 4;

// Using Contains
if (!myAL.Contains(obj))
Console.WriteLine("({0}) is not found", obj);
else
{
int index1 = myAL.IndexOf(obj);
Console.WriteLine("({0}) is at index {1}.", obj, index1);
}

// Using BinarySearch
int index2 = myAL.BinarySearch(obj);
if (index2 < 0)
Console.WriteLine("({0}) is not found. The next larger object is at index {1}.", obj, ~index2);
else
Console.WriteLine("The object to search for ({0}) is at index {1}.", obj, index2);

//(3) is not found
//(3) is not found. The next larger object is at index 2.

//(4) is at index 2.
//The object to search for (4) is at index 2.
```

#### 備註

ArrayList.Sort() 這個方法使用 [Array.Sort](http://msdn.microsoft.com/zh-tw/library/system.array.sort.aspx)，並採用 QuickSort 演算法。QuickStort 演算法是比較排序 (也稱為不穩定的排序，亦即，如果有兩個項目相同，可能無法保留其順序。)，表示「 小於或等於」比較作業會判斷最終排序清單中應該先發生兩個項目中的。不過，如果兩個項目相等，其原本的順序可能不會保留。相反地，穩定排序可以保留相等項目的順序。若要執行穩定排序，您必須實作自訂 [IComparer](http://msdn.microsoft.com/zh-tw/library/system.collections.icomparer) 介面，以搭配使用這個方法的其他多載。  

平均而言，這個方法是 O(n log n) 運算，其中 n 為 Count；最壞的狀況是變成 O(n ^ 2) 運算。

## IsSynchronized 屬性和 ArrayList.Synchronized 方法

[ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) 本身並不是一個執行緒安全的物件，若要在多執行緒作業中訪問，就必須自已動手調用 [lock](http://msdn.microsoft.com/zh-tw/library/c5kehkcz.aspx) 機制以保持執行緒同步。例如：
```c#
ArrayList al = new ArrayList();
lock (al.SyncRoot)
{
al.Add("test1");
al.Add("test2");
//....
}
```

如果使用 [ArrayList.Synchronized](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.synchronized.aspx) 方法取得的實體，那麼就不用考慮執行序同步的問題，因為這個實體本身就是執行序安全的。  實際上 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) 內部有實作一個執行序安全的類別， [Synchronized](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.synchronized.aspx) 回傳的就是這個類別，它裡面每個屬性都用了 [lock](http://msdn.microsoft.com/zh-tw/library/c5kehkcz.aspx) 關鍵字來保護執行緒同步。  

- [IsSynchronized](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.issynchronized.aspx) ：指出對 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) 的存取是否已同步處理 (執行緒安全)。
- [Synchronized](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.synchronized.aspx) ：傳回同步 (安全執行緒) 的清單包裝函式。
```c#
ArrayList al = new ArrayList();
ArrayList sync_al = ArrayList.Synchronized(al);

sync_al.Add("test1");
sync_al.Add("test2");
```

#### 總結

- The .NET Framework supports a variety of collection classes that can be used in different circumstances.
- The [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) is a simple collection of       **unordered** items.
- The [Add](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringcollection.add.aspx) and [AddRange](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.addrange.aspx) methods of the [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) are used to add items to an [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.aspx) .
- The [Insert](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringcollection.insert.aspx) and [InsertRange](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.insertrange.aspx) methods of the [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) are used to insert items into specific places in a collection.
- The [Remove](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.remove.aspx) , [RemoveAt](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.removeat.aspx) , and [RemoveRange](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist.removerange.aspx) methods of the [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) are used to delete items from a collection.
- The indexer of the [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) can be used to iterate over a collection.
- The [IEnumerable](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable.aspx) and [IEnumerator](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerator.aspx) interfaces can be used to enumerate through a collection as well.
- The foreach construct in Visual Basic and C# use the [IEnumerable](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable) interface to concisely iterate over a collection.


- [使用 foreach 存取集合類別](http://msdn.microsoft.com/zh-tw/library/9yb8xew9.aspx)