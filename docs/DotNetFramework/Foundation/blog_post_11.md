---
title: 陣列
layout: default
parent: Foundation
nav_order: 11
description: "陣列"
date: 2012-05-08
tags: [DotNetFramework,Foundation]
postid: "1884938693921836365"
---
### 陣列宣告

- C# 的陣列索引起始值是0，若宣告一個 n 大小的陣列，表示該陣列含有 n 個元素，其索引值範圍： 0 ~ n-1 。
- VB 的陣列索引起始也是0，若宣告一個 n 大小的陣列，表示該陣列含有 n+1 個元素，其索引值範圍： 0 ~ n 。
```c#
string[] array1 = new string[2];
string[] array2 = { "男", "女" };
string[] array3 = new string[2] { "男", "女" };
string[] array4 = new string[] { "男", "女" };
```
Dim array1(2) As String
Dim array2() As String = {"", "男", "女"}
Dim array3() As String = New String(2) {"", "男", "女"}
Dim array4() As String = New String() {"", "男", "女"}

### 陣列存取
```c#
array1[0] = "男";
array1[1] = "女";
```
array1(0) = ""
array1(1) = "男"
array1(2) = "女"

### 陣列排序
```c#
Array.Sort(欲排序的陣列);
```

PS. 如果陣列中的存放的是自訂型別，必須實作 [IComparable](http://msdn.microsoft.com/zh-tw/library/system.icomparable.aspx) 介面，使用 [Array.Sort](http://msdn.microsoft.com/zh-tw/library/system.array.sort.aspx) 才有意義。

### 陣列搜尋
```c#
Array.BinarySearch(陣列名稱,要找的元素)  //用 Binary Search, 需先 Sort 
Array.IndexOf(陣列名稱,要找的元素)       //用 Linear Search
```
```c#
string[] dinosaurs = {
"People", 
"Apple", 
"Tiger", 
"Monkey", 
"Dog", 
"Elephent"};

int index = Array.BinarySearch(dinosaurs, "Cat");       // -1
index = Array.BinarySearch(dinosaurs, "Tiger");         //  2
index = Array.BinarySearch(dinosaurs, "Apple");         // -1
index = Array.BinarySearch(dinosaurs, "ZOO");           // -7

Array.Sort(dinosaurs);

index = Array.BinarySearch(dinosaurs, "Cat");           // -2
index = Array.BinarySearch(dinosaurs, "Tiger");         //  5
index = Array.BinarySearch(dinosaurs, "Apple");         //  0
index = Array.BinarySearch(dinosaurs, "ZOO");           // -7
```

### 陣列間的型別轉換

例如要將一個string型別陣列裡面的元素轉到int型別陣列，通常會這樣做：
```c#
string[] strArray = new string[] { "1", "2", "3", "4", "5" };
int[] intArray = new int[5];
for (int i = 0; i < strArray.Count(); i++)
{
intArray[i] = Convert.ToInt32(strArray[i]);
}
```

但如果是 .Net Framework 3.0 以上可以這樣做：
```c#
string[] strArray = new string[] { "1", "2", "3", "4", "5" };
int intArray = Array.ConvertAll<string, int>(strArray, int.Parse);
```

### 陣列合併

合併二個陣列，可以使用苦力將二個陣列中的元素，一個個複製到大的陣列。  如果使用 .Net 3.5 以上，則可以使用 System.Linq 的擴充功能 [Union](http://msdn.microsoft.com/zh-tw/library/system.linq.enumerable.union.aspx) 來合併二個陣列。  而且這個方法會自動剔除重複的元素。  
```c#
char[] array1 = { 'x', 'y', 'z' };
char[] array2 = { 'a', 'b', 'y' };

var result = array1.Union(array2).ToArray();

foreach (char item in result)
{
Console.WriteLine(item);  // z, y, z, a, b
}
```