---
title: 平行程式設計
layout: default
parent: 執行緒
nav_order: 4
description: "平行程式設計"
date: 2015-08-21
tags: [DotNetFramework,執行緒]
postid: "1710069834524431696"
---
之前曾經介紹過如何使用 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.aspx) 或 ThreadPool 建立執行緒，以達到多個工作同步執行的目的。  並利用「同步處理」的機制，以取得非同步作業的執行結果。  自 .NET 4.0 開始，在 System.Threading.Tasks 命名空間中，已提供幾個相關的 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 類別，可以用來簡化撰寫並行作業的程式碼。  這一組相關的類別庫就稱為「工作平行程式庫」（https://msdn.microsoft.com/zh-tw/library/dd460717%28v=vs.110%29.aspx ,TPL），它簡化了撰寫平行作業的許多細節，讓開發人員可以更專注於程式所應完成的工作。  

- [System.Threading.Tasks.Task](https://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task%28v=vs.100%29.aspx) ：表示非同步作業。
- [System.Threading.Tasks.Task&lt;TResult&gt;](https://msdn.microsoft.com/zh-tw/library/dd321424%28v=vs.100%29.aspx) ：表示含有傳回值的非同步作業。

# Task

## Task 類別 & Task&lt;TResult&gt; 類別

### Task 類別

這個類別用來表示一個無回傳值的非同步作業。  底下幾個是它的建構函式，其中 Action 表示一個沒有回傳值的委派。  
```c#
Task(Action)
Task(Action<Object>, Object)
Task(Action, CancellationToken)
Task(Action, TaskCreationOptions)
Task(Action<Object>, Object, CancellationToken, TaskCreationOptions)
...
```

### Task&lt;TResult&gt; 類別

這個 [Task&lt;TResult&gt;](https://msdn.microsoft.com/zh-tw/library/dd321424%28v=vs.100%29.aspx) 類別表示一個有回傳值的非同步作業。  所以和上面 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 類別，差別只在於其建構函式中傳入的是一個可回傳值的委派。  
```c#
Task<TResult>(Func<TResult>)：
Task<TResult>(Func<Object, TResult>, Object)：
Task<TResult>(Func<TResult>, CancellationToken)：
Task<TResult>(Func<TResult>, TaskCreationOptions)：
Task<TResult>(Func<Object, TResult>, Object, CancellationToken, TaskCreationOptions)：
...
```

#### Task&lt;TResult&gt;(Func&lt;TResult&gt;)

這個建構函式表示：

- 這是一個泛型類別，類別中會使用到一個非特定型別（TResult）。
- 具現化時，必須使用一個無參數的 Func 委派當做參數。
- 委派的執行函式必須回傳（TResult）型別的值。
```c#
private AtomEntry UploadPhoto1()
{
return UploadPhotoAsync(gsAlubmId, @"01.jpg"); //return AtomEntry;
}
private void bnTaskResult_Click(object sender, EventArgs e)
{
// 建立 Task<TResult>(Func<TResult>) 
Task<AtomEntry> task = new Task<AtomEntry>(
delegate ()
{
return UploadPhoto1();
}
);

// 上面程式碼，可簡寫如下：
Task<AtomEntry> task = new Task<AtomEntry>(UploadPhoto1);
}
```

#### Task&lt;TResult&gt;(Func&lt;Object, TResult&gt;, Object)

這個建構函式表示：

- 這是一個泛型類別，類別中會使用到一個非特定型別（TResult）。
- 具現化時，必須使用一個含一個參數的 Func 委派當做參數。
- 委派的執行函式必須傳入一個 Object 型別參數，並回傳（TResult）型別的值。
```c#
private AtomEntry UploadPhoto2(Object filename)
{
return UploadPhotoAsync(gsAlubmId, filename.ToString()); //return AtomEntry;
}
private void bnTaskResult_Click(object sender, EventArgs e)
{
// Task<TResult>(Func<Object, TResult>, Object)
Task<AtomEntry> task2 = new Task<AtomEntry>(UploadPhoto2, filename);
}
```

## 起始非同步工作

[Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 類別可用來簡化 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.aspx) 或 [ThreadPool](http://msdn.microsoft.com/zh-tw/library/system.threading.threadpool.aspx) 執行非同步工作的邏輯，你可以先建立 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 執行個體，再叫用 [Task.Start](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.start.aspx) 方法來啟動非同步工作；或者使用靜態方法 [TaskFactory.StartNew](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.taskfactory.startnew.aspx) 直接建立 Task 並執行非同步工作。  

### Task.Start()

底下範例會自動建立多個Thread執行上傳動作。
```c#
private void UploadPhoto0(Object filename)
{
UploadPhotoAsync(gsAlubmId, filename.ToString());
}

private void bnTask2_Click(object sender, EventArgs e)
{
FileInfo[] files = GetFileList();
foreach (FileInfo file in files)
{
string filename = file.FullName;
Task task = new Task(UploadPhoto0, filename);
task.Start();
}
Console.WriteLine("Finish");
}
```

如果你要等待所有非同步工作執行結束才繼續往下的話，可以叫用 [Task.WaitAll](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.waitall.aspx) 方法。
```c#
private void bnTask2_Click(object sender, EventArgs e)
{
FileInfo[] files = GetFileList();
List<Task> tasks = new List<Task>();
foreach (FileInfo file in files)
{
string filename = file.FullName;
Task task = new Task(UploadPhoto0, filename);
task.Start();
}
Task.WaitAll(tasks.ToArray());  //主執行緒會停留在這一行，直到所有 Task 執行完畢，才會繼續往下。
Console.WriteLine("Finish");
}
```

由於 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 建構函式中是一個 Action 委派參數，所以我們也可以使用 Lambda 運算式和匿名方法來建立委派。  
```c#
FileInfo[] files = GetFileList();
List<Task> tasks = new List<Task>();
foreach (FileInfo file in files)
{
string filename = file.FullName;
Task task = new Task(
() =>
{
UploadPhotoAsync(gsAlubmId, filename);
});
task.Start();
tasks.Add(task);
}
Task.WaitAll(tasks.ToArray());  //主執行緒會停留在這一行，直到所有 Task 執行完畢，才會繼續往下。
Console.WriteLine("Finish");
```

### TaskFactory.StartNew()

[TaskFactory](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.taskfactory.aspx) 類別是 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 類別的輔助工具，提供 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 物件操作的便利性。  例如，你可以直接叫用其 [StartNew](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.taskfactory.startnew.aspx) 方法，就可以建立 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 執行個體，並啟動該並行作業。  所以，上面的例子，可以改寫如下：  
```c#
FileInfo[] files = GetFileList();
foreach (FileInfo file in files)
{
string filename = file.FullName;
Task task = Task.Factory.StartNew(UploadPhoto0, filename);
}
Console.WriteLine("Finish");
```

[StartNew](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.taskfactory.startnew.aspx) 有多個多載方法，其使用方法都類似 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 類別。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi6MuVLfuBiqi1OFHS2HuZfE06QbbswRjTL9Tjzr9x2k_K_6MQ01DiTry3j10SqgQaci024Q-aYqB1Gn-HjbvZYRYkEkTGByPZXHya87SLf0Ce9BnmkcPWbzBROmO8n4pUC9AQEJTphGU0/s753/taskfactory-startnew.png)

### Task.Run()

## 取得非同步工作的執行結果

雖然 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 類別本身無法回傳執行結果，  但是它的泛型類別 [Task&lt;TResult&gt;](https://msdn.microsoft.com/zh-tw/library/dd321424%28v=vs.100%29.aspx) 可以。  當使用 [Task&lt;TResult&gt;](https://msdn.microsoft.com/zh-tw/library/dd321424%28v=vs.100%29.aspx) 類別建立非同步工作時，若要取得執行結果，可以直接由執行個體的 [Result](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.result.aspx) 屬性取得。  不過，要取得執行結果，你必須先等待工作執行結束，因此在程式碼中必須加入等待結束的機制。  

- [Task.Wait](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.wait.aspx) ：等候該 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 完成執行。
- [Task.WaitAny](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.waitany.aspx) ：這個靜態方法，會等候任一提供的 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 物件完成執行。
- [Task.WaitAll](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.waitall.aspx) ：這個靜態方法，會等候所有提供的 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 物件完成執行。

底下這個例子，在 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 啟動後，便叫用 [Wait](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.wait.aspx) 方法等待該 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 執行完畢才繼續往下。  
```c#
private AtomEntry UploadPhoto2(Object filename)
{
return UploadPhotoAsync(gsAlubmId, filename.ToString());
}

private void bnTaskResult_Click(object sender, EventArgs e)
{
FileInfo[] files = GetFileList();

foreach (FileInfo file in files)
{
string filename = file.FullName;
Task<AtomEntry> task = new Task<AtomEntry>(UploadPhoto2, filename);
task.Start();
task.Wait();            //等待並行作業執行完畢，再繼續往下。

Photo photo = new Photo();
photo.AtomEntry = task.Result;  //讀取非同步工作的執行結果
Console.WriteLine("ID:{0} URI:{1}", photo.Id, photo.PhotoUri);
}
｝
```

上面範例中，其實在 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 作業中，只要該 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 有叫用到 [Result](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.result.aspx) 屬性，既使沒有呼叫 [Wait](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.wait.aspx) 方法，程式碼也會停在該行程式，直到該並行作業執行完畢才續繼往下。  

另外的做法，你也可以先啟用所有的 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) ，再叫用 [WaitAll](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.waitall.aspx) 方法，等待所有並行的作業執行完畢。  不過這個方法無法確保每個 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 的執行順序，如果在意每個工作先後完成的順序，就只能使用上面方法。  
```c#
FileInfo[] files = GetFileList();

List<Task> tasks = new List<Task>();
foreach (FileInfo file in files)
{
string filename = file.FullName;
Task<AtomEntry> task = new Task<AtomEntry>(UploadPhoto2, filename);
task.Start();
tasks.Add(task);
}
Task.WaitAll(tasks.ToArray());  //主程式會停留在這一行，直到所有 Task 執行完畢，才會繼續往下。

foreach (Task<AtomEntry> task in tasks)
{
Photo photo = new Photo();
photo.AtomEntry = task.Result;
Console.WriteLine("ID:{0} URI:{1}", photo.Id, photo.PhotoUri);
}
```

## Task.ContinueWith

如果在 [Task&lt;TResult&gt;](https://msdn.microsoft.com/zh-tw/library/dd321424%28v=vs.100%29.aspx) 作業執行結束後，你希望接著執行另一段程式，可以使用 [ContinueWith](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.continuewith.aspx) 。它的多載方法有：  

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhUyarIGRsBfcL2ciF2y_kutY_LE4X4mxRxExs6Id0wEHbkFPEp69hjY_040-aIK7emrEsH4Cr_YXAsEICtK-evxX1WiXe4AIHh8FUVm7y5qo3Px5wm79WdY5kDXz7BaxECT58CbIuN6eM/s800/task-continue-with.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhUyarIGRsBfcL2ciF2y_kutY_LE4X4mxRxExs6Id0wEHbkFPEp69hjY_040-aIK7emrEsH4Cr_YXAsEICtK-evxX1WiXe4AIHh8FUVm7y5qo3Px5wm79WdY5kDXz7BaxECT58CbIuN6eM/s0/task-continue-with.png)

#### Task ContinueWith(Action&lt;Task&lt;TResult&gt;&gt; continuationAction)

這個方法表示：

- ContinueWith 會回傳一個 [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 型別值。
- ContinueWith 的參數是一個 Action 委派。
- 該委派要執行的對向（方法），必須含有一個 [Task&lt;TResult&gt;](https://msdn.microsoft.com/zh-tw/library/dd321424%28v=vs.100%29.aspx) 型別參數。

#### Task&lt;TNewResult&gt; ContinueWith&lt;TNewResult&gt;(Func&lt;Task&lt;TResult&gt;, TNewResult&gt; continuationFunction)

這個方法表示：

- ContinueWith 會回傳一個 Task&lt;TNewResult&gt; 型別值。
- ContinueWith 的參數是一個 Func 委派。
- 該委派要執行的對向（方法），必須含有一個 [Task&lt;TResult&gt;](https://msdn.microsoft.com/zh-tw/library/dd321424%28v=vs.100%29.aspx) 型別參數，也就是將已完成的工作當做引數傳遞給委派。

例：
```c#
private Photo UpdatePhoto2(Task<AtomEntry> task)
{
return new Photo();
}

private void bnTaskResultContinueWith_Click(object sender, EventArgs e)
{
FileInfo[] files = GetFileList();

foreach (FileInfo file in files)
{
string filename = file.FullName;

Task<Photo> task = Task.Factory
.StartNew<AtomEntry>(UploadPhoto2, filename)
.ContinueWith<Photo>(UpdatePhoto2);

Photo photo = task.Result;
Console.WriteLine("ID:{0} Title:{1} Summary:{2}", photo.Id, photo.Title, photo.Summary);
}
Console.WriteLine("Finish");
}
```

叫用 [ContinueWith](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.continuewith.aspx) 時，你無法傳送額外的參數給執行方法，若須使用到參數，可考慮改用 Lambda 。
```c#
foreach (FileInfo file in files)
{
string filename = file.FullName;

Task<Photo> task = Task.Factory
.StartNew<AtomEntry>(UploadPhoto2, filename)
.ContinueWith<Photo>(pre_task =>
{
Photo ori_photo = new Photo();
ori_photo.AtomEntry = pre_task.Result;
string title = Path.GetFileNameWithoutExtension(filename);
string summary = title;
return UpdatePhoto(ori_photo.Id, title, summary);
});

Photo photo = task.Result;
Console.WriteLine("ID:{0} Title:{1} Summary:{2}", photo.Id, photo.Title, photo.Summary);
}
```

# Parallel

[Parallel](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.parallel.aspx) 類別是專門用來支援平行迴圈的作業模式，也就是簡化上面範例中，使用 Loop + [Task](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.task.aspx) 的程式碼。  所以，若一個集合中的所有元素，都要使用相同的操作行為，就非常適合使用 [Parallel](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.parallel.aspx) 類別來進行同步處理。  

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvniFoLBzPT6nQZckwOgmB6h942oMSIyQnlQ3v11-WHc0TvohYoFdpju8pNC9ijycUZ0dkH9cJduJQfWt2r_xvlcXdC12cGNOLv5gtZd9lBvhbnqcPFDsyv5APPdZGxWhJVMEL5iBaA0k/s800/parallel-foreach.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvniFoLBzPT6nQZckwOgmB6h942oMSIyQnlQ3v11-WHc0TvohYoFdpju8pNC9ijycUZ0dkH9cJduJQfWt2r_xvlcXdC12cGNOLv5gtZd9lBvhbnqcPFDsyv5APPdZGxWhJVMEL5iBaA0k/s0/parallel-foreach.png)
```c#
private void UploadFile0(string filename)
{
UploadFileAsync(gsAlubmId, filename);
}
private void bnParallel_ForEach_Click(object sender, EventArgs e)
{
List<string> files = GetFileList();

// 建立 ParallelLoopResult ForEach<TSource>(IEnumerable<TSource> source, Action<TSource> body);

Parallel.ForEach<string>(files,
delegate(string file)
{
UploadFile0(file);
});

// 上一行程式碼，可以用 Lambda 簡化如下：
Parallel.ForEach(files, (file) =>
{
UploadFile0(file);
});

// 如果要執行的程式碼都包在一個函式裡，且符合委派簽名，那麼還可以簡化如下：
Parallel.ForEach(files, UploadFile0);
}
```

注意事項：  

- [Parallel.ForEach](http://msdn.microsoft.com/zh-tw/library/system.threading.tasks.parallel.foreach.aspx) 迴圈，並不保證每個並行工作的啟動先後。
- 每個並行工作都必須執行完畢，程式才會繼續往下執行迴圈外的程式。
## 參考資料  

- [TPL 和傳統 .NET Framework 非同步程式設計](https://msdn.microsoft.com/zh-tw/library/Dd997423%28v=VS.110%29.aspx)
- [以 .NET Framework 進行平行程式設計](https://msdn.microsoft.com/zh-tw/library/dd460693%28v=vs.110%29.aspx)
- [工作平行程式庫 (Task Parallel Library, TPL)](https://msdn.microsoft.com/zh-tw/library/dd460717%28v=vs.110%29.aspx)
- [簡介.NET 4.0的多工執行利器--Task](http://blog.darkthread.net/post-2012-07-20-net4-task.aspx)
- [C# 學習筆記：多執行緒 (6) - TPL](http://huan-lin.blogspot.com/2013/06/csharp-notes-multithreading-6-tpl.html)
- [TPL(Task Parallel Language) – 平行化處理](http://arthurmvc.blogspot.tw/2013/09/tpltask-parallel-language.html)