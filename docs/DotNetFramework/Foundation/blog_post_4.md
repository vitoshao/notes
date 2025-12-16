---
title: 實值型別
layout: default
parent: Foundation
nav_order: 4
description: "實值型別"
date: 2011-12-16
tags: [DotNetFramework,Foundation]
postid: "7666115628544381975"
---
實值型別的執行個體是儲存在一個稱為「堆疊」(Stack)的記憶體區塊。  若與參考型別做比較，因為該區塊就直接包含資料，所以會有較佳的執行效率。  在.NET中，所有實值型別都在16個位元組以下。

一般實值型別可分成以下三種：

- 內建實值型別 (Built-in types)，如 [Int16](http://msdn.microsoft.com/zh-tw/library/system.int16.aspx) 、 [Boolean](http://msdn.microsoft.com/zh-tw/library/system.char.aspx) 、 [Byte](http://msdn.microsoft.com/zh-tw/library/system.byte.aspx) 、 [Guid](http://msdn.microsoft.com/zh-tw/library/system.guid.aspx) 、 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 、 [Point](http://msdn.microsoft.com/zh-tw/library/system.drawing.point.aspx)
- 自訂型別 (User-defined types)，如 結構 （Structures）
- 列舉型別 (Enumerations)，如 [DayOfWeek](http://msdn.microsoft.com/zh-tw/library/system.dayofweek.aspx) 、 [ConsoleColor](http://msdn.microsoft.com/zh-tw/library/system.consolecolor.aspx)

# 內建實值型別

Int16 、 Int32 、 [Byte](http://msdn.microsoft.com/zh-tw/library/system.byte.aspx) 、 [Boolean](http://msdn.microsoft.com/zh-tw/library/system.char.aspx) 、 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 都是內建實值型別，而 short 、 int 、 bool 、 byte 是它們的別名。  詳細內容可參考「[內建型別資料表](http://msdn.microsoft.com/zh-tw/library/ya5y69ds.aspx)」  

## 實值型別有幾個重要特性：

- 實值型別因為具有隱含的建構函式(implicit constructor)，所以宣告執行個體時，可以**不需要使用New**。
- 實值型別都**必需明確初始化之後才可以使用**。
- 實值型別預設**不允許Null值**，除非特別宣告成 [Nullable](http://msdn.microsoft.com/zh-tw/library/fs5xdbk8.aspx) （一個泛型類別）。
- 若要判斷變數是否已經指派數值，可將變數宣告成 [Nullable](http://msdn.microsoft.com/zh-tw/library/fs5xdbk8.aspx) 泛型類別，再使用 [HasValue](http://msdn.microsoft.com/zh-tw/library/sksw8094) 偵測。

#### 實值型別不允許指定null
```c#
int num;

//num = null;             //實值型別不允許 null 值
//int num2 = num;         //田為num是未初始化的變數，不能使用

num = new int();          // 呼叫 int 型別的default constructor 以進行初始化
Console.WriteLine(num);   // 0

num = 0;                  // 給定一個值以進行初始化
Console.WriteLine(num);   // 0

Point point = new Point();  // Invoke default constructor for the struct.
//在這個呼叫之後，結構型別將被視為已經明確的指派；也就是，它的所有成員會自動初始化它們的預設值。
Console.WriteLine("X={0}, Y={1}", point.X, point.Y);
```

## 內建實值型別表

|  | C# 型別 | .NET 型別 | 長度 | 範圍 | C# 型別 | .NET 型別 | 長度 | 範圍 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
|  | UnSign | UnSign | UnSign | UnSign | Sign | Sign | Sign | Sign |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 布林 | bool | System.Boolean | 1 bit |  |  |  |  |  |
| 整數 | byte | System.Byte | 1 byte | 0 ～ 255 | sbyte | System.Sbyte | 1 byte | -128 ～ 127 |
| ushort | System.UInt16 | 2 byte | 0 ～ 65,535 | short | System.Int16 | 2 byte | -32,768 ～ 32,767 |
| uint | System.UInt32 | 4 byte | 0   &lt;br&gt;～ 4,294,967,295 | int | System.Int32 | 4 byte | -2,147,483,648   &lt;br&gt;～ 2,147,483,647 |
| ulong | System.UInt64 | 8 byte | 0   &lt;br&gt;～ 18,446,744,073,709,551,615 | long | System.Int64 | 8 byte | -9,223,372,036,854,775,808   &lt;br&gt;～ 9,223,372,036,854,775,807 |
| 字元 | char | System.Char | 2 byte |  |  |  |  |  |
| 浮點值 | float | System.Single | 4 byte | 7 個數字 |  |  |  |  |
| double | System.Double | 8 byte | 15-16 個數字 |  |  |  |  |
| Decimal | decimal | System.Decimal | 16 byte | 15-16 個數字 |  |  |  |  |

# 自訂實值型別

結構 ( structs ) 就是一種自訂實值型別，它是由數個型別組合而成，以便在程式設計時能更容易處理相關的資料。

- 結構宣告內不能初始化欄位，除非將其宣告為 const 或 static。
- 結構為實值型別，而類別則是參考型別。
- 與類別不同的是，結構並不需要使用 new 運算子就能實體化。
- 結構**不支援繼承**。
- 結構**可實作介面**。

.NET 裡內建了不少有用的結構型別，如： [Guid](http://msdn.microsoft.com/zh-tw/library/system.guid.aspx) 、 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 、 [Point](http://msdn.microsoft.com/zh-tw/library/system.drawing.point.aspx) 這些都是實值型別。若要自訂結構，可參考下面範例：
```c#
struct sPerson
{
public string firstname;
public string lastname;
public Genders genger;
public int age;
//public int age = 5; 結構宣告內不能初始化欄位

public sPerson(string _firstname, string _lastname, int _age, Genders _gender)
{
firstname = _firstname;
lastname = _lastname;
age = _age;
genger = _gender;
}

public override string ToString()
{
return firstname.ToString() + " " + lastname.ToString() + " (" + genger + ")" + " age:" + age ;
}
}
```

### 結構只允許含有參數的建構函式

結構只允許定義含有參數的建構函式，不允許定義沒有參數的建構函式 (預設建構函式)。  因為編譯器會自動提供一個預設建構函式給結構，所以不允許再宣告一個沒有參數建構函式。  不過必須當 struct 是用 new 建立實體時，才會叫用這個預設建構函式。  另外，所有 struct 物件 (包括所有內建的實值型別) ，都可以透過指派值以進行初始化，就不需要呼叫預設建構函式來初始化。  

#### 使用預設建構函式初始化
```c#
int i = new int();
Console.WriteLine(i);   //i 已初始化，可以使用

int j ;                 //j 沒有使用 new 具現化
Console.WriteLine(j);   //編輯錯誤，嘗試使用尚未初始化的物件 j
```

#### 使用指派值初始化
```c#
int a = 44;  // Initialize the value type...
int b;
b = 33;      // Or assign it before using it.
Console.WriteLine("{0}, {1}", a, b);
```

# 列舉型別

[列舉 (Enumerations)](http://msdn.microsoft.com/zh-tw/library/cc138362.aspx)是一群相關連的符號，且具有相同的型別。其目的是為了簡化程式的撰寫及增進程式的閱讀性。

### 列舉值的型別

預設，列舉中每個項目的基礎型別是 int。  你可以使用冒號指定其他型別，但只支援以下數字型別：byte、sbyte、short、ushort、int、uint、long 或 ulong。  
```c#
enum fruitA : int
{
apple, strawberry, watermelon, banana
}

enum fruitB
{
apple=2, strawberry, watermelon=5, banana
}
```

### 列舉項目的順序

預設，列舉項目的順序是從 0 開始，也就是第一個列舉項目的列舉值為 0 ，往後每一個項目列舉值會遞增 1 。  你也可以直接自行指定列舉項目的列舉值，若沒有指定的項目，其列舉值就是前一項加 1 。  
```c#
foreach (int frunt in Enum.GetValues(typeof(fruitA)))
{
Console.WriteLine(frunt.ToString());　　　  //輸出 1 2 3 4
}

foreach (int frunt in Enum.GetValues(typeof(fruitB)))
{
Console.WriteLine(frunt.ToString());        //輸出 2 3 5 6
}
```

### 取得列舉值

當使用列舉變數取出的值，它會得到一個列舉項目；如果你要取出列舉值，只要透過型別轉換即可。  
```c#
fruitA fruit = fruitA.berry;
Console.WriteLine(fruit);            //berry
Console.WriteLine((int)fruit);       //1
```

如果要取得所有列舉項目，你可以透過 Enum 類別的方法取得。  

- [Enum.GetName](https://msdn.microsoft.com/zh-tw/library/system.enum.getname.aspx)：擷取有指定數值的指定列舉型別的常數名稱。
- [Enum.GetNames](https://msdn.microsoft.com/zh-tw/library/system.enum.getnames.aspx)：在指定的列舉中擷取常數名稱的陣列。
- [Enum.GetValues](https://msdn.microsoft.com/zh-tw/library/system.enum.getvalues.aspx)：在指定的列舉中擷取常數值的陣列。
- [Enum.Parse](https://msdn.microsoft.com/zh-tw/library/essfb559.aspx)：把一個或更多列舉型別常數的名稱或數字數值的字串表示轉換為一個對等列舉型別物件。
```c#
string s = Enum.GetName(typeof(fruitA), 2);
Console.WriteLine(s);                       //watermelon

foreach (string frunt in Enum.GetNames(typeof(fruitA)))
{
Console.WriteLine(frunt);               //apple berry watermelon banana
}

foreach (int frunt in Enum.GetValues(typeof(fruitA)))
{
Console.WriteLine(frunt);               //1 2 3 4
}

//將字串轉換成對等的列舉項目
fruitA banana = (fruitA)Enum.Parse(typeof(fruitA), "banana");
```

# 隱含型別 var

從 Visual C# 3.0 開始，在方法範圍宣告的變數具有**隱含型別** [var](http://msdn.microsoft.com/zh-tw/library/bb383973.aspx)。  隱含型別區域變數也是強型別 (Strongly Typed)，就和您自行宣告型別一樣，  差別在於自行宣告是直接指定型別，隱含型別是由編譯器 (Compiler) 從初始化陳述式右側的運算式推斷變數的型別。  下列兩個 i 宣告的功能相同：  
```c#
// implicitly typed
// 當程式進行編譯時，再依據變數值，決定型別。
var i = 10;     

// explicitly typed
int i = 10;     
```

隱含型別變數也常用在迴圈、陣列宣告、LINQ運算式中，例如：  
```c#
//使用隱含型別宣告陣列變數
var ar = new[] { 1, 2, 3, 4 };

//使用隱含型別宣告一個 LINQ 運算式
var exp =
from item in ar
where item % 2 == 0
select item;

//使用隱含型別列舉 foreach 中的項目
foreach (var item in exp)
{
Console.Write(item);
}    
```

# 匿名型別

「匿名型別」是 C# 所提供的一種方便語法，它可以將一些相關的資料，直接使用一個隱含的物件封裝起來，而不需另外自行定義一個物件來包裝。  匿名型別的實體一樣必須透過 new 來建立，但是因為沒有型別名稱，所以宣告時只能使用隱含型別 var 來宣告。  
```c#
var emp = new { Name = "vito", Age = 30 };

Console.WriteLine(emp.Name);
Console.WriteLine(emp.Age);  
```

# 判斷 ValueType or ReferenceType

透過 Type 類別的 [IsValueType()](http://msdn.microsoft.com/zh-tw/library/system.type.isvaluetype.aspx) 方法，可以取得是否為實值類別。
```c#
Byte var_byte = 0;
Int32 var_int = 0;
DateTime var_datetime = System.DateTime.Now;
Guid var_guid = new Guid();

string var_str = "";
Exception var_ex = new Exception();

object[] types = { var_byte, var_int, var_guid, var_datetime, var_str, var_ex };

foreach (object o in types)
{
string type;
if (o.GetType().IsValueType)
type = "Value type";
else
type = "Reference Type";
Console.WriteLine("{0}: {1}", o.GetType(), type);
}
//System.Byte:      Value type
//System.Int32:     Value type
//System.DateTime:  Value type
//System.Guid:      Value type
//System.String:    Reference Type
//System.Exception: Reference Type
```

# Nullable 類別

在 .NET 裡，實值型別是不允許指派 Null 值的。  不過有一種變通方法是將實值型別宣告成 [Nullable](http://msdn.microsoft.com/zh-tw/library/fs5xdbk8.aspx) 類別（一個泛型類別）。  若宣告成 [Nullable](http://msdn.microsoft.com/zh-tw/library/fs5xdbk8.aspx) 類別，才可以指派 Null 值給實值型別。  

#### Nullable 類別的定義：
```c#
public Nullable(T value);
```

#### Nullable 類別的屬性和方法：
```c#
public bool HasValue { get; }                   //判斷目前變數是否有值。
public T Value { get; }                         //取得目前變數的值。

public T GetValueOrDefault();                   //取得目前變數的值，若物件為null，則回傳預設值。
public T GetValueOrDefault(T defaultValue);
```

#### 使用 ? 縮寫形式

宣告一個 [Nullable](http://msdn.microsoft.com/zh-tw/library/fs5xdbk8.aspx) 類別，可以這麼用
```c#
Nullable<int> num;
```

也可以用 ? 附註在實值型別之後
```c#
int? num = null;
```

**範例**
```c#
// Nullable 宣告方式１Nullable<int> num;
num = null;    // Nullable 宣告方式2 (這一行較精簡的句子,等同上面二行)  //int? num = null;    // 使用 HasValue 判斷變收是否有值  if (num.HasValue)      Console.WriteLine("num is {0}", num.Value);  else      Console.WriteLine("num is not set");    // 使用 GetValueOrDefault 擷取目前 Nullable<T> 物件的值，或物件的預設值。  Console.WriteLine(num);                         //  Console.WriteLine(num.GetValueOrDefault());     //0  ```

#### 附記：什麼是 ??

若程式碼長這樣子 「 x??0 」表示什意思?

「??」運算子稱為 [null 聯合運算子（null-coalescing operator）](https://msdn.microsoft.com/zh-tw/library/ms173224.aspx?f=255&MSPPError=-2147217396)。  意思是說，如果左方運算元不是 null，則會傳回左方運算元，否則傳回右方運算元。

所以上面運算式就相當於運算式 x!=null ? x : 0
```c#
Nullable<int> x, y;
x = 1;
y = null;

int a = x ?? 0;   // ( if x=null then a=0 else a=x )
int b = y ?? 0;

// a=1, b=0
```

# unchecked & checked

這二個關鍵字是用來告知編譯器是否執行**溢位判斷**。預設值是 unchecked .  溢位判斷是針對整數類的資料型別，只有設定為 checked 狀態，當資料值發生溢位時，才會引發錯誤。  

- [checked](http://msdn.microsoft.com/zh-tw/library/74b4xzyw) ：啟用整數資料型別 (Integral Type) 算術運算和轉換的溢位檢查。
- [unchecked](http://msdn.microsoft.com/zh-tw/library/74b4xzyw) ：隱藏整數資料型別 (Integral Type) 算術運算和轉換的溢位檢查。
```c#
try
{
int int1 = 2147483647;
int int2 = (int1 + 1);              // 這行程式碼結果為 -2147483648
int int3 = unchecked(int1 + 1);     // 同上 
int int4 = checked(int1 + 1);       // 產生溢位錯誤
}
catch (Exception ex)
{
Console.WriteLine(ex.Message);
}
```


- [實值型別 (C# 參考)](http://msdn.microsoft.com/zh-tw/library/s1ax56ch)
- [型別參考表 (C# 參考)](http://msdn.microsoft.com/zh-tw/library/1dhd7f2x)
-