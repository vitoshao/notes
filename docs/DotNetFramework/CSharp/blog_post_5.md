---
title: 運算子
layout: default
parent: CSharp
nav_order: 5
description: "運算子"
date: 2013-08-22
tags: [DotNetFramework,CSharp]
postid: "9162639093433724878"
---
# ＜＜ 和 ＞＞運算子

- 左移運算子 (&lt;&lt;)：將第一個運算元向左移動，移動的位元數由第二個運算元所指定。
- 右移運算子 (&gt;&gt;)：將第一個運算元向右移動，移動的位元數由第二個運算元所指定。

#### 用法
```c#
Y = X >> n ;
Y = X << n ;
```

- 若 X 為 int 型別，則 n 值的最大有效值為 31 ，也就是最多只能移位 31 位元
- 若 X 為 long 型別，則 n 值的最大有效值為 63 ，也就是最多只能移位 63 位元

**範例**
```c#
int x = 1;      //1       0000 0000 0001
x = x << 1;     //2       0000 0000 0010
x <<= 1;        //4       0000 0000 0100
x <<= 1;        //8       0000 0000 1000
x <<= 2;        //32      0000 0010 0000
x <<= 3;        //256     0001 0000 0000
```

# ？？運算子

?? 運算子稱為 null 聯合運算子( null-coalescing operator )，而用來判斷左側運算元是否為 null，若是則回右側運算元。 

#### 用法
```c#
// if ( X == null ) Y=-1 else Y=X 

int? X=null;

int Y = X ?? -1;                // y = -1

int? i = X ?? default(int);     // i = 0
```

# h1\_XXXXX

0