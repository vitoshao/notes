---
title: Generic Collections
layout: default
parent: 集合與泛型
nav_order: 1
description: "Generic Collections"
date: 2012-01-16
tags: [DotNetFramework,集合與泛型]
postid: "3650165736718266059"
---
# How Generics Work

#### Generics簡述

[Generics](http://msdn.microsoft.com/zh-tw/library/512aeb7t.aspx) 是.Net2.0才加入的功能。是一種型別參數的概念，也就是當class或method在設計時，可以先擱置型別，直到用戶端程式要使用時再行處理型別。  目的在於避免run time時需要boxing/unboxing的情況。泛型可以用在.Net中的許多地方，但是最常見的就屬泛型集合類別。

下列程式碼分別以整數型別和泛型設計一個串列類別，可以清楚的看到，差別就在int與&lt;T&gt;的不同而已。

| ```c#
public class IntList : IEnumerable<br>{<br>    private ArrayList list = new ArrayList();<br>    public void Add(int val)<br>    {<br>        list.Add(val);<br>    }<br>    public int this[int index]<br>    {<br>        get<br>        {<br>            return (int)list[index];<br>        }<br>    }<br>}<br>            <br>IntList myList1 = new IntList();<br>myList1.Add(4);<br>myList1.Add(5);<br>myList1.Add(6);<br>foreach (int num in myList1)<br>{<br>    Console.WriteLine(num.ToString());<br>}<br>``` | ```c#
public class GenList<T> : IEnumerable<br>{<br>    private ArrayList list = new ArrayList();<br>    public void Add(T val)<br>    {<br>        list.Add(val);<br>    }<br>    public T this[int index]<br>    {<br>        get<br>        {<br>            return <T> list[index];<br>        }<br>    }<br>}<br>            <br>GenList<int> myList2 = new GenList<int>();<br>myList2.Add(4);<br>myList2.Add(5);<br>myList2.Add(6);<br>foreach (int num in myList2)<br>{<br>    Console.WriteLine(num.ToString());<br>}<br>``` |
| --- | --- |

#### Generics優點

- 重複使用：只設計一份程式碼，就可以適用各種型別。
- 型別安全：若有不合法的型別資料，在編譯階段就可以發覺。
- 效率提高：執行階段，避免不必要的boxing/unboxing，效能自然提高。

#### 何時使用泛型集合

當集合元素為實值型別時，泛型集合型別通常要比對應的非泛型集合型別有較理想的效能 (也優於衍生自非泛型基底集合型別的型別)，因為有了泛型就不需要將這些元素進行 Box 處理。

#### 泛型委派

List&lt;T&gt; 類別有一些方法可以接受泛型委派，這是非泛型集合型別中所沒有的。

- IComparer&lt;T&gt;：指定自己的排序及搜尋介面
- Predicate&lt;T&gt;：指定搜尋清單之方法
- Action&lt;T&gt;：表示在清單的每一個元素上執行的方法
- Converter&lt;TInput, TOutput&gt;：定義型別之間的轉換

# Improving Safety and Performance

大部分的集合類別，都有相對應的泛型存在。另外還有幾個新集合只適用於泛型型別。下表為一些集合型別，以及它們所對應的泛型型別

## Equivalent Generic Types

| Type | Generic Type |
| --- | --- |
| ArrayList | List&lt;T&gt; |
| Queue | Queue&lt;T&gt; |
| Stack | Stack&lt;T&gt; |
| SortedList | SortedList&lt;T,U&gt; |
| N/A | SortedDictionary&lt;&gt; |
| Hashtable | Dictionary&lt;T,U&gt; |
| ListDictionary |
| HybridDictionary |
| OrderedDictionary |
| NameValueCollection |
| DictionaryEntry | NameValuePair&lt;&gt; |
| stringCollection | List&lt;string&gt; |
| stringDictionary | Dictionary&lt;string&gt; |
| N/A | LinkedList&lt;&gt; |
| CollectionBase | Collection&lt;T&gt; |
| ReadOnlyCollectionBase | ReadOnlyCollection&lt;T&gt; |

## List&lt;T&gt; 類別

[List&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/6sh2ey19) 類別是 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) 類別的泛型對應項，具有以下幾項特色：。  

- 可以直接使用 index 存取這個集合中的元素。index 值從零起始。
- 提供搜尋、排序和管理清單的方法。
- 使用一個可動態增減的陣列空間，以實作 [IList&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/5y536ey6) 泛型介面。
- 集合中的項目並不保證已經經過排序。若要執行類似 [List&lt;T&gt;.BinarySearch](http://msdn.microsoft.com/zh-tw/library/3f90y839.aspx) 運算之前，一定要對先 [List&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/6sh2ey19) 進行排序。
- 集合接受 null 做為參考型別的有效值，並允許重複的項目。
```c#
List<int> intList = new List<int>();

// 加入項目
intList.Add(5);
intList.Add(2);
intList.Add(3);

// 更新第2項
intList[1] = 4;

// 使用 indexer 取值
for (int i = 0; i < intList.Count; i++)
{
Console.WriteLine(intList[i]);
}

// 排序
intList.Sort();

// 使用 foreach 巡訪取值
foreach (int i in intList)
{
Console.WriteLine(i);
}
```
```c#
class Product{}
class Product1 : Product{}
class Product2 : Product{}
class Product3{}

private void button3_Click(object sender, EventArgs e)
{
List<Product> product_list = new List<Product>();
product_list.Add(new Product());
product_list.Add(new Product1());
product_list.Add(new Product2());
//product_list.Add(new Product3());
product_list.Add(null);
}
```

另外，值得一提的是，這個 [List&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/6sh2ey19) 類別，在做 Sort() 方法時，支援泛型委派。  
```c#
//泛型委派(generic delegates)
//They are just like generic classes or structures, but generic parameters 
//are used only to define the calling convention of the delegate.
static int SortByStringLength(string x, string y)
{
return y.Length - x.Length;
}

private void button3_Click(object sender, EventArgs e)
{
List<string> myList = new List<string>();
myList.Add("orange");
myList.Add("apple");
myList.Add("tangerine");

myList.Sort();
foreach (string item in myList)
{
Console.WriteLine(item);
}
//原始排序: apple orange tangerine

myList.Sort(SortByStringLength);  //自行定義一個泛型委派,依字串長度排序
foreach (string item in myList)
{
Console.WriteLine(item);
}
//自訂排序: tangerine orange apple
}```

## Stack&lt;T&gt; 類別

這是 [Queue](http://msdn.microsoft.com/zh-tw/library/system.collections.queue.aspx) and [Stack](http://msdn.microsoft.com/zh-tw/library/system.collections.stack.aspx) 類別的型別安全版本。  
```c#
Queue<string> q = new Queue<string>(); 
q.Enqueue("First"); 
q.Enqueue("Second"); 
q.Enqueue("Third"); 
q.Enqueue("Fourth"); 
while (q.Count > 0) 
{ 
Console.WriteLine(q.Dequeue()); 
}

Stack<string> s = new Stack<string>(); 
s.Push("First"); 
s.Push("Second"); 
s.Push("Third"); 
s.Push("Fourth");
while (s.Count > 0) 
{ 
Console.WriteLine(s.Pop()); 
}```

## Dictionary&lt;T,U&gt; 類別
```c#
[SerializableAttribute]
[ComVisibleAttribute(false)]
public class Dictionary<TKey, TValue> : IDictionary<TKey, TValue>, 
ICollection<KeyValuePair<TKey, TValue>>, IEnumerable<KeyValuePair<TKey, TValue>>, 
IDictionary, ICollection, IEnumerable, ISerializable, IDeserializationCallback
}
```

- Dictionary&lt;TKey, TValue&gt; 是可用來儲存 key/value 的泛型字典集合。
- TKey 不能重複，它是由 equality 的實作來判斷是否相等，預設為 [EqualityComparer&lt;t&gt;.Default&lt;/t&gt;](http://msdn.microsoft.com/zh-tw/library/ms224763)。
- Dictionary&lt;TKey, TValue&gt; 的容量會依需要藉由重新配置內部陣列而自動增加。
- Dictionary&lt;TKey, TValue&gt; 的每一個項目都視為表示一個值及其索引鍵的 [KeyValuePair&lt;TKey, TValue&gt;](http://msdn.microsoft.com/zh-tw/library/5tbh8a42) 結構。
```c#
Dictionary<string, string> dict = new Dictionary<string, string>();
dict["D3"] = "Three";
dict["D4"] = "Four";
dict["D1"] = "One";
dict["D2"] = "Two";

string str = dict["D2"];        //使用 key 取值

// 使用 key 取值
foreach (string key in dict.Keys)
{
Console.WriteLine("{0}, {1}", key, dict[key]);
}

// D3 = Three
// D4 = Four
// D1 = One
// D2 = Two
// 集合保持原來的順序
}```

泛型與非泛型的 Dictionary 類別，最大的差異是 [Dictionary&lt;T,U&gt;](http://msdn.microsoft.com/zh-tw/library/xfhwa508) 不是使用 [DictionaryEntry](http://msdn.microsoft.com/zh-tw/library/system.collections.dictionaryentry.aspx) 物件來保存key/value，而是使用新的泛型型別 [KeyValuePair&lt;TKey, TValue&gt;](http://msdn.microsoft.com/zh-tw/library/5tbh8a42.aspx) 來保存。  
```c#
// 使用 foreach 巡訪，並利用新的泛型型別 KeyValuePair取值
foreach (KeyValuePair<int, string> i in dict)
{
Console.WriteLine("{0} = {1}", i.Key, i.Value);
}```

## SortedDictionary&lt;T,U&gt; 類別

[SortedList&lt;T,U&gt;](http://msdn.microsoft.com/zh-tw/library/ms132319) and [SortedDictionary&lt;T,U&gt;](http://msdn.microsoft.com/zh-tw/library/f7fta44c.aspx) 這二個泛型集合都類似 [Dictionary&lt;T,U&gt;](http://msdn.microsoft.com/zh-tw/library/xfhwa508) ，相似處有：  

- 二者都是二進位搜尋樹狀目錄。
- 集合中的項目都會根據 key 值做排序

這二個泛型集合主要的不同在於，它們插入和移除項目的實作方法不同，所以在記憶體的使用上和執行速度上會有所不同。  

- SortedList&lt;T,U&gt; 使用的記憶體少於 SortedDictionary&lt;T,U&gt;。
- 對於未排序的資料，執行插入和移除操作時，SortedDictionary&lt;T,U&gt; 使用 O(log n) 優於 SortedList&lt;T,U&gt; 的 O(n)。
- 如果從排序的資料一次全部填入清單，則 SortedLis&lt;T,U&gt; 快於 SortedDictionary&lt;T,U&gt;。

另外，在使用上，二者的 Values 屬性雖然都是回傳值的集合，但回傳的型別不同。SortedList&lt;T,U&gt; 回傳的是 IList&lt;T&gt; ，SortedDictionary&lt;T,U&gt; 回傳的是 SortedDictionary&lt;T, U&gt;.ValueCollection 。
```c#
SortedList<string, string> sortedList = new SortedList<string, string>();

sortedList.Add("111", "apple");
sortedList.Add("333", "book");
sortedList.Add("222", "cherry");
sortedList.Add("444", "dog");
sortedList.Remove("111");
sortedList.Add("111", "apple");

foreach (KeyValuePair<string, string> item in sortedList)
{
Console.WriteLine("{0} {1}", item.Key, item.Value);
}
// 111 apple
// 222 cherry
// 333 book
// 444 dog
foreach (KeyValuePair<string, string> item in sortedList)
{
Console.WriteLine("{0} {1}", item.Key, item.Value);
}
// 111 apple
// 222 cherry
// 333 book
// 444 dog
foreach (string value in sortedList.Values)
{
Console.WriteLine(value.ToString());
}
// apple
// cherry
// book
// dog
for (int i = 0; i < sortedList.Count; i++)
{
Console.WriteLine("{0} {1}", sortedList.Keys[i], sortedList.Values[i]);
}
// 111 apple
// 222 cherry
// 333 book
// 444 dog
```
```c#
// 用法結果都同 SortedList

SortedDictionary<string, string> sortedDict = new SortedDictionary<string, string>();

sortedDict.Add("111", "apple");
sortedDict.Add("333", "book");
sortedDict.Add("222", "cherry");
sortedDict.Add("444", "dog");
sortedDict.Remove("111");
sortedDict.Add("111", "apple");

foreach (KeyValuePair<string, string> item in sortedDict)
{
Console.WriteLine("{0} {1}", item.Key, item.Value);
}

foreach (KeyValuePair<string, string> item in sortedDict)
{
Console.WriteLine("{0} {1}", item.Key, item.Value);
}

foreach (string value in sortedDict.Values)
{
Console.WriteLine(value.ToString());
}
//不支援索引鍵
//for (int i = 0; i < sortedDict.Count; i++)
//{
//    Console.WriteLine("{0} {1}", sortedList.Keys[i], sortedList.Values[i]);
//}
```

## LinkedList&lt;T&gt; 類別

- LinkedList&lt;T&gt; 表示雙向連結串列 (Doubly Linked List)。
- LinkedList&lt;T&gt; 它支援列舉值並實作 [ICollection](http://msdn.microsoft.com/zh-tw/library/system.collections.icollection) 介面，與 .NET Framework 中的其他集合類別一致。
- LinkedList&lt;T&gt; 物件中每一個節點都是 [LinkedListNode&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/ahf4c754) 型別。
- LinkedList&lt;T&gt; 是雙向連結串列，可以由 [Next](http://msdn.microsoft.com/zh-tw/library/b4t9w6be) 屬性取得下一個節點， [Previous](http://msdn.microsoft.com/zh-tw/library/de80z2x6) 屬性取得前一個節點
- 
- 

底下是 [LinkedList&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/he2s3bh7) 類別的組成成員
```c#
public class LinkedList<T> : ICollection<T>, IEnumerable<T>, ICollection, IEnumerable, ISerializable, IDeserializationCallback
{
public LinkedListNode<T> First { get; }                                     //第一個節點
public LinkedListNode<T> Last { get; }                                      //最後一個節點

public void AddFirst(LinkedListNode<T> node);                               //開頭加入指定的新節點。
public void AddLast(LinkedListNode<T> node);                                //結尾加入指定的新節點。
public void AddBefore(LinkedListNode<T> node, LinkedListNode<T> newNode);   //現有節點前加入指定的新節點。
public void AddAfter(LinkedListNode<T> node, LinkedListNode<T> newNode);    //現有節點後加入指定的新節點
public LinkedListNode<T> AddFirst(T value);                                 //開頭加入包含指定值的新節點。
public LinkedListNode<T> AddLast(T value);                                  //結尾加入包含指定值的新節點。
public LinkedListNode<T> AddBefore(LinkedListNode<T> node, T value);        //現有節點前加入包含指定值的新節點。
public LinkedListNode<T> AddAfter(LinkedListNode<T> node, T value);         //現有節點後加入包含指定值的新節點
public LinkedListNode<T> Find(T value);                                     //尋找包含指定值的第一個節點。
public LinkedListNode<T> FindLast(T value);                                 //尋找包含指定值的最後一個節點。
public void Remove(LinkedListNode<T> node);                                 //移除指定的節點。
public bool Remove(T value);                                                //移除第一次出現的指定值。
public void RemoveFirst();                                                  //移除 LinkedList<T> 開頭的節點。
public void RemoveLast();                                                   //移除 LinkedList<T> 結尾的節點。
public void Clear();
public bool Contains(T value);
public void CopyTo(T[] array, int index);
public LinkedList<T>.Enumerator GetEnumerator();
public virtual void GetObjectData(SerializationInfo info, StreamingContext context);
public virtual void OnDeserialization(object sender);
}```
```c#
LinkedList<string> links = new LinkedList<string>();

LinkedListNode<string> last = links.AddLast("999");                 //加一節點至Last
LinkedListNode<string> first = links.AddFirst("111");               //加一節點至First
LinkedListNode<string> second = links.AddBefore(last, "222");       //在last節點前面加入一個節點
links.AddAfter(second, "333");                                      //在second節點後面加入一個節點

foreach (string s in links)
{
Console.WriteLine(s);
}

//111
//222
//333
//999
```

# Generic Collection Class Structure

## 泛型集合介面 Generic Collection Interfaces

一般集合都有支援相關的介面 (interfaces)，如 ICollection, IDictionary, IEnumerable 。  泛型集合除了同樣支援這些介面外，而且額外支援泛型介面 (generic interfaces)，以提供型別安全。  
```c#
List<string> stringList = new List<string>();
// ...

// List<T> 支援 非泛型的 IList 介面
IList theList = (IList)stringList;
object firstItem = theList[0];

// List<T> 也支援 泛型的 IList<T> 介面
//若使用泛型的IList介面,可增加型別安全
IList<string> typeSafeList = (IList<string>)stringList;
string firststring = typeSafeList[0];
```

## 泛型集合列舉 Generic Collection Enumerators

泛型集合也支援列舉 (iterating)，透過 [GetEnumerator](http://msdn.microsoft.com/zh-tw/library/b0yss765) 方法，可以取得 enumerator 。  這個回傳的 enumerator 也是個泛型，其型別與它的集合型別相同。  
```c#
List<string> myList = new List<string>();
myList.Add("111");
myList.Add("222");

List<string>.Enumerator enumerator = myList.GetEnumerator();
while (enumerator.MoveNext())
{
string s = enumerator.Current;
Console.WriteLine(s);
}
//111
//222

foreach (string s in myList)
{
Console.WriteLine(s);
}
//111
//222
```

## 泛型比較類別 Comparisons

[Comparer&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/cfttsh47.aspx) 與 [EqualityCompare&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/cfttsh47.aspx) 是泛型的二個基底類別，這二個基底類別分別實作了 [ICompare&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/8ehhxeaf) 與 [IEqualityComparer&lt;T&gt;](http://msdn.microsoft.com/zh-tw/library/ms132151) 介面。  因此，若需要實作自已的比較邏輯，可以繼承這些基底類別，覆寫基底類別的抽象方法，以執行自已的比較邏輯。  
```c#
class MyComparer<T>; : Comparer<T>
{
public override int Compare(T x, T y)
{
return x.GetHashCode() - y.GetHashCode();
}
}

private void button8_Click(object sender, EventArgs e)
{
MyComparer<string> myComp = new MyComparer<string>();

SortedList<string, int> sortList = new SortedList<string, int>(myComp);
sortList["Four"] = 4;
sortList["Two"] = 2;
sortList["Three"] = 3;
foreach (KeyValuePair<string, int> i in sortList)
{
Console.WriteLine(i);
}
}```