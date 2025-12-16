---
title: 使用文化特性
layout: default
parent: 全球化
nav_order: 1
description: "使用文化特性"
date: 2012-03-28
tags: [DotNetFramework,全球化]
postid: "1139717053952988951"
---
當設計一個有多語系需求的程式時，除了UI上面的文字以外，另外像日期或幣值的顯示格式也都是要考量進來的議題。  例如：日元的顯示格式為 ￥123,456，而台幣的顯示格式為 NT$123,456；或者像日期的格式，美國使用的是 月/日/年 ，而日本使用的是 年/月/日。這些差異都會因為不同地區而有所不同，簡稱文化特性。  像這類狀況，因為我們無法預期未來會面對什麼樣的使用者需求，如果等系統開發完成後，才依照不同的地區性去做修改，那這個成本肯定會變的更大。  因此如何將資料依不同的文化特性格式化就變的很重要的課題。.Net Framework 提供了 [System.Globalization](http://msdn.microsoft.com/zh-tw/library/system.globalization.aspx) 命名空間來幫助開發者解決這個問題。  

以下是三個與 Globalization 息息相關的類別。

- [CultureInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.aspx) ：提供有關特定文化特性 (Culture) 的資訊，包括文化特性的名稱、書寫系統、使用的曆法，以及日期和排序字串的格式。
- [RegionInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.regioninfo) ：包含關於國家/地區的資訊。
- [CultureAndRegionInfoBuilder](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureandregioninfobuilder) ：定義新的自訂文化特性，或根據其他文化特性和國家/地區的自訂文化特性。

&lt;!-- more --&gt;    
一般來說，當組件部署時就定下了該組件的文化特性，不過，由程式之中，還是可以變更這個文化特性的值，但這個變更將只會在目前的應用程式中有效而已。  

系統中有二個不同的屬性可以用來變更文化特性。  

- [Thread.CurrentThread.CurrentCulture](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.currentculture.aspx) ：  
這個值是用來取得或設定目前執行緒的文化特性，與內部計算的功能相關。例如：日期、數字、貨幣格式等等。
- [Thread.CurrentThread.CurrentUICulture](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.currentuiculture.aspx) ：  
這個值是用來取得或設定資源管理員目前用以在執行階段查詢特定文化特性資源所用的文化特性。  例如：若你的系統提供多語系的資源檔時，可以透過這個值的設定，讓資源管理員知道應該要載入哪一個語系的資源檔。

# CultureInfo

[CultureInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.aspx) 類別提供有關特定文化特性的資訊 (文化特性在 Unmanaged 程式碼開發中稱為「地區設定」(Locale))。  提供的資訊包括文化特性的名稱、書寫系統、使用的曆法，以及日期和排序字串的格式。  

CultureInfo 建構子
```c#
public CultureInfo(int culture);
public CultureInfo(string name);
public CultureInfo(int culture, bool useUserOverride);
public CultureInfo(string name, bool useUserOverride);
```

下表是 [CultureInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.aspx) 類別的一些基本用法：

- 控制**字串**的比較
- 控制**數字**的比較與顯示格式
- 控制**日期**的比較與顯示格式
- 控制**資源**的存取與使用

## 取得系統中的文化特性

使用 [CultureInfo.GetCultures](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.getcultures) 可以取得系統中的文化特性，它必須傳入一個 [CultureTypes](http://msdn.microsoft.com/zh-tw/library/system.globalization.culturetypes.aspx) 列舉值當參數，以定義要擷取哪一類型的文化特性清單。  
```c#
//==================================================================
// 目前 Thread 使用的 Culture
//==================================================================
CultureInfo currentCluture = Thread.CurrentThread.CurrentCulture;
Console.WriteLine("The current culture  : " + currentCluture.Name);
Console.WriteLine("The Display Name : " + currentCluture.DisplayName);
Console.WriteLine("The Native Name : " + currentCluture.NativeName);
Console.WriteLine("The ISO Abbreviation : " + currentCluture.TwoLetterISOLanguageName);

//The current culture  : zh-TW
//The Display Name : 中文 (繁體，台灣)
//The Native Name : 中文(中華民國)
//The ISO Abbreviation : zh

//==================================================================
// 列舉系統中的 Culture
//==================================================================
CultureInfo[] cultureInfos = CultureInfo.GetCultures(CultureTypes.SpecificCultures | CultureTypes.UserCustomCulture) ;
foreach (CultureInfo cultureInfo in cultureInfos)
{
Console.WriteLine("Culture: " + cultureInfo.Name);
}
//Culture: jp-JP
//Culture: en-US
//Culture: en-VE
//Culture: zh-CN => 中文 (簡體，中華人民共和國)
//Culture: zh-HK => 中文 (繁體，香港特別行政區)
//Culture: zh-MO => 中文 (繁體，澳門特別行政區)
//Culture: zh-SG => 中文 (簡體，新加坡)
//Culture: zh-TW => 中文 (繁體，台灣)
//Culture: en-SG => 英文 (新加坡)
//Culture: ko-KR => 韓文 (韓國)
//Culture: ja-JP => 日文 (日本)
//......

//==================================================================
// CurrentUICulture
//==================================================================
CultureInfo currentUICluture = Thread.CurrentThread.CurrentUICulture;
Console.WriteLine("The current UI culture : " + currentUICluture.Name); // zh-TW
```

## 顯示與文化特性相關的資訊

- [DateTimeFormat](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.aspx) ：這個屬性定義 **DateTime**相關的顯示格式。
- [AbbreviatedDayNames](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.abbreviateddaynames.aspx) ：星期縮寫名稱的一維陣列
- [AbbreviatedMonthNames](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.abbreviatedmonthnames.aspx) ：月份縮寫名稱的一維陣列
- [DayNames](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.daynames.aspx) ：星期完整名稱的一維陣列
- [MonthNames](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.monthnames.aspx) ：月份完整名稱的一維陣列
- [FirstDayOfWeek](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.firstdayofweek.aspx) ：週的第一天。
- [DateSeparator](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.dateseparator.aspx) ：分隔日期的字串。
- [TimeSeparator](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.timeseparator.aspx) ：分隔時間的字串。
- [FullDateTimePattern](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.fulldatetimepattern.aspx) ：完整日期和完整時間值的格式，這值與 'F' 格式模式相關聯。
- [LongDatePattern](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.longdatepattern.aspx) ：完整日期值的自訂格式字串，這個字串與 "D" 標準格式字串相關聯。
- [LongTimePattern](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.longtimepattern.aspx) ：完整時間值的格式模式，這值與 "T" 格式模式相關聯。
- [ShortDatePattern](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.shortdatepattern.aspx) ：簡短日期值的格式模式，這值與 "d" 格式模式相關聯。
- [ShortTimePattern](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.shorttimepattern.aspx) ：簡短時間值的格式模式，這值與 "t" 格式模式相關聯。
- [YearMonthPattern](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.yearmonthpattern.aspx) ：年份和月份值的格式模式，這值與 "y" 和 "Y" 格式模式相關聯。
- [NumberFormat](http://msdn.microsoft.com/zh-tw/library/system.globalization.numberformatinfo.aspx) ：這個屬性定義了與 **數值**相關的顯示格式。例如：
- [CurrencyDecimalDigits](http://msdn.microsoft.com/zh-tw/library/system.globalization.numberformatinfo.currencydecimaldigits.aspx) ：貨幣值中使用的小數位數。
- [CurrencyDecimalSeparator](http://msdn.microsoft.com/zh-tw/library/system.globalization.numberformatinfo.currencydecimalseparator.aspx) ：貨幣值中小數分隔符號的字串。
- [CurrencyNegativePattern](http://msdn.microsoft.com/zh-tw/library/system.globalization.numberformatinfo.currencynegativepattern.aspx) ：負數貨幣值的格式模式。／0:($n) ／1:-$n／2:$-n／3:$n-／4:(n $)／
- [CurrencyPositivePattern](http://msdn.microsoft.com/zh-tw/library/system.globalization.numberformatinfo.currencypositivepattern.aspx) ：正數貨幣值的格式模式。／0:$n／1:n$／2:$ n／3:n $／
- [CurrencySymbol](http://msdn.microsoft.com/zh-tw/library/system.globalization.numberformatinfo.currencysymbol.aspx) ：貨幣符號的字串。
- [NumberDecimalDigits](http://msdn.microsoft.com/zh-tw/library/system.globalization.numberformatinfo.numberdecimaldigits.aspx) ：使用的小數位數。
- [NumberDecimalSeparator](http://msdn.microsoft.com/zh-tw/library/system.globalization.numberformatinfo.numberdecimalseparator.aspx) ：小數分隔符號的字串。
- [NumberNegativePattern](http://msdn.microsoft.com/zh-tw/library/system.globalization.numberformatinfo.numbernegativepattern.aspx) ：負數值的格式。／0:(n)／1:-n／2:- n／3:n-／4:n -／

這個範例示範如何變更不同文化特性。
```c#
int tmpNum = 123456;
DateTime tmpDate = new DateTime(2012, 7, 10);

//==================================================================
// Default CultureInfo
//==================================================================
CultureInfo currentCluture = Thread.CurrentThread.CurrentCulture;
Console.WriteLine("CurrentCulture is : " + currentCluture.Name);                   // zh-TW
Console.WriteLine("幣值：" + tmpNum.ToString("C"));                                // 幣值：NT$123,456
Console.WriteLine("日期：" + tmpDate.ToShortDateString());                         // 日期：2012/7/10

//==================================================================
// Set CultureInfo
//==================================================================

//指定 zh-TW 文化特性，並使台灣曆法
CultureInfo culture = new CultureInfo("zh-TW");
culture.DateTimeFormat.Calendar = new System.Globalization.TaiwanCalendar();       // 指定台灣曆法
System.Threading.Thread.CurrentThread.CurrentCulture = culture;
Console.WriteLine("CurrentCulture is : " + Thread.CurrentThread.CurrentCulture);   // zh-TW
Console.WriteLine("幣值：" + tmpNum.ToString("C"));                                // 幣值：NT$123,456
Console.WriteLine("日期：" + tmpDate.ToShortDateString());                         // 日期：101/7/10

//指定 ja-JP 文化特性
Thread.CurrentThread.CurrentCulture = new CultureInfo("ja-JP");
Console.WriteLine("CurrentCulture is : " + Thread.CurrentThread.CurrentCulture);   // ja-JP
Console.WriteLine("幣值：" + tmpNum.ToString("C"));                                // 幣值：￥123,456
Console.WriteLine("日期：" + tmpDate.ToShortDateString());                         // 日期：2012/07/10

//指定 zh-CN 文化特性
Thread.CurrentThread.CurrentCulture = new CultureInfo("zh-CN");
Console.WriteLine("CurrentCulture is : " + Thread.CurrentThread.CurrentCulture);   // zh-CN
Console.WriteLine("幣值：" + tmpNum.ToString("C"));                                // 幣值：￥123,456.00
Console.WriteLine("日期：" + tmpDate.ToShortDateString());                         // 日期：2012/3/28

//指定 委內瑞拉 文化特性
Thread.CurrentThread.CurrentCulture = new CultureInfo("es-VE");
Console.WriteLine("CurrentCulture is : " + Thread.CurrentThread.CurrentCulture);   // es-VE
Console.WriteLine("幣值：" + tmpNum.ToString("C"));                                // 幣值：Bs. F. 123.456,00  (小數點與千分位和一般完全相反)
Console.WriteLine("日期：" + tmpDate.ToShortDateString());                         // 日期：10/07/2012

//指定 香港 文化特性，並且用 minus sign 顯示負值的貨幣
culture = new CultureInfo("zh-HK");
culture.NumberFormat.CurrencyNegativePattern = 1;
Thread.CurrentThread.CurrentCulture = culture;
tmpNum = -123456;
Console.WriteLine("CurrentCulture is : " + Thread.CurrentThread.CurrentCulture);   // zh-HK
Console.WriteLine("幣值：" + tmpNum.ToString("C"));                                // 幣值：-HK$123,456.00
Console.WriteLine("日期：" + tmpDate.ToShortDateString());                         // 日期：10/7/2012
```

這個範例示範如何取得特定的 Culture 的相關屬性值。
```c#
CultureInfo UsersCulture = new CultureInfo("zh-TW");

// 取得符合文化特性的星期名稱
String[] Days = UsersCulture.DateTimeFormat.DayNames;     
Console.Write("星期名稱：");
foreach (String Day in Days)
{
Console.Write(Day + " ");
}
//星期名稱：星期日 星期一 星期二 星期三 星期四 星期五 星期六 

// 取得符合文化特性的月份名稱
String[] Months = UsersCulture.DateTimeFormat.MonthNames; 
foreach (String Month in Months)
Console.Write(Month + " ");

// 一些常用屬性
Console.WriteLine("日期的分隔符號：" + UsersCulture.DateTimeFormat.DateSeparator);
Console.WriteLine("貨幣符號      ：" + UsersCulture.NumberFormat.CurrencySymbol); 
Console.WriteLine("小數點符號    ：" + UsersCulture.NumberFormat.NumberDecimalSeparator);
Console.WriteLine("千分位符號    ：" + UsersCulture.NumberFormat.NumberGroupSeparator);

//日期的分隔符號：/
//貨幣符號      ：NT$
//小數點符號    ：.
//千分位符號    ：，
```

上面範例中，我們使用變更組件的文化特性，以顯示不同文化特性下的特定資訊。  下面這個範例，直接指定顯示字串的文化特性，也可以顯示特定文化特性的日期和貨幣格式。
```c#
DateTime tmpDate = DateTime.Now;

// 以 default culture 顯示日期
Console.WriteLine(tmpDate.ToString());                                  //2012/7/10 下午 12:20:14

// 以 English-Great Britain culture 顯示日期
Console.WriteLine(tmpDate.ToString(new CultureInfo("en-GB")));          //10/07/2012 12:20:14

// 以 Russian culture 顯示日期
Console.WriteLine(tmpDate.ToString(new CultureInfo("ru-RU")));          //10.07.2012 12:20:14

double number = 1234567.89;

// 以 default culture 顯示幣別格式
Console.WriteLine(number.ToString("C"));                                //NT$1,234,567.89

// 以 English-Great Britain culture 顯示幣別格式
Console.WriteLine(number.ToString("C", new CultureInfo("en-GB")));      //￡1,234,567.89

// 以 Russian culture 顯示幣別格式
string RussianCurrency = number.ToString("C", new CultureInfo("ru-RU"));
Console.WriteLine(number.ToString("C", new CultureInfo("ru-RU")));      //1 234 567,89р.
```

# RegionInfo

RegionInfo 建構子
```c#
public RegionInfo(int culture);
public RegionInfo(string name);
```

## 使用 RegionInfo 顯示額外資訊

RegionInfo 和使用者的語言或文化特性沒有相關。   它包含了一些 [CultureInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.aspx) 類別沒有的額外資訊，如關於國家/地區的資訊。  透過 [CultureInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.aspx) 的 [Name](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.name) 或 [LCID](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.lcid) 這二個屬性連結使用。  
```c#
CultureInfo UsersCulture = Thread.CurrentThread.CurrentCulture;
//UsersCulture.LCID= 1028 
//UsersCulture.Name= zh-TW

RegionInfo DemoRegion1 = new RegionInfo(UsersCulture.LCID);
RegionInfo DemoRegion2 = new RegionInfo(UsersCulture.Name); //結果同上

Console.WriteLine("English Name: " + DemoRegion1.EnglishName);
Console.WriteLine("Display Name: " + DemoRegion1.DisplayName);
Console.WriteLine("Currency Symbol: " + DemoRegion1.CurrencySymbol);
Console.WriteLine("TwoLetterISORegionName : " + DemoRegion1.TwoLetterISORegionName);
Console.WriteLine("ThreeLetterISORegionName  : " + DemoRegion1.ThreeLetterISORegionName);

//English Name: Taiwan
//Display Name: 台灣
//Currency Symbol: NT$
//TwoLetterISORegionName : TW
//ThreeLetterISORegionName  : TWN

RegionInfo DemoRegion5 = new RegionInfo("zh-CN");
Console.WriteLine("English Name: " + DemoRegion5.EnglishName);
Console.WriteLine("Display Name: " + DemoRegion5.DisplayName);
Console.WriteLine("Currency Symbol: " + DemoRegion5.CurrencySymbol);
Console.WriteLine("TwoLetterISORegionName : " + DemoRegion5.TwoLetterISORegionName);
Console.WriteLine("ThreeLetterISORegionName  : " + DemoRegion5.ThreeLetterISORegionName);
//English Name: People's Republic of China

//Display Name: 中華人民共和國
//Currency Symbol: ￥
//TwoLetterISORegionName : CN
//ThreeLetterISORegionName  : CHN

RegionInfo DemoRegion3 = new RegionInfo("CA");
Console.WriteLine("English Name: " + DemoRegion3.EnglishName);
Console.WriteLine("Display Name: " + DemoRegion3.DisplayName);
Console.WriteLine("Currency Symbol: " + DemoRegion3.CurrencySymbol);
Console.WriteLine("TwoLetterISORegionName : " + DemoRegion3.TwoLetterISORegionName);
Console.WriteLine("ThreeLetterISORegionName  : " + DemoRegion3.ThreeLetterISORegionName);

//English Name: Canada
//Display Name: 加拿大
//Currency Symbol: $
//TwoLetterISORegionName : CA
//ThreeLetterISORegionName  : CAN
```

# 排序與比較

不同的文化特性，在執行排序或比較時，會有不一樣的結果。  或許中文中比較難以想像這種情況，書中舉例說，像 &lt;font style="font-size:large"&gt;Æ&lt;/font&gt; 這個字母 (AE連在一起)，  丹麥文會將字元 "Æ" 視為單一字母，其字母排列位於字母 "Z" 的後面，  在德文中 "Æ" 類似 "ae" ，其字母排列位於 A 的後面，  在英文中 "Æ" 被似為特殊號，其字母排列位於 A 的前面。  

另外，一種語言也可能不只一種排序，像 CultureInfo("zh-cn")，就有拼音(pronunciation)和筆劃(stroke count)二種排序方式。  

所以在進行字串比較的時候，就要特別注意是否要需要考量文化特性的差異。  在 [CultureInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.aspx) 物件中，有一個 [CompareInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.compareinfo.aspx) 屬性，它就是一個 [CompareInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.compareinfo.aspx) 實體。  這個物件提供許多區分文化特性 (Culture) 的字串比較方法。  像 [String.Compare](http://msdn.microsoft.com/zh-tw/library/system.string.compare.aspx) 也是使用 [CultureInfo.CompareInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.compareinfo.aspx) 中的資訊來比較字串。   

- [Compare](http://msdn.microsoft.com/zh-tw/library/system.globalization.compareinfo.compare.aspx) ：比較兩個字串。
- [Equals](http://msdn.microsoft.com/zh-tw/library/system.globalization.compareinfo.equals.aspx) ：判斷指定的物件是否等於
- [IndexOf](http://msdn.microsoft.com/zh-tw/library/system.globalization.compareinfo.indexof.aspx) ：傳回某值在字串或字串的一部分內，第一次出現的索引(以零起始)。
- [LastIndexOf](http://msdn.microsoft.com/zh-tw/library/system.globalization.compareinfo.lastindexof.aspx) ：傳回某值在字串或字串的一部分內，最後一次出現的索引(以零起始)。
- [GetCompareInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.compareinfo.getcompareinfo.aspx) ：初始化新的 [CompareInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.compareinfo.aspx) 物件。

這個物件比較特別的是它沒有 Constructor ，你必須由 [Thread.CurrentThread.CurrentCulture](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.currentculture.aspx) 取得，或由它本身的靜態方法 [CompareInfo.GetCompareInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.compareinfo.getcompareinfo.aspx) 建立一個新的實體。

## 使用 CompareInfo 來做符合文化特性的字串比對

下面這個例子中使用二個不一樣字串，因不同的文化特性，比較結果也不同。
```c#
string str1 = "Æble";
string str2 = "Apple";

CultureInfo culture ;
CompareInfo comparer ;

//=================================================
//使用 en-US 文化特性進行字串比較與排序
//=================================================
culture = new CultureInfo("en-US");
comparer = culture.CompareInfo;

result = comparer.Compare(str1, str2);      // result = -1 (前者較小)

//=================================================
//使用 da-DK 文化特性進行字串比較
//=================================================
culture = new CultureInfo("da-DK");         //Denmark
comparer = culture.CompareInfo;

result = comparer.Compare(str1, str2);      // result = 1 (前者較大)
```

下面這個例子使用特定的文化特性搜尋字串。
```c#
CompareInfo comparer;
string tmpstring = "tAEl";

//===============================================================
// 使用 en-US 文化特性進行字串搜尋
//===============================================================
comparer = new CultureInfo("en-US").CompareInfo;
int idx1 = comparer.IndexOf(tmpstring, "Æ");        //return -1

//===============================================================
// 使用 da-DK 文化特性進行字串搜尋
//===============================================================
comparer = new CultureInfo("da-DK").CompareInfo;
int idx2 = comparer.IndexOf(tmpstring, "Æ");        //return -1

//===============================================================
// 使用不分文化特性進行字串搜尋
//===============================================================
comparer = CultureInfo.InvariantCulture.CompareInfo;
int idx3 = comparer.IndexOf(tmpstring, "Æ");        //return 1
int idx4 = tmpstring.IndexOf("Æ", StringComparison.InvariantCulture); //同上
```

## 搭配 CompareOptions 設定，進行字串比對

[CompareOptions](http://msdn.microsoft.com/zh-tw/library/system.globalization.compareinfo) 是個列舉值，可以用來指定比較時的一些選項設定，如：

- IgnoreCase：忽略大小寫
- IgnoreSymbols：忽略符號，例如空白字元、標點符號、貨幣符號、百分比符號、數學符號、＆ 符號等等。
- IgnoreWidth：忽略字元寬度
- ...

下面這個例子，或許二個字串看起來不一樣，但在某些條件下，它們比較結果可以是相同的。像日文中的平假名和片假名，也可以用相同方法比較。
```c#
CompareInfo compareInfo = Thread.CurrentThread.CurrentCulture.CompareInfo;

string s1 = "測試";
string s2 = "測 試";
Console.WriteLine(compareInfo.Compare(s1, s2));                                 //1 : S1>S2
Console.WriteLine(compareInfo.Compare(s1, s2, CompareOptions.IgnoreSymbols));   //0 : S1=S2
Console.WriteLine(string.Compare(s1, s2, StringComparison.InvariantCulture));   //1
Console.WriteLine(string.Compare(s1, s2, Thread.CurrentThread.CurrentCulture, CompareOptions.IgnoreSymbols)); //0
```
```c#
string str1 = "a";
string str2 = "A";
string str3 = "Ａ";

CompareInfo comparer = Thread.CurrentThread.CurrentCulture.CompareInfo;

int r1 = comparer.Compare(str1, str2);                              // -1
int r2 = comparer.Compare(str2, str3);                              // -1
int r3 = comparer.Compare(str1, str2, CompareOptions.IgnoreCase);   // 1
int r4 = comparer.Compare(str2, str3, CompareOptions.IgnoreWidth);  // 1
int r5 = comparer.Compare(str1, str3, CompareOptions.IgnoreCase | CompareOptions.IgnoreWidth);  // 1
```

## 不分文化特性的比較 (Culture-Insensitive)

因為不同的文化特性可能會發生排序或比較的結果不同，但這樣子的結果有時候確不是我們想要的。  要避免因為文化特性造成比較結果的不同，我們可以使用：

- [CultureInfo.InvariantCulture](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.invariantculture) ：建立一個不分文化特性而異的執行個體。
- [StringComparison.InvariantCulture](http://msdn.microsoft.com/zh-tw/library/system.stringcomparison) ：覆寫字串的比較子，使用不因文化特性而異的字組比較規則。
```c#
//===============================================================
// 使用 InvariantCulture 比較字串
//===============================================================
Thread.CurrentThread.CurrentCulture = new CultureInfo("da-DK");
string tmpstring = "Apple";
int idx1 = tmpstring.IndexOf("Æ");  //丹麥中，Apple 找不到 Æ
int idx2 = tmpstring.IndexOf("Æ", StringComparison.InvariantCulture); //若不分文化特性，就找的到了
// idx1=-1  idx2=0
```

## 不同文化特性的排序
```c#
string[] words = new string[] { "Apple", "Æble" };

Thread.CurrentThread.CurrentCulture = new CultureInfo("en-US");
SortWords(words);
//Æble
//Apple

Thread.CurrentThread.CurrentCulture = new CultureInfo("da-DK");
SortWords(words);
//Apple
//Æble

Thread.CurrentThread.CurrentCulture = CultureInfo.InvariantCulture;
SortWords(words);
//Æble
//Apple
```

# 自訂文化特性

若系統中現有的文化特性都不符合需求，也可以透過 [CultureAndRegionInfoBuilder](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureandregioninfobuilder) 類別，自訂一個文化特性。

雖然 [CultureAndRegionInfoBuilder](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureandregioninfobuilder) 的命名空間也是 [System.Globalization](http://msdn.microsoft.com/zh-tw/library/system.globalization) ，但是它的組件是放在 sysglobl.dll 中，必須自已手動加入參考，它位在 %SystemRoot%\Windows\.NET Framework\[VERSION]\sysglobl.dll。

#### [CultureAndRegionInfoBuilder](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureandregioninfobuilder) 類別中，幾個重要方法：

- [LoadDataFromCultureInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureandregioninfobuilder.loaddatafromcultureinfo) ：將指定的 [CultureInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.aspx) 上的資料，設定到建立的 [CultureAndRegionInfoBuilder](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureandregioninfobuilder) 物件上。
- [LoadDataFromRegionInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureandregioninfobuilder.loaddatafromregioninfo) ：將指定的 [RegionInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.regioninfo) 上的資料，設定到建立的 [CultureAndRegionInfoBuilder](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureandregioninfobuilder) 物件上。
- [NumberFormat](http://msdn.microsoft.com/zh-tw/library/system.globalization.numberformatinfo.aspx) ：取得或設定 [NumberFormatInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.numberformatinfo.aspx) 物件，定義數字、貨幣和百分比在文化特性上適當的顯示格式。
- [GregorianDateTimeFormat](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureandregioninfobuilder.gregoriandatetimeformat.aspx) ：取得或設定 [DateTimeFormatInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.aspx) 物件，此物件會根據西曆定義日期和時間的格式。
- [Register](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureandregioninfobuilder.register) ：將自訂的文化特性保存至本機電腦上。必須有系統管理員權限。
- [Save](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureandregioninfobuilder.register) ：將目前 [CultureAndRegionInfoBuilder](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureandregioninfobuilder) 物件的 XML 表示寫入指定檔案中。

底下這個範例，示範如何建立一個 culture ，並設定底下屬性：  
&gt; 幣別符別： VS$  
&gt; 幣別小數點符號： '  
&gt; 幣別小數點位數： 3  
&gt; 星期： Ｄ１, Ｄ２, Ｄ３, Ｄ４, Ｄ５, Ｄ６, Ｄ７  
&gt; 年月日分隔符號：|  
```c#
CultureInfo TWCulture = new CultureInfo("zh-TW");
RegionInfo TWRegion = new RegionInfo("TW");
CultureAndRegionInfoBuilder DemoBuilder = new CultureAndRegionInfoBuilder("zh-VITO", CultureAndRegionModifiers.None);   //建立一個全新的 CultureAndRegionInfoBuilder
DemoBuilder.LoadDataFromCultureInfo(TWCulture);     // 載入 TWCulture 上的資料
DemoBuilder.LoadDataFromRegionInfo(TWRegion);       // 載入 TWRegion 上的資料

NumberFormatInfo NumberInfo = new NumberFormatInfo();
NumberInfo.CurrencySymbol = "VS$";
NumberInfo.CurrencyDecimalDigits = 3;
NumberInfo.CurrencyDecimalSeparator = "'";
DemoBuilder.NumberFormat = NumberInfo;

DateTimeFormatInfo DateInfo = new DateTimeFormatInfo();
DateInfo.DayNames = new String[] { "Ｄ１", "Ｄ２", "Ｄ３", "Ｄ４", "Ｄ５", "Ｄ６", "Ｄ７" };
DateInfo.DateSeparator = "|";
DemoBuilder.GregorianDateTimeFormat = DateInfo;

//註冊自訂的文化特性
DemoBuilder.Register();

//註冊後才可以使用
CultureInfo ci = new CultureInfo("zh-VITO");

//顯示文化特性屬性值
Console.WriteLine("Name: . . . . . . . . . . . . . {0}", ci.Name);
Console.WriteLine("EnglishName:. . . . . . . . . . {0}", ci.EnglishName);
Console.WriteLine("DisplayName:. . . . . . . . . . {0}", ci.DisplayName);

//顯示文化特性中的星期格式
String[] Days = ci.DateTimeFormat.DayNames;
Console.WriteLine("Day Name for 'zh-VITO' : ");
foreach (String Day in Days)
Console.WriteLine(Day + " ");

//設定目前組件的cultrue為上面自訂的cultrue
System.Threading.Thread.CurrentThread.CurrentCulture = ci;

//顯示幣值與日期格式
Console.WriteLine("幣值：" + (1234567).ToString("C"));              //幣值：VS$1,234,567'000
Console.WriteLine("日期：" + DateTime.Now.ToShortDateString());     //日期：03|28|2012

//刪除自訂的文化特性
CultureAndRegionInfoBuilder.Unregister("zh-VITO");

//=============以下是輸出結果=============
//Name: . . . . . . . . . . . . . zh-VITO
//EnglishName:. . . . . . . . . . Chinese (Traditional, Taiwan)
//DisplayName:. . . . . . . . . . 中文(中華民國)
//Day Name for 'zh-VITO' : Ｄ１ Ｄ２ Ｄ３ Ｄ４ Ｄ５ Ｄ６ Ｄ７
//幣值：VS$1,234,567'000
//日期：03|28|2012
```

PS.
[Microsoft Visual Studio International Feature Pack 2.0](http://www.microsoft.com/zh-tw/download/details.aspx?id=18970)  
這個套件提供了一組控制項和類別庫以幫助.NET開發人員建立符合國際化需求的應用程式。  譬如它可進行中文繁體, 中文簡體, 日文以及韓文的轉換。