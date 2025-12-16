---
title: WorkFlow 簡介
layout: default
parent: Workflow Foundation
nav_order: 1
description: "WorkFlow 簡介"
date: 2014-06-16
tags: [DotNetFramework,Workflow Foundation]
postid: "7567162134209363061"
---
Windows Workflow Foundation 是在 VS2005 , 也就是 .NET Framework 3.0 時代才被加入的一個功能。  它被整合在 .NET Framework 之中，用來協助在應用程式中開發與工作流程相關的功能。  搭配 VS 的圖型化設計介面，讓您可以在 Windows 上快速建置支援工作流程的應用程式。  

# WF 架構簡介

Windows Workflow Foundation 架構主要分份兩個部份：一是**工作流程內容**，另一是執行工作流程的**執行階段引擎**。  

## WF4 專案範本

Visual Studio 2010 提供四種 WF 範本：

![](http://127.0.0.1:8080/_images/cs/wf-templates.png)

## 定義活動與工作流程（Activities and Workflows）

### 活動（Activities）

在 WF 中，「工作流程」中的每一個關卡，都稱為一個「活動（Activities）」。「活動」是「工作流程」中的基礎單元。  在工具箱中，有各種現成的活動元件可以使用，你可以使用這些元件來建構你的工作流程，也可以使用自行開發的自訂活動來建構你的工作流程。  

下圖是一個簡單工作流程，其中包含了三個活動。  

![](http://127.0.0.1:8080/_images/cs/wf-activity-sample.png)

下圖是一個較複雜的計算機的工作流程，其中包含了多個活動。  

![](http://127.0.0.1:8080/_images/cs/wf-activity-caculator.png)

### 工作流程（Workflows）

Visual Studio 提供了各式活動，將這些活動靈活組合運用，你就可以設計出許多程式所需要的工作流程。

## 建立工作流程實體

定義好工作流程，你必須在應用程式中建立該工作流程的實體，以執行工作流程中的內容。  .NET Framework 提供以下幾種方式來建立工作流程實體。  

### 使用 WorkflowInvoker 類別

[WorkflowInvoker](http://msdn.microsoft.com/zh-tw/library/system.activities.workflowinvoker.aspx) 類別提供較簡易但功能較少的方法來建立工作流程實體。  使用這個方法建立的工作流桯，當啟動流程後，就會一直執行到完成為止。  

#### 同步方式執行

若要以同步方式執行工作流程，只要叫用靜態方法 [WorkflowInvoker.Invoke](http://msdn.microsoft.com/zh-tw/library/system.activities.workflowinvoker.invoke.aspx) 即可。  
```c#
WorkflowInvoker.Invoke(new wf_SayHello());
```

#### 非同步方式執行

這個類別也支援非同步方式執行工作流桯，不過它必須由 [WorkflowInvoker](http://msdn.microsoft.com/zh-tw/library/system.activities.workflowinvoker.aspx) 實體類別的 [InvokeAsync](http://msdn.microsoft.com/zh-tw/library/system.activities.workflowinvoker.invokeasync.aspx) 方法啟動。  
```c#
WorkflowInvoker invoker = new WorkflowInvoker(new wf_SayHello());
invoker.InvokeAsync();
invoker.InvokeCompleted += new EventHandler＜InvokeCompletedEventArgs＞(workerInvokeCompleted);
```

### 使用 WorkflowApplication 類別

[WorkflowApplication](http://msdn.microsoft.com/zh-tw/library/system.activities.workflowapplication.aspx) 類別提供較多的方法，可以用來控制工作流程。  例如使用書籤(Bookmark)來繼續工作流程中的活動，或者在應用程式中接收工作流程的事件通知等等。  你可以使用以下幾個方法來控制工作流程：  

- [Run](http://msdn.microsoft.com/zh-tw/library/system.activities.workflowapplication.run.aspx) ：開始或繼續執行工作流程執行個體。
- [Abort](http://msdn.microsoft.com/zh-tw/library/system.activities.workflowapplication.abort.aspx) ：通知工作流程執行階段此工作流程執行個體應該中止。
- [Cancel](http://msdn.microsoft.com/zh-tw/library/system.activities.workflowapplication.cancel.aspx) ：取消工作流程執行個體。
- [Load](http://msdn.microsoft.com/zh-tw/library/system.activities.workflowapplication.load.aspx) ：Loads a workflow instance from an instance store.
- [Persist](http://msdn.microsoft.com/zh-tw/library/system.activities.workflowapplication.persist.aspx) ：將工作流程執行個體保存到執行個體存放區。
- [Terminate](http://msdn.microsoft.com/zh-tw/library/system.activities.workflowapplication.terminate.aspx) ：保存或卸載工作流程執行個體。
- [Unload](http://msdn.microsoft.com/zh-tw/library/system.activities.workflowapplication.unload.aspx) ：保存或卸載工作流程執行個體。
```c#
//TODO: 1.建立工作流程實體
wfInstance = new WorkflowApplication(new Calculator());

//TODO: 2.建立工作流程完成時所要執行的方法委派
wfInstance.Completed = delegate(WorkflowApplicationCompletedEventArgs args)
{

Dispatcher.BeginInvoke(new Action<decimal>(UpdateUI), args.Outputs["Result"]);

wfInstance = null;
};

//TODO: 3.啟動工作流程
wfInstance.Run();
```

# 建立 Sequence 工作流程

# 建立自訂活動（Code Activity）

## 工作流程也可當做活動

當你定義好一個工作流程，在專案建置之後，你可以在工具箱中發現，每個工作流程都變成一個可用的活動，被歸類在同一個工具群組之中。如下圖：  

![](http://127.0.0.1:8080/_images/cs/wf-custom-activity-1.png)

所以說，如果你有一段流程會在很多工作流程中使用，你可以將這一段流程獨立到一個工作流程，再建立成活動，就可以在其他的工作流程中重複使用這一段流程。

## 繼承 CodeActivity 類別

你也可以將某些會重複執行的程式碼設計成活動，讓不同的工作流程都可以直接使用。例如存取資料庫的程式碼。  這一類型的活動，就稱「程式碼活動」。在 workflow 專案的項目中就有這個範本。  

![](http://127.0.0.1:8080/_images/cs/wf-custom-activity-2.png)

「程式碼活動」本身就是一個類別檔，只不過繼承了 [CodeActivity](http://msdn.microsoft.com/zh-tw/library/system.activities.codeactivity.aspx) 類別。  你只要將工作流程要用到的程式碼撰寫在 [Execute](http://msdn.microsoft.com/zh-tw/library/system.activities.nativeactivity.execute.aspx) 方法中即可。  
```c#
public sealed class ca_ShowMessage : CodeActivity
{
// 定義字串型別的活動輸入引數
public InArgument<string> Text { get; set; }

// 如果您的活動要傳回值，請從 CodeActivity<TResult> 衍生該值，
// 並從 Execute 方法傳回該值。
protected override void Execute(CodeActivityContext context)
{
// 取得 Text 輸入引數的執行階段值
string text = context.GetValue(this.Text);
MessageBox.Show(text);   
}
}
```

## 繼承 NativeActivity 類別

繼承 [CodeActivity](http://msdn.microsoft.com/zh-tw/library/system.activities.codeactivity.aspx) 類別所產生的程式碼活動，功能較少。如果需要在自訂的程式碼活動中使用書籤，或是存取執行環境中的完整功能，就必須改為繼承 [NativeActivity](http://msdn.microsoft.com/zh-tw/library/system.activities.nativeactivity.aspx) 類別。  
```c#
public sealed class na_RetrieveString : NativeActivity<string>
{
// 定義活動的輸入引數
public InArgument<string> BookmarkName { get; set; }

protected override void Execute(NativeActivityContext context)
{
context.CreateBookmark(BookmarkName.Get(context), new BookmarkCallback(ReturnValue));
}

public void ReturnValue(NativeActivityContext context, Bookmark bookmark, object obj)
{
Result.Set(context, (string)obj);
}

protected override bool CanInduceIdle
{
get {return true;}
}
}
```

# 建立書籤（Bookmark）

如果你的工作流程必須暫時停止，等待外部應用程式傳送訊號進來之後再繼續執行，這時候就必須使用到書籤。  所以「書籤」就是流程暫停後再繼續執行的點。  要設計「書籤」，你必須自訂一個繼承 NativeActivity 類別的「程式碼活動」。  

#### 建立書籤步驟：

1. 建立繼承 NativeActivity 類別的「程式碼活動」。
2. 使用 CreateBookmark 方法建立書籤。
3. 覆寫 CanIncludeIdle 屬性。
```c#
// 1. 繼承 NativeActivity 類別
public sealed class na_RetrieveString : NativeActivity<string>
{
public InArgument<string> BookmarkName { get; set; }

protected override void Execute(NativeActivityContext context)
{
// 2. 使用 CreateBookmark 方法建立書籤
context.CreateBookmark(BookmarkName.Get(context), new BookmarkCallback(ReturnValue));
}

public void ReturnValue(NativeActivityContext context, Bookmark bookmark, object obj)
{
Result.Set(context, (string)obj);
}

// 3. 覆寫 CanIncludeIdle 屬性
protected override bool CanInduceIdle
{
get {return true;}
}
}
```

# 建立 Flowchart 工作流程

通常使用 Sequence 活動可以用來建立較簡單且循序執行的工作流程，但是如果要建立較複雜的流程，例如計算機流程或者表單系統流程等，就必須使用 Flowchart 活動。  

![](http://127.0.0.1:8080/_images/cs/wf-activity-caculator.png)
## 參考資料  
![](http://127.0.0.1:8080/_images/cs/ReferencePage.png)

- [Windows Workflow 概觀](http://msdn.microsoft.com/zh-tw/library/dd489465%28v=vs.110%29.aspx)
- [WF4 第一課 – 初探](http://michaelchpeng5555.wordpress.com/2009/11/21/workflow-foundation-4%e7%ac%ac%e4%b8%80%e8%aa%b2-%e2%94%80-%e5%88%9d%e6%8e%a2/)
- [WF4 第二課 – Hello WF 4](http://michaelchpeng5555.wordpress.com/2009/11/21/workflow-foundation-4%e7%ac%ac%e4%ba%8c%e8%aa%b2-%e2%94%80-hello-wf-4/)
- [WF4 第三課 – 設計Code Activity](http://michaelchpeng5555.wordpress.com/2009/11/23/workflow-foundation-4%e7%ac%ac%e4%b8%89%e8%aa%b2-%e8%a8%ad%e8%a8%88code-activity/)
- [WF4 第四課 – 動態執行流程](http://michaelchpeng5555.wordpress.com/2009/11/24/workflow-foundation-4-%e7%ac%ac%e5%9b%9b%e8%aa%b2-%e2%80%93-%e5%8b%95%e6%85%8b%e5%9f%b7%e8%a1%8c%e6%b5%81%e7%a8%8b/)
- [WF4 第五課 – 測試WF流程](http://michaelchpeng5555.wordpress.com/2009/11/25/workflow-foundation-4%e7%ac%ac%e4%ba%94%e8%aa%b2-%e6%b8%ac%e8%a9%a6wf%e6%b5%81%e7%a8%8b/)
- [WF4 第六課 – WorkflowApplication](http://michaelchpeng5555.wordpress.com/2009/12/04/workflow-foundation-4-%e7%ac%ac%e5%85%ad%e8%aa%b2-workflowapplication/)
- [WF4 第七課 – If/Else判斷邏輯](http://michaelchpeng5555.wordpress.com/2009/12/11/workflow-foundation-4%e7%ac%ac%e4%b8%83%e8%aa%b2-%e2%80%93-ifelse%e5%88%a4%e6%96%b7%e9%82%8f%e8%bc%af/)
- [WF4 第八課 – 錯誤處理](http://michaelchpeng5555.wordpress.com/2009/12/11/workflow-foundation-4-%e7%ac%ac%e5%85%ab%e8%aa%b2-%e9%8c%af%e8%aa%a4%e8%99%95%e7%90%86/)
- [WF4 第九課 – Activity Designer](http://michaelchpeng5555.wordpress.com/2009/12/12/workflow-foundation-4-%e7%ac%ac%e4%b9%9d%e8%aa%b2-%e2%80%93-activity-designer/)
- [WF4 第十課 – 自訂的WF設計器](http://michaelchpeng5555.wordpress.com/2010/01/02/workflow-foundation-4%e7%ac%ac%e5%8d%81%e8%aa%b2-%e2%80%93-%e8%87%aa%e8%a8%82%e7%9a%84wf%e8%a8%ad%e8%a8%88%e5%99%a8/)
- [.NET 4.5 中 Windows Workflow Foundation 的新功能](http://msdn.microsoft.com/zh-tw/library/hh305677%28v=vs.110%29.aspx)
- 
-