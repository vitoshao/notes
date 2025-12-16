---
title: 全球化與當地語系的設定
layout: default
parent: Globalization and Accessibility
nav_order: 2
description: "全球化與當地語系的設定"
date: 2012-10-08
tags: [Web,ASPNET,Globalization and Accessibility]
postid: "360461750974012459"
---
#### About ASP.NET Resources

To display an ASP.NET page in one of several different languages, you could prompt the user for his or her preferred language, and then write if-then statements to update the text of your page.   However, that would be a complex and time-consuming.  

ASP.NET uses resource files to support multiple languages.   A resource file contains a language-specific set of text for a page or the entire site.   Your code accesses a resource file based on the user's requested language.  

If a resource file exists for that language, ASP.NET uses it and shows your site in the requested language.   If no resource file exists for the given request, ASP.NET uses the default language setting for the site.  

資料檔的類型有兩種：區域和全域。

# Using Local Resource Files

區域資源檔(local resource)是針對單一網頁，用來支援多語版本網頁的設計。  它必須存放在 App\_LocalResources 子目錄，不同目錄底下的網頁，都需要一個 App\_LocalResources 子目錄。  

資源檔可以依據使用者的語系和文化特性來定義，如下格式：  

- Default.aspx.resx
- Default.aspx.zh.resx
- Default.aspx.zh-TW.resx
- Default.aspx.zh-CN.resx

## Culture-Specific Local Resources

在 ASP.NET 的開發設計裡，culture 指的是 regional language 和 formattiong differences.  所以只要指定了不同的 culture ，.NET Framework 就會自動依須求調整顯示的格式。  

若要支援什麼語系，就必須在資源檔中加入該文化特性的生字範本(culture designation)。且命名格式如下：
```xml
<PageName>.aspx.<languageId>-<cultureId>.resx.
```

## Generating Local Resources

You can use Visual Studio to automatically generate the default version of your local resource file.  

- Open your page in Design mode
- From the Tools menu, select Generate Local Resource.  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg_bVwWFxd3jzFXFlW4uj1TqQd-2kULHk00IaHv6ng7mx6zMzs9ba2UCQKXDP53Q9zD1D6QPxrUbSEqJoDM1UrdtL11tJA6OOB88Ugd15V7_WaGX-C18cJ-oMfOgDqdco9ZB-BeLPavJao/s306/generate-local-resource.png)

result:

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhKgEFNU1RgHPTsTCK7bW1Lm3vMJ85QgkZsjU90dv1d45gKizsIcMHsZaugjUqNXKjvBRAvkrfwt6moHnptC65kSX8DFXTJGPPHofFGVa3174E1TpWrawSBjqKMpZNlMUF10do_OxdkeX0/s286/local-resource.png)

## Attaching Controls to a Resource

When Visual Studio generates a resource file, it also automatically changes your markup to attach page elements to the given resource.   To do so, it adds the markup **meta:resourcekey** as an attribute of a given control.   This tells ASP.NET to look up the value for this item from the resource file using the specified resource key (name of the data element in the .resx XML).  
```xml
<asp:Label ID="labName" runat="server" Text="請輸入姓名" meta:resourcekey="labNameResource1"></asp:Label>
```

The **meta:resourcekey** is then mapped to the resource file (.resx).   ASP.NET then uses this key to find any properties that might be set inside the resource file as meta:resourcekey.&lt;propertyname&gt;.  &lt;/propertyname&gt;

## Creating Language-Specific Local Resources

You typically create culture- and language-specific resource files by first creating a default  resource file. You can use this default file as a base. You copy and paste it to create a new  resource file for the target language or culture and name the file accordingly. You then need  to modify the resource values for the new language or culture. To copy the default resource    After creating a default resource file,   you can copy and paste it to create a new resource file for the target language or culture and name the file accordingly.  You then need to modify the resource values for the new language or culture.

## Testing Resource Files for Other Cultures

#### 載入瀏灠器預設的語系

當使用自動產生區域資源檔時，系統會自動在 Page 宣告中加入 culture="auto" 和 uiculture="auto" 屬性。  這二個屬性會讓系統自動根據瀏灠器的預設語系，載入適當的語系。  

底下是變更瀏灠器預設語系的地方

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhXUwB7h5b_C5yx5jnXHu_XnYMtXWA8fPy61wwnGccXa4RBH7EbN1e2y1hrcb8KaEL3wVmImb4b5tGFOauPzL7XbYRTrW-MFroFu_eHIxTC0nelFcOv3mcR2DA5enYA97m3I4B5QGmeTLw/s647/browser-default-language.png)

#### 以程式碼載入指定的語系

如果我們在網頁中設計一個語系切換功能，我們也可以透過程式碼來載入指定的語系。

要在網頁中變更語系，必須覆寫 [InitializeCulture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.initializeculture.aspx) 方法，並且指定 Page 的 [Culture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.culture.aspx) 和 [UICulture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.uiculture.aspx) 屬性。
```c#
//使用 Session 記錄使用者設定的語系
protected void ddlLanguage_SelectedIndexChanged(object sender, EventArgs e)
{
string currentPage = Page.Request.Path;

Session["culture"] = ddlLanguage.SelectedValue;
Response.Redirect(currentPage);
}

// 可以將這段程式碼，移到 BasePage 中使用
protected override void InitializeCulture()
{
if (!IsPostBack)
{
if (Session["culture"] == null)
Session["culture"] = "zh-TW";

string sCulture = Session["culture"].ToString();

Page.UICulture = sCulture;
Page.Culture = sCulture;
base.InitializeCulture();
}
}
```

為了方便每個網頁都可以使用以上程式碼，可以將 [InitializeCulture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.initializeculture.aspx) 方法放在 BasePage 裡頭，讓每個網頁繼承使用。

# Using Global Resources

A global resource is one that is defined to be read from any page or code that is in the Web site.   Global resource files are stored in the App\_GlobalResources folder at the root of an application.  

## Creating a Global Resource File

1. 要建立全域資源檔，必須在根目錄下先建立 App\_GlobalResources 目錄，如下所示：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjjm8ofU4spopO8tMHfvEX4qsulxiPN4o8GLM6cAMW4ZVmejMjIyQKF2FH6u6W6U_-SozYugUdWsCXGY1IIxxuc-OaohMwr68PBmzxoMZo7QM1EuvTQ0b4MsLDhPZ4qgowNaUc_MV-hYH4/s850/add-global-resouce.png)

2. 然後在這個目錄中加入資源檔。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgipM_fhobXEuB0KUn0hjREQxUhj8qpcVLO8mvbr7Jqh9c-zDxukESbz5eULYM9wp0MBIBd6oEp6B8Nw-b8cdG7g7rRT2emexGjYdJoV3dX5llbBTH8e9yM8u_hNNgyd-z6JcaQByQyBK4/s523/global-resource-file.png)

3. 你可以在資源檔中加入 strings, images, icons, audio, files, and other resources.。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjms-d4oeYxpUQf6bXBV9pzAJ7y-gHqp-6yY90Sfm5E5DVeDpRaYw7Bf2ExeIzNNp7Vba1jROX2HKw0AQ1njie3Kx0DPFSv7sDEjd0-n-jkZXwCPeIBSmIZoec1JE6nLYpY4g4iUFfEqdo/s228/global-resource.png)

4. 使用 copy and paste 再建立其他語系的資源檔。

## Attaching Control Properties to Global Resources

如果我們在全域資源檔中，定義了二個字串，公司名稱和專案名稱，那麼要如何在頁面裡使用這些資源？

方法就是透過控制項的 Expressions 屬性來指定。

1. 在控制項的屬性視窗中，找到 Expressions 屬性，並點選 (...) 按鈕。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhRHyKgfokdoCtCUfiHO-s3C4y0XTZ1ZZSiOChINbC08-xdxcKYR8c3q3OjgWtXnAnuaAzJvIl7s_k7AvloMtLb59DN6OLCZELpmjMeIHFxqBMucA7SbbvtdG6VQMd4UL9dQCZFZS56y6k/s850/bind-global-resource.png)
2. From the Bindable Properties list, select a property that you want to bind to a resource.
3. From the Expression type list, select **Resources**.
4. In the Expression Properties list, set **ClassKey** to the name of your global resource file
5. set **ResourceKey** to the name of the resource within the resource file.

底下是設定好的結果。
```xml
<asp:Label ID="labCorpName" runat="server" 
Text="<%$ Resources:Resource1, CorpName %>" 
meta:resourcekey="labCorpNameResource1"></asp:Label><br />

<asp:Label ID="labProjectName" runat="server" 
Text="<%$ Resources:Resource1, ProjectName %>" 
meta:resourcekey="labProjectNameResource1"></asp:Label><br />
```

## Accessing Resource Values Programmatically

那在程式之中，要如何存取全域資源檔呢？

很簡單，只要使用 Resources.ResourceFile.Resource 語法即可。例如：
```c#
protected void Button1_Click(object sender, EventArgs e)
{
labProjectName.Text = Resources.Resource1.ProjectName;
}
```

The preceding syntax assumes your resources are available at design time.   If they are, Visual Studio can generate the matching class files.   If, however, your resource files are not available at design time, Visual Studio cannot generate these classes.   In this case, you must use the [GetLocalResourceObject](http://msdn.microsoft.com/zh-tw/library/system.web.ui.templatecontrol.getlocalresourceobject.aspx) and [GetGlobalResourceObject](http://msdn.microsoft.com/zh-tw/library/system.web.ui.templatecontrol.getglobalresourceobject.aspx) methods.  
```c#
protected object GetGlobalResourceObject(string className, string resourceKey);
protected object GetGlobalResourceObject(string className, string resourceKey, Type objType, string propName);
protected object GetLocalResourceObject(string resourceKey);
protected object GetLocalResourceObject(string resourceKey, Type objType, string propName);
```
```c#
protected void Button1_Click(object sender, EventArgs e)
{
labProjectName.Text = GetGlobalResourceObject("Resource1", "ProjectName").ToString();
}
```

# HTML Layout Best Practices

為了配合不同語言的長相，因為同一個字可能有長有短，底下幾點指導方針，可建立較佳的版面配置，以避免因不同語系導至版面亂掉的問題：

- Avoid using absolute positioning and sizes for controls
- Use the entire width and height of forms
- Size elements relative to the overall size of the form  
```xml
<div style='
height: expression(document.body.clientHeight / 2);
width: expression(document.body.clientWidth / 2); '>
```
- Use a separate table cell for each control
- Avoid enabling the NoWrap property in tables
- Avoid specifying the Align property in tables

# Setting the Culture

## Setting Language and Culture

In an ASP.NET Web page, you use two different Page properties to set language and culture:  

#### Culture

This object determines the results of culture-dependent functions, such as the date, number, and currency formatting.   You can only define the [Culture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.culture.aspx) object with specific cultures that define both language and regional formatting requirements,   such as "es-MX" or "fr-FR."   You cannot define the [Culture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.culture.aspx) object with neutral cultures that define only a language, such as "es" or "fr" .  

#### UICulture

This property determines which global or local resources are loaded for the page.   You can define [UICulture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.uiculture.aspx) with either neutral or specific cultures.  

You define the [Culture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.culture.aspx) and [UICulture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.uiculture.aspx) properties by overriding the page's [InitializeCulture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.initializeculture.aspx) method.   From this method, define the [Culture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.culture.aspx) and [UICulture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.uiculture.aspx) properties, and then call the page's base [InitializeCulture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.initializeculture.aspx) method.   The following code sample demonstrates this, assuming that DropDownList1 contains a list of cultures:  
```c#
protected override void InitializeCulture()
{
if (Request.Form["DropDownList1"] != null)
{
//define the language
Page.UICulture = Request.Form["DropDownList1"];

//define the formatting (requires a specific culture)
Page.Culture = Request.Form["DropDownList1"];
}
base.InitializeCulture();
}
```

## Using Default Culture

You can also declaratively set the culture for a Web site or Web page.

For an entire Website, add a &lt;globalization&gt; section to the Web.config file, and then set the [UICulture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.uiculture.aspx) and [Culture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.culture.aspx) attributes.
```xml
<globalization uiculture="en" culture="en-US" />
```

For an Web page, define the [UICulture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.uiculture.aspx) and [Culture](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.culture.aspx) attributes of the @Page directive
```xml
<%@ Page uiculture="en" culture="en-US" %>
```
## 參考資料  

- [嵌入已當地語系化的資源供 JavaScript 檔使用](http://msdn.microsoft.com/zh-tw/library/bb398868%28v=vs.100%29.aspx)
- [將已當地語系化的資源加入至 JavaScript 檔](http://msdn.microsoft.com/zh-tw/library/bb398935%28v=vs.100%29.aspx)
-