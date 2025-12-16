---
title: Dictionaries
layout: default
parent: 集合與泛型
nav_order: 3
description: "Dictionaries"
date: 2012-01-16
tags: [DotNetFramework,集合與泛型]
postid: "2839697891349278570"
---
# 什麼是 Dictionaries

當資料型態具有 key/value 特性時，就可以使用 Dictionary 類型的集合。它可以用來建立索引表，將任意的 key 與 value 做關連對應。.NET Framework 包含下列 Dictionary 類別：

- [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) ：最基本的字典集合，每個項目都是儲存在 [DictionaryEntry](http://msdn.microsoft.com/zh-tw/library/system.collections.dictionaryentry.aspx) 物件中的 key/value 組。可透過 key 取得項目的 value。
- [SortedList](http://msdn.microsoft.com/zh-tw/library/system.collections.sortedlist.aspx) ：類似 Hashtable，會自動以 key 排序集合內的項目。
- [ListDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.listdictionary) ：當集合項目少於10個時，採用這個集合類別效率較佳。
- [HybridDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.hybriddictionary) ：使用這個集合，當集合項目少於10個時，會採用 [ListDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.listdictionary) ，若項目變大時，會自動轉成 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 。
- [OrderedDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.ordereddictionary) ：類似 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) ，但集合中的項目會依加入的先後順序排列，不像 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 是依 hashcode 排列。

底下二個是比較特殊類似字典的集合

- [StringDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.stringdictionary.aspx) ：類似 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) ，但 key/value 都只允許字串型別， Key 不能重複，一個 key 只能對應一個 value 。
- [NameValueCollection](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.namevaluecollection.aspx) ：類似 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) ，但 key/value 都只允許字串型別， Key 不能重複，一個 key 可以對應多個 value 。

#### 下列幾點是有關於 Dictionary 的幾點特性：

- 每個項目都是儲存在 [DictionaryEntry](http://msdn.microsoft.com/zh-tw/library/system.collections.dictionaryentry.aspx) 物件中的 Key/Value。
- Key 必需唯一的。
- Value 可以是 null ，而且不一定要是唯一的。
- [IDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.idictionary.aspx)實作分為三類：唯讀、固定大小、變數大小。
- 唯讀的 [IDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.idictionary.aspx) 物件無法修改。
- 固定大小的 [IDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.idictionary.aspx) 物件不允許加入或移除項目，但允許修改現有項目。
- 變數大小的 [IDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.idictionary.aspx) 物件允許加入、移除和修改項目。

# Hashtable 類別

最基本的字典集合就是 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.Hashtable.aspx) ，它可以達到上述 Dictionary 的特性。

下表為 [IDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.idictionary.aspx) 與 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 主要的屬性與方法
```c#
//IDictionary 介面的組成成員
public interface IDictionary: ICollection, IEnumerable

//properties
bool IsFixedSize { get; }               //取得值，指出 IDictionary 物件是否具有固定的大小。
bool IsReadOnly { get; }                //取得值，指出 IDictionary 物件是否為唯讀。
Object Item[Object key] { get; set; }   //取得或設定具有指定索引鍵的元素。
ICollection Keys { get; }               //取得 ICollection 物件，其中包含 IDictionary 物件的索引鍵。
ICollection Values { get; }             //取得 ICollection 物件，其中含有 IDictionary 物件中的值。

//method
void Add(Object key,Object value)       //將隨附有索引鍵和值的項目加入至 IDictionary 物件。
void Clear()                            //將所有項目從 IDictionary 物件移除。
bool Contains(Object key)               //判斷 IDictionary 物件是否包含具有指定索引鍵的項目。
void CopyTo(Array array,int index)      //從特定的 Array 索引開始，複製 ICollection 項目至 Array。 (繼承自 ICollection)。
IEnumerator GetEnumerator()             //傳回會逐一查看集合的列舉程式
void Remove(Object key)                 //將有指定索引鍵的項目從 IDictionary 物件移除。
}```
```c#
//Hashtable 介面的組成成員 (Hashtable繼承自IDictionary,所以除了IDictionary的功能外,也支援了兩個可以用來測試索引鍵與值是否存在的方法。
public class Hashtable : IDictionary, ICollection, IEnumerable, ISerializable, IDeserializationCallback, ICloneable
{
public virtual bool ContainsKey(Object key)         //判斷 Hashtable 集合裡否包含特定索引鍵。
public virtual bool ContainsValue(Object value)     //判斷 Hashtable 集合裡是否含有特定值。
}```

下面範例將以 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 為例，示範 Dictionary 類型集合的幾種操作：

- 1. 新增/移除項目
- 2. 巡訪項目
- 3. 集合項目的 Equality (相等性)
- 4. 使用 IEqualityCompare

## 1. 新增/移除項目
```c#
Hashtable emailLookup = new Hashtable();

//使用 Add 方法,加入項目
emailLookup.Add("shaojay", "shaojay@contoso.com");
emailLookup.Add("oldwang", "oldwang@gmail.com");

//使用 Remove 方法,移除項目
emailLookup.Remove("oldwang@gmail.com");

//透過 key 值，取得 value 值，不能使用 indexer
Console.WriteLine(emailLookup["oldwang@gmail.com"]);

//直接指定某個 key 值的 value, 作用等同 Add
emailLookup["peter"] = "peter@gmail.com";

//直接指定某個 key 值的 value, 但因為 key 值存在，作用等同更新
emailLookup["oldwang"] = "old.wang@gmail.com";
```

## 2. 巡訪資料項目 (Iterate)
```c#
//逐一列舉 emailLookup 的成員
foreach (object obj in emailLookup)
{
Console.WriteLine(obj);
}
//System.Collections.DictionaryEntry
//System.Collections.DictionaryEntry
```

#### 注意

使用 foreach 陳述式時， [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 裡的每個項目都是由一組 key/value 組成，因此集合項目的型別既不是 key 的型別，也不是 value 的型別。元素真正的型別為  [DictionaryEntry](http://msdn.microsoft.com/zh-tw/library/system.collections.dictionaryentry.aspx)。  所以，上例輸出的結果為 System.Collections.DictionaryEntry ，而不是我們想要的結果，如果想要顯示所有使用者的姓名，正確做法如下:  
```c#
//使用 DictionaryEntry 逐一列舉 emailLookup 的成員
//Key = shaojay, Value = shao.jay@gmail.com
//Key = peter, Value = peter@gmail.com
foreach (DictionaryEntry entry in emailLookup)
{
Console.WriteLine("Key = {0}, Value = {1}", entry.Key, entry.Value);
}

//使用 Value 逐一列舉 emailLookup 的成員
//shao.jay@gmail.com
//peter@gmail.com
foreach (object value in emailLookup.Values)
{
Console.WriteLine(value);
}

//使用 Key 逐一列舉 emailLookup 的成員
//Key = shaojay, Value = shao.jay@gmail.com
//Key = peter, Value = peter@gmail.com
foreach (object key in emailLookup.Keys)
{
Console.WriteLine("Key = {0}, Value = {1}", key, emailLookup[key]);
}

//使用 Enumerator 逐一列舉 emailLookup 的成員

IDictionaryEnumerator enumerator = emailLookup.GetEnumerator();
DictionaryEntry dentry;
while (enumerator.MoveNext())                           //列舉值一開始會位於集合中的第一個元素之前
{
dentry = (DictionaryEntry)enumerator.Current;       //Current:取得目前項目
Console.WriteLine("Key = {0}, Value = {1}", dentry.Key, dentry.Value);
}
```

## 3. 集合項目的相等性 (Equality)

[Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 類別是一種特定型別的字典類別，它使用一個整數值(就是雜湊值hash)來輔助索引鍵的儲存。 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 就是使用這個 hash 值來加速集合中搜尋指定的索引鍵。  .Net中的每個物件都是繼承自 Object 類別，所以這個類別也支援 [GetHashCode](http://msdn.microsoft.com/zh-tw/library/system.object.gethashcode) 方法，同時利用這個方法所回傳的整數值，用以識別每一個物件。  

所以，當要新增一個項目到 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 裡的時候，它會以 key 值去取得 GetHashCode，判斷物件是否相等。  但是，如果 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 發現兩個物件有相同的 hashcode，它會再呼叫 [Equals](http://msdn.microsoft.com/zh-tw/library/system.object.equals) 方法來判斷這二個物件實際上是否相等。  

**例一：**下面這個例子，雖然 obj1, obj2, obj3 是不同的執行個體，但卻發生 key 值重複問題。  這是因為這三個物件的 [GetHashCode](http://msdn.microsoft.com/zh-tw/library/system.object.gethashcode) 方法都會回傳相同的值，所以 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 新增資料時，會認定 key 值重複。  
```c#
Hashtable hash_table = new Hashtable();

Object obj1 = "apple";      //1657858284
Object obj2 = "apple";      //1657858284
String obj3 = "apple";      //1657858284

Console.WriteLine(obj1.GetHashCode().ToString());
Console.WriteLine(obj2.GetHashCode().ToString());
Console.WriteLine(obj3.GetHashCode().ToString());

hash_table.Add(obj1,"abe");
hash_table.Add(obj2, "abe");    //--> key 值重複失敗
hash_table.Add(obj3, "abe");    //--> key 值重複失敗
```

**例二：**下面這個例子，我們建立了二個 Employee 執行個體，而且這二個執行個體也具有相同的 id 和 name，但卻不會發生 key 值重複問題。  這是因為 Object 類別在實做 [GetHashCode](http://msdn.microsoft.com/zh-tw/library/system.object.gethashcode) 方法時，對於每個執行個體都會分別建立一個唯一的 hash，所以 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 自然會認定這二個執行個體是不相等的。  
```c#
class Employee
{
string id { get; set; }
string name { get; set; }

public Employee(string strID, string strName)
{
id = strID;
name = strName;
}
}

private void button7_Click(object sender, EventArgs e)
{
Hashtable hash_table = new Hashtable();
Employee e1 = new Employee("E0001", "Tom");
Employee e2 = new Employee("E0001", "Tom");

//雖然 e1, e2 具有相同的 id 和 name
//但是其 GetHashCode 回傳值不相同，所以下面程式不會造成錯誤
Console.WriteLine(e1.GetHashCode().ToString());
Console.WriteLine(e2.GetHashCode().ToString());

hash_table.Add(e1, 30000);
hash_table.Add(e2, 30000);
}```

**例三：**所以，若希望 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 知道它們是相等的，我們就必須覆寫 Employee 類別中的 [GetHashCode](http://msdn.microsoft.com/zh-tw/library/system.object.gethashcode) 方法，讓 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 知道它們是相等的。  
```c#
class Employee
{
string id { get; set; }
string name { get; set; }

public Employee(string strID, string strName)
{
id = strID;
name = strName;
}

public override int GetHashCode()
{
return id.GetHashCode();
}
}```

**例四：**上例中，雖然e1.GetHashCode()=e2.GetHashCode()，但 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 還是不會認定它們是相等的，因為 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 若發現兩個物件有相同的 hashcode，它會再呼叫 [Equals](http://msdn.microsoft.com/zh-tw/library/system.object.equals) 方法來判斷這二個物件實際上是否相等。  因此我們必需再覆寫 Employee 類別中的 [Equals](http://msdn.microsoft.com/zh-tw/library/system.object.equals) 方法，這樣子具備相同 id 的執行個體，才會被 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 認定是相等的：  
```c#
class Employee
{
string id { get; set; }
string name { get; set; }

public Employee(string strID, string strName)
{
id = strID;
name = strName;
}

public override int GetHashCode()
{
return id.GetHashCode();
}

public override bool Equals(object obj)
{
Employee otherEmployee = obj as Employee;
if (otherEmployee == null) return false;
return otherEmployee.id == id;
}
}```

#### 備註：關於 Object.GetHashCode

- HashCode 是在相等比較時，用來識別物件的數值。它也可以做為集合中物件的索引。
- GetHashCode 方法的預設實作，無法保證不同物件的回傳值是唯一的。
- GetHashCode 也不保證這個方法在不同版本的 .NET Framework 中都能傳回相同的值。
- 因此，這個方法的預設實作不能當做雜湊用途的唯一物件辨識項。
- 實值型別必須覆寫這個方法，以提供該型別所適用的雜湊函式
- 雜湊函式 (hash function) 是用來快速產生一個對應到 object 的值（雜湊碼）。
- 雜湊函式至少要指定 instance 的一個欄位當做輸入。
- 雜湊函式必須具有下列特性：
- 1. 兩個相等的物件必須具有相同的雜湊碼。 (但物件不相等時，這個方法沒有一定要傳回不同的值)
- 2. 對於任何一個物件，不論叫用甚麼方法，其 [GetHashCode](http://msdn.microsoft.com/zh-tw/library/system.object.gethashcode) 永遠都必須返回相同的值。
- 3. 對於所有輸入，雜湊函式應在所有整數中產生一隨機分佈。
- 覆寫 [GetHashCode](http://msdn.microsoft.com/zh-tw/library/system.object.gethashcode) 的衍生類別也必須覆寫 Equals，以保證視為相等的兩個物件具有相同的雜湊程式碼，否則，Hashtable 型別可能無法正確執行。

## 4. 使用 IEqualityComparer 介面

若使用下列二行程式碼建立 Employee，這時 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 會認定此為二個不同的物件，因為，我們覆寫的 GetHashCode，是依據 id 這個屬性來回傳 hashcode。  
```c#
Employee e1 = new Employee("e0001", "Tom");
Employee e2 = new Employee("E0001", "Tom");```

如果我們希望建立索引鍵時要忽略字串的大小寫，除了改寫上面例子中的 [GetHashCode](http://msdn.microsoft.com/zh-tw/library/system.object.gethashcode) 和 [Equals](http://msdn.microsoft.com/zh-tw/library/system.object.equals) 方法外，另外也可以透過實作 [IEqualityComparer](http://msdn.microsoft.com/zh-tw/library/system.collections.iequalitycomparer.aspx) 介面來達成。   [IEqualityComparer](http://msdn.microsoft.com/zh-tw/library/system.collections.iequalitycomparer.aspx) 可以用來定義物件相等比較的方法，而 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 也支援以實作 [IEqualityComparer](http://msdn.microsoft.com/zh-tw/library/system.collections.iequalitycomparer.aspx) 介面的物件為參數，來建立新的執行個體。    [IEqualityComparer](http://msdn.microsoft.com/zh-tw/library/system.collections.iequalitycomparer.aspx) 支援兩個方法： [GetHashCode](http://msdn.microsoft.com/zh-tw/library/system.collections.iequalitycomparer.gethashcode) 與 [Equals](http://msdn.microsoft.com/zh-tw/library/system.collections.iequalitycomparer.equals) 。這些方法可以讓比較器類別自已來處理物件的相等性，而不必仰賴物件本身支援這項功能。  
```c#
//這個類別實作了 IEqualityComparer 介面，提供雜湊碼與相等性的比較。
//GetHashCode：將傳入的物件轉換成小寫字母，然後才回傳其hashcode，以便不區分大小寫。
//Equals：   以內建的 CaseInsensitiveComparer 執行真正的比較。
public class myComparer : IEqualityComparer
{
CaseInsensitiveComparer _comparer = new CaseInsensitiveComparer();

public int GetHashCode(object obj)
{
return obj.ToString().ToLowerInvariant().GetHashCode();
}

public new bool Equals(object A, object B)
{
if (_comparer.Compare(A, B) == 0)
return true;
else
return false;
}
}

private void button8_Click(object sender, EventArgs e)
{
//以 IEqualityComparer 的執行個體當做參數，建立 Hashtable
Hashtable score_table = new Hashtable(new myComparer());

score_table["vito"] = 90;
score_table["tom"] = 60;
score_table["frank"] = 80;
score_table["VITO"] = 85;

//因為建立 Hashtable 時使用了這個不分大小寫的方法來比較物件，
//它將 "vito" 和 "VITO" 視為相同，所以最後集合只會出現四個項目。
Console.WriteLine(score_table.Count);  //3

Student s3 = new Student("Allen", 60);
Student s4 = new Student("Peter", 70);
Student s5 = new Student("peter", 80);
score_table.Add(s3, 60);
score_table.Add(s4, 70);
score_table.Add(s5, 80);    //--> error : key 值重複
}

class Student : IComparable
{
string name { get; set; }
int score { get; set; }

public Student(string _Name, int _Score)
{
name = _Name;
score = _Score;
}

//自訂比較邏輯
public int CompareTo(object obj)
{
Student s = obj as Student;
if (s != null)
return String.Compare(s.name, this.name, true);  //不分大小寫
else
throw new ArgumentException("Object is not a Student");
}
}
```

若使用泛型的 IComparable&lt;Ｔ&gt; ，就可以減少轉型的問題。
```c#
class Student2 : IComparable<string>
{
string name { get; set; }
int score { get; set; }

public Student2(string _Name, int _Score)
{
name = _Name;
score = _Score;
}

//自定比較邏輯
public int CompareTo(string other)
{
if (other != null)
{
return string.Compare(this.name, other, true);
}
else
throw new ArgumentException("Object is not a Student");
}
}
```

上面這個範例中，我們在集合中加入了 key 值是以字串型別的項目，因為字串或所有的實值型別都有實作 IComparable 介面，所以物件可以進行 Compare 方法。  但是例子後半段，我們在集合中加入了自訂型別，為了讓物件能夠執行 Compare 方法，該類別中就必須實作 IComparable 介面才可以進行比較。

# SortedList 類別

[SortedList](http://msdn.microsoft.com/zh-tw/library/w1xa3kh1.aspx) 也是用來表示 key/value 的集合，且這個集合中的項目會按照 key 值大小自動排序，而且可以透過 key 和 index 存取項目。  
```c#
SortedList score_table = new SortedList();

score_table["vito"] = 90;
score_table["tom"] = 60;
score_table["apple"] = 70;
score_table["frank"] = 80;
score_table["VITO"] = 80;

foreach (DictionaryEntry entry in score_table)
{
Console.WriteLine("{0} = {1}", entry.Key, entry.Value);
}
//輸出結果：按英文字母排序
//apple = 70
//frank = 80
//tom = 60
//vito = 90
//VITO = 85

//取得key=vito這個項目在集合中的index位置
Console.WriteLine(@"index of key[""vito""] = {0}", score_table.IndexOfKey("vito").ToString());

//取得value=80這個項目在集合中第一次出現的index位置
Console.WriteLine(@"index of value[""80""] = {0}", score_table.IndexOfValue(80).ToString());

//透過 index 取得項目的值
int i = score_table.IndexOfKey("vito");
Console.WriteLine(@"item of index {0} = {1}", i.ToString(), score_table.GetByIndex(i));

//index of key["vito"] = 3
//index of value["80"] = 1
//item of index 3 = 90

//透過 SetByIndex(index, value) 設定項目的值
score_table.SetByIndex(i, 100);
```

SortedList在操作增減項目時，便會自動執行排序動作。另外也支援指定一個 IComparer，以便控制排序的動作。
```c#
public class DescendingComparer : IComparer
{
CaseInsensitiveComparer _comparer = new CaseInsensitiveComparer();
public int Compare(object x, object y)
{
//將二值對調,呼叫CaseInsensitiveComparer.Compare,以比較大小
return _comparer.Compare(y, x);
}
}

private void button9_Click(object sender, EventArgs e)
{
SortedList score_table = new SortedList(new DescendingComparer());
score_table["apple"] = 90;
score_table["tom"] = 60;
score_table["vito"] = 70;

foreach (DictionaryEntry entry in score_table)
{
Console.WriteLine("{0} = {1}", entry.Key, entry.Value);
}
//輸出結果：按英文字母Desc排序
//vito = 70
//tom = 60
//apple = 90
}
```

# 特殊的 Dictionaries 集合

[Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 和 [SortedList](http://msdn.microsoft.com/zh-tw/library/system.collections.sortedlist.aspx) 是二個標準字典型態的集合，為了彌補功能上的不足或提升效率，.NET 另外提供以下三種字典集合：  

- [ListDictionary](http://msdn.microsoft.com/zh-tw/library/dc6bdt5e.aspx)：使用單向連結串列 (Singly-Linked List) 實作 IDictionary。集合項目若在 10 個以下，使用這個字典集合效能最好。
- [HybridDictionary](http://msdn.microsoft.com/zh-tw/library/he1ex4wh.aspx)：使用這個集合類型，若為小型集合時，則採用 [ListDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.listdictionary) 實作 [IDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.idictionary.aspx) ；當它項目逐漸增加成為大型集合時，則切換以 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 實作。
- [OrderedDictionary](http://msdn.microsoft.com/zh-tw/library/ms132568.aspx)：有序的 [Hashtable](http://msdn.microsoft.com/zh-tw/library/system.collections.hashtable.aspx) 。可依 key 或 index 存取集合中的項目。

#### 補充說明OrderedDictionary

- 具有Hashtable集合的特性，但多了一些Hashtable沒有的方法。
- Insert：使用指定的索引鍵和值，將新元素插入 [OrderedDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.ordereddictionary) 集合中指定的索引處。
- RemoveAt：從 [OrderedDictionary](http://msdn.microsoft.com/zh-tw/library/system.collections.specialized.ordereddictionary) 集合移除指定之索引處的元素。
- 加入集合中的內容會按照加入的順序排列；Hashtable內容存放的順序則與加入順序無關，Hashtable 會依自己的演算方式來排序順序。
```c#
OrderedDictionary score_table = new OrderedDictionary();
score_table["apple"] = 90;
score_table["tom"] = 60;
score_table["vito"] = 70;

score_table.Insert(0, "shao", "80");            //在最前頭插入一個項目
score_table.RemoveAt(score_table.Count - 1);    //移除最後一個

foreach (DictionaryEntry entry in score_table)
{
Console.WriteLine("{0} = {1}", entry.Key, entry.Value);
}
//項目的順序，依照加入位置的先後
//shao = 80
//apple = 90
//tom = 60

//也可以使用 indexer 取得項目值
Console.WriteLine(score_table[0]);  //80
```