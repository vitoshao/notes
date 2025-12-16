---
title: 使用 Unmanaged 程式碼
layout: default
parent: Interoperation with COM
nav_order: 1
description: "使用 Unmanaged 程式碼"
date: 2012-05-18
tags: [DotNetFramework,Interoperation with COM]
postid: "6440107889660961674"
---
除了舊有的 COM 元件是 Unmanaged 程式碼之外，在 Windows 系統底下還有許多的 Windows APIs 是 .NET Framework 沒有包裝的。  這個章節就是要來認識如何叫用 Windows APIs.  

# 平台叫用 ( P/Invoke )

在 .Net 習慣用語中, 從 Managed 程式碼中呼叫 Unmanaged 程式碼的動作稱為 Platform Invoke (平台叫用), 簡稱為 P/Invoke。

## 如何使用 P/Invoke

#### 一般來說，我們要使用 P/Invoke 呼叫 Windows API 有下面幾個重要步驟：

- 1. 加入 **System.Runtime.InteropServices** 命名空間。
- 2. 宣告一個**靜態**的**外部**方法，並以要呼叫的那個 API 函式名稱命名。  
這個步驟中，將方法宣告成靜態 (static/shared) 是必然的，  而宣告成外部 (external) 則是告訴編譯器，該方法並非存在於內部，必須在指定的那個 DLL 中找到。  若這個方法有參數的話，也必須在這個宣告中轉換成對應於 C# 當中的型態。  另外，若想以自訂名稱命名也可，不過，要先指明進入點(EntryPoint)這個屬性即可。例如：[DllImport("user32.dll"), EntryPoint="GetForegroundWindow"]
- 3. 替這個方法加上 **DllImport** 屬性，指明該函式是在哪個動態連結函式庫中 ( DLL )。
- 4. 在程式碼中，呼叫這個方法。

我們拿 user32.dll 中的 GetForegroundWindow , GetWindowText 函式來看看。以下是 API 的原型宣告：
```c#
HWND WINAPI GetForegroundWindow(void);          //The return value is a handle to the foreground window. 

int WINAPI GetWindowText(                       //The return value is the length of WindowText
__in   HWND hWnd,
__out  LPTSTR lpString,
__in   int nMaxCount
);
```

#### 在 C# 中要如何宣告對應的方法呢？請看下面示範：
```c#
//下面這段宣告表示
//程式中若呼叫 GetForegroundWindow 方法，應該要叫用 user32.dll 中的 GetForegroundWindow 函式

[DllImport("user32.dll")]
private static extern IntPtr GetForegroundWindow();

//下面這段宣告表示
//程式中若呼叫 GetWindowText 方法，應該要叫用 user32.dll 中的 GetWindowText 函式

[DllImport("user32.dll")]
private static extern Int32 GetWindowText(IntPtr hWnd,StringBuilder textValue, Int32 counter);
```

#### 在 C# 中要如何呼叫這個外部方法呢？請看下面示範：
```c#
//取得作用中視窗的 handle 
StringBuilder DemoBuilder = new StringBuilder(BufferSize);
IntPtr DemoHandle = GetForegroundWindow();

//取得視窗標題中的文字
if (GetWindowText(DemoHandle, DemoBuilder, BufferSize) > 0)
{
Console.WriteLine(DemoBuilder.ToString());
}
```

#### API查詢：

- [Microsoft Win32 to Microsoft .NET Framework API Map](http://msdn.microsoft.com/en-us/library/aa302340.aspx)
- [PInvoke.NET](http://pinvoke.net/index.aspx)
- [Windows API Reference for C#, VB.NET and VB6](http://www.webtropy.com/articles/Win32-API-DllImport-art9.asp)

## 如何使用包裝類別 (Wrapper Class)

這裏指的包裝類別是指將常用的 DLL 函式包裝在 Managed 類別中。  這只是一種封裝平台功能的方法，在叫用 P/Invoke 上，並沒有強制一定得這麼做，只是讓叫用 P/Invoke 方法時，變得比較方便。  

如下面範例，我們建立了一個封裝類別，並在該類別中指定叫用的 DLL，同時宣告一個靜態方法以對應到 DLL 中的方法。  這樣子，當程式叫用 Win32MessageBox.Show 方法時，就可以無需理會實際上叫用的 Unmanaged Function。  
```c#
class Win32MessageBox
{
[DllImport("user32.dll")]
private static extern int MessageBox(IntPtr hWnd, String text, String caption, uint type);

public static void Show(string message, string caption)
{
MessageBox(new IntPtr(0), message, caption, 0);
}
}

private void button3_Click(object sender, EventArgs e)
{
Win32MessageBox.Show("Hello, world!", "My MessageBox");
}
```

# 封送資料 (marshal)

因為 Managed 和 Unmanaged 程式碼的資料型別不相同，如果二者在互通時，必須傳遞資料，這時候就必須做型別轉換，這個轉換過程就稱為封送資料。  大部分的資料型別都有其預設的封送處理行為，所以並不一定需要手動轉換。  通常只有在某個型別可以封送多個型別，又必須特別指定時才必須手動處理。  

以下是二種不同情狀下的使用方法：

- [MarshalAs](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.marshalasattribute.aspx) ：指示如何在 Managed 和 Unmanaged 程式碼之間封送處理資料。
- [StructLayout](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.structlayoutattribute.aspx) ：控制類別或結構之資料欄位的實際配置。

## 使用 MarshalAs 特性

[MarshalAs](http://msdn.microsoft.com/zh-tw/library/t167a1e9.aspx) 屬性是用來指示如何在 Managed 和 Unmanaged 程式碼之間封送處理資料(marshal the data)。.  這個屬性可套用在參數、欄位或傳回值。  這個屬性是選擇性 (Optional) 的，因為每個資料型別都有預設的封送處理行為。通常只有在指定的型別可以封送處理至多個型別時，才需要這個屬性。  例如，您 String 可以封送處理成 LPStr、LPWStr、LPTStr、BStr 至 Unmanaged 程式碼。  依預設，Common Language Runtime 封送處理 String 參數為 BStr 至 COM 方法。  您可以套用 [MarshalAs](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.marshalasattribute.aspx) 屬性至個別欄位或參數，讓該特定字串封送處理為 LPStr 而不是 BStr。  例如：
```c#
[DllImport("msvcrt.dll")]
public static extern int puts([MarshalAs(UnmanagedType.LPStr)] string m);

class MarshalAsDemo
{
[MarshalAs(UnmanagedType.LPStr)]
public String FirstName;

[MarshalAs(UnmanagedType.Bool)]
public Boolean IsCurrentlyWorking;

public String LastName( [MarshalAs(UnmanagedType.LPStr)] String lastName);

}
```

## 使用 StructLayoutAttribute 特性

在 Unmanaged 程式碼中，常常會使用結構來當做參數傳遞，所以當使用 P/Invoke 時，若要傳遞結構給 Unmanaged 程式碼，就得指明結構的實體配置才行。  而 [StructLayout](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.structlayoutattribute) 和 [FieldOffset](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.fieldoffsetattribute) 就是用來說明結構的配置屬性。  

[StructLayout](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.structlayoutattribute) 屬性的功能在於設定類別或結構在記憶體中的排列方式，通常，在 Managed 程式碼中，CLR 會自動控管記憶體中類別或結構之資料欄位的實際配置。  如果類別或結構需要以某種方式排列，便可以使用 [StructLayout](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.structlayoutattribute) 屬性加以設定。  在預設的情況下，編譯器會依據類別或結構中各成員的大小做最佳化排列。  但是若該類別或結構用於 P/Invoke 情況以當作參數轉呼叫 DLL 動態連接函式庫時，則請務必設定 [StructLayout](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.structlayoutattribute) 屬性，避免影響轉呼叫外部 UnManaged 程式碼時因類別或結構記憶體位置錯置而導致非預期的結果。  

#### 建構 [StructLayout](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.structlayoutattribute) 時，可以有３種設定值

- LayoutKind.Auto ：預設值，由 CLR 自動配置。
- LayoutKind.Sequential ：依序配置，其順序即是將它們匯出至 Unmanaged 記憶體時所出現的順序。
- LayoutKind.Explicit ：在 Unmanaged 記憶體中的物件，明確地指定其成員的配置順序。每個成員必須使用 FieldOffsetAttribute，表示該欄位在型別中的位置。

####  LayoutKind.Sequential 

使用這個列舉成員定義類別或結構，其成員會依據定義的順序在記憶體中排列。  以下面的例子中，便會以先 x 後 y 的順序排列。  
```c#
[StructLayout(LayoutKind.Sequential, CharSet = CharSet.Unicode)]
public struct MyStruct
{
[MarshalAs(UnmanagedType.ByValTStr, SizeConst = 4)]    
public string fname;    
[MarshalAs(UnmanagedType.ByValTStr, SizeConst = 4)] 
public string lname;    
[MarshalAs(UnmanagedType.ByValTStr, SizeConst = 7)] 
public string phone;
}   
```

####  LayoutKind.Explicit 

使用這個列舉成員定義類別或結構，每個成員必須使用 FieldoffsetAttribute ，表示該欄位在型別中的記憶體相對位置。  如此程式設計師可以精確的掌控所有欄位於記憶體中的儲存順序。  
```c#
StructLayout(LayoutKind.Explicit)]
public struct Rect 
{
[FieldOffset(0)] public int left;
[FieldOffset(4)] public int top;
[FieldOffset(8)] public int right;
[FieldOffset(12)] public int bottom;
}   
```

## 其他封送資料時的屬性控制

- [ComVisible](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.comvisibleattribute.aspx) ：設定 Managed 程式碼中的型別或成員，在 COM 中是否可見。
- [In](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.inattribute.aspx) ：資料只會由 caller 封送處理到 callee ，不會封送處理回 caller。
- [Out](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.outattribute.aspx) ：資料由 caller 封送處理到 callee ，會再由 callee 封送處理回 caller。
- [AutomationProxy](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.automationproxyattribute.aspx) ：whether the type should be marshaled using the Automation marshaler or a custom proxy and stub.
- [ClassInterface](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.classinterfaceattribute.aspx) ：這個屬性會控制型別程式庫匯出工具 (Tlbexp.exe) 是否會自動為屬性類別產生類別介面。
- [ComRegisterFunction](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.comregisterfunctionattribute.aspx) ：
- [ComSourceInterfaces](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.comsourceinterfacesattribute.aspx) ：

## Guidelines for Exposing .NET Types to COM Applications

- 類別不可 abstract
- 成員必須 public
- 類別必須具有 default constructor
- Define event-source interfaces in managed code.
- Include HRESULT error codes in custom exception classes.
- Supply GUIDs for types that require them.

## 範例一

底下範例，示範如何使用 API GetVersionEx 以取得系統的版本資訊。
```c#
[DllImport("Kernel32.dll")]
public static extern bool GetVersionEx(ref OSVERSIONINFOEX osvi);

[StructLayout(LayoutKind.Sequential)]
public struct OSVERSIONINFOEX                  //重新定義屬於 C# 的 OSVERSIONINFOEX  結構。
{
public Int32 dwOSVersionInfoSize;
public Int32 dwMajorVersion;
public Int32 dwMinorVersion;
public Int32 dwBuildNumber;
public Int32 dwPlatformId;
[MarshalAs(UnmanagedType.ByValTStr, SizeConst = 128)]
public String szCSDVersion;
public Int16 wServicePackMajor;
public Int16 wServicePackMinor;
public Int16 wSuiteMask;
public Byte wProductType;
public Byte wReserved;
}

private void button5_Click(object sender, EventArgs e)
{
OSVERSIONINFOEX osvi = new OSVERSIONINFOEX();
osvi.dwOSVersionInfoSize = Marshal.SizeOf(typeof(OSVERSIONINFOEX));
bool bGetVersionEx = GetVersionEx(ref osvi);
if (bGetVersionEx)
{
Console.WriteLine("MajorVersion : {0}", osvi.dwMajorVersion);
Console.WriteLine("MinorVersion : {0}", osvi.dwMinorVersion);
Console.WriteLine("BuildNumber : {0}", osvi.dwBuildNumber);
Console.WriteLine("CSDVersion : {0}", osvi.szCSDVersion);
}
Console.WriteLine("OSVersion : {0}", Environment.OSVersion);
}
```

## 範例二

底下範例，示範如何由程式Ａ，尋找程式Ｂ中的某個按鈕，並按下Click事件，且自動關閉訊息視窗。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhYJwLZP2_Ot6cyJx1tZpWvYoxSuJ1iI3vZ3b3rXv_KLfJmbV93YU789LWho4qAiCRZPSrb-Oa4cbnqJffb3zVIzYXGmjGFcWno5MGNOFsNNYTusmyanuilHfP1waPDcoIzt-z20HED_TY/s499/p-invoke02.png)![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiXnEaUlhw_gfEM9HqPUB9akmMf9Kvj4PyfkKHok1krkxVyEHLJZjiwYz2btQLLC5Fymwob488mCi3pxHFGIA0lJqV2F6jAuQqYnpCBLS52N1ZblLVjZMBqQS60VegwBnZKVMJ_UBp3M1o/s395/p-invoke01.png)
```c#
[DllImport("USER32.DLL")]
public static extern IntPtr FindWindow(string lpClassName, string lpWindowName);

[DllImport("USER32.DLL")]
public static extern bool SetForegroundWindow(IntPtr hWnd);

[DllImport("user32.dll")]
static extern IntPtr FindWindowEx(IntPtr hwndParent, int hwndChildAfter, string lpszClass, string lpszWindow);

[DllImport("user32.dll")]
private static extern IntPtr PostMessage(IntPtr hwnd, int wMsg, IntPtr wParam, IntPtr lParam);

[DllImport("user32.dll", EntryPoint = "SendMessageA")]
private static extern int SendMessage(IntPtr hwnd, int wMsg, int wParam, IntPtr lParam);

private void button8_Click(object sender, EventArgs e)
{
string sWindowCaption = txtWindowCaption.Text;
string sButtonText = txtButtonText.Text;
string sClassName = txtClassName.Text;     

// 取得 視窗 的 handle
IntPtr hWnd = FindWindow(null, sWindowCaption);

// 取得 Button 的 handle
IntPtr hButton = FindWindowEx(hWnd, 0, sClassName, sButtonText);

if (!hButton.Equals(IntPtr.Zero))
{
// 設為 active windows
SetForegroundWindow(hWnd);

// invoke button click
PostMessage(hButton, BM_CLICK, IntPtr.Zero, IntPtr.Zero);

Thread.Sleep(2000);

// 取得訊息視窗的 handle
IntPtr handle = FindWindow("#32770", null);

// 關閉訊息視窗
SendMessage(new HandleRef(null, handle), WM_CLOSE, IntPtr.Zero, IntPtr.Zero);
}
}
```
```c#
private void btnTest_Click(object sender, EventArgs e)
{
MessageBox.Show("hello");
}
```

# 如何設計回呼方法

傳統上，回呼方法都是以指標方式來設計的。所以我們必須提供一個方法以便接收 Unmanaged 方法的回傳。  需要使用回呼的方法，通常會帶有以 lp- 開頭 (long pointer)，-Func 結尾 (function) 的參數。例如：  
```c#
BOOL EnumWindows(WNDENUMPROC lpEnumFunc, LPARAM lParam)
```

下列步驟是處理需要回呼方法的 P/Invoke  

1. 建立處理回呼的方法。
2. 替這個方法建立一個委派宣告。
3. 宣告函式的原型，並以委派為參數。
4. 呼叫函式。

下面範例示範如何呼叫 EnumWindows 函式
```c#
BOOL WINAPI EnumWindows(
__in  WNDENUMPROC lpEnumFunc,       // A pointer to an application-defined callback function. For more information, see EnumWindowsProc. 
__in  LPARAM lParam
);

BOOL CALLBACK EnumWindowsProc(
__in  HWND hwnd,                    // A handle to a top-level window. 
__in  LPARAM lParam
);
```
```c#
public delegate bool CallBackFunc(int hwnd, int lParam);

[DllImport("user32")]
public static extern int EnumWindows(CallBackFunc callback, int lParam);

public static bool OutputWindowsHandle(int hwnd, int lParam)
{
Console.WriteLine("Window handle is " + hwnd);
return true;
}

private void button6_Click(object sender, EventArgs e)
{
EnumWindows(OutputWindowsHandle, 0);
}
```

# Exception Handling in Managed Code

HRESULT 是一個 32 位元值，它劃分為三個不同欄位：嚴重性代碼(severity code)、設備代碼(facility code)、錯誤碼(error code)。  當 Managed 程式碼擲回例外狀況時，Runtime 會將 HRESULT 傳遞至 COM 用戶端。  當 Unmanaged 程式碼傳回錯誤時，HRESULT 會轉換成 Runtime 接著將擲回的例外狀況。  

當 COM 的方法發生錯誤時，就會回傳一個 HRESULT ；但是 .NET 的方法發生錯誤，則是擲回 exceptions。  因此在 Interoperation 作業時，這兩者之間的轉換則由 CLR 負責。.NET Framework 中的每一個例外狀況類別都會對應到一個 HRESULT。  

如果我們自行定義一個例外狀況，也是可以自行指定一個適當的 HRESULT ，這樣子 COM 方法在使用時才有辨法回傳適當的 HRESULT。  

您可以在例外狀況的建構函式 (Constructor) 中提供 HRESULT 值，將自行定義的例外狀況類別對應到指定的 HRESULT。  如果沒有指定，基底類別會指派 HRESULT 值。  
```c#
public class NoAccessException : ApplicationException
{
public NoAccessException(string msg) : base(msg)
{
HResult = 12;
}
}
```

一般來說，呼叫 API 後，只會得到一個 true/false 表示呼叫結果是否有錯誤發生，必須另外再使用 [Marshal.GetLastWin32Error](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.marshal.getlastwin32error) 方法以取得錯誤碼。例如：  
```c#
bool returnValue = LogonUser(userName, domainName, password, LOGON32_LOGON_INTERACTIVE, LOGON32_PROVIDER_DEFAULT, out impersonatedToken);

// 檢查認證是否正確
if ( returnValue == false )
{
int ret = Marshal.GetLastWin32Error();
Console.WriteLine("LogonUser failed with error code : {0}", ret);
throw new System.ComponentModel.Win32Exception(ret);
}
```

# Limitations of Unmanaged Code

- Performance
- Type safety
- Code security
- Versioning

# Marshal 類別

這個類別提供許多靜態方法，用來配置 Unmanaged 記憶體、複製 Unmanaged 記憶體區塊和將 Managed 型別轉換為 Unmanaged 型別，也包括其他和 Unmanaged 程式碼互動時使用的方法。例如：  

- [GetLastWin32Error](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.marshal.getlastwin32error) ：傳回使用平台叫用來呼叫的最後 Unmanaged 函式所傳回的錯誤碼。
- [SizeOf](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.marshal.sizeof) ：傳回類別的 Unmanaged 大小
- [Copy](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.marshal.copy) ：Copies data between a managed array and an unmanaged memory pointer。
- [ReadByte](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.marshal.readbyte),   ReadInt16,  ReadInt32,  ReadInt64,  and ReadIntPtr ：Reads values from unmanaged memory。
- [WriteByte](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.marshal.writebyte), WriteInt16, WriteInt32, WriteInt64, and WriteIntPtr：Writes values to unmanaged memory。
- [GetExceptionForHR](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.marshal.getexceptionforhr.aspx) ：將 HRESULT 錯誤碼轉換成對應的 [Exception](http://msdn.microsoft.com/zh-tw/library/system.exception.aspx) 物件。
- [ThrowExceptionForHR](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.marshal.throwexceptionforhr.aspx) ：擲回具有特定錯誤 HRESULT 值的例外狀況。

另一個有用的方法是 [Marshal.SizeOf](http://msdn.microsoft.com/zh-tw/library/system.runtime.interopservices.marshal.sizeof) ，它可以用來取得一個類別或物件的大小。  例如：  
```c#
Console.WriteLine(Marshal.SizeOf(typeof(Point)));
Point p = new Point();
Console.WriteLine(Marshal.SizeOf(p));
```

#### 表: P/Invoke 資料型態轉換對應表：

| Wtypes.h 中的 Unmanaged 型別 | Unmanaged C 語言型別 | Managed 類別名稱 | 說明 |
| --- | --- | --- | --- |
| HANALE | void\* | System.IntPtr | 32 位元 Windows 作業系統上為 32 位元。&lt;br&gt;              &lt;br&gt;                  64 位元 Windows 作業系統上為 64位元。 |
| BYTE | unsigned char | System.Byte | 8 位元。 |
| SHORT | short | System.Int16 | 16 位元。 |
| WORD | unsigned short | System.UInt16 | 16 位元。 |
| INT | int | System.Int32 | 32 位元。 |
| UINT | unsigned int | System.UInt32 | 32 位元。 |
| LONG | long | System.Int32 | 32 位元。 |
| BOOL | long | System.Int32 | 32 位元。 |
| DWORD | unsigned long | System.UInt32 | 32 位元。 |
| ULONG | unsigned long | System.UInt32 | 32 位元。 |
| CHAR | char | System.Char | 以 ANSI 修飾。 |
| LPSTR | char\* | System.String 或&lt;br&gt;              &lt;br&gt;                  System.Text.StringBuilder | 以 ANSI 修飾。 |
| LPCSTR | Const char\* | System.String 或&lt;br&gt;              &lt;br&gt;                  System.Text.StringBuilder | 以 ANSI 修飾。 |
| LPWSTR | wchar\_t\* | System.String 或&lt;br&gt;              &lt;br&gt;                  System.Text.StringBuilder | 以 ANSI 修飾。 |
| LPCWSTR | Const wchar\_t\* | System.String 或&lt;br&gt;              &lt;br&gt;                  System.Text.StringBuilder | 以 ANSI 修飾。 |
| FLOAT | Float | System.Single | 32 位元。 |
| DOUBLE | Double | System.Double | 64 位元。 |

## 參考資料  

- [互通性概觀 (C# 程式設計手冊)](http://msdn.microsoft.com/zh-tw/library/ms173185.aspx)
- [與 Unmanaged 程式碼互通](http://msdn.microsoft.com/zh-tw/library/sd10k43k)
-