---
title: HtmlHelper Class
layout: default
parent: MVC
nav_order: 1
description: "HtmlHelper Class"
date: 2013-01-29
tags: [Web,MVC]
postid: "6979824659144716690"
---
若要在 MVC 應用程式中使用 HTML 控制項，除了直接建立 HTML 控制項之外，  也可以使用 [System.Web.Mvc.Html](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.aspx) 命名空間底下的擴充控制項，來協助建立 HTML 控制項。  該命名空間包括支援表單、輸入控制項、連結、部分檢視和驗證等的類別。   

# FormExtensions

[FormExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.formextensions.aspx) 類別是來用產生 &lt;Form&gt; 標籤用的，它包含三個擴充方法，分別是：

- [BeginForm](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.formextensions.beginform.aspx) ：
- [BeginRouteForm](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.formextensions.beginrouteform.aspx) ：
- [EndForm](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.formextensions.endform.aspx) ：
```xml
@{Html.BeginForm("Edit", "Employees");}

@Html.HiddenFor(model => model.EmployeeID)
<div>
FirstName：
@Html.TextBox("FirstName")
@Html.ValidationMessageFor(model => model.FirstName)
</div>
<div>
LastName：@Html.TextBox("LastName")
@Html.ValidationMessageFor(model => model.LastName)
</div>
<div>
Title：@Html.TextBox("Title")
</div>
<div>
BirthDate：@Html.TextBox("BirthDate")
</div>
<div>
Age：@Html.TextBox("Age")
</div>
<input type="submit" value="Save" />

@{Html.EndForm();}
```

產生內容
```xml
<form action="/Employees/Edit" method="post">
<input id="EmployeeID" name="EmployeeID" type="hidden" value="10" />
<div>
FirstName：
<input id="FirstName" name="FirstName" type="text" value="vito2" />
<span data-valmsg-for="FirstName" data-valmsg-replace="true"></span>
</div>
<div>
LastName：
<input id="LastName" name="LastName" type="text" value="vito2" />
<span data-valmsg-for="LastName" data-valmsg-replace="true"></span>
</div>
...
<input type="submit" value="Save" />
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjPSSAlbmqFbQfDzVc1bbAvQF9TQTtSbrnaBUJ5m0OK-z73a2GW7e-xLxZvFvs-zUCT5wKkKFJcoOsajqrofX0VIaP60cPBKARjUR_09sFD0dxye_m9G-9rswgnKzZeEAKBU1dM742_pmY/s470/mvc-helper-form.png)

以下是 [BeginForm](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.formextensions.beginform.aspx) 的另一種用法，可讀性較高：
```xml
@using (Html.BeginForm("Edit", "Employees"))
{
@Html.HiddenFor(model => model.EmployeeID)
<div>
@Html.LabelFor(model => model.FirstName)
@Html.EditorFor(model => model.FirstName)
@Html.ValidationMessageFor(model => model.FirstName)
</div>
<div>
@Html.LabelFor(model => model.LastName)
@Html.EditorFor(model => model.LastName)
@Html.ValidationMessageFor(model => model.LastName)
</div>  
<div>
@Html.LabelFor(model => model.Title)
@Html.EditorFor(model => model.Title)
</div> 
<div>
@Html.LabelFor(model => model.BirthDate)
@Html.EditorFor(model => model.BirthDate)
</div>     
<div>
@Html.LabelFor(model => model.Age)
@Html.EditorFor(model => model.Age)
</div>   
<input type="submit" value="Save"/>
}
```

BeginForm 有多個多載方法，其中一個 [BeginForm(actionName, controllerName, method)](https://msdn.microsoft.com/zh-tw/library/dd460344.aspx) 可以用來設定表單提交時，要使用哪一種 action method 處理。

# LabelExtensions 、 DisplayExtensions 、 DisplayTextExtensions

通常為了在 View 中顯示特定資料，我們會在 [Controller](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controller.aspx) 中，將資料先儲存在 [ViewData](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewdata.aspx) 中，再由 View 中取出。如下面範例：  
```c#
public ActionResult About()
{
ViewData["Message"] = "歡迎使用 ASP.NET MVC!";
}
```
```xml
@ViewData["Message"].ToString() 
```

MVC 提供我們幾個更簡便的方式以顯示這些資料。  你可以透過 [DisplayExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displayextensions.aspx) 類別提供的下列方法，將物件資料，以 HTML 格式呈現：  

[Display](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displayextensions.display.aspx) 方法通常用來顯示 [ViewData](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewdata.aspx) 字典中的資料；   [DisplayFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displayextensions.displayfor.aspx) 和 [DisplayForModel](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displayextensions.displayformodel.aspx) 方法通常用來顯示 Model 屬性所公開之物件的値。  
```c#
[DisplayName("員工")]
public class Person
{
[DisplayName("編號")]
public int ID { get; set; }

[Required()]
[DisplayName("大名")]
public string FirstName { get; set; }

[Required(ErrorMessage = "請輸入LastName")]
[DisplayName("姓氏")]
public string LastName { get; set; }

[Required()]
[MaxLength(10)]
[DisplayName("電話")]
public string Tel { get; set; }

[DisplayName("已婚")]
public bool Merried { get; set; }
}
```

## LabelExtensions

[LabelExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.labelextensions.aspx) 會使用 &lt;label&gt; 標籤來轉譯指定的 Property 的 name 。它包含底下幾個方法：

- [Label](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.labelextensions.label%20.aspx) ：使用字串指定 Property ，回傳該 Peoperty 的顯示名稱。
- [LabelFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.labelextensions.labelfor.aspx) ：使用 Expression 指定 Property ，回傳該 Peoperty 的顯示名稱。
- [LabelForModel](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.labelextensions.labelformodel.aspx) ：這個方法，隱含地使用模型。回傳該 Model 的 Property 名稱，若不指定 Property 則回傳 Model 的顯示名稱。
```xml
@Html.Label("FirstName", new { @class = "test classA" }) <%--回傳 property 設定的 DisplayName--%>
@Html.LabelFor(m => m.FirstName, new { @class = "test classB" })
@Html.LabelForModel("FirstName")                      <%--回傳 property name--%>
@Html.LabelForModel("FirstName", new { @class = "test classA" })
@Html.LabelForModel(new { @class = "test classB" })   <%--若不指定 property , 會回傳 model name--%>
```
```xml
<label class="test classA" for="FirstName">大名</label>
<label class="test classB" for="FirstName">大名</label>
<label for="">FirstName</label> 
<label class="test classA" for="">FirstName</label>
<label class="test classA" for="">學生</label>
```

## DisplayTextExtensions

[DisplayTextExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displaytextextensions.aspx) 會直接將指定的 Property 的 value 轉譯出去。它包含底下幾個方法：

- [DisplayText](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displaytextextensions.displaytext.aspx) (string name) ：使用字串指定 Property
- [DisplayTextFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displaytextextensions.displaytextfor.aspx) (Expression expression) ：使用 Expression 指定 Property
```xml
<div>
@Html.DisplayText("FirstName")
</div>
<div>
@Html.DisplayTextFor(m => m.FirstName)
</div>
```
```xml
<div>
vito
</div>
<div>
vito
</div>
```

## DisplayExtensions

[DisplayExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displayextensions.aspx) 會直接將指定的 Property 的 value 轉譯出去，而且支援使用 template 來設定輸出的樣式。它包含底下幾個方法：

- [Display](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displayextensions.display.aspx) ：使用指定的範本，傳回由字串指定的 Property 的 Value 。
- [DisplayFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displayextensions.displayfor.aspx) ：使用指定的範本，傳回由 Expression 指定的 Property 的 Value 。
- [DisplayForModel](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displayextensions.displayformodel.aspx) ：使用指定的範本，傳回模型中每一個屬性的 Name 和 Value 。
- 
```xml
@Html.Display("Tel", "myTelTemplate")
@Html.DisplayFor(m => m.Tel, "myTelTemplate")
@Html.DisplayForModel()
@Html.DisplayForModel("myEmployeeTemplate")
```
```xml
@model String
<span class="tel">@System.Text.RegularExpressions.Regex.Replace(Model, @"(\d{2})(\d{3})(\d{4})", "$1-$2-$3");</span>
```
```xml
@model Person
<div>@Model.FirstName</div>
<div>@Model.LastName</div>
```
```xml
<span class="tel">02-393-9889;</span>

<span class="tel">02-393-9889;</span>

<div class="display-label">編號</div>
<div class="display-field">3</div>
<div class="display-label">大名</div>
<div class="display-field">vito</div>
<div class="display-label">姓氏</div>
<div class="display-field">shao</div>
<div class="display-label">電話</div>
<div class="display-field">023939889</div>
<div class="display-label">已婚</div>
<div class="display-field"><input class="check-box" disabled="disabled" type="checkbox" /></div>

<div>vito</div>
<div>shao</div>
<div>023939889</div>
```

### Display

使用指定的範本，傳回運算式表示的物件中每一個屬性的 HTML 標記。  
```c#
public static MvcHtmlString Display(this HtmlHelper html, string expression);
public static MvcHtmlString Display(this HtmlHelper html, string expression, string templateName);
public static MvcHtmlString Display(this HtmlHelper html, string expression, string templateName, string htmlFieldName);

public static MvcHtmlString Display(this HtmlHelper html, string expression, object additionalViewData);
public static MvcHtmlString Display(this HtmlHelper html, string expression, string templateName, string htmlFieldName, object additionalViewData);
```

您可以使用 [Display](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displayextensions.display.aspx) 方法顯示從 [ViewData](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controllerbase.viewdata.aspx) 字典和從 Model 屬性所公開之物件的資料。  
```xml
@ViewData["Message"].ToString()  @\* 一般顯示方法 \*@

@Html.Display("Message")         @\* 若有 Message 物件，則將其值顯示出來 \*@

@Html.Label("FirstName")：       @\* 顯示 Employees 公開的屬性 FirstName 的欄位名稱 \*@
@Html.Display("FirstName")       @\* 顯示 Employees 公開的屬性 FirstName 的值 \*@
@Html.Label("BirthDate")：
@Html.Display("BirthDate", "myDateTime")    @\* 使用 myDateTime 這個 Partail View ，顯示 BirthDate 物件值 \*@
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgqvdujyYv406kr1n8dBXH-8TRFqnIZOO9xxNFJEScfsKGxVSPvCpYJTbuL85BkXtoBrcZPBSU5ko02CUeLIRCXK87VTFcBG3Yk54X68gboFz-7gxJU9jU17l3YVxVwr3P6eCQzF1iwFiU/s272/mvc-htmlhelper-display.png)

上面範例中的 myDateTime 是一個自訂的 partial view (user control) ，程式碼如下：
```xml
<span style="font-weight:bold;color:green;">
@Html.Encode(string.Format("{0:yyyy\\/MM\\/dd}", Model))
</span>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh_QtSKWf3gyFOL9_LghjsQ5sUvUNc9f8Tz9NkDhg89Kg8AR3OFSMy3zySFKkamBjXP5V3KaCr8uolyAcdSQ_spu3Gs9WvBW3VExAyrqXUXbZJ5qFV1bNRJ6kebnh0RnpY-Q3ydfFhbYqg/s262/mvc-partial-view-myDateTime.png)

### DisplayFor

使用指定的範本，傳回由 Expression 表示之物件中包含各個屬性值的字串。

其用法同 [Display](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displayextensions.display.aspx) ，只是它的運算式是一個**強型別物件**。
```c#
public static MvcHtmlString DisplayFor(this HtmlHelper html, Expression expression);
public static MvcHtmlString DisplayFor(this HtmlHelper html, Expression expression, string templateName);
public static MvcHtmlString DisplayFor(this HtmlHelper html, Expression expression, string templateName, string htmlFieldName);

public static MvcHtmlString DisplayFor(this HtmlHelper html, Expression expression, object additionalViewData);
public static MvcHtmlString DisplayFor(this HtmlHelper html, Expression expression, string templateName, string htmlFieldName, object additionalViewData);
```
```xml
@Html.LabelFor(emp => emp.FirstName)：
@Html.DisplayFor(emp => emp.FirstName)
@Html.LabelFor(emp => emp.BirthDate)：
@Html.DisplayFor( emp => emp.BirthDate , "myDateTime")
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjEQnUVtqVcZJHAhcuLNmDWJESSdWnE4xSlWThoKWBs1_A4RqO-5fmvjOm5oaXe77iBswDamD8FUM3lqdFjgU-Jfb5S0iWLs9pXFCZprLAwjwJn2_puxBRQFc-j9NSoV7wWzI2AbDFRWUk/s273/mvc-htmlhelper-displayfor.png)

### DisplayForModel

使用指定的範本，傳回模型中每一個屬性的 HTML 標記
```c#
public static MvcHtmlString DisplayForModel(this HtmlHelper html);
public static MvcHtmlString DisplayForModel(this HtmlHelper html, object additionalViewData);
public static MvcHtmlString DisplayForModel(this HtmlHelper html, string templateName);
public static MvcHtmlString DisplayForModel(this HtmlHelper html, string templateName, object additionalViewData);
public static MvcHtmlString DisplayForModel(this HtmlHelper html, string templateName, string htmlFieldName);
public static MvcHtmlString DisplayForModel(this HtmlHelper html, string templateName, string htmlFieldName, object additionalViewData);
```

1. 通常在強型別的 Veiw 中，要顯示該型別中特定的公開資料，你可以如此做：
```xml
<div>FirstName：@Html.DisplayFor(emp => emp.FirstName)</div>
<div>LastName：@Html.DisplayFor(emp => emp.LastName)</div>
<div>BirthDate：@Html.DisplayFor(emp => emp.BirthDate)</div>
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi3-7462SizUlBp3vl4KanhD6z01Hz3ZbOTGHCD8RfqZXrAgCpGpSMjPAwwkjjaIdNIvi6LzhNhsGcMXgNTb0MGq-Cv1TzNILkQp5CU3cRxFLjgKDHr-YQxNJ3oPq9u9GDXpmTlF1T_Umk/s261/mvc-helper-displayfor.png)

2. 若要顯示該型別的**所有公開的資料**，使用直接使用 [DisplayForModel](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.displayextensions.displayformodel.aspx) 轉譯整個模型，以下範例：
```xml
@Html.DisplayForModel()
```

它就會自動將該 Model 中的所有公開屬性，轉譯成 html 字串，如下所示：
```xml
<div class="display-label">員工ID</div>
<div class="display-field">2</div>
<div class="display-label">名</div>
<div class="display-field">Fuller</div>
<div class="display-label">姓</div>
<div class="display-field">Andrew</div>
<div class="display-label">Title</div>
<div class="display-field">Vice President, Sales</div>
<div class="display-label">TitleOfCourtesy</div>
...
```

3. 你也可以將該模型資料，交由指定的範本(partial view or user control)來產出。  也就是在 user control 中設計你要的樣式，再使用這個 user control 來展現你的模型資料。  如下範例：
```xml
@Html.DisplayForModel("EmpDetail")
```
```xml
@model MvcApplication1.Models.Employees  <div>FirstName：@Html.DisplayFor(emp => emp.FirstName)</div>
<div>LastName：@Html.DisplayFor(emp => emp.LastName)</div>
<div>BirthDate：@Html.DisplayFor(emp => emp.BirthDate)</div>
```

# InputExtensions

[InputExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.inputextensions.aspx) represents support for HTML **input controls** in an application.  這個類別包含了五個擴充方法，用來建立以下五種 html 中的 input 項目：  

- [TextBox](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.inputextensions.textbox.aspx) ：
- [CheckBox](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.inputextensions.checkbox.aspx) ：
- [RadioButton](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.inputextensions.radiobutton.aspx) ：
- [Password](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.inputextensions.password.aspx) ：
- [Hidden](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.inputextensions.hidden.aspx) ：

這五個擴充方法也都各有一個加了 For 結尾的擴充方法，如 [TextBoxFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.inputextensions.textboxfor.aspx) , [CheckBoxFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.inputextensions.checkboxfor.aspx) , [HiddenFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.inputextensions.hiddenfor.aspx) , [PasswordFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.inputextensions.passwordfor.aspx) , [HiddenFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.inputextensions.hiddenfor.aspx) 用來接受一個運算式物件的參數。  

下面這個例子，是他們在使用上的差別
```c#
@Html.TextBoxFor("Caption")
@Html.TextBoxFor(model => model.Title)
```
```xml
<input id="title1" name="Caption" type="text" value="" />
<input id="Title" name="Title" type="text" value="Aniseed Syrup" />
```

# EditorExtensions

[EditorExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.editorextensions.aspx) 底下只有一個 [@Html.Editor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.editorextensions.editor.aspx) 方法，它可以產生像 @Html.TextBox 一樣的 input control 。  除此之外，最主要的差別在 @Html.TextBox 只會產生 type="text" 屬性的 input control ，而 @Html.Editor 則會自動根據 Model 的 Property 型別，自動使用適合的 type 來建立 input control 。  還有這個方法也可搭配 templates 產出指定的樣式。  
```c#
public static MvcHtmlString Editor(string expression);
public static MvcHtmlString EditorFor<TModel, TValue>(Expression<Func<TModel, TValue>> expression, string templateName, string htmlFieldName, object additionalViewData);
public static MvcHtmlString EditorForModel(string templateName, string htmlFieldName, object additionalViewData);
```
```c#
@Html.TextBoxFor(m => m.Merried)    // Merried 是 boolean 型別
@Html.EditorFor(m => m.Merried)     // Merried 是 boolean 型別
```
```xml
<input id="Merried" name="Merried" type="text" value="" />
<input id="Merried" name="Merried" type="checkbox" value="true" />
```

# LinkExtensions

[LinkExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.linkextensions.aspx) 類別主要用來產生 HTML 的連結控制項。

## ActionLink

[ActionLink](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.linkextensions.actionlink.aspx) 方法可以用來針對 action method 建立連結控制項.

### 建構方法
```c#
public static MvcHtmlString ActionLink(this HtmlHelper htmlHelper, string linkText, string actionName, 
string controllerName);

public static MvcHtmlString ActionLink(this HtmlHelper htmlHelper, string linkText, string actionName, 
object routeValues);

public static MvcHtmlString ActionLink(this HtmlHelper htmlHelper, string linkText, string actionName, 
string controllerName, 
object routeValues, 
object htmlAttributes);
```

### 範例１：使用 ActionLink 建立 Action 的連結

若要使用 [ActionLink](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.linkextensions.actionlink.aspx) ，可以這樣用：
```xml
@Html.ActionLink("LinkText1", "List", "Employees");
@Html.ActionLink("LinkText2", "List", new { controller = "Employees" });
@Html.ActionLink("LinkText3", "List", new { controller = "Employees", page = "2", category ="02"});
@Html.ActionLink("LinkText4", "List", "Employees", new { area = "Admin" }, null);
```

上面會分別產生以下連結
```xml
<a href="/Employees/List">LinkText1</a>
<a href="/Employees/List">LinkText2</a>
<a href="/Employees/List?page=2&category=02">LinkText3</a>
<a href="/Admin/Employees/List">Linkext4</a>
```

### 範例２：解決控制器名稱重複的問題

上面範例中，當送出 /Employees/List 需求時，因為我們在 Areas 底下也建立一個 Employees 控制器，所以系統底下共有二個同名的 Employees 控制器。  這樣子當路由在比對時，會產生以下錯誤：  

[![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEge2HOYmVOF5AfnY-jt1McIJk4NUOsCPnVz7h07kap7HefUM5R1SNonme3NU7M7_0hoIS8if8OqRkiaB0Av_eau91C4pR1PWN6cIBr4K2z55Rh3DI_lHiCKCHTCnb_sLWWnvbDnDCviHnA/s800/mvc-controller-duplicate.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEge2HOYmVOF5AfnY-jt1McIJk4NUOsCPnVz7h07kap7HefUM5R1SNonme3NU7M7_0hoIS8if8OqRkiaB0Av_eau91C4pR1PWN6cIBr4K2z55Rh3DI_lHiCKCHTCnb_sLWWnvbDnDCviHnA/s0/mvc-controller-duplicate.png)

要解決控制器名稱重複的問題，可以在 Routing 定義中加入控制器的命名空間，建立**條件約束**來區分。
```xml
routes.MapRoute(
"Default", 
"{controller}/{action}/{id}", 
new { controller = "Home", action = "Index", id = UrlParameter.Optional },
new string[] { "MvcApplication2.Controllers" }      //只搜尋特定命名空間下的控制器
);
```

## RouteLink

[RouteLink](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.linkextensions.routelink.aspx) 方法可以用來針對 action method, file, resource 建立連結控制項。

### 建構方法
```c#
public static MvcHtmlString RouteLink(this HtmlHelper htmlHelper, string linkText, string routeName);
public static MvcHtmlString RouteLink(this HtmlHelper htmlHelper, string linkText, object routeValues);
public static MvcHtmlString RouteLink(this HtmlHelper htmlHelper, string linkText, string routeName, object routeValues);
```

參數說明：

- linkText：連結項目的顯示文字。
- routeName：用來傳回虛擬路徑之路由的名稱。
- routeValues：這是一個物件型別的參數，物件中必須包含用來建立 route 的參數值。  這些物件參數，會透過反映(reflection)的方法，以檢查物件中的成員，並擷取各個屬性值出來。

### 範例１：使用 RouteLink 建立 Action 的連結
```c#
routes.MapRoute(
"DefaultA"                                                                   
, "{controller}/{action}/{id}"                                              
);

routes.MapRoute(
"DefaultB"                                                  
, "BBB/{controller}/{action}/{id}"
);
```

上面這二個 route ，你可以透過 [RouteLink](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.linkextensions.routelink.aspx) 方法，產生 action 的連結。  
```xml
@Html.RouteLink("LinkText4", "DefaultA");
@Html.RouteLink("LinkText5", "DefaultB");

@Html.RouteLink("LinkText6", "DefaultA", new { controller = "Employees", action = "List" });
@Html.RouteLink("LinkText7", "DefaultB", new { controller = "Employees", action = "List" });
```

上面會分別產生以下連結
```xml
<a href="/">LinkText4</a>
<a href="/BBB">LinkText5</a>

<a href="/Employees/List">LinkText6</a>
<a href="/BBB/Employees/List">LinkText7</a>
```

### 範例２：ActionLink vs RouteLink

[RouteLink](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.linkextensions.routelink.aspx) 和 [ActionLink](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.linkextensions.actionlink.aspx) 最大的不同之處在於，因為 [Action](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.childactionextensions.action.aspx) 和 [Routes](http://msdn.microsoft.com/zh-tw/library/system.web.routing.routetable.routes.aspx) 並不是一對一的關系，  當使用 [ActionLink](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.linkextensions.actionlink.aspx) 時，系統會以 controller + action 找到第一個符合比對的路由樣式，建立連結。  若使用 [RouteLink](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.linkextensions.routelink.aspx) ，則可以使用指定的路由樣式，建立連結。  

在前面的範例中，我們使用 EmployeeController 建立 [ActionLink](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.linkextensions.actionlink.aspx) ，得到 /Employees/List 的輸出連結。這是因為在 RouteConfig 的定義中， DefaultA 寫在 DefaultB 上頭。  若將 DefaultB 寫在上頭，連結就會變成 BBB/Employees/List  
```xml
@Html.ActionLink("LinkText8", "List", new { controller = "Employees" });
```

若使用 [RouteLink](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.linkextensions.routelink.aspx) 就可以避免這個問題。  
```xml
@Html.RouteLink("LinkText9", "DefaultB", new { controller = "Employees", action = "List" });
```

## 建立 Areas 的連結

若我們使用 Areas 功能，要注意的首先是控制器重複的問題。另一個常見需求就是如何建立 Admin 區域的連結，或者如何由 Admin 區域建立外層網頁的連結。  

### 如何使用 ActionLink 或 RouteLink 建立 Admin 區域連結？
```c#
@Html.ActionLink("AdminActionLink1", "List", "Employees", new { area = "Admin" }, null)
@Html.ActionLink("AdminActionLink2", "List",  new { area = "Admin", controller = "Employees" }, null)

@Html.RouteLink("AdminRouteLink1", "AdminRoute", new { controller = "Employees", action = "List" })
@Html.RouteLink("AdminRouteLink2", new { area = "Admin", controller = "Employees", action = "List" }) 
```

### 如何由 Admin 區域連結到外層的網頁？
```c#
@Html.ActionLink("ActionLink1", "List", "Employees", new { area = "" }, null) 
@Html.ActionLink("ActionLink2", "List",  new { area = "", controller = "Employees" }, null) 

@Html.RouteLink("RouteLink1", "DefaultA", new { controller = "Employees", action = "List" })
@Html.RouteLink("RouteLink2", new { area = "", controller = "Employees", action = "List" })
```

# ChildActionExtensions

[ChildActionExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.childactionextensions.aspx) 類別主要用來呼叫子系的 action method ，以取得 partial view 內容。
```c#
public static MvcHtmlString Action(this HtmlHelper htmlHelper, string actionName);
public static MvcHtmlString Action(this HtmlHelper htmlHelper, string actionName, object routeValues);
public static MvcHtmlString Action(this HtmlHelper htmlHelper, string actionName, string controllerName);
public static MvcHtmlString Action(this HtmlHelper htmlHelper, string actionName, string controllerName, object routeValues);

public static void RenderAction(this HtmlHelper htmlHelper, string actionName);
public static void RenderAction(this HtmlHelper htmlHelper, string actionName, object routeValues);
public static void RenderAction(this HtmlHelper htmlHelper, string actionName, string controllerName);
public static void RenderAction(this HtmlHelper htmlHelper, string actionName, string controllerName, object routeValues);
```

## Action

[Action](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.childactionextensions.action.aspx) 方法會叫用指定的子系的 action method 已取得回應的 partial view，並以 HTML 字串形式傳回結果。
```xml
@model MvcApplication1.Models.Customers

@Html.Action("CustomerOrders", new { CustomerID = Model.CustomerID })  
```

叫用 controller 中的 CustomerOrders 方法，並傳入參數 CustomerID 。
```c#
public ActionResult CustomerOrders(string CustomerID)
{
NorthwindEntities db = new NorthwindEntities();

var query = from order in db.Orders
where order.CustomerID == CustomerID
select order;

return View(query.ToList());
}
```
```xml
@model IEnumerable<MvcApplication1.Models.Orders>
<table>
@foreach (var item in Model)
{
<tr>
<td>@Html.DisplayFor(modelItem => item.CustomerID)</td>
<td>@Html.DisplayFor(modelItem => item.OrderDate)</td>
<td>@Html.DisplayFor(modelItem => item.ShipAddress)</td>
<td>@Html.DisplayFor(modelItem => item.ShipCity)</td>
</tr>
}
</table>
```

## RenderAction

[RenderAction](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.childactionextensions.renderaction.aspx) 同 [Action](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.childactionextensions.action.aspx) 功能，差別在它不傳回值，直接將轉譯的 HTML 碼輸出到 parent view。
```c#
@{ Html.RenderAction("ShowTime", "Home"); }
```

叫用 Home [Controller](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.controller.aspx) 中的 ShowTime 方法。
```c#
public ActionResult ShowTime()
{
return View();
}
```
```xml
@DateTime.Now.ToString()
```

# PartialExtensions and RenderPartialExtensions

[PartialExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.partialextensions.aspx) 和 [RenderPartialExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.renderpartialextensions.aspx) 類別都是用來取得 partial view 內容。  和 [ChildActionExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.childactionextensions.aspx) 差別在不會呼叫 controller 中的 action method 。  
```c#
public static MvcHtmlString Partial(this HtmlHelper htmlHelper, string partialViewName);

public static void RenderPartial(this HtmlHelper htmlHelper, string partialViewName);
```

## Partial

Partial 方法會將 partial view 轉譯成 HTML 編碼字串，然後回傳字串。
```c#
@Html.Partial("CopyRightControl")
```
```xml
<p>Copyright © 2013 XXX Inc. All rights reserved</p>
```

## RenderPartial

[RenderPartial](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.renderpartialextensions.renderpartial.aspx) 同 Partial 功能，差別在它不傳回值，直接將轉譯的 HTML 碼輸出到 parent view。
```c#
@{ Html.RenderPartial("CopyRightControl"); }
```

# ValidationExtensions

[ValidationExtensions](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.aspx) 包含以下方法：

- [Validate](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validate.aspx) ：以文字格式回傳驗證錯誤的訊息
- [ValidationMessage](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationmessage.aspx) ：以 html 格式回傳驗證錯誤的訊息
- [ValidationSummary](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationsummary.aspx) ：輸出驗證錯誤訊息的清單
- [ValidateFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validatefor.aspx) ：以 metadata 中所設定的資訊來驗證輸入的資料
- [ValidationMessageFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationmessagefor.aspx) ：以 html 格式回傳驗證錯誤的訊息

在上面幾個方法中， [Validate](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validate.aspx) 和 [ValidationMessage](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationmessage.aspx) 主要差異在於 [Validate](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validate.aspx) 回傳的訊息是經過 Encode 的，也就是會先將 html 標籤轉成字碼。  而 [ValidationMessage](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationmessage.aspx) 不會，所以若訊息文字中含有 html 標籤，就會顯示出該有的樣式。  另外帶有 For 字尾的方法表示可以利用 expression 表示式來指定驗證欄位，也就是透過強型別方式來做設定；而不帶 For 結尾的方法就是只能使用字串方式來設定驗證欄位。  

MVC 的 Model 驗證機制，讓我們可以只透過 Model 的 DataAnnotations 屬性設定，就可以提供用戶端和伺服器驗證檢查，而不需要撰寫其他程式碼。   這些驗證後所產生的錯誤訊息會自動存放在 [ModelState](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.modelstate.aspx) 系統變數中，  ModelState 是一個 [ModelStateDictionary](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.modelstatedictionary.aspx) 型別物件，哪一個 Property Name 驗證有錯誤，就會建立一筆資料，並將錯誤存放在 Values.Errors 屬性，同時將原始資料保留在 Values.Value 屬性中。  你也可以在程式碼中透過 [ModelState.AddModelError](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.modelstatedictionary.addmodelerror%28v=vs.118%29.aspx) 方法，將自訂的錯誤訊息記錄下來，並依據自行定義的 key 值分類。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhMl_w4wMrYyMt7QrcNZlHK9oMwwQpAnrziP1jTj7QloLNyUrFfUqOG3TaSvthYU6RxYr4JHOH_ookMCDWGF0rZTa2hky23mjnjYTbzuB5SMDgp_SVSj7sEyXnmjxvUvfSWfZHKDzjcV8k/s0/mvc-helper-2.png)

使用 [ValidationMessage](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationmessage.aspx) 或 [ValidationMessageFor](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationmessagefor.aspx) 時，必須指定一個 Property ，就會顯示與該 Property 相關的錯誤訊息，但只會顯示一筆。  而 [ValidationSummary](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.html.validationextensions.validationsummary.aspx) 則會顯示 [ModelState](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.modelstate.aspx) 中的全部錯誤訊息。  
```c#
[HttpPost]
public ActionResult Create(Student student)
{
ModelState.AddModelError("keyA", "錯誤訊息Ａ1");
ModelState.AddModelError("keyB", "錯誤訊息Ｂ1");

if (ModelState.IsValid)
{
db.Students.Add(student);
db.SaveChanges();
return RedirectToAction("Index");
}
return View(student);
}
```
```xml
@Html.ValidationSummary("", new { @class = "text-danger" })

@Html.ValidationMessageFor(model => model.LastName, "", new { @class = "text-danger" })
@Html.ValidationMessageFor(model => model.FirstMidName, "", new { @class = "text-danger" })
@Html.ValidationMessageFor(model => model.EnrollmentDate, "", new { @class = "text-danger" })
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgwwdUqYK06F8_VN_OkR-w5_LT1WpSB6w4lslOvTIcRKGqMUghaE9cIHhYvXRMK26GEtwWd5cp05BXnUBXyPMBwNFRLAQbgUNLl2x323O2fbKphkb8a8bP4wdz1AKLByuvUgjCY5yeE1Lc/s0/mvc-helper-1.png)

#### 清除

如果你不想錯誤訊息輸出到 View 端，你可以叫用 [ModelState.Clear](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.modelstatedictionary.clear.aspx) 方法移除全部記錄，或者 [ModelState.Remove](http://msdn.microsoft.com/zh-tw/library/system.web.mvc.modelstatedictionary.remove.aspx) 移除特定項目。
```c#
ModelState.Clear();
ModelState.Remove("LastName");
```
## 參考資料  

- [使用 HTML Helper 在 ASP.NET MVC 呈現表單](http://msdn.microsoft.com/zh-tw/library/dd410596%28v=vs.100%29.aspx)
- 
-