---
title: 非同步程式設計
layout: default
parent: 執行緒
nav_order: 1
description: "非同步程式設計"
date: 2012-02-01
tags: [DotNetFramework,執行緒]
postid: "749616538090630891"
---
非同步作業通常是用來執行可能需要很長時間才會完成的工作，例如，開啟大型檔案、連接到遠端電腦或查詢資料庫。  要設計非同步作業，除了自行利用 [Thread](http://msdn.microsoft.com/zh-tw/library/system.threading.thread.aspx) 物件增加執行管道外，在 .NET 中已提供幾種設計模式來快速達到非同步作業的目的。  

- [非同步程式設計模型（Asynchronous Programming Model, APM）](https://msdn.microsoft.com/zh-tw/library/ms228963.aspx)
- [Calling Asynchronous Methods Using **IAsyncResult**](https://msdn.microsoft.com/zh-tw/library/ms228975.aspx)
- [Asynchronous Programming Using **Delegates**](https://msdn.microsoft.com/zh-tw/library/22t547yb.aspx)
- [使用事件為基礎的非同步作業模式（Event-based Asynchronous Pattern, EAP）](https://msdn.microsoft.com/zh-tw/library/ms228969.aspx)
- [使用工作為基礎的非同步作業模式（Task-based Asynchronous Pattern, TAP）](https://msdn.microsoft.com/zh-tw/library/hh873175.aspx)

# 非同步程式設計

在.Net Framework裡，有許多類別都有提供 **BeginXXX** 與 **EndXXX** 的方法來支援 APM。  例如：FileStream 的 **BeginRead**() 與 **EndRead**() 方法。  BeginXXX 會開始進行非同步作業，並回傳一個 [IAsyncResult](http://msdn.microsoft.com/zh-tw/library/system.iasyncresult) 介面的物件，可以用來判斷非同步作業非 (Asynchronous Operation) 的狀態。   

上面提到過，總不能當非同步作業的執行緒執行到一半，就把結果拿來用。那該如何判斷，才知道非同步的呼叫已經執行完成了呢？  這時候就會用到會合點的技巧(Rendezvous Models)。  

APM有下列三種程式設計方式來處理非同步呼叫之後的動作：等待完成、輪詢、回呼。

## 等待模式模型 (Wait-Until-Done Model)

這個模型的做法是，在啟動 APM 執行非同步作業後，主程序依然可以繼續執行其他的工作。  若主程序結束了其他的工作之後，可以試著呼叫結束 APM 的動作。  若非同步作業中的執行緒還在執行，則主程序就會 block 在此一直等待，直到非同步作業完成為止。  
```c#
//等待模式：Wait until down model

byte[] buffer = new byte[100];
string filename = string.Concat(Environment.SystemDirectory, "\\mfc71.pdb");

// 使用APM開啟檔案資料流  (FileOptions 設定為 Asynchronous，表示以非同步讀取)
FileStream stream = new FileStream(filename, FileMode.Open, FileAccess.Read, FileShare.Read, 1024, FileOptions.Asynchronous);

// 啟動非同步呼叫讀取檔案
IAsyncResult result = stream.BeginRead(buffer, 0, buffer.Length, null, null);

// 因為使用非同步呼叫，所以既使檔案還沒讀取結束，程式也會繼續下一行
// 所以等待階段，這裡可以繼續執行程式碼
// TO DO ....

// 呼叫 EndRead：在此進行等待。這行程式會被鎖住，直到非同步工作結束。
int numBytes = stream.EndRead(result);

stream.Close();     // close the stream

Console.WriteLine("Read {0} Bytes", numBytes);
Console.WriteLine(BitConverter.ToString(buffer));
```

## 輪詢模型 (Polling Model)

藉由不停的詢問 [IAsyncResult](http://msdn.microsoft.com/zh-tw/library/system.iasyncresult) 的狀態，以判斷非同步作業是否已完成。
```c#
byte[] buffer = new byte[100];
string filename = string.Concat(Environment.SystemDirectory, "\\mfc71.pdb");

// 使用APM開啟檔案資料流  (FileOptions 設定為 Asynchronous，表示以非同步讀取)
FileStream strm = new FileStream(filename, FileMode.Open, FileAccess.Read, FileShare.Read, 1024, FileOptions.Asynchronous);

// 啟動非同步呼叫讀取檔案
IAsyncResult result = strm.BeginRead(buffer, 0, buffer.Length, null, null);

// 不停的輪詢，測試看看是否工作結束
while (!result.IsCompleted)
{
// TO DO ....
Application.DoEvents();
}

// 因為 IAsyncResult 狀態已經 Completed, EndRead 肯定不會被 block 住。
int numBytes = strm.EndRead(result);

strm.Close();       // close the stream
Console.WriteLine("Read {0} Bytes", numBytes);
Console.WriteLine(BitConverter.ToString(buffer));
```

## 回呼模型 (Callback Model)

上面方法，使用不停詢問的方式判斷非同步作業的狀態，技巧上似乎不怎麼有效率。  有時候可以使用回呼這個模型，它必須指定一個回呼方法給 [AsyncCallback](http://msdn.microsoft.com/zh-tw/library/system.asynccallback) 委派，等到非同步作業完成，程式就會自動進入這個方法。  

#### 現有物件的回呼模型

下面例子，我們使用 FileStream 物件，執行回呼模式的非同步作業讀取作業。
```c#
public override IAsyncResult BeginRead(byte[] array, int offset, int numBytes, AsyncCallback userCallback, object stateObject);
```
```c#
[Serializable]
[ComVisible(true)]
public delegate void AsyncCallback(IAsyncResult ar);
```
```c#
private void button8_Click(object sender, EventArgs e)
{
string filename = string.Concat(Environment.SystemDirectory, "\\mfc71.pdb");

// 使用APM開啟檔案資料流  (Asynchronous：以非同步讀取)
FileStream strm = new FileStream(filename, FileMode.Open, FileAccess.Read, FileShare.Read, 1024, FileOptions.Asynchronous);

// 這裡建立了一個 AsyncCallback 委派，並指定該執行緒結束時所要呼叫的方法。
AsyncCallback callback = new AsyncCallback(CompleteRead);

// 啟動非同步呼叫讀取檔案，
IAsyncResult result = strm.BeginRead(buffer, 0, buffer.Length, callback, strm); //傳入自訂物件 strm
}
static void CompleteRead(IAsyncResult result)
{
Console.WriteLine("Read Completed");
FileStream strm = (FileStream)result.AsyncState;    //由AsyncState屬性，取得自訂的物件。

// 因為 IAsyncResult 狀態已經 Completed, EndRead 肯定不會被 block 住。
int numBytes = strm.EndRead(result);

strm.Close();       // close the stream
Console.WriteLine("Read {0} Bytes", numBytes);
Console.WriteLine(BitConverter.ToString(buffer));
}
```

上面這個例子中的 BeginRead 方法中的最後一個參數，我們放入了 stream 物件，目的是希望傳給 CompleteRead 方法，讓它可以 close stream。  而 CompleteRead 是 [AsyncCallback](http://msdn.microsoft.com/zh-tw/library/system.asynccallback) 委派的方法，必須使用相同簽名，所以僅接收 [IAsyncResult](http://msdn.microsoft.com/zh-tw/library/system.iasyncresult) 做為參數。  若要取得傳入的 stream 物件，則可以透過 [IAsyncResult.AsyncState](http://msdn.microsoft.com/zh-tw/library/system.iasyncresult.asyncstate) 屬性，以取得使用者定義的物件。  

#### 自訂物件的回呼模型

下面這個例子，假設 GetFileContents 方法是一個耗時的方法，用來讀取檔案並回傳內容。  若想要使用回呼模式的非同步執行，我們可以宣告一個委派，然後叫用它的 BeginInvoke 方法。  

底下程式碼示範如何使用回呼方式，執行非同步作業，並取得執行結果。  
```c#
public delegate string AsyncGetFileContents(string FileName);

private void btnBeginInvoke_Click(object sender, EventArgs e)
{
string sFileName = "";

AsyncGetFileContents GetFielContent = new AsyncGetFileContents(BeginGetFileContents);
AsyncCallback callback = new AsyncCallback(EndGetFileContents);
IAsyncResult result = GetFielContent.BeginInvoke(sFileName, callback, GetFielContent);
}

private string BeginGetFileContents(string FileName)
{
// time consuming job.
Thread.Sleep(1000\*5);
return "abc";
}
private void EndGetFileContents(IAsyncResult result)
{
AsyncGetFileContents d = (AsyncGetFileContents)result.AsyncState;    //由AsyncState屬性，取得自訂的物件。
string sContent = d.EndInvoke(result);
Console.WriteLine(sContent);
}
```

#### APM 的例外狀況

若非同步執行的工作發生例外，一般來講，這些例外都可以在呼叫 EndXXX 方法時捕捉到。如果要捕捉例外狀況，可改成以下程式碼
```c#
int numBytes = 0;
try
{
numBytes = strm.EndRead(result);
}
catch (IOException)
{
Console.Write("An IO Exception occurred");
}
```


- [非同步程式設計模式](http://msdn.microsoft.com/zh-tw/library/ms228969.aspx)
- [以非同步的方式呼叫同步方法](https://msdn.microsoft.com/zh-tw/library/2e08f6yc%28v=vs.110%29.aspx)
- [WaitCallback 委派](http://msdn.microsoft.com/zh-tw/library/system.threading.waitcallback.aspx)
- [非同步作業 IAsyncResult / AsyncCallback](http://www.dotblogs.com.tw/yc421206/archive/2011/01/03/20540.aspx)