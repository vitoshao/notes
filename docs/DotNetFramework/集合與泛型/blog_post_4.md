---
title: Stack、Queue 集合
layout: default
parent: 集合與泛型
nav_order: 4
description: "Stack、Queue 集合"
date: 2012-01-16
tags: [DotNetFramework,集合與泛型]
postid: "4501654813348604594"
---
在 [ArrayList](http://msdn.microsoft.com/zh-tw/library/system.collections.arraylist) 中，我們可以直接存取集合中任何一個資料項目，但有時候我們並不需要如此功能，只需要可以循序存取集合中的項目即可。  在 .Net 裡，提供了二個循序串列 (sequential list) 物件：[Queue](http://msdn.microsoft.com/zh-tw/library/system.collections.queue.aspx) 與 [Stack](http://msdn.microsoft.com/zh-tw/library/system.collections.stack.aspx) 。  這二個物件的特色是，可以在一個步驟中，由集合中取出資料項目，並同時將該資料項目由集合中移除。  

# Queue

表示先進先出 (FIFO) 集合。

- [Queue.Enqueue](http://msdn.microsoft.com/zh-tw/library/system.collections.queue.enqueue) ：將物件加入 [Queue](http://msdn.microsoft.com/zh-tw/library/system.collections.queue.aspx) 的末端。
- [Queue.Dequeue](http://msdn.microsoft.com/zh-tw/library/system.collections.queue.dequeue) ：移除並傳回在 [Queue](http://msdn.microsoft.com/zh-tw/library/system.collections.queue.aspx) 前端的物件。
- [Queue.Peek](http://msdn.microsoft.com/zh-tw/library/system.collections.queue.peek) ：傳回 [Queue](http://msdn.microsoft.com/zh-tw/library/system.collections.queue.aspx) 前端的物件而不需移除它。
- [Queue.Clear](http://msdn.microsoft.com/zh-tw/library/system.collections.queue.aspx)：從 Queue 移除所有物件。
```c#
//Queue 類別的組成成員
public interface Queue : ICollection, IEnumerable, ICloneable
{  
public virtual int Count { get; }

public virtual Object <strong class="fcOrange">Dequeue</strong>()         //移除並傳回在 Queue 前端的物件。
public virtual void <strong class="fcOrange">Enqueue</strong>(Object obj) //將物件加入 Queue 的末端。
public virtual Object <strong class="fcOrange">Peek</strong>()            //傳回 Queue 前端的物件而不需移除它。
}```
```c#
Queue q = new Queue();
q.Enqueue("First");
q.Enqueue("Second");
q.Enqueue("Third");
q.Enqueue("Fourth");
while (q.Count > 0)
{
Console.WriteLine(q.Dequeue());
}```

若要取出一個item,但不想將其移除,這時候可以利用Peek方法來解決
```c#
if (q.Peek() is String)
{
Console.WriteLine(q.Dequeue());
}```

# Stack

表示後進先出 (LIFO) 集合。

- [Stack.Push](http://msdn.microsoft.com/zh-tw/library/system.collections.stack.push) ：將物件插入 [Stack](http://msdn.microsoft.com/zh-tw/library/system.collections.stack.aspx) 的頂端。
- [Stack.Pop](http://msdn.microsoft.com/zh-tw/library/system.collections.stack.pop) ：移除並傳回在 [Stack](http://msdn.microsoft.com/zh-tw/library/system.collections.stack.aspx) 頂端的物件。
- [Stack.Peek](http://msdn.microsoft.com/zh-tw/library/system.collections.stack.peek) ：傳回 [Stack](http://msdn.microsoft.com/zh-tw/library/system.collections.stack.aspx) 頂端的物件而不需移除它。
- [Stack.Clear](http://msdn.microsoft.com/zh-tw/library/system.collections.stack.aspx)：從 Stack 移除所有物件。
```c#
//Stack 類別的組成成員
public class Stack : ICollection, IEnumerable, ICloneable
{  
public virtual int Count { get; }

public virtual Object <strong class="fcOrange">Pop</strong>()             //移除並傳回在 Stack 頂端的物件。
public virtual void <strong class="fcOrange">Push(Object obj)</strong>    //將物件插入 Stack 的頂端。
public virtual Object <strong class="fcOrange">Peek</strong>()            //傳回 Stack 頂端的物件而不需移除它。
}```
```c#
Stack s = new Stack();
s.Push("First");
s.Push("Second");
s.Push("Third");
s.Push("Fourth");
while (s.Count > 0)
{
Console.WriteLine(s.Pop());
}```