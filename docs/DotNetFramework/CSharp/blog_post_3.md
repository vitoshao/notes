---
title: 使用 foreach 存取集合類別
layout: default
parent: CSharp
nav_order: 3
description: "使用 foreach 存取集合類別"
date: 2012-06-15
tags: [DotNetFramework,CSharp]
postid: "7060510228407220926"
---
若要使用 [foreach](http://msdn.microsoft.com/zh-tw/library/ttw7t8t6.aspx) 陳述式逐一查看集合，則集合必須符合下列需求︰

- 它必須是介面、類別或結構。
- 它必須包括可傳回型別的公用 [GetEnumerator](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable.getenumerator%28v=vs.80%29.aspx) 方法。
- 傳回型別包含名為 [Current](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerator.current%28v=vs.80%29.aspx) 的公用屬性和名為 [MoveNext](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerator.movenext%28v=vs.80%29.aspx) 的公用方法。

其他詳細資訊，請參閱 [HOW TO：使用 foreach 存取集合類別 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/9yb8xew9%28v=vs.80%29.aspx)。 

要自訂一個集合類別，除了直接實作 [IEnumerable](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable.aspx)、[IEnumerator](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerator.aspx) 介面外， 另外，只要該類別具有必要的 [GetEnumerator](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerable.getenumerator.aspx)、[MoveNext](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerator.movenext.aspx)、[Reset](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerator.reset.aspx) 和 [Current](http://msdn.microsoft.com/zh-tw/library/system.collections.ienumerator.current.aspx) 成員，就可以搭配 foreach 使用。 
```c#
public interface IEnumerable
{
IEnumerator GetEnumerator();//傳回會逐一查看集合的列舉程式。
}

public interface IEnumerator
{
// 方法
bool MoveNext();//將列舉值往前推至下集合中的下一個項目。如果成功則返回為 true；如果超過集合結尾，則返回false。
void Reset();//設定列舉值至它的初始位置，這是在集合中第一個元素之前。

// 屬性
object Current { get; }//取得集合中目前的項目。
}
```