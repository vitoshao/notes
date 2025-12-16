---
title: 驗證控制項
layout: default
parent: 驗證與瀏灠控制項
nav_order: 1
description: "驗證控制項"
date: 2012-10-02
tags: [Web,ASPNET,驗證與瀏灠控制項]
postid: "1880667509455304942"
---
# Understanding the Validation Framework

Validation is a work to ensure the data entered by users is valid.   ASP.NET has a built-in data validation framework to provides both **client-side** and **server-side** validation with minimal coding.  

**Client-side** validation is a convenience to users.   It improves performance by checking the data at the browser before sending the data to the server.   This avoids unnecessary round trips to the server.   However, client-side validation can be easily defeated by hackers.   

**Server-side** validation is the only secure means of validating the data that is posted back to the server.  Using both client-side and server-side validation provides a better experience to the user and secure validation for your Web site.  

## 建立控制項的 Validation

1. **加入 Validation Controls ：**
2. **設定 [ControlToValidate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.controltovalidate.aspx) 屬性：**  
指定驗證的欄位。
3. **設定 [ErrorMessage](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.errormessage.aspx) 屬性：**
4. **加入 [ValidationSummary](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.validationsummary.aspx) 控制項：**  
彙總所有驗證的錯誤訊息到網頁上的單一位置。
5. **設定 [ToolTip](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.webcontrol.tooltip.aspx) 屬性：**  
當指標移入 Validation Controls 時，會出現提示文字。
6. **設定 [Text](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.text.aspx) 屬性：**  
[Text](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.text.aspx) 屬性同 [ErrorMessage](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.errormessage.aspx) 屬性，都是用來顯示驗證錯誤時的訊息。  
若同時設定，以 [Text](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.text.aspx) 屬性為主。  
[ErrorMessage](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.errormessage.aspx) 屬性另一個主要用途是用在 [ValidationSummary](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.validationsummary.aspx) 控制項， [Text](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.text.aspx) 屬性不支援這個。
```xml
帳號<asp:TextBox ID="txt_Account" runat="server" CausesValidation="True" ></asp:TextBox>
<asp:RequiredFieldValidator ID="Account_RequiredValidator" runat="server" 
ControlToValidate="txt_Account" 
ErrorMessage="帳號沒有輸入" 
ToolTip="請輸入帳號資訊。"
Text="帳號輸入錯誤"/>
```

## 建立整個網頁的 Validation Summary

有時候可以考慮在網頁中使用 [ValidationSummary](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.validationsummary.aspx) 控制項，它有幾點優點：

- display all the validation error messages in one location.
- useful in a crowded layout page.

####  [ValidationSummary](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.validationsummary.aspx) 控制項幾個常用屬性：

- [ShowMessageBox](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.validationsummary.showmessagebox.aspx) ：是否使用 msgbox ,顯示驗證摘要。
- [ShowSummary](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.validationsummary.showsummary.aspx) ：是否使用內嵌,顯示驗證摘要。
- [DisplayMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.validationsummary.displaymode.aspx) ：顯示模式 { **BulletList** | List | SingleParagraph }

## Server-Side Validation

若頁面上放置了 Validation Controls ，當 Page's Load 的時候，會自動去執行 Page 的 [Validate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.validate.aspx) 方法。  這個方法會透過 [Validators](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.validators.aspx) 屬性取得網頁中所有驗證控制項，並檢查是否有未驗證的項目。  若驗證通過就會將 Page 的 [IsValid](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.isvalid.aspx) 屬性設成 True 。  所以在後端程式碼中，只要透過 Page 物件的 [IsValid](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.isvalid.aspx) 屬性，就可以判斷驗證控制項是否有全部檢驗通過。  

- (M) [Page.Validate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.validate.aspx) ：Instructs any validation controls included on the page to validate their assigned information.
- (P) [Page.Validators](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.validators.aspx) ：取得網頁中所有驗證控制項的集合。
- (P) [Page.IsValid](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.isvalid.aspx) ：指出網頁驗證是否成功。
```c#
protected void btnSubmit_Click(object sender, EventArgs e)
{
myMessage.Show(this, "Page.IsValid = " + Page.IsValid);
}
```

## Client-Side Validation

驗證控制項會(自動)在伺服器上執行驗證。  它們也具有完整的用戶端實作，可以在用戶端執行驗證。  用戶端驗證會在使用者的輸入尚未傳送至伺服器之前就進行檢查，以加強驗證程序。  如此可在表單送出前，偵測到用戶端上的錯誤，進而避免伺服器端驗證所需的資訊往返。  

根據預設，用戶端驗證(EnableClientScript)這個選項的值為啟用(true)，如果瀏覽器支援它，它則會啟用用戶端驗證。  如果你想在執行Client端驗證之前先執行部分Server的程式碼，例如測試Server端的程式碼，您也可以先停用Client端驗證，這樣才會執行後端的程式碼。  

- [EnableClientScript](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.enableclientscript.aspx) ：是否啟用 client 端驗證。
- [SetFocusOnError](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.setfocusonerror.aspx) ：是否要在驗證失敗時，將焦點設定到指定的控制項。
```xml
<asp:RequiredFieldValidator ID="Account_RequiredValidator" runat="server" 
ControlToValidate="txt_Account" ErrorMessage="帳號沒有輸入" 
EnableClientScript="true"
SetFocusOnError="true"   />
```

如果停用 client 端驗證，在 server 端還是可以使用 [IsValid](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.isvalid.aspx) 來判斷是否驗證控制項都有符合驗證。  這是因為 PageLoad 時，會自動執行 [Validate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.validate.aspx) 方法，檢驗所有的驗證控制項。  

如果你希望停用自動執行 [Validate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.validate.aspx) ，可以將按鈕的 [CausesValidation](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.button.causesvalidation.aspx) 設定成 Fasle ，就不會引發 [Validate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.validate.aspx) 方法，此時就不能直接使用 [IsValid](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.isvalid.aspx) 來判斷是否驗證控制項都有符合驗證。  因為沒有執行 [Validate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.validate.aspx) 方法， [IsValid](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.isvalid.aspx) 屬性就沒有被初始值。  這時如果要判斷 [IsValid](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.isvalid.aspx) ，必須手動呼叫 [Page.Validate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.validate.aspx) 以執行這些檢驗工作。  
```c#
public virtual void Validate();                         //驗證網頁上所有驗證控制項
public virtual void Validate(string validationGroup);   //驗證網頁上特定群組的驗證控制項
```
```xml
<asp:TextBox ID="TextBox1" runat="server"></asp:TextBox>
<asp:RequiredFieldValidator ID="Required1" runat="server" Text="\*" 
ControlToValidate="TextBox1" 
ValidationGroup="GroupA" 
EnableClientScript="False" /><br />

<asp:TextBox ID="TextBox2" runat="server"></asp:TextBox>
<asp:RequiredFieldValidator ID="Required2" runat="server" Text="\*" 
ControlToValidate="TextBox2" 
ValidationGroup="GroupA" 
EnableClientScript="False" /><br />

<asp:Button ID="Button1" runat="server" Text="Button" 
ValidationGroup="GroupA" 
CausesValidation="False" 
onclick="Button1_Click"/><br />
```
```c#
protected void Button1_Click(object sender, EventArgs e)
{
Validate("GroupA");
myDebug.ResponseBR(IsValid.ToString());
}
```

## Determining When to Cause Validation

驗證控制項一般都是在頁面發生 postback 之前就會執行 client 端的驗證工作，以免傳送不合法的資料到 server 端。  但是如果某些情況不希望引發 validation ，例如：某個 Help 按鈕，你希望他在發生 Click 事件時，不要驗證頁面上的輸入，以便繼續執行 Server-Side 程式碼。  這時候就可以將該控制項的 [CausesValidation](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.button.causesvalidation.aspx) 屬性設定成 false ，以停用觸發驗證。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjqkMWwC7J406otTDM-By4Ive2_ZF2itxblt_AVCZ4GpUANF9T-D2GsJvTNdPcX-eLUxtCBq8lu5Pv1ZpHOUsoeBg3tvFr_GYSjMK72cS_qwJqAi3HHz1OyMmHYk7y8kDPXF-f4cwa9WfE/s298/CausesValidation.png)
```xml
<asp:Button ID="Button1" runat="server" Text="CausesValidation" 
CausesValidation="false"
OnClick="Button1_Click"/>
```

因為這個 postback 停用驗證，所以在 Page's Load 時就不會執行 Page.Validate() ，此時若你要讀取 Page.IsValid 屬性，就必須手動執行 Page.Validate() 方法。  
```c#
protected void Button1_Click(object sender, EventArgs e)
{
Page.Validate();
if (IsPostBack)
{
// DoWork
}
}
```

## Using Validation Groups

若表單上有二個群組的輸入控制項和二個 button ，在預設情況下，按下任何一個按鈕，都會驗證所有的輸入控制項。  若希望按下其中一個按鈕只驗證其中一組輸入控制項，只要使用 [ValidationGroup](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.validationgroup.aspx) 設定即可。  
```xml
<form id="form1" runat="server">
<div>
<asp:TextBox ID="TextBox1" runat="server"></asp:TextBox>
<asp:RequiredFieldValidator ID="Required1" runat="server" Text="\*" 
ControlToValidate="TextBox1" 
ValidationGroup="GroupA" /><br />
<asp:TextBox ID="TextBox2" runat="server"></asp:TextBox>
<asp:RequiredFieldValidator ID="Required2" runat="server" Text="\*" 
ControlToValidate="TextBox2" 
ValidationGroup="GroupA" /><br />
<asp:Button ID="Button1" runat="server" Text="Button" 
ValidationGroup="GroupA"/><br />

<asp:TextBox ID="TextBox3" runat="server"></asp:TextBox>
<asp:RequiredFieldValidator ID="Required3" runat="server" Text="\*" 
ControlToValidate="TextBox3"
ValidationGroup="GroupB" /><br />
<asp:TextBox ID="TextBox4" runat="server"></asp:TextBox>
<asp:RequiredFieldValidator ID="Required4" runat="server" Text="\*" 
ControlToValidate="TextBox4"
ValidationGroup="GroupB" /><br />
<asp:Button ID="Button2" runat="server" Text="Button" 
ValidationGroup="GroupB"/><br />
</div>
</form>
```

前面提過的 [Page.IsValid](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.isvalid.aspx) 屬性，若是由設定 [ValidationGroup](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.validationgroup.aspx) 的按鈕引發的 postback，  則只要該組的驗證控制項都通過， [IsValid](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.isvalid.aspx) 就會等於 true 。

## Understanding the BaseValidator Class

The validation controls inherit from the [BaseValidator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.aspx) abstract class.   This class contains most of the validation functionality exposed by the validation framework.  

#### BaseValidator Properties

- [ControlToValidate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.controltovalidate.aspx) ：Set this to the control that is to be validated.
- [EnableClientScript](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.enableclientscript.aspx) ：Set to false to disable client-side validation. Default is true.
- [ErrorMessage](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.errormessage.aspx) ：Set to the text that displays when validation fails.
- [IsValid](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.isvalid.aspx) ：The valid status of a single validation control.
- [Enabled](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.enabled.aspx) ：Set the property to false to completely disable the control.
- [Display](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.display.aspx)：
- None ：通常有設定 [ValidationSummary](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.validationsummary.aspx) ，就會將 [Display](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.display.aspx) 設成 None .
- Static ：即使不顯示訊息，也會佔用固定大小的空間。
- Dynamic ：當不顯示訊息時，不佔用訊息空間。

#### Validation Control Hierarchy

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj52MFOMD-BgbZeOjo3lsRQRkkCfwb4iy3SX24TbVxXIwxFMPkYj0TvLCmpxRkiv-9Bim-AOwj_0Ce35HqHGnPfuOz3FaG4R8W0VqVpXVDmlKkZrkqe1NVFNQ339FKzc7h9ud860GoDyuE/s734/validation-control-hierarchy.png)

# BaseCompareValidators

The [RangeValidator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.rangevalidator.aspx) and [CompareValidator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.comparevalidator.aspx) controls inherit from the [BaseCompareValidator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basecomparevalidator.aspx) control.   This base class contains common comparison behavior used by these controls.   The [BaseCompareValidator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basecomparevalidator.aspx) contains the [Type](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basecomparevalidator.type.aspx) property,   which you can set to the data type that the text is converted to before a comparison is made.   The data types that are available are as :  follows:  

- Currency ：
- Date ：
- Double ：
- Integer ：
- String ：

The [BaseCompareValidator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basecomparevalidator.aspx) class is a base class and not an actual validation control.  We look at its features more closely when discussing the child controls that inherit from [BaseCompareValidator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basecomparevalidator.aspx) .

# 1. CompareValidator

CompareValidator 控制項有以下幾個功能：

- 驗證輸入的資料型態：[]。
- 驗證輸入的資料的大小範圍：[]。
- 搭配 ValueToCompare 屬性，可用來驗證資料是否與。
- 當驗證數字型態時，可以搭配 ValueToCompare 屬性，以驗證數字範圍區間。
- 
- 

ControlToCompare屬性會取得優先權。    
####  CompareValidator 主要屬性如下：

- [ControlToValidate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.controltovalidate.aspx) ：取得或設定要驗證輸入的控制項。
- [Type](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basecomparevalidator.type.aspx) ：取得或設定要驗證資料的型態。 { **String** | Integer | Double | Date | Currency }
- [Operator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.comparevalidator.operator.aspx) ：設定比較運算子。 { **Equal** | NotEqual | GreaterThan | GreaterThanEqual | LessThan | LessThanEqual | DataTypeCheck }
- [ControlToCompare](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.comparevalidator.controltocompare.aspx) ：設定要比較的控制項。
- [ValueToCompare](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.comparevalidator.valuetocompare.aspx) ：設定要比較的值。（若同時設定 ControlToCompare ，會以 ControlToCompare 為優先）
```xml
請輸入：<asp:TextBox ID="TextBox3" runat="server"></asp:TextBox>
<asp:CompareValidator ID="Compare1" runat="server" 
ErrorMessage="輸入錯誤"
ControlToValidate="TextBox3"
ValueToCompare="3200"
Type = "Integer" 
Operator="GreaterThan"
/>
```

# 2. RangeValidator

#### RangeValidator 主要屬性如下：

- [MinimumValue](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.rangevalidator.minimumvalue.aspx) ：設定驗證範圍的最小值。
- [MaximumValue](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.rangevalidator.maximumvalue.aspx) ：設定驗證範圍的最大值。
```xml
<asp:RangeValidator ID="Range1" runat="server" 
ErrorMessage="輸入錯誤" 
ControlToValidate="TextBox4" 
Type = "Integer" 
MinimumValue="10" 
MaximumValue="99"
/>
```

# 3. RequiredFieldValidator

The [RequiredFieldValidator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.comparevalidator.aspx) provides the property called [InitialValue](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.requiredfieldvalidator.initialvalue.aspx) . It is used when the  control that you are validating defaults to an initial value and you want to ensure that the  user changes this value. For example, if you set some default text in your control, you can set  the [InitialValue](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.requiredfieldvalidator.initialvalue.aspx) property to this text to ensure that it is not a valid user input.  
```xml
請選擇：
<asp:DropDownList ID="DropDownList1" runat="server">    
<asp:ListItem>請選擇</asp:ListItem>    
<asp:ListItem>1</asp:ListItem>    
<asp:ListItem>2</asp:ListItem>    
<asp:ListItem>3</asp:ListItem>
</asp:DropDownList>
<asp:RequiredFieldValidator ID="RequiredFieldValidator1" runat="server" 
ErrorMessage="DropDownList1 尚未設定" 
ControlToValidate="DropDownList1" 
InitialValue="請選擇"
/>
```

# 4. RegularExpressionValidator

The [RegularExpressionValidator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.regularexpressionvalidator.aspx) control performs its validation based on a regular expression.  This control uses the [ValidationExpression](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.regularexpressionvalidator.validationexpression.aspx) property to set a valid regular expression  that is applied to the data that is to be validated.  

Visual Studio does have a number of predefined regular expressions from which to choose.  

![](http://127.0.0.1:8080/_images/cs/RegularExpressionValidator-predefined-expressions.png)
```xml
<asp:RegularExpressionValidator ID="Regular1" runat="server"
ErrorMessage="輸入錯誤" 
ControlToValidate="TextBox5" 
ValidationExpression="\w+([-+.']\w+)\*@\w+([-.]\w+)\*\.\w+([-.]\w+)\*"
/>
```

底下範例要求輸入５碼數字的驗證
```xml
<asp:TextBox ID="ZipCode" runat="server"></asp:TextBox>

<asp:RegularExpressionValidator ID="v1" runat="server" 
ControlToValidate="ZipCode" Display="Dynamic"
ValidationExpression="^\d{5}$"
ErrorMessage="輸入不符規定" />

<asp:RequiredFieldValidator ID="v2" runat="server" 
ControlToValidate="ZipCode"
ErrorMessage="此為必要輸入欄位" />
```

# 5. CustomValidator

The [CustomValidator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.customvalidator.aspx) control performs validation-based code you write.   You can write validation code that will be executed on the client side using JavaScript;   you can also write server-side validation code using your preferred .NET language.  

- (P) [ClientValidationFunction](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.customvalidator.clientvalidationfunction.aspx) ：指定 client 端驗證用的 javascript 函式名稱。
- (E) [ServerValidate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.customvalidator.servervalidate.aspx) ：當驗證在伺服器上執行時，會引發 [ServerValidate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.customvalidator.servervalidate.aspx) 事件。可用來寫驗證邏輯。

## Custom Client-Side Validation

使用驗證控制項時，若要自訂驗證內容，可以依照以下步驟設計：  

1. 設計一個 javascript 函式，函式名稱可以自訂，但是參數格式必須同底下簽名：  
```javascript
function ClientFunctionName(source, arguments)
```
2. 在函式中撰寫驗證邏輯，若驗證通過，則設定 **arguments.IsValid** = true
3. 將上面設計的函式名稱，指定給 [CustomValidator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.customvalidator.aspx) 中的 ClientFunctionName 屬性。
```xml
<head id="Head1" runat="server">
<script language="javascript" type="text/javascript">
function ValidateAccount(sender, args) {
var data = args.Value.split('');

//設成未完成驗證
args.IsValid = false;

//長度必須 6~12 字
if (data.length < 6 || data.length > 12) return;

//必須包含大寫
var uc = false;
for (var c in data) {
if (data[c] >= 'A' && data[c] <= 'Z') {
uc = true;
break;
}
}
if (!uc) return;

//必須包含小寫
var lc = false;
for (var c in data) {
if (data[c] >= 'a' && data[c] <= 'z') {
lc = true;
break;
}
}
if (!lc) return;

//必須包含數字
var num = false;
for (var c in data) {
if (data[c] >= '0' && data[c] <= '9') {
num = true; break;
}
}
if (!num) return;

//設成已驗證
args.IsValid = true;
}

</script>
</head>
<body>
<form id="form1" runat="server">
<div>
帳號<asp:TextBox ID="txt_Account" runat="server" CausesValidation="True" ></asp:TextBox>

<asp:CustomValidator ID="Account_CustomValidator" runat="server" 
ControlToValidate="txt_Account" 
ErrorMessage="輸入錯誤"
ClientValidationFunction="ValidateAccount"  />

<asp:Button ID="btnSubmit" runat="server" Text="Submit" OnClick="btnSubmit_Click" />
</div>
</form>
</body>
```

## Custom Server-Side Validation

The [CustomValidator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.customvalidator.aspx) control can work client-side, server-side, or both.   We recommend that you do validation on both the client and the server in the same way the other validation controls operate.    To implement server-side validation with the [CustomValidator](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.customvalidator.aspx) control, you override its [ServerValidate](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.customvalidator.servervalidate.aspx) event.   This server-side event can be trapped in your code-behind file to execute code that you write.  

Like client-side validation, server-side validation also provides the same two parameters: 

- source : provides access to the source validation control.
- args : provides the args.Value property for checking the data that shouldbe validated.
```c#
protected void ValidateAccount_ServerSide(object source, ServerValidateEventArgs args)
{
string data = args.Value;

//設成未完成驗證
args.IsValid = false;

//長度必須 6~12 字
if (!(8 <= data.Length && data.Length <= 12)) return;

//必須包含大寫
bool uc = false;
foreach (char c in data)
{
if (c >= 'A' && c <= 'Z')
{
uc = true; break;
}
}
if (!uc) return;

//必須包含小寫
bool lc = false;
foreach (char c in data)
{
if (c >= 'a' && c <= 'z')
{
lc = true; break;
}
}
if (!lc) return;

//必須包含數字
bool num = false;

foreach (char c in data)
{
if (c >= '0' && c <= '9')
{
num = true; break;
}
}
if (!num) return;

//設成已驗證
args.IsValid = true;
}
```
```xml
帳號<asp:TextBox ID="txt_Account" runat="server" CausesValidation="True" ></asp:TextBox>
<asp:CustomValidator ID="Account_CustomValidator" runat="server" 
ControlToValidate="txt_Account" 
ErrorMessage="輸入錯誤"
ClientValidationFunction="ValidateAccount" 
EnableClientScript="false"
OnServerValidate="ValidateAccount_ServerSide" 
Display="None"
/>
```

PS:  
若要驗證 server-side 的程式碼，可以先關閉 client 端驗證 ( [EnableClientScript](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.enableclientscript.aspx) = false ) 。

# 6. 自訂驗證控制項

覆寫 [EvaluateIsValid](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.basevalidator.evaluateisvalid.aspx) 方法，並在方法內撰寫自訂的驗證邏輯。  
```c#
public class MyValidation : BaseValidator
{
protected override bool EvaluateIsValid()
{
string value = GetControlValidationValue(this.ControlToValidate);
bool isValid = value.Length > 3;
return isValid;
}
}
```