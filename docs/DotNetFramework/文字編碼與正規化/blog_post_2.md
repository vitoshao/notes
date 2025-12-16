---
title: 正規表示式
layout: default
parent: 文字編碼與正規化
nav_order: 2
description: "正規表示式"
date: 2012-01-16
tags: [DotNetFramework,文字編碼與正規化]
postid: "9171946574899641201"
---
文字處理是很頻繁的例行工作，[Regular Expression](http://zh.wikipedia.org/wiki/Regex) 正是一個複雜，但有效率的文字處理技術。  
RegExp簡單的觀念就是先建立一組 pattern 字串， 再用這組字串去和資料做筆對。  
例如判斷字串中是否含有"abc"，或者含有email，或者含有身份證格式的文字....  

# 如何使用 RegExpression 進行比對

命名空間: [System.Text.RegularExpressions](http://msdn.microsoft.com/zh-tw/library/c75he57e.aspx)

#### 幾個主要類別與方法：

- [Regex 類別](http://msdn.microsoft.com/zh-tw/library/6f7hht7k.aspx)：Represents an immutable regular expression.
- [Regex.IsMatch](http://msdn.microsoft.com/zh-tw/library/s19z3syh.aspx) ：在輸入的字串中，規則運算式是否有比對到符合的資料。
- [Regex.Match](http://msdn.microsoft.com/zh-tw/library/3zy662f6.aspx) ：在輸入字串中，搜尋符合規則運算式的項目，並且將第一個相符項目當做單一 [Match](http://msdn.microsoft.com/zh-tw/library/system.text.regularexpressions.match.aspx) 物件傳回。
- [Regex.Matchs](http://msdn.microsoft.com/query/dev10.query?appId=Dev10IDEF1&l=ZH-TW&k=k%28SYSTEM.TEXT.REGULAREXPRESSIONS.REGEX.MATCHES%29;k%28TargetFrameworkMoniker-%22.NETFRAMEWORK%2cVERSION%3dV4.0%22%29;k%28DevLang-CSHARP%29&rd=true) ：在輸入字串中，搜尋規則運算式的所有相符項目，並傳回所有成功的相符項目。
- [Regex.Replace](http://msdn.microsoft.com/zh-tw/library/xwewhkd1.aspx) ：在指定的輸入字串內，使用指定的取代字串來取代符合規則運算式模式的所有字串。
- [Match 類別](http://msdn.microsoft.com/zh-tw/library/system.text.regularexpressions.match.aspx)：表示單一規則運算式 (Regular Expression) 比對的結果。
- [Match.Groups](http://msdn.microsoft.com/zh-tw/library/system.text.regularexpressions.match.groups.aspx) ：取得符合規則運算式的群組集合。
- [Group 類別](http://msdn.microsoft.com/zh-tw/library/system.text.regularexpressions.group.aspx) ：表示單一擷取群組的結果。
- [MatchCollection 類別](http://msdn.microsoft.com/zh-tw/library/system.text.regularexpressions.matchcollection.aspx) ：所有找到的成功比對的集合。

#### 如何比對簡單文字

下面這個例子會對 data 進行比對，判斷 data 是否由5個數字組成
```c#
string pattern = "^\d{5}$";     //由５個數字組成，該５個數字是開頭，該５個數字是結尾
string data = "12335";

if (Regex.IsMatch(data,pattern))
Console.WriteLine("It is matched.");
else
Console.WriteLine("It is NOT matched");
```

| ![](http://127.0.0.1:8080/_images/cs/reg_1.jpg) | 「^」：次方符號表示字串開始  &lt;br&gt;      "\d" ：表示數字字元  &lt;br&gt;      "{5}"：表示前面規則要連續五個  &lt;br&gt;      「$」：表示字串結尾 |
| --- | --- |

上例的一些小變化

| pattern | data | result |
| --- | --- | --- |
| ^\d{5}$ | 12345 | match |
| ^\d{5}$ | abcd12345 | no match |
| ^\d{5}$ | 12345abcd12345 | no match |
| ^\d{5}.\*\d{5}$ | 12345abcd12345 | match |
| \d{5}$ | abcd12345 | match |
| \d{5}$ | drop custom 12345 | match |

# 規則運算式的特定字元

下方列表是用來定義規則運算式的特定字元

#### **與位置相關的比對**，詳細資訊請參閱MSDN[\[錨點\]](http://msdn.microsoft.com/zh-tw/library/h5181w5w.aspx)

| 記號 | 說明 | 模式 | 符合項目 |
| --- | --- | --- | --- |
| ^ | 必須發生在字串開頭 | ^\d{3} | "901-333-" 中的 "901-" |
| $ | 必須發生在字串結尾 | -\d{3}$ | "-901-333" 中的 "-333" |
| \A | 比對必須發生在字串開頭(忽略多行)。 | \A\d{3 | "-901-333" 中的 "901" |
| \Z | 比對必須發生在字串結尾(忽略多行) | -\d{3}\Z | "-901-333" 中的 "-333" |
| \z | 比對必須發生在字串結尾(忽略多行) | -\d{3}\z | "-901-333" 中的 "-333" |
| \G | 比對必須發生在先前比對結束的位置。 | \G\(\d\) | "(1)(3)(5)[7](9)" 中的 "(1)"、"(3)"、"(5)" |
| \b | 比對必須發生在 \w (英數) 和 \W (非英數) 字元之間的界限上。 | \b\w+\s\w+\b | "them theme them them" 中的 "them them" |
| \B | 比對不可以發生在 \b 界限上。 | \Bend\w\*\b | "end sends endure lender" 中的 "ends"、"ender" |

#### **與文字相關的比對**，詳細資訊請參閱MSDN[\[字元類別\]](http://msdn.microsoft.com/zh-tw/library/20bw873z.aspx)

| 記號 | 說明 | 模式 | 符合項目 |
| --- | --- | --- | --- |
| . | 萬用字元 |  | 代表除了換行符號 (\n) 以外的任一字元。如果要包括換行符號，請使用 [\s\S] |
| x|y | 代表「或」 |  | 例如 (Sun|Mon|Tue|Wed|Thu|Fri|Sat)， (日|一|二|三|四|五|六) ; 必須以左右括號括住 |
| [xyz] | 比對[]中的任何單一字元。 | [abc] | "gray" 中的 "a"  &lt;br&gt;"lane" 中的 "a"、"e" |
| [a-z] | 比對[]中的字元範圍 | [A-Z] | "AB123" 中的 "A"、"B" |
| [^xyz} | 否定字元 | [^aei] | "reign" 中的 "r"、"g"、"n" |
| \p{name} | 符合 name 所指定[類別名稱](http://msdn.microsoft.com/zh-tw/library/20bw873z.aspx#SupportedUnicodeGeneralCategories)或[區塊名稱](http://msdn.microsoft.com/zh-tw/library/20bw873z.aspx#SupportedNamedBlocks)之所有字元 | \p{Lu}  &lt;br&gt;\p{IsCyrillic} | "City Lights" 中的 "C"、"L"（找大寫類別文字）  &lt;br&gt;"ДЖem" 中的 "Д"、"Ж"（找IsCyrillic區塊文字） |
| \P{name} | 不符合 name 所指定[類別名稱](http://msdn.microsoft.com/zh-tw/library/20bw873z.aspx#SupportedUnicodeGeneralCategories)或[區塊名稱](http://msdn.microsoft.com/zh-tw/library/20bw873z.aspx#SupportedNamedBlocks)之所有字元 | \P{Lu}  &lt;br&gt;\P{IsCyrillic} | "City" 中的 "i"、"t"、"y"（找非大寫類別文字）  &lt;br&gt;"ДЖem" 中的 "e"、"m"（找非IsCyrillic區塊文字） |
| \w | 比對任何文字字元，  &lt;br&gt;      相等於[A-Za-z0-9\_]。 | \w | "ID A1.3" 中的 "I"、"D"、"A"、"1"、"3" |
| \W | 比對任何非文字字元，  &lt;br&gt;      相等於[^A-Za-z0-9\_]。 | \W | "ID A1.3" 中的 " "、"." |
| \s | 比對任何泛空白字元。(包含空白、Tab、換頁符號)，  &lt;br&gt;      相等於[\f\n\r\t\v]。 | \w\s | "ID A1.3" 中的 "D " |
| \S | 比對任何非泛空白字元，  &lt;br&gt;      相等於[^\f\n\r\t\v]。 | \s\S |  |
| \d | 比對任何十進位數字。 | \d |  |
| \D | 比對不是十進位數字的任何字元。 | \D |  |

#### **與文字數量相關的比對**，詳細資訊請參閱MSDN[\[數量詞\]](http://msdn.microsoft.com/zh-tw/library/3206d374.aspx)

| Greedy記號 | Lazy記號 | 說明 | 範例 |
| --- | --- | --- | --- |
| \* | \*? | 零次or多次 | 91\* ：9後面接零或多個1 |
| + | +? | 一次or多次  &lt;br&gt;              + 數量詞會比對前置項目一次或多次。它就相當於 {1,}。 | 比對開頭字母為 a ，接著一個或多個字母 n ，再接文字字元&lt;br&gt;              &lt;br&gt;&lt;br&gt;| pattern | 資料 | 結果 |&lt;br&gt;| --- | --- | --- |&lt;br&gt;| ^an+\w\*? | anxxx | an |&lt;br&gt;| annnn12abcdefg1234 | annnn |&lt;br&gt;| ^an+\w\* | anxxx | anxxx |&lt;br&gt;| annnn12abcdefg1234 | annnn12abcdefg1234 |&lt;br&gt;| ^an+\w+? | anxxx | anx |&lt;br&gt;| annnn12abcdefg1234 | annnn1 |&lt;br&gt;| ^an+\w+ | anxxx | anxxx |&lt;br&gt;| annnn12abcdefg1234 | annnn12abcdefg1234 | |
| ? | ?? | 零次or一次 |
| {n} | {n}? | n是一個非負整數，比對正好n次。 | 比對至少 3 個字組字元，但盡可能減少次數，後接點或句號字元。&lt;br&gt;              &lt;br&gt;&lt;br&gt;| pattern | 資料 | 結果 |&lt;br&gt;| --- | --- | --- |&lt;br&gt;| ^an+\w{3,} | anxxx | anxxx |&lt;br&gt;| annnn12abcdefg1234 | annnn12abcdefg1234 |&lt;br&gt;| ^an+\w{3,}? | anxxx | anxxx |&lt;br&gt;| annnn12abcdefg1234 | annnn12a | |
| {n,} | {n,}? | n是一個非負整數，比對至少出現n次 |
| {n,m} | {n,m}? | m，n均為非負整數，n&lt;=m，比對至少出現n次，至多出現m次 |
| 將 ? 字元附加至限定詞之後，則使用非貪婪式(nongreedy pattern matching)  &lt;br&gt;              也就是找最短的符合結果。 | 例如在字串"oooo"中，  &lt;br&gt;              若 pattern = "o+?" ，則符合的結果是 "o"  &lt;br&gt;              若 pattern = "o+" ，則符合的結果是 "oooo" |

#### **規則運算式的子運算式**，詳細資訊請參閱MSDN[\[群組建構\]](http://msdn.microsoft.com/zh-tw/library/bs2twtah.aspx)

| 記號 | 說明 | 模式 | 符合項目 |
| --- | --- | --- | --- |
| (...) | 擷取符合的子運算式，並指派以零為起始的序號給它。 | (\w)\1 | "deep" 中的 "ee" |
| (?&lt;name&gt;...) | 將符合的子運算式擷取到具名群組中。 | (?&lt;city&gt;.\*$) |  |

# 如何擷取比對資訊

除了比對某個字串是否與pattern相符之外，Regexp也可以把符合的資訊擷取出來。

#### 1.只取得第一個符合的字串 ( Regex.Match )
```c#
string pattern = @"Company Name: (.\*$)";
string data = @"
Company Name: Contoso, Inc.
Company Name: Fis Inc.
Company Name: china time newspaper";

Regex r1 = new Regex(pattern, RegexOptions.Multiline);
Match m1 = r1.Match(data);
Console.WriteLine(m1.Groups[1]);

//======output:======
//Contoso, Inc.
```

#### 2.逐步比對，取出所有符合的字串 ( Match.NextMatch )
```c#
//2.逐步比對，取出所有符合的字串
Regex r2 = new Regex(pattern, RegexOptions.Multiline);
Match m2;
for (m2 = r2.Match(data); m2.Success; m2 = m2.NextMatch())
{
Console.WriteLine("Found '{0}' at position {1}", m2.Groups[1], m2.Groups[1].Index);
}
//======output:======
//Found 'Contoso, Inc.' at position 28
//Found 'Fis Inc.' at position 69
//Found 'china time newspaper' at position 105
```

#### 3.一次比對，取出所有符合的字串 ( Regex.Matchs )
```c#
Regex r3 = new Regex(@"Company Name: (.\*$)", RegexOptions.Multiline);
MatchCollection matches = r2.Matches(inputString);
foreach (Match m3 in matches)
{
Console.WriteLine("Found '{0}' at position {1}", m3.Value, m3.Index);
}
//======output:======
//Found 'Company Name: Contoso, Inc.' at position 14
//Found 'Contoso, Inc.' at position 28
//Found 'Company Name: Fis Inc.' at position 55
//Found 'Fis Inc.' at position 69
//Found 'Company Name: china time newspaper' at position 91
//Found 'china time newspaper' at position 105
```

#### 4.子運算式的比對，使用群組擷取符合的字串 ( Match.Groups )

規則運算式模式可以包含子運算式，子運算式是以括號括住的部分，每個子運算式都形成一個群組。  
例如，pattern ="(\d{3})-(\d{3}-\d{4})" 會比對北美地區的電話號碼，其中包含兩個子運算式。  
第一個子運算式「(\d{3})」代表區碼，第二個子運算式「 (\d{3}-\d{4})」代表電話號碼，  
接著可從 Groups 屬性傳回的 GroupCollection 物件擷取這兩個群組，如下列範例所示。  
```c#
string pattern = @"(\d{3})-(\d{3}-\d{4})";
string input = "212-555-6666 906-932-a111 415-222-3333 425-888-999";
MatchCollection matches = Regex.Matches(input, pattern);

foreach (Match match in matches)
{
Console.WriteLine("Area Code:        {0}", match.Groups[1].Value);
Console.WriteLine("Telephone number: {0}", match.Groups[2].Value);
}
//output:
//match.Groups[0]:  212-555-6666
//Area Code:        212
//Telephone number: 555-6666
//match.Groups[0]:  415-222-3333
//Area Code:        415
//Telephone number: 222-3333
```

#### 5.使用具名群組擷取字串 ( Match.Groups )
```c#
string pattern = @"First Name : (?<firstname>.\*$)\n" + 
@"Last Name : (?<lastname>.\*$)\n" +
@"Age : (?<age>.\*$)\n" +
@"City : (?<city>.\*$)";

string data = @"First Name : Vito
Last Name : Shao
Age : 18
City : Taipei
";

Match m = Regex.Match(data, pattern, RegexOptions.Multiline);
if (m.Success)
{
Console.WriteLine("firstName:{0}", m.Groups["firstname"]);
Console.WriteLine("lastName:{0}", m.Groups["lastname"]);
Console.WriteLine("city:{0}", m.Groups["city"]);
Console.WriteLine("age:{0}", m.Groups["age"]);
}
//======OUTPUT======
//firstName:Vito
//lastName:Shao
//city:Taipei
//age:18
```

# 如何使用 RegExpression 替換子字串

下面例子示範如何利用 [Regex.Replace](http://msdn.microsoft.com/zh-tw/library/xwewhkd1.aspx) 方法  
該運算式會比對一或多個空白字元，將結果以單一空白字元取代。  
```c#
string input = "This is   text with   far  too   much   " + 
"whitespace.";
string pattern = "\\s+";
string replacement = " ";
Regex rgx = new Regex(pattern);
string result = rgx.Replace(input, replacement);

Console.WriteLine("Original String: {0}", input);
Console.WriteLine("Replacement String: {0}", result);    
// The example displays the following output:
//       Original String   ：This is   text with   far  too   much   whitespace.
//       Replacement String：This is text with far too much whitespace.
}```

範例說明

| 用途 | Pattern | 說明 |
| --- | --- | --- |
| 身份證 | [A-Z][12](\d{8}) |  |
|  | ^(?=.\*\d)(?=.\*[a-z])(?=.\*[A-Z]).{6,30}$ | 1aAxxxyyy |
|  |  |  |

![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [MSDN 規則運算式語言項目](http://msdn.microsoft.com/zh-tw/library/az24scfc.aspx)
- [這個網站](http://regexlib.com/?AspxAutoDetectCookieSupport=1)有很多現成的pattern可供參考