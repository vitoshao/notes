---
title: Form 三二事
layout: default
parent: WinForm
nav_order: 5
description: "Form 三二事"
date: 2015-08-28
tags: [DotNetFramework,WinForm]
postid: "4817118975264442855"
---
在 WinForm 表單設計中，常會使用到表單來傳值。  例如： FormA 開啟 FormB 後，想傳遞資料給 FormB ，或者 FormA 開啟 FormB 後，經過使用者的操作後， FormB 要將資料傳回 FormA 。  底下是這些相關的操作的備忘錄：  

## Form.Show() VS. Form.ShowDialog()

這二個方法都可以用來開啟表單，主要差別在於：  

- [Form.Show](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.show.aspx) ：以非強制回應表單(modaless)的方式顯示表單。
- [Form.ShowDialog](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.showdialog.aspx) ：以強制回應對話方塊(modal dialog)的方式顯示表單。也就是呼叫這個方法時，它隨後的程式碼都必須等到對話方塊關閉之後才會執行。

####  [DialogResult](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.dialogresult.aspx) 屬性：

[DialogResult](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.dialogresult.aspx) 屬性值，是關閉強制回應對話視窗時，要回應給父表單的狀態訊息。  你可以直接指定 [Form.DialogResult](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.dialogresult.aspx) 屬性值，以指定回應狀態。  或者對子視窗中的特定 Button ，設定其 [DialogResult](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.dialogresult.aspx) 屬性的值，用以表示要回傳給父表單的 [DialogResult](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.dialogresult.aspx) 屬性值。  當這些 Button 被 Click 之後，就會自動變更 [Form.DialogResult](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.dialogresult.aspx) 屬性。  
```c#
public FormTestB()
{
InitializeComponent();

//對視窗中的特定 Button 設定其 DialogResult 屬性的值
bnYes.DialogResult = DialogResult.Yes;
bnNo.DialogResult = DialogResult.No;
bnCancel.DialogResult = DialogResult.Cancel; 
}
```

### Dialog 視窗關閉時，執行個體不會結束

使用 [ShowDialog](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.showdialog.aspx) 與 [Show](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.show.aspx) 開啟表單時，還有一個最大的差異點在於關閉表單時。  在非強制回應表單中，若使用者按了右上角的Ｘ關閉表單，子表單執行個體會被 Close 結束掉；  若在強制回應表單中，若使用者按了右上角的Ｘ關閉表單，子表單只會被 Hidden ，在父視窗中，你可以隨時再叫用該執行個體的 [ShowDialog](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.showdialog.aspx) 方法來顯示子表單。  除非你不再使用到該子表單，你可以叫用 [Form.Dispose](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.dispose.aspx) 結束它的生命。  
```c#
private void bnShow_Click(object sender, EventArgs e)
{
FormTestB formB = new FormTestB(textBox1.Text);
formB.Show();
}

private void bnShowDialog_Click(object sender, EventArgs e)
{
FormTestB formB = new FormTestB(textBox1.Text);

DialogResult result1 = formB.ShowDialog(this);
Console.WriteLine(result1.ToString());

DialogResult result2 = formB.ShowDialog(this);　//without creating a new instance 
Console.WriteLine(result2.ToString());

formB.Dispose();    //no longer needed by your application.
}
```

### Dialog 視窗如何關掉，並回傳訊息

若打算關掉 Dialog 視窗，你可以叫用 Close() 方法來關掉強制回應表單，並且設定 DialogResult 屬性，以便告知主視窗強制回應表單的狀態。  
```c#
private void bnShowDialog_Click(object sender, EventArgs e)
{
FormTestB formB = new FormTestB();

if (formB.ShowDialog(this) == DialogResult.Yes)
{
this.textBox1.Text = formB.Tag.ToString();
}

formB.Dispose();
}
```
```c#
private void bnYes2_Click(object sender, EventArgs e)
{
this.Tag = textBox1.Text;
this.DialogResult = DialogResult.Yes;
this.Close();
}
```

### 如何判斷目前表單是哪一種顯示方式

當 FormB 被顯示時，你可以在 FormB 中，直接由 [Form.Modal](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.modal.aspx) 屬性判斷目前視窗是哪一種開啟方式。  不過這個屬性值必須要等到 Form\_Load 事件發生時才會有效，太早判斷會沒有意義，例如在 [Form](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.aspx) 的建構式中。  
```c#
public FormB()
{
InitializeComponent();

if (this.Modal) { }  //Modal值尚未設定
}
private void FormB_Load(object sender, EventArgs e)
{
if (this.Modal)
{
MessageBox.Show("Modal Form");
}
else
{
MessageBox.Show("Modaless  Form");
}
}
```

## 從 FormA 如何存取 FormB 中的資訊

### 透過 Constructor

若 FormA 開啟 FormB 的時候，想要傳遞資料給 FormB ，最簡單的方法就是直接由 Constructor 的參數來傳遞。
```c#
public FormTestB()
{
InitializeComponent();

//對視窗中的特定 Button 設定其 DialogResult 屬性的值
bnYes.DialogResult = DialogResult.Yes;
bnNo.DialogResult = DialogResult.No;
bnCancel.DialogResult = DialogResult.Cancel; 
}

//新增一個含有參數的 Constructor
public FormTestB(string para1) :this()
{
textBox1.Text = para1;
}
```
```c#
private void bnShow_Click(object sender, EventArgs e)
{
FormTestB formB = new FormTestB(this.textBox1.Text);
formB.Show();
}
```

### 透過公開方法或屬性

若 FormA 想要存取 FormB 上控制項的屬性值，你可以在 FormB 中自訂公開的方法或屬性來存取。  
```c#
public void SetTextBox1(string txt)
{
textBox1.Text = txt;
}

public string TextBox1Text
{
get{
return textBox1.Text;
}
set{
textBox1.Text = value;
}
}
```
```c#
private void bnShow2_Click(object sender, EventArgs e)
{
FormTestB formB = new FormTestB();

//透過 public method 存取
formB.SetTextBox1(this.textBox1.Text);

//透過 public property 存取
formB.TextBox1Text = this.textBox1.Text;

formB.Show();
}
```

### 變更 FormB 控制項的 Modifier

若要存取 FormB 上控制項的屬性值，另外一個做法就是直接變更 FormB 上的控制項的 [Modifier](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.control.modifier.aspx) 屬性為 internal 或 public ，那麼在 FormA 中就可以直接存取 FormB 中的控制項屬性。  這個 [Modifier](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.control.modifier.aspx) 屬性值，在 C# 中，預設是 private ，所以必須變更為 internal 或 public 後，才可以由 FormA 中直接存取。  但是在 Vb.NET 中，預設是 Friend ，所以可以不必修改就可以直接讀取。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi3Dzv1pOoBb-wSdq17RKtMnjNlajZIgMPtR6LGqXrjiwwjFrkoe8pliqCJy4iFTMLI8f-ui1TNvc8LccAioCymcADNyqnAi9M3oHr6edn5i6Zs4Lx7Pf4tO6_FX7MucZE2xMTmdhNNhNg/s250/control-modifier.png)

設定好**modifier**就可以由 A 表單中直接讀取 B 表單中的控制項的值。
```c#
private void bnShowDialog_Click(object sender, EventArgs e)
{
FormTestB formB = new FormTestB(textBox1.Text);
if (result1 == DialogResult.Yes)
{
this.textBox1.Text = formB.textBox1.Text;
}
formB.Dispose();
}
```

## 從 FormB 如何存取 FormA 中的資訊

要由子表單存取父表單，基本上要先知道其父表單是誰，通常的做法是在子表單中加入一個可以傳入父表單的建構子。  
```c#
private Form parentForm;
public FormTestB(Form frm)
{
this.parentForm = frm;
}

private void FormTestB_Load(object sender, EventArgs e)
{
this.textBox1.Text = ((FormTestA)parentForm).TextBox1Text;
}
```
```c#
public string TextBox1Text
{
get{
return textBox1.Text;
}
set{
textBox1.Text = value;
}
}
```