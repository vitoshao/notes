---
title: Datetime 型別
layout: default
parent: Foundation
nav_order: 13
description: "Datetime 型別"
date: 2012-08-21
tags: [DotNetFramework,Foundation]
postid: "4886348612633858125"
---
# DateTime 物件

## 認識 DateTime 物件

[DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 是一個結構型態的實值型別，是用來表示日期和時間的物件，具有以下幾個特性：

- 可記錄範圍：0001/1/1 12:00:00 AM ～ 9999/12/31 11:59:59 PM
- 時間值是以刻度（Tick）為最小單位，每個 Tick 等於 100 奈秒。
- Tick 值由 0001/1/1 12:00:00 AM 開始累加計算，每 100 奈秒，Tick 值加一。

## 標準時間

DateTime 物件本身是含時區觀念的一個結構，在使用這個物件前，可以先瞭解一下什麼是 UTC 與 GMT 。

### 格林威治標準時間 (GMT)

以通過格林威治的子午線作為劃分地球東西兩半球的經度零度。  全球都以格林威治的時間作為標準來設定時間，所以也稱格林威治標準時間 (Greenwich Mean Time，簡稱G.M.T.)。

### 世界協調時間 (UTC)

世界協調時間 (Coordinated Universal Time，UTC) 是一種由國際原子時所綜合精算而成的時間，全球統一使用的時間，也是最主要的世界時間標準。  UTC 比 GMT 來得更加精準。其誤差值必須保持在0.9秒以內，若大於0.9秒則由位於巴黎的國際地球自轉事務中央局發佈閏秒，使 UTC 與地球自轉週期一致。

### 各地時間

#### 台灣時間

GMT+8  

#### 美國東部標準時間 (Eastern Standard Time, EST)

GMT-5。晚台灣 13 小時。  

#### 太平洋標準時 (Pacific Standard Time, PST)

GMT-8。晚台灣 16 小時。  

#### 英國時間

GMT-0。晚台灣 8 小時。
&lt;!-- more --&gt;    
# DateTime 的轉換與運算

## 具現化 DateTime 物件
```c#
DateTime dtime1 = new DateTime(2012, 2, 15);
DateTime dtime2 = new DateTime();
DateTime dtime3 = new DateTime(2012, 2, 15, 10, 8, 45);
DateTime dtime4 = new DateTime(2012, 2, 15, 10, 8, 45, DateTimeKind.Local);
DateTime dtime5 = new DateTime(2012, 2, 15, 10, 8, 45, DateTimeKind.Utc);   
DateTime dtime6 = DateTime.Parse("2012-3-15 10:8:00");
DateTime dtime7 = DateTime.Now;
DateTime dtime8 = DateTime.UtcNow;
```

在 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 的建構子中，比較特別的是下面這幾個
```c#
1) public DateTime(int year, int month, int day, int hour, int minute, int second, Calendar calendar);
2) public DateTime(int year, int month, int day, int hour, int minute, int second, DateTimeKind kind);
3) public DateTime(int year, int month, int day, int hour, int minute, int second, int millisecond, Calendar calendar, DateTimeKind kind);
```

其中有幾個比較重要的參數：

- 1) [DateTimeKind](http://msdn.microsoft.com/zh-tw/library/system.datetimekind.aspx) ：這個列舉值用來表示 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx)的類別。
- Unspecified ：未指定類別。 (=0，預設值)
- Utc ：表示的時間為 UTC。 (=1)
- Local ：表示的時間為本地時間。 (=2)
```c#
DateTime t1 = new DateTime();
Console.WriteLine(t1.Kind);         //Unspecified

DateTime t2 = DateTime.Now;
Console.WriteLine(t2.Kind);         //Local

DateTime t3 = DateTime.UtcNow;
Console.WriteLine(t3.Kind);         //Utc

DateTime t4 = DateTime.SpecifyKind(t3, DateTimeKind.Local);
Console.WriteLine(t4.Kind);         //Local
```
- 2) [Calendar](http://msdn.microsoft.com/zh-tw/library/system.globalization.calendar.aspx) ：這個類別用來表示 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 的曆法  
不同的曆法，除時間劃分的單位有所不同外，潤年的定義和天數也都不盡相同。

## 本地時間和世界標準時間的轉換

下面二個方法可以用來進行[**本地時間**]和[**標準時間**]的轉換。

- **[ToLocalTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.tolocaltime.aspx)** ：將目前 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 物件的值轉換成本地時間。  
當使用 [ToLocalTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.tolocaltime.aspx) 方法的時候，它為根據參考物件的 [Kind](http://msdn.microsoft.com/zh-tw/library/system.datetime.kind.aspx)屬性，回傳結果：
- [Kind](http://msdn.microsoft.com/zh-tw/library/system.datetime.kind.aspx) = Utc ：將執行個體時間轉換成 LocalTime 回傳。
- [Kind](http://msdn.microsoft.com/zh-tw/library/system.datetime.kind.aspx) = Local  ：不執行轉換
- [Kind](http://msdn.microsoft.com/zh-tw/library/system.datetime.kind.aspx) = Unspecified ：將 [Kind](http://msdn.microsoft.com/zh-tw/library/system.datetime.kind.aspx) 當成 Utc 來執行轉換。
- **[ToUniversalTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.touniversaltime.aspx)** ：將目前 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 物件的值轉換成 UTC 標準時間。  
當使用 [ToUniversalTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.touniversaltime.aspx) 方法的時候，它為根據參考物件的 [Kind](http://msdn.microsoft.com/zh-tw/library/system.datetime.kind.aspx)屬性，回傳結果：
- [Kind](http://msdn.microsoft.com/zh-tw/library/system.datetime.kind.aspx) = Utc ：不執行轉換。
- [Kind](http://msdn.microsoft.com/zh-tw/library/system.datetime.kind.aspx) = Local  ：將執行個體時間轉換成 UTC 回傳。
- [Kind](http://msdn.microsoft.com/zh-tw/library/system.datetime.kind.aspx) = Unspecified ：將 [Kind](http://msdn.microsoft.com/zh-tw/library/system.datetime.kind.aspx) 當成 Local 來執行轉換。
```c#
DateTime dtime1 = new DateTime(2012, 2, 15, 10, 8, 00);
DateTime dtime2 = dtime1.ToUniversalTime();
DateTime dtime3 = dtime1.ToLocalTime();

DateTime dtime4 = new DateTime(2012, 2, 15, 10, 8, 00, DateTimeKind.Utc);
DateTime dtime5 = new DateTime(2012, 2, 15, 10, 8, 00, DateTimeKind.Local);
DateTime dtime6 = dtime5.ToUniversalTime();

Console.WriteLine("{1} [{0}]", dtime1.Kind, dtime1.ToString());     // 2012/2/15 上午 10:08:00 [Unspecified]
Console.WriteLine("{1} [{0}]", dtime2.Kind, dtime2.ToString());     // 2012/2/15 上午 02:08:00 [Utc]
Console.WriteLine("{1} [{0}]", dtime3.Kind, dtime3.ToString());     // 2012/2/15 下午 06:08:00 [Local]

Console.WriteLine("{1} [{0}]", dtime4.Kind, dtime4.ToString());     // 2012/2/15 上午 10:08:00 [Utc]
Console.WriteLine("{1} [{0}]", dtime5.Kind, dtime5.ToString());     // 2012/2/15 上午 10:08:00 [Local]
Console.WriteLine("{1} [{0}]", dtime6.Kind, dtime6.ToString());     // 2012/2/15 上午 02:08:00 [Utc]

int i = DateTime.Compare(dtime4, dtime5);                   // return 0 , 雖然表示不同區的時間,但時間值是相等的
```

詳細轉換資訊，可參考 MSDN ：[在各時區間轉換時間](http://msdn.microsoft.com/zh-tw/library/bb397769.aspx)

## 不同曆法與日期時間的關係

#### Calendar 類別

這個類別用來表示某個曆法的時間，每種曆法各有其獨特的開始日期，每種曆法對月份的天數也不盡相同。  

每一 [CultureInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.aspx) 都支援一套曆法。 不同的曆法分別由不同的 [Calendar](http://msdn.microsoft.com/zh-tw/library/system.globalization.calendar.aspx) 類別實作，你可以直接由 [CultureInfo.Calendar](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.calendar.aspx) 屬性取得目前系統文化特性的曆法  

以[中華民國曆法 (Taiwan Calendar)](http://msdn.microsoft.com/zh-tw/library/system.globalization.taiwancalendar.aspx)而言，開始於 1911/1/1 ，其餘就和西元曆法相同，較無特別之處。  
[回曆 (Hijri Calendar)](http://msdn.microsoft.com/zh-tw/library/system.globalization.hijricalendar.aspx)則比較特別，每月只有 29 或 30 天，一年有 354 天，閏年有 355 天。  

### 西曆 (Gregorian Calendar)

一般生活中講的西曆包含兩個紀元：西元前(B.C.) 及 西元後(A.D.) 。  但是 .NET 中實作西曆的 [GregorianCalendar](http://msdn.microsoft.com/zh-tw/library/system.globalization.gregoriancalendar.aspx) 類別，它只能識別西元後 (A.D)。

### 台灣曆 (Taiwan Calendar)
```c#
DateTime date;

date = new DateTime(2012, 2, 15, 10, 8, 0);
Console.WriteLine(date.ToString());                      // 2012/2/15 上午 10:08:00

Calendar calendar = new PersianCalendar();
date = new DateTime(2012, 2, 15, 10, 8, 0, calendar);    // 波斯曆
Console.WriteLine(date.ToString());                      // 2633/5/5 上午 10:08:00
Console.WriteLine(calendar.GetDayOfYear(date));          // DayOfYear = 46

calendar = new HijriCalendar();
date = new DateTime(2012, 2, 15, 10, 8, 0, calendar);    // 回曆
Console.WriteLine(date.ToString());                      // 2573/10/12 上午 10:08:00
Console.WriteLine(calendar.GetDayOfYear(date));          // DayOfYear = 45

calendar = new TaiwanCalendar();
date = new DateTime(101, 9, 3, 10, 8, 0, calendar);      // 台灣曆
Console.WriteLine(date.ToString());                      // 2012/9/3 上午 10:08:00
Console.WriteLine(calendar.GetDayOfYear(date));          // DayOfYear = 247

DateTime date1 = new DateTime(101, 2, 15, 10, 8, 0);             
DateTime date2 = new DateTime(101, 2, 15, 10, 8, 0, calendar);   

Console.WriteLine(date1.ToString());                    // 0101/2/15 上午 10:08:00
Console.WriteLine(date2.ToString());                    // 2012/2/15 上午 10:08:00
Console.WriteLine(DateTime.Compare(date1, date2));      // -1

CultureInfo culture = new CultureInfo("zh-TW");         // 指定台灣曆法的文化特性
culture.DateTimeFormat.Calendar = new System.Globalization.TaiwanCalendar();    
```

### 陰曆 (Lunisolar Calendar)

因為陰曆一年可能有 13 個月，所以下面例子中的 101/9/15 ，並不能說成 9 月 15 ，正確應該說成第 9 個月的第 15 天  因為陰曆 101 年，有潤四月，所以陰曆中的 101/9/15 相當於該年的中秋節，也就是西元 2012/9/30  
```c#
calendar = new TaiwanLunisolarCalendar();
date = new DateTime(101, 9, 15, 10, 8, 0, calendar);     // 陰曆 101/9/15
Console.WriteLine(date.ToString());                      // 等於西元 2012/9/30
Console.WriteLine(calendar.GetDayOfYear(date));          // 該陰曆年度的第 252 天

//陰曆所支援的最小與最大日期
Console.WriteLine(calendar.MinSupportedDateTime.ToString());    //1912/2/18 上午 12:00:00
Console.WriteLine(calendar.MaxSupportedDateTime.ToString());    //2051/2/10 下午 11:59:59
```

更詳細的陰曆介紹，請參考 MSDN：[TaiwanLunisolarCalendar 類別](http://msdn.microsoft.com/zh-tw/library/system.globalization.taiwanlunisolarcalendar.aspx)。  不過，CultureInfo 類別支援的任何文化特性都沒有使用 TaiwanLunisolarCalendar 。所以，這個類別只可以用來計算台灣陰曆的日期。

## DateTime 的運算

更多內容請看[MSDN : DateTime 方法](http://msdn.microsoft.com/zh-tw/library/System.DateTime_methods.aspx)
```c#
--時間運算
public DateTime Add(TimeSpan value);
public DateTime AddYears(int value);
public DateTime AddMonths(int months);
public DateTime AddDays(double value);
public DateTime AddHours(double value);
public DateTime AddMinutes(double value);
public DateTime AddSeconds(double value);

--時間比較
public static int Compare(DateTime t1, DateTime t2);
public int CompareTo(DateTime value);
public static bool Equals(DateTime t1, DateTime t2);
public bool Equals(DateTime value);
```
```c#
DateTime t1 = DateTime.Parse("2012/2/15 08:30:00");
DateTime t2 = DateTime.Parse("2012/2/16 09:30:00");

int r1 = DateTime.Compare(t1, t2);      // if t1 < t2 return -1
int r2 = t1.CompareTo(t2);              // if t1 < t2 return -1
```
```c#
TimeSpan tsDiff = t2 - t1;
DateTime t3 = t2.Add(tsDiff);           //2012/2/17 AM 10:30:00
```
```c#
DateTime t1 = DateTime.Parse("2012/2/15 08:30:00");
DateTime t2 = DateTime.Parse("2012/2/15 09:30:00");
DateTime t3 = DateTime.Parse("2012/2/16 07:30:00");
DateTime t4 = DateTime.Parse("2012/2/16 09:30:00");

int days1 = (t2 - t1).Days;         //0
int days2 = (t3 - t1).Days;         //0
int days3 = (t4 - t1).Days;         //1

int days4 = (t1 - t2).Days;         //0
int days5 = (t1 - t3).Days;         //0
int days6 = (t1 - t4).Days;         //-1

DateTime t5 = DateTime.Parse("2012/2/15 23:30:00");
DateTime t6 = DateTime.Parse("2012/2/16 10:30:00");

int days7 = (t6 - t5).Days;             //0
int days8 = (t6.Date - t5.Date).Days;   //1   只比較日期部份, 不看時間
```

t1.CompareTo(t2)

- if (t1&lt;t2) return (-1)
- if (t1=t2) return (0)
- if (t1&gt;t2) return (1)

# Ticks & TimeSpan

## Ticks

[Ticks](http://msdn.microsoft.com/zh-tw/library/system.datetime.ticks.aspx) 是用來表示 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 執行個體日期和時間的刻度 (Tick) 數目。  每個刻度等於 100 奈秒(ns)。  Ticks 值的計算是由 0001/1/1 12:00:00 AM 開始累計，每 100 奈秒，刻度值加一。  

- 100 ns        =                 1 tick
- 1 ms          =            10,000 tick
- 1 second      =        10,000,000 tick
- 1 millisecond =         1 / 1,000 second (ms , 千分之一秒, milli second)
- 1 micorsecond =     1 / 1,000,000 second (µs , 百萬分之一秒, millionth)
- 1 nanosecond  = 1 / 1,000,000,000 second (ns , 十億分之一秒, billionth)
```c#
DateTime dt1 = new DateTime(2012, 2, 15, 10, 8, 20, 999);
DateTime dt2 = new DateTime(2012, 2, 15, 10, 8, 21, 999);

long tick1 = dt1.Ticks;                 //634648973009990000
long tick2 = dt2.Ticks;                 //634648973019990000

long diff = tick2 - tick1;              //10000000

DateTime dt3 = new DateTime(diff);
Console.WriteLine(dt3.ToString());      // 0001/1/1 上午 12:00:01
```

## TimeSpan

[TimeSpan](http://msdn.microsoft.com/zh-tw/library/system.timespan.aspx)這個結構型別，是用來表示時間的間隔。底下是它常用的屬性：
- Days：取得二時間的天數間隔
- Hours：取得二時間的時數間隔
- Minutes：取得二時間的分數間隔
- Seconds：取得二時間的秒數間隔
- Milliseconds：取得二時間的毫秒數間隔

以上是取得同一單位的差距，以下則是取二個時間差距總單位數。

- TotalHours：取得二時間間隔的總時數
- TotalMinutes：取得二時間間隔的總分數
- TotalSeconds：取得二時間間隔的總毫秒數
```c#
DateTime dt4 = new DateTime(2012, 2, 15, 10, 8, 20, 999);
DateTime dt5 = new DateTime(2012, 2, 16, 10, 18, 30, 999);
TimeSpan timespan = dt5 - dt4;
Console.WriteLine("TimeSpan={0}, Days={1}, Hours={2}, Minutes={3}, Seconds={4}, Milliseconds={5}",
timespan.ToString(), timespan.Days, timespan.Hours, timespan.Minutes, timespan.Seconds, timespan.Milliseconds);
//TimeSpan=1.00:10:10, Days=1 Hours=0 Minutes=10 Seconds=10, Milliseconds=0
```

# DateTime to String

## 標準日期和時間格式字串

下表是 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 用來將日期時間型別轉換成字串型別的方法，其中 format 參數必須為 [DateTimeFormatInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.aspx) 所定義的格式規範字元之一：
```c#
public string ToString(IFormatProvider provider);
public string ToString(string format);
public override string ToString();
public string ToString(string format, IFormatProvider provider);

public string ToLongDateString();       // = dtime.ToString("D")
public string ToShortDateString();      // = dtime.ToString("d")
public string ToLongTimeString();       // = dtime.ToString("T")
public string ToShortTimeString();      // = dtime.ToString("t")
```
```c#
DateTime dtime = new DateTime(2012, 2, 15, 10, 8, 0, 789);

Console.WriteLine(dtime.ToString());                                            //2012/2/15 上午 10:08:00 (預設以 短日期 長日期 格式)
Console.WriteLine(dtime.ToUniversalTime().ToString());                          //2012/2/15 上午 02:08:00 (轉 UTC 時間再顯示)

Console.WriteLine(dtime.ToLongDateString());                                    //2012年2月15日
Console.WriteLine(dtime.ToShortDateString());                                   //2012/2/15   
Console.WriteLine(dtime.ToLongTimeString());                                    //上午 10:08:00
Console.WriteLine(dtime.ToShortTimeString());                                   //上午 10:08   

Console.WriteLine(dtime.ToString("D")); // 2012年2月15日                       (長日期 格式) = ToLongDateString
Console.WriteLine(dtime.ToString("d")); // 2012/2/15                          (短日期 格式) = ToShortDateString

Console.WriteLine(dtime.ToString("T")); // 上午 10:08:00                       (長時間 格式) = ToLongTimeString
Console.WriteLine(dtime.ToString("t")); // 上午 10:08                          (短時間 格式) = ToShortTimeString

Console.WriteLine(dtime.ToString("F")); // 2012年2月15日 上午 10:08:00          (完整日期時間 格式)
Console.WriteLine(dtime.ToString("f")); // 2012年2月15日 上午 10:08             (長日期 短時間 格式)

Console.WriteLine(dtime.ToString("G")); // 2012/2/15 上午 10:08:00             (短日期 長時間 格式)
Console.WriteLine(dtime.ToString("g")); // 2012/2/15 上午 10:08                (短日期 短時間 格式)

Console.WriteLine(dtime.ToString("M")); // 2月15日                             (月日 格式)
Console.WriteLine(dtime.ToString("m")); // 2月15日                             (月日 格式)

Console.WriteLine(dtime.ToString("U")); // 2012年2月15日 上午 02:08:00          (先轉UTC時間,再以完整日期時間 格式顯示)
Console.WriteLine(dtime.ToString("u")); // 2012-02-15 10:08:00Z                (先轉UTC時間,再以國際可排序 格式顯示,不因文化特性而異)

Console.WriteLine(dtime.ToString("O")); // 2012-02-15T10:08:00.7890000         (含 時區 格式,不因文化特性而異)
Console.WriteLine(dtime.ToString("o")); // 2012-02-15T10:08:00.7890000         (含 時區 格式,不因文化特性而異)

Console.WriteLine(dtime.ToString("Y")); // 2012年2月                            (年月 格式) 
Console.WriteLine(dtime.ToString("y")); // 2012年2月                            (年月 格式)

Console.WriteLine(dtime.ToString("yyyy/MM/dd HH:mm:ss.ffff"));       // 2012/02/05 10:08:00.7890
```

## 特定文化特性的日期和時間格式字串

在 [CultureInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.aspx) 物件中有個 [DateTimeFormat](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.datetimeformat.aspx) 屬性，它定義了 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 相關的顯示格式。  所以使用不同的文化特性來表示日期時間會有不同的顯示格式。下表是它所定義的相關格式：  

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
```c#
DateTime tmpDate = new DateTime(2012, 2, 15, 10, 8, 0, 789);

// 以 default culture 顯示日期
Console.WriteLine(tmpDate.ToString());                                  // 2012/2/15 上午 10:08:00

// 以 English-Great Britain culture 顯示日期
Console.WriteLine(tmpDate.ToString(new CultureInfo("en-GB")));          // 15/02/2012 10:08:00
Console.WriteLine(tmpDate.ToString("F", new CultureInfo("en-GB")));     // 15 February 2012 10:08:00

// 以 Russian culture 顯示日期
Console.WriteLine(tmpDate.ToString(new CultureInfo("ru-RU")));          // 15.02.2012 10:08:00
Console.WriteLine(tmpDate.ToString("F", new CultureInfo("ru-RU")));     // 15 февраля 2012 г. 10:08:00

// 以 Japan culture 顯示日期
Console.WriteLine(tmpDate.ToString(new CultureInfo("ja-JP")));          // 2012/02/15 10:08:00
Console.WriteLine(tmpDate.ToString("F", new CultureInfo("ja-JP")));     // 2012年2月15日 10:08:00

// 以 不分文化特性 顯示日期
Console.WriteLine(tmpDate.ToString(CultureInfo.InvariantCulture));      // 02/15/2012 10:08:00
Console.WriteLine(tmpDate.ToString("F", CultureInfo.InvariantCulture)); // Wednesday, 15 February 2012 10:08:00

// 使用 zh-TW Culture，並搭配 TaiwanCalendar 顯示日期
CultureInfo culture = new CultureInfo("zh-TW");  
culture.DateTimeFormat.Calendar = new System.Globalization.TaiwanCalendar();
Console.WriteLine(tmpDate.ToString(culture));                           // 101/2/15 上午 10:08:00
```

# String to DateTime

- [Parse](http://msdn.microsoft.com/zh-tw/library/system.datetime.parse.aspx)          ：將指定的日期時間字串，轉換成相對應的 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 型別。若轉換失敗會產生 FormatException 。
- [TryParse](http://msdn.microsoft.com/zh-tw/library/system.datetime.tryparse.aspx)       ：將指定的日期時間字串，轉換成相對應的 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 型別，回傳值表示轉換是否成功。
- [ParseExact](http://msdn.microsoft.com/zh-tw/library/system.datetime.parseexact.aspx)     ：將指定的日期時間字串，轉換成相對應的 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 型別，字串表示的格式必須完全符合指定的格式，否則會擲回例外狀況。
- [TryParseExact](http://msdn.microsoft.com/zh-tw/library/system.datetime.tryparseexact.aspx)  ：將指定的日期時間字串，轉換成相對應的 [DateTime](http://msdn.microsoft.com/zh-tw/library/system.datetime.aspx) 型別，字串表示的格式必須完全符合指定的格式，回傳值表示轉換是否成功。

### Parse
```c#
public static DateTime Parse(string s);
public static DateTime Parse(string s, IFormatProvider provider);
public static DateTime Parse(string s, IFormatProvider provider, DateTimeStyles styles);
```

[Parse](http://msdn.microsoft.com/zh-tw/library/system.datetime.parse.aspx) 這個方法會嘗試完整地剖析日期時間字串，它會盡可能忽略無法辨認的資料，以避免擲回 FormatException。
```c#
DateTime dtime;

dtime = DateTime.Parse("2012/2/15 10:08:00");
Console.WriteLine(dtime.ToString());            // 2012/2/15 上午 10:08:00

dtime = DateTime.Parse("2012/2/15");
Console.WriteLine(dtime.ToString());            // 2012/2/15 上午 12:00:00

dtime = DateTime.Parse("10:08:00");
Console.WriteLine(dtime.ToString());            // 2012/8/21 上午 10:08:00

dtime = DateTime.Parse("2012-2-15T10:08:00.0000000Z");      // 表示此為 UTC 時間
Console.WriteLine(dtime.ToString());                        // 2012/2/15 下午 06:08:00

dtime = DateTime.Parse("2012-2-15T10:08:00.0000000-01:00"); // 比 UTC 時間早一小時的時區時間
Console.WriteLine(dtime.ToString());                        // 2012/2/15 下午 07:08:00

dtime = DateTime.Parse("2012-2-15T10:08:00.0000000+01:00"); // 比 UTC 時間晚一小時的時區時間
Console.WriteLine(dtime.ToString());                        // 2012/2/15 下午 05:08:00

dtime = DateTime.Parse("Wednesday, 15 February 2012 10:08:00 GMT");　 //指定 GMT 時區
Console.WriteLine(dtime.ToString());                        // 2012/2/15 下午 06:08:00
```

### TryParse
```c#
public static bool TryParse(string s, out DateTime result);
public static bool TryParse(string s, IFormatProvider provider, DateTimeStyles styles, out DateTime result);
```

- [TryParse](http://msdn.microsoft.com/zh-tw/library/system.datetime.tryparse.aspx) 方法與 [Parse](http://msdn.microsoft.com/zh-tw/library/system.datetime.parse.aspx) 方法類似，但有一點除外，就是 [TryParse](http://msdn.microsoft.com/zh-tw/library/system.datetime.tryparse.aspx) 方法在轉換失敗時不會擲回例外狀況。
- [TryParse](http://msdn.microsoft.com/zh-tw/library/system.datetime.tryparse.aspx) 也是使用目前 [DateTimeFormatInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.datetimeformatinfo.aspx) 物件中的格式化資訊進行剖析。
- [TryParse](http://msdn.microsoft.com/zh-tw/library/system.datetime.tryparse.aspx) 預設會以目前文化特性的格式進行剖析，若要使用不同文化特性，可使用指定文化特性參數的多載方法。
- 如果字串不包含時區資訊，則剖析回傳值的 [Kind](http://msdn.microsoft.com/zh-tw/library/system.datetime.kind.aspx) 屬性便會是 DateTimeKind.Unspecified ，若包含時區資訊，則 [Kind](http://msdn.microsoft.com/zh-tw/library/system.datetime.kind.aspx) 屬性便會是 DateTimeKind.Local 。
```c#
string tmp = "2012-22-15T10:08:08.0000000";
DateTime theDate;
if (DateTime.TryParse(tmp, out theDate))
{
Console.WriteLine("{0} [{1}]", theDate.ToString(), theDate.Kind);   // 2012/2/15 上午 10:08:08 [Unspecified]
}
else
{
Console.WriteLine("{0} [{1}]", theDate.ToString(), theDate.Kind);   // 0001/1/1 上午 12:00:00 [Unspecified]
}

tmp = "2012-2-15T10:08:08.0000000-08:00";
if (DateTime.TryParse(tmp, out theDate))
{
Console.WriteLine("{0} [{1}]", theDate.ToString(), theDate.Kind);   // 2012/2/16 上午 02:08:08 [Local]
}
tmp = "2012-2-15 10:08:08 GMT";
if (DateTime.TryParse(tmp, out theDate))
{
Console.WriteLine("{0} [{1}]", theDate.ToString(), theDate.Kind);   // 2012/2/15 下午 06:08:08 [Local]
}
```

### ParseExact
```c#
public static DateTime ParseExact(string s, string format, IFormatProvider provider);
public static DateTime ParseExact(string s, string format, IFormatProvider provider, DateTimeStyles style);
public static DateTime ParseExact(string s, string[] formats, IFormatProvider provider, DateTimeStyles style);
```

- 使用 [ParseExact](http://msdn.microsoft.com/zh-tw/library/system.datetime.parseexact.aspx) 這個方法，必須指定一個 format 參數以指定格式，可以進行剖析日期時間字串。
- 如果 format 是不包含日期或時間分隔符號 (例如 "yyyyMMdd HHmm") 的自訂格式模式，請在 provider 參數中使用不因文化特性而異的格式。
- 如果 provider 為 null，則會使用對應到目前文化特性的 [CultureInfo](http://msdn.microsoft.com/zh-tw/library/system.globalization.cultureinfo.aspx) 物件。
- 在 .NET Framework 4 中，如果要剖析的字串包含不在協議中的小時元件和 AM/PM 指示項，則 [ParseExact](http://msdn.microsoft.com/zh-tw/library/system.datetime.parseexact.aspx) 方法會擲回 FormatException。在 .NET Framework 3.5 和舊版中，會忽略 AM/PM 指示項。
```c#
DateTime dtime;
CultureInfo provider = CultureInfo.InvariantCulture;

dtime = DateTime.ParseExact("2012-02-15T10:08:00.7890000", "o", provider);
Console.WriteLine(dtime.ToString());                        // 2012/2/15 上午 10:08:00

dtime = DateTime.ParseExact("2012/2/15", "d", CultureInfo.CurrentCulture);
Console.WriteLine(dtime.ToString());                        // 2012/2/15 上午 12:00:00

dtime = DateTime.ParseExact("20120215100800", "yyyyMMddHHmmss", provider);
Console.WriteLine(dtime.ToString());                        // 2012/2/15 上午 10:08:00

provider = new CultureInfo("zh-TW");
dtime = DateTime.ParseExact("2012/2/15 上午 10:08", "g", provider);
Console.WriteLine(dtime.ToString());                        // 2012/2/15 上午 10:08:00

provider = new CultureInfo("fr-FR");
dtime = DateTime.ParseExact("15/02/2012 10:08", "g", provider);
Console.WriteLine(dtime.ToString());                        // 2012/2/15 上午 10:08:00
```

下面這個例子，使用 DateTimeStyles.AssumeLocal 表示字串為當地時間格式。
```c#
string source = "20120215100800";
string format = "yyyyMMddHHmmss";

DateTime dtime = DateTime.ParseExact(source, format, null);
Console.WriteLine(dtime.ToString());            // 2012/2/15 上午 10:08:00

// 系統改成台灣曆法

CultureInfo twCulture = new CultureInfo("zh-TW");
twCulture.DateTimeFormat.Calendar = new System.Globalization.TaiwanCalendar();        // 指定台灣曆法

dtime = DateTime.ParseExact(source, format, twCulture, DateTimeStyles.AssumeLocal);
Console.WriteLine(dtime.ToString());            // 3923/2/15 上午 10:08:00

source = "01010215100800";
dtime = DateTime.ParseExact(source, format, twCulture, DateTimeStyles.AssumeLocal);
Console.WriteLine(dtime.ToString());            // 2012/2/15 上午 10:08:00
```

### TryParseExact
```c#
public static bool TryParseExact(string s, string format, IFormatProvider provider, DateTimeStyles style, out DateTime result);
public static bool TryParseExact(string s, string[] formats, IFormatProvider provider, DateTimeStyles style, out DateTime result);
```
```c#
DateTime theDate;
string source = "";
CultureInfo provider = CultureInfo.InvariantCulture;

source = "2012-02-15T10:08:08.0000000";
if (DateTime.TryParseExact(source, "o", provider, DateTimeStyles.None, out theDate))
{
Console.WriteLine("{0} [{1}]", theDate.ToString(), theDate.Kind);   // 2012/2/15 上午 10:08:08 [Unspecified]
}
else
{
Console.WriteLine("{0} [{1}]", theDate.ToString(), theDate.Kind);   // 0001/1/1 上午 12:00:00 [Unspecified]
}

source = "2012/2/15";
provider = new CultureInfo("zh-TW");
if (DateTime.TryParseExact(source, "d", provider, DateTimeStyles.None, out theDate))
{
Console.WriteLine("{0} [{1}]", theDate.ToString(), theDate.Kind);   // 2012/2/15 上午 12:00:00 [Unspecified]
}

source = "2012/2/15 上午 10:08";
provider = new CultureInfo("zh-TW");
if (DateTime.TryParseExact(source, "g", provider, DateTimeStyles.None, out theDate))
{
Console.WriteLine("{0} [{1}]", theDate.ToString(), theDate.Kind);   // 2012/2/15 上午 10:08:00 [Unspecified]
}

source = "15/02/2012 10:08";
provider = new CultureInfo("fr-FR");
if (DateTime.TryParseExact(source, "g", provider, DateTimeStyles.None, out theDate))
{
Console.WriteLine("{0} [{1}]", theDate.ToString(), theDate.Kind);   // 2012/2/15 上午 10:08:00 [Unspecified]
}
```