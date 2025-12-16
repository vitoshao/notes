---
title: Models
layout: default
parent: MVC
nav_order: 2
description: "Models"
date: 2013-01-03
tags: [Web,ASPNET,MVC]
postid: "6641071826178666525"
---
建立一個 ASP.NET MVC 網站，通常會依下列步驟來做：   

1. 建立 Model
- 建立資料庫的 Entity 模型
2. 建立 Controller
- 使用 VisualStudio 程式碼產生器產生程式骨架 (這個步驟也會順便建立 View)
3. 調整 View
- 移除不要的顯示欄位或表單欄位

# Creating a Custom Model

MVC 之中的 Model 並沒有特別的限制，它就是一個類別，你可以自訂一個資料類別，也可以透過 visual studio 工具，自動產生一個資料模型。  底下是 Model 的主要功能：  

- 這個 Model 類別應當要提供 CRUD 的方法。
- 這個 Model 類別應當要提供取得清單資料或單筆資料的方法。
- 這個 Model 類別應當要包含 business rules 和 validation ，以便執行 CRUD 操作時使用。

通常 Model 類別會放在 Models 目錄之中，不過它就是個類別，只要參考的到的位置都行。  如果多個專案要共用，也可以建成類別庫。  

在 Models 中，通常習慣一個 entity 建一個 class ，底下就是一個 model 的雛型。  
```c#
public class CutsomModel
{
public int Id { get; set; }
public string Name { get; set; }
public string Address { get; set; }
public string City { get; set; }
public string State { get; set; }
public string PostalCode { get; set; }
public string Phone { get; set; }

public static int Insert(Customer customer)
{
}
public static void Update(Customer customer)
{
}
public static void Delete(int id)
{
}
public static List<Customer> GetAllCustomers()
{
}
public static Customer GetCustomer(int id)
{
}
}
```

## 範例：建立 ADO.NET Entity Data Model

ADO.NET 實體資料模型（ADO.NET Entity Data Model）是 .NET 支援的一個 ORM Framwwork 。  你可以利用 Visual Studio 工具，針對現有的資料庫，快速地產生 ADO.NET 實體資料模型。  

1. 加入「ADO.NET 實體資料模型」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiqE6jY4WXEjitL3hNkhA3sNAabu0J5Pbwh9xqHDWBw93Zn9X6fYhjaeS5o3zs_TyFtwc6IvE98EfWhWyRi6XyFCU-Bd1keDzvQOQTF4CAVjdshiijRwbfiAT-phExumTZXFrbAYn8Xy6w/s528/mvc-controller-01.png)

2. 因為資料庫已經存在，所以選擇「從資料庫產生」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhCtTTNSPMGEQlu7etIAp4o2F8pwnLlu1E4KX8tPT99JR1tF8a9bWPOYImtXlcNyAqhcrjOWjqz5mBY7nqfRufgrU08dM9_nZn4gHhuA9CpHXspZZY40NJB1eBc_nQSKNPD1ss7zXkYPOs/s626/mvc-controller-02.png)

3. 接著設定連線、資料表等與資料庫相關的資訊後，最後就會產生類似底下的實體資料模型。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhyW10YC6m60hU3PLHUl5KYykmJgkT4JPtusBHzmJMzJzcoZOZQscGaMfuKiheJUjpyuOFEvSRaZ_OCy-JIpJpeTwrRaqWOHXchg_4q2qeiWblLmL15g1POgsYnlqvXd-_A7Y5sFvKiC-A/s615/mvc-controller-03.png)

# Creating a Partial Model

在建立好 entity model 之後，若你想對欄位定義的額外屬性，例如變更顯示的文字或者加入驗證資訊等，  你就可以自訂一個**中繼類別(metadata class)**來定義欄位的屬性，然後再利用 partial class ，將該中繼類別指定給 MetadataType 屬性。  這些 MetadataType 屬性都包含在 [DataAnnotations](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.aspx) 之中。  例如：  
```c#
using System.ComponentModel.DataAnnotations;
using System.Web.DynamicData 

namespace MvcApplication5_Auth.Models
{
[MetadataType(typeof(CustomersMetadata))]
public partial class Customers
{}

public partial class CustomersMetadata
{
[DataType(DataType.Date)]
[Display(Name = "連絡電話")]
[Required(ErrorMessage = "請輸入連絡電話")]
[RegularExpression(@"^\(?\d{2}\)?-\d{4}-\d{4}$")]
public object Phone { get; set; }

[DataType(DataType.EmailAddress)]
public object Email { get; set; }

[DataType(DataType.Url)]
public object WebSite { get; set; }

//使用名稱為 CalendarPicker 的 Partial View 來顯示 BirthDate 欄位資料
[UIHint("CalendarPicker")]
public object BirthDate { get; set; }
}
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh52Nrurq7OmgoVG9X0W8FIbmKgaCPHEyBNypQN40cRueEqnB-KMfSTzsVAGxZLJBw7aa-DjrtssDJEinUsUtLuGCXVDzOybcINwyu-DmapVWaGtgNR9J2gw7fe_L62Uz9MhUTkIpMfByo/s457/mvc-DataAnnotations-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOKyNHTRhmabJ8F26ZZUeAjvyALm49XSfGBCVOAXWXf9-WXnjlJCZIDBa_41NTOAQ9xkiZE9YE-xYEvotAYl85t7QPUix_xzGU2CtAFijUzRxfttCocUWxDFTPbYVCctL14-QKs9rv63o/s651/mvc-DataAnnotations-2.png)

以下是常見用來設定欄位的屬性類別：

- [Editable](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.editableattribute.aspx) ：指定資料欄位是否可編輯
- [DataType](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.datatypeattribute.aspx) ：指定資料欄位的資料型態  
DataType 有幾種內建的 template 可以用，例如：當指定為 Url 時，就會顯示為超連結，當指定為 EmailAddress 型別時，就是會加上 mailto。
- [UIHint](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.uihintattribute.aspx) ：使用特定範本或自訂控制項來顯示資料欄位。
- [ScaffoldTable](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.scaffoldtableattribute.aspx) ：指定資料表 (Data Table) 是否使用 Scaffolding。
- [ScaffoldColumn](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.scaffoldcolumnattribute.aspx) ：指定資料行 (Data Column) 是否使用 Scaffolding。
- [DisplayFormat](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.displayformatattribute.aspx)  ：指定資料欄位的顯示格式。

# Model Validation Rules

One of the easiest ways to add validation and business rules to your model is to use the [DataAnnotations](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.aspx) classes.  
```c#
[Required(ErrorMessage = "CompanyName id is required")]
[Display(Name="公司名稱")]
public object CompanyName { get; set; }
```

You can also use the [ValidationException](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.validationexception.aspx) object to throw an exception in your custom validation method .  
```c#
public partial class Customers
{
partial void OnCompanyNameChanging(String value)
{
if (value.Trim().Length == 0)
{
throw new ValidationException("公司名稱不可以空白");
}
}
}
```

以下是常見用來驗證欄位的屬性類別：

- [CustomValidation](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.customvalidationattribute.aspx) ：自訂驗證
- [CreditCard](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.creditcardattribute.aspx) ：驗證CreditCard
- [EmailAddress](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.emailaddressattribute.aspx) ：驗證Email
- [MinLength](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.minlengthattribute.aspx) ：驗證欄位值的Length
- [Phone](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.phoneattribute.aspx) ：驗證PhoneNumber
- [Range](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.rangeattribute.aspx) ：驗證欄位值的Range
- [RegularExpression](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx) ：驗證RegularExpression
- [Required](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.requiredattribute.aspx) ：驗證必要輸入欄位
- [Url](http://msdn.microsoft.com/zh-tw/library/system.componentmodel.dataannotations.urlattribute.aspx) ：驗證Url