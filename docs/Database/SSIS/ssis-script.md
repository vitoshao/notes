---
title: 在 SSIS 中自訂程式碼
layout: default
parent: Advanced Topics
nav_order: 2
description: "在 SSIS 中自訂程式碼"
date: 2014-01-29
tags: [SQL,Advanced Topics]
postid: "2027614946845903410"
---

SSIS 本身就有提供許多工具或元件可以用來執行各種作業需求，如果這些現成的元件還不能滿足需求話，你也可以利用以下元件，透過自訂程式碼的方式來設計封裝。  

- [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) ：允許在 Control Flow 中執行 .Net 腳本。
- [Script Component](http://technet.microsoft.com/en-us/library/ms137640.aspx) ：允許在 Data Flow 中執行 .Net 腳本。
- Custom Components ：自訂元件，方便重複使用與部署。

# Script Task

不管是使用 [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) 或 [Script Component](http://technet.microsoft.com/en-us/library/ms137640.aspx) 都會使用到二個設計模式。  一個是在**編輯介面**中，直接指定元件的屬性；另一個模式則是用來**撰寫指令碼的環境**。  在 SQL Server 2012 中的指令碼編輯環境稱為：Microsoft Visual Studio Tools for Applications（VSTA）。   而 早期的 SQL Server 2005 的指令碼編輯環境則稱為：Microsoft Visual Studio for Applications (VSA) 。   

## Dts Object

在 [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) 中，你除了可以使用完整的 .NET 類別庫和命名空間來撰寫程式碼之外，你也可以透過 Dts 物件來存取 package 本身變數。  

## Configuring the Script Task

在 Control Flow 中加入 [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) 後，通常必須先設定好以下屬性，才開始撰寫程式碼：

- ScriptLanguage：腳本要使用的程式語言版本。
- EntryPoint：指定一個Method名稱，做為腳本的啟始Method。
- ReadOnlyVariable：指定要在腳本中使用到的 SSIS 唯讀變數。
- ReadWriteVariable：指定要在腳本中使用到的 SSIS 讀寫變數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhc2e76zDoxcjGBVsntqAa5PPaIw6t0unTvgW8W6_6uqqUVVW0PB1ytD_UCqtCyfQivpm2Iq49nRzcnNVs2HCRx-oqAcjKXIksFTgTiCVrJgMkB4xx21FFPXdoD629Nn6ewCdtWMygKyLw/s0/ssis-script-01.png)

## Coding the Script Task

### VSTA 編輯環境

當你執行「編輯指令碼」，就會開啟 VSTA 編輯環境。你可以在這個環境執行以下作業：

- 使用自訂的類別與方法
- 使用 .NET 類別庫和命名空間來撰寫程式碼
- 加入 .NET assemblies 、 COM components 、 Web Service 、或其他專案等參考
- 使用 Dts 物件來存取 package 本身變數或連線資訊。
- 使用 breakpoint 來 debug 腳本程式。

你可以由 Dts 的 Variables 和 Connections 屬性取得 package 中的變數和連線資訊。  以下是他們的用法，注意型別轉換及大小寫。  
```c#
public void Main()
{
	// 存取變數
	string var2 = Dts.Variables["User::var2"].Value.ToString();

	// 存取 ADO.Net 連線 
	SqlConnection conn1 = (SqlConnection)Dts.Connections["adonet"].AcquireConnection(null);

	// 存取 OleDB 連線  ( ref Microsoft.SqlServer.DTSRuntimeWrap in Microsoft.SqlServer.DTSRuntimeWrap.dll )
	ConnectionManager cm = Dts.Connections["oledb"];
	IDTSConnectionManagerDatabaseParameters100 cmParams = cm.InnerObject as IDTSConnectionManagerDatabaseParameters100;
	OleDbConnection conn2 = cmParams.GetConnectionForSchema() as OleDbConnection;

	Dts.TaskResult = (int)ScriptResults.Success;
}
```

### 回傳結果

若Script執行結束，你可以透過以下屬性，讓封裝可以判定指令碼工作的執行結果。  

#### TaskResult

Dts.TaskResult 屬性是用來回傳 True/False ，表示指令碼工作是成功或失敗。
```c#
Dts.TaskResult = (int)ScriptResults.Success;
```

#### ExecutionValue

Dts.TaskResult 屬性是用來回傳使用自訂物件，以提供更多的回傳資訊。
```c#
int rowAffected;
...
rowAffected = 1000;
Dts.ExecutionValue = rowAffected;
```

# Script Component

[Script Component](http://technet.microsoft.com/en-us/library/ms137640.aspx) 與 [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) 一樣，都提供你完整的自訂程式碼的能力，只不過它是用在 Data Flow 中的元件。  

## Configuring the Script Component

### 設定元件用途

[Script Component](http://technet.microsoft.com/en-us/library/ms137640.aspx) 的用法類似 [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) ，不過，它是 Data Flow 中使用的元件，所以會有輸入與輸出的設定。  就像你在設計 Data Flow 時，你由 Toolbox 中拉出的元件，不外乎：Data Source 、 Data Destination 、 Transformation 。  因此在設計一個指令碼元件，一開始的時候就必須先設定這個元件的用途。共有以下三種選擇：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiB1b4D-PavPmg3IOTfEmoUGJh_Nf7ilvGnDbhOKfJI8yUesx7JwfNss3RqOcJz4Eq1iwcqft1dY4ViiD3ZwE1l7anr49MDY8OzdCR-f37_Ju-4ww0wpAFFIaym-D1DU2Rbv3VLSz2I5PU/s0/ssis-script-02.png)

| 元件類型 | 說明 | 輸入 | 輸出 |
| --- | --- | --- | --- |
| 來源 | 指令碼元件可支援多個輸出。 | X | 多個 |
| 目的地 | 指令碼元件可支援一個輸入。 | 一個 | X |
| 轉換 | 指令碼元件可支援一個輸入和多個輸出。 | 一個 | 多個 |

其實，這個設定動作的目的，只是預設幫你建立適當的程式碼，你可以在編輯器中，自行再去增減輸入輸出的數量。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj5PpJGV9aiEmjLwlaGFZnskuc4H6-q4HfP8bmRX40nE8HcvdqIt2fHsdSzQcUypCxDTIXlAVsTDJIviRrJZa-_duhIo2Nw5dlp42JX1wkIyp3UyBwMIvj7zqzQUEixhJFwBO2tfWsq0dw/s0/ssis-script-03.png)

### 設定輸入

你可以在輸入頁面中，設定輸入欄位。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8iEGIytC7i4RXPnMODMkK08pIvJBrQtbbt1NskjmWZEbc-SyErVS4vyQnh0L2wgfATTHQ5zar_S1pEvxEhb9AsAVNDbpXtubY8bGtm8sG09IlCG0PgaVSSxzHjo-H8eamzskYNdxN7TE/s0/ssis-script-09.png)

### 設定輸出

你可以在輸出頁面中，設定輸出欄位。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh_Rd5rhOmQrYuzXDy3Bam-PjjYFDzGWtILOJzQ1SSDZE2fnJFKttG07fVHwz4wF9aHb6zPaTPFYtQ-IZCrBhC03ZPasag08Y2r8YmcojKU7HDhYbTTB6LupiqmfNMUO0EveNGBkjU2uVU/s0/ssis-script-05.png)

### 同步與非同步輸出（Sync or Async ）

當你的 [Script Component](http://technet.microsoft.com/en-us/library/ms137640.aspx) 包含多個輸出時，你可以將他們設定成**同步**或**非同步**輸出。  此處的同步與非同步指的是輸出是否與輸入同步，詳細說明如下：  

#### 同步輸出

- 當要處理的資料列彼此沒有相關連，你就可以採用**同步輸出**。
- **同步輸出**模式，每次讀取一列，處理完後，就立即輸出一筆。
- 要使用**同步輸出**，你必須將 **同步輸入 ID（SynchronousInputID）** 這個屬性值指定為同一個元件中的那個輸入的 ID （因為輸入也只有一個，你也無法隨便輸入，你可以在下拉選單中找到）。
- 在同步輸出模式下，所有的輸入資料列都會導向至所有可用的輸出。
- 不過，你可以在同步輸出模式下設定 **互斥群組（ExclusionGroup）** 屬性，就可以在程式碼中，指定特定的資料列進入到特定的輸出。   **ExclusionGroup** 值沒有特別要求用什麼值，只要是相同的非零值即可，表示這些輸出彼此是同一個互斥群組。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi-cEI_iYX2m8vp-nc9ZXkNTkkR_28CxChCEUSyliricjJ7nvY0mIzWCgh8rx6TwYIE2Y3okuJ18RJTpDkHVKIk2UObBWouWT6rdHfPXmc8UCElzN7QTQRaIEiZpPmZ7qat2Jww5BNMY6I/s850/ssis-script-11.png)

在程式碼中，指定特定的資料列進入到特定的輸出：
```c#
public override void 輸入0_ProcessInputRow(輸入0Buffer Row)
{
	if (Row.married == true)
	{
		Row.DirectRowTo輸出0();
	}
	else if (Row.carOwned == true)
	{
		Row.DirectRowTo輸出1();
	}
	else if (Row.houseOwned == true)
	{
		Row.DirectRowTo輸出2();
	}
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj__1ZHOAq1wkz_XZbYLxXOte5K0xkSxYPM8Xa3iMca8Q59osYjlp_BqSO8SyTSaHCECsu6EfnvLi1-dfx11GrU7D5aC_k1H8YKBzLrmdd9_Md9QvDyrKhBwPasL6SPvIQKY4UmnxbpBxc/s0/ssis-script-12.png)

#### 非同步輸出

- 當要處理的資料列彼此有相關連，你就必須採用**非同步輸出**。  例如下列情形：
- 輸出Ａ與輸出Ｂ有先後關係的必要需求.
- 某個輸出要等所有資料都讀取完，做完排序或合併相關資訊才能輸出。
- 因為要執行彙總，所以要等所有輸出都讀取完畢。
-
- 使用**非同步輸出**，你必須將 **SynchronousInputID** 屬性值設定成「無(None)」
- 在**非同步輸出**模式下，你還可以設定輸出是否使用排序。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhMsrRwKiU4GrRHorbV5DUHD934S3N4__J7p5LyebdnwlYnyulToR8STg7Y2-fghKv-uPU5IDgypp5F4pRAkWHOP5MymhthuUaPQtXvQMOyF5c588u30pt-a4Ge9nGvajNKGurSTWhpsHI/s0/ssis-script-04.png)

- If the value of the **SynchronousInputID** property is **None**, then the output is       **asynchronous**.
- If you use asynchronous outputs, then you can also define whether the output is       **sorted**.
- If the value of the **SynchronousInputID** property is the **component's input ID**, then the output is      **synchronous**.
- If you use synchronous outputs, then you can also configure the **ExclusionGroup** property to identify redirections of rows to different outputs.   
For example, you could redirect part of the rows to the regular output and part to the error output.

## Coding the Script Component

### 指令碼專案中的項目：

當你開啟 VSTA 編輯器時，它會根據以上的設定自動產生基礎的程式碼架構，包含以下三個檔案：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjZ7Axi6wAvCgvoLMCf7f6rQw6lB11PUD_eO8JWkaX-QXqGLbTpKa3teQG6mWSY9VbvclQoqsxf4g45SjCbZShALPq89sQk8wL4zRktTPZEIKpknQPYrTU1gYbU1vfuGYXas85o2N2n7j0/s0/ssis-script-10.png)

#### Main.cs

這個檔案包含一個 ScriptMain 類別，繼承自以下的 UserComponent 類別。  它是主程式部份，讓你撰寫程式用的。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4PINhKLFCceNJxcHYkRKp-W5Uh8d6c2Y27EDDURrK6lizmtQIv97PYGXaWn7b4nKAsJ7YuvHGBbaLSQUYJ1BThikehHAeTbRAErw9ZL0YqkjXqxdVJcG_Buekqg3rXBnaENLQxA7ior8/s0/ssis-script-08.png)

#### ComponentWrapper.cs

這個檔案包含一個 **UserComponent** 類別，繼承自 ScriptComponent 類別。  該類別包含了用來處理資料時會使用到的 Method 和 Property 。  這個檔案也包含了**變數**和**連線**的類別，提供你存取封裝本身的資訊。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvCa3640xAlCk7WUmDTTY2GF6w2F4L3jKwDEagfRU4RWRoeD-VOZh7gRcPzJamT8kwMn7SwZKVeQQLOL4Br9AJPWvBLzWd9Jq1cDVEAJvVl4sPUg7xts6et3eN1BB1L9zogfDBXadeQH4/s850/ssis-script-06.png)

#### BufferWrapper.cs

這個檔案包含所有的**輸入**和**輸出**。  這些類別都繼承自 ScriptBuffer 類別，類別的屬性就是輸入和輸出的**欄位**。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQ1IsU_oLZZpI_g6OxjFPtq2xx89CWweAFlhyphenhyphencHcW3f8DwyOccumkn89BiW7vGKONEtHj_Miyjl6NtWCTa3eOrdg-4Lv0ICI51EdTc5u0J6FyQdH0ZxbxksxwzpHF1mCeSHgUKDKdAjtE/s0/ssis-script-07.png)

## 執行階段的方法（run-time method）

Custom Component 和 Script Component 是一樣的，都包含相同的執行階段方法（run-time method）。  下列清單是幾項比較重要的方法：  

- **AcquireConnections**：  
Use the AcquireConnections method to retrieve the connection
- **PreExecute**：  
If you need to perform an action once before processing the rows, override the PreExecute method of the ScriptComponent base class.
- **PostExecute**：  
If you need to perform an action once after processing the rows, override the PostExecute method of the ScriptComponent base class.
- **\<InputX\>_ProcessInputRow**：  
write the processing code here to process each row of an input.

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4PINhKLFCceNJxcHYkRKp-W5Uh8d6c2Y27EDDURrK6lizmtQIv97PYGXaWn7b4nKAsJ7YuvHGBbaLSQUYJ1BThikehHAeTbRAErw9ZL0YqkjXqxdVJcG_Buekqg3rXBnaENLQxA7ior8/s0/ssis-script-08.png)

# Custom Components

自訂元件最主要的目的就是為了重複使用(resuability)。要建構一個這樣子的元件，大至分成以下三個階段：  

## 一、設計


## 二、部署

要部署自訂元件，必須先將組件複制到 SSIS Task 或者 Component 目錄，然後再使用 **gacutil** 工具程式，將其登錄到 **GAC** 。  

Task 的預設目錄為：

- 32-bit edition ： %ProgramFiles(x86)%\Microsoft SQL Server\110\DTS\Tasks
- 64-bit edition ： %ProgramFiles%\Microsoft SQL Server\110\DTS\Tasks

Component 的預設目錄為：

- 32-bit edition ： %ProgramFiles(x86)%\Microsoft SQL Server\110\DTS\PipelineComponents
- 64-bit edition ： %ProgramFiles%\Microsoft SQL Server\110\DTS\PipelineComponents

## 三、使用

## 參考資料  

- [指令碼工作](http://technet.microsoft.com/zh-tw/library/ms141752.aspx)
- [指令碼元件](http://technet.microsoft.com/zh-tw/library/ms137640.aspx)
- [建立自訂工作（Custom Task）](http://technet.microsoft.com/zh-tw/library/ms345156.aspx)
- [開發自訂資料流程元件](http://technet.microsoft.com/zh-tw/library/ms136078.aspx)
- [了解 Script Component 的同步和非同步轉換](http://technet.microsoft.com/zh-tw/library/aa337074.aspx)
- [Design-time Methods of a Data Flow Component](http://msdn.microsoft.com/en-us/library/ms135969)
-