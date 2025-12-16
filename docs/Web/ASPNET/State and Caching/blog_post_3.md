---
title: 使用快取
layout: default
parent: State and Caching
nav_order: 3
description: "使用快取"
date: 2012-10-17
tags: [Web,ASPNET,State and Caching]
postid: "8021294905507647016"
---
Caching 是 ASP.NET 的一種機制，由 [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 類別實作，用來提升網頁的效率。  它可以把經常使用的資料存放在記憶體中，就不用每次都得到檔案或資料庫提取。  當然我們也可以自行實作這樣子的機制，只是 ASP.NET 都幫我們做好了，讓我們不用寫程式碼就可以處理複雜的快取。  ASP.NET 提供二種不同的快取：  

- **應用程式快取 (Application caching)**  
應用程式快取是透過程式碼將資料以 key/value 的型態儲存在記憶體中。      它有點類似網站之中的 [Application](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplicationstate.application.aspx) State ，不同的是，它的生命週期不像 [Application](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplicationstate.application.aspx) 會跟隨整個網站的生命週期。      它會隨著記憶體空間的限制或時間過期等因素，自動的由集合中被移除。
- **網頁輸出快取 (Page output caching)**  
網頁輸出快取可以將 render 後的網頁的 html 儲存起來，提供給後續相同的網頁要求，以減少回應時間。

# 應用程式快取 (Application caching)

## Using the Cache Object

「應用程式快取」指的是將資料儲存在 [Cache Object](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 之中的方式。  

- [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 類別定義在 [System.Web.Caching](http://msdn.microsoft.com/zh-tw/library/system.web.caching.aspx) 命名空間。
- [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 物件是 [Page](http://msdn.microsoft.com/zh-tw/library/system.web.ui.page.aspx) 物件的一個屬性。
- [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 物件是一個**集合**物件。
- [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 物件是整個應用程式共用的。
- 使用 [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 物件前必須先判斷是不是 null 。
```c#
Cache["Greeting"] = "Hello, world!";
if (Cache["Greeting"] != null)
value = (string)Cache["Greeting"];
else
value = "Hello, world!";
```

## 在 Cache 中新增或移除項目

前面我們使用類似存取 [Application](http://msdn.microsoft.com/zh-tw/library/system.web.httpapplicationstate.application.aspx) 物件的方式來使用 [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 物件。  另外，也可以直接叫用它提供的 [Insert](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.insert.aspx) 方法來新增快取。  

使用 [Insert](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.insert.aspx) 方法新增快取，可以同時設定快取物該在何時自動移除。例如以下屬性都可以用來設定讓快取失效：  

- **dependencies**：設定一個相依物件，當相依物件被改變的時候，快取就失效。
- **absoluteExpiration**：這個參數是一個**時間**型別的參數（從設定當下開始算），不管快取有沒有被讀取，只要過了這個時間，快取就會被清除。
- **slidingExpiration**：這個參數是一個**時間區間**型別的參數（從每次被存取開始算），如果快取在這個時間內，一直都沒有被讀取，快取就會被清除。
- **priority**：設定優先權，如果記憶體效能降低時，優先權較低的物件會先被移除。

另外 onRemoveCallback 屬性可以讓我們設定一個當快取失效時的回呼方法。

####  [Cache.Insert](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.insert.aspx) 方法
```c#
public void Insert(string key, object value);
public void Insert(string key, object value, CacheDependency dependencies);
public void Insert(string key, object value, CacheDependency dependencies, DateTime absoluteExpiration, TimeSpan slidingExpiration);
public void Insert(string key, object value, CacheDependency dependencies, DateTime absoluteExpiration, TimeSpan slidingExpiration, CacheItemUpdateCallback onUpdateCallback);
public void Insert(
string key,                                  // key
object value,                                // value
CacheDependency dependencies,                // 相依性物件，當相依性物件有任何變更時，快取就會被清除。 (optional)
DateTime absoluteExpiration,                 // 不管快取有沒有被讀取，只要過了這個時間，快取就會被清除。 (optional)
TimeSpan slidingExpiration,                  // 如果快取在這個時間內，一直都沒有被讀取，快取就會被清除。 (optional)
CacheItemPriority priority,                  // 如果記憶體效能降低時，優先權較低的物件會先被移除。 (optional)
CacheItemUpdateCallback onUpdateCallback     // 當物件從快取移除時，所呼叫的事件處理常式。 (optional)
);
```

#### 注意事項：

- absoluteExpiration 和 slidingExpiration 不能同時使用。
- 若使用 absoluteExpiration ，請將 slidingExpiration 參數設定為 [NoSlidingExpiration](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.noslidingexpiration.aspx) 。
- 若使用 slidingExpiration ，請將 absoluteExpiration 參數設定為 [NoAbsoluteExpiration](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.noabsoluteexpiration.aspx) 。
- 

####  [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 物件的其他方法
```c#
public object Add(string key, object value, 
CacheDependency dependencies, 
DateTime absoluteExpiration, 
TimeSpan slidingExpiration, 
CacheItemPriority priority, 
CacheItemRemovedCallback onRemoveCallback);
public object Remove(string key);
public object Get(string key);
```

- Insert：Inserts an item into the Cache object with a cache key
- Add：Adds the specified item to the Cache object with dependencies, expiration and priority policies
- Remove：從應用程式的 Cache 物件移除指定的項目。
- Get：擷取的快取項目，或如果沒有找到索引鍵時，則為 null。

## 快取相依物件（Cache Dependency）
```c#
public CacheDependency(string filename);
public CacheDependency(string[] filenames);
public CacheDependency(string[] filenames, string[] cachekeys);
public CacheDependency(string[] filenames, string[] cachekeys, CacheDependency dependency);
public CacheDependency(string[] filenames, string[] cachekeys, CacheDependency dependency, DateTime start);
...
```

[CacheDependency](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cachedependency.aspx) 物件是用來設定一個關聯物件，用以監控 [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 物件。  當關聯物件發生任何異動時， [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 物件就會自動失效。  這個關聯物件可以是：檔案、快取索引鍵、快取索引鍵陣列、 [CacheDependency](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cachedependency.aspx) 物件。  

### 使用檔案或目錄做為快取相依物件

下面這個例子，我們將一個檔案內容放置至 [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 中，直到這個檔案被異動才會讓快取失效。
```c#
string filename = "Test.xml";
CacheDependency cacheDepd = new CacheDependency(Server.MapPath(filename));
Cache.Insert("FileCache", File.ReadAllText(Server.MapPath(filename)), cacheDepd);
```

### 使用快取索引鍵的陣列做為快取相依物件

下面這個例子，已知 [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 中有二個項目。  我們可以將這二個 [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 項目當作是某個 [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 的相依物件，  則當這二個快取項目有任何異動時，這個新增的快取就會失效。
```c#
//原有 cache
Cache["CacheKey1"] = "Cache Value 1";
Cache["CacheKey2"] = "Cache Value 1";

//以原有 cache 的 key 值，建立相依物件
string[] cacheKeys = { "CacheKey1", "CacheKey2" };
CacheDependency cacheDepd = new CacheDependency(null, cacheKeys);

//新增一個快取，並以上面二個快取當做相依物件
Cache.Insert("CacheKeySet", "My Cache Content", cacheDepd);
```

### 同時使用二種快取相依物件
```c#
//CacheDependency 1 

string filename = "Test.xml";
CacheDependency cacheDepd1 = new CacheDependency(Server.MapPath(filename));

//CacheDependency 2

Cache["CacheKey1"] = "Cache Value 1";
Cache["CacheKey2"] = "Cache Value 1";
string[] cacheKeys = { "CacheKey1", "CacheKey2" };
CacheDependency cacheDepd2 = new CacheDependency(null, cacheKeys);

AggregateCacheDependency aggreDepd = new AggregateCacheDependency();
aggreDepd.Add(cacheDepd1);
aggreDepd.Add(cacheDepd2);
Cache.Insert("AggreCache", "This is Aggregate Cache Value1", aggreDepd);
```

## Setting an Absolute Cache Expiration

To cache an object for a set amount of time, pass the absoluteExpiration parameter to the [Cache.Insert](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.insert.aspx) method.  

This parameter is a **DateTime** type
```c#
DateTime endTime = DateTime.Now.AddMinutes(10);
Cache.Insert("CacheKey1", "Cache Value 1", null, endTime, Cache.NoSlidingExpiration);
```

## Setting a Sliding Cache Expiration

A sliding expiration indicates the amount of time that must elapse between subsequent requests before an item is removed from the cache.  Each time a new request comes in for an item, the sliding scale restarts.  

This parameter is a **TimeSpan** type
```c#
TimeSpan idleTime = new TimeSpan(0, 2, 0);
Cache.Insert("CacheKey1", "Cache Value 1", null, Cache.NoAbsoluteExpiration, idleTime);
```

Especially with a sliding cache expiration,   it is possible that heavy usage will result in an item never being removed from the cache,   preventing updates to the original object from being accessed.     By defining an absolute expiration in addition to a sliding expiration, you can be sure an object is eventually removed.   When possible, add a cache dependency to your absolute and sliding cache expirations to immediately remove the cached object when the original is updated.

## 如何同時使用 absoluteExpiration 和 slidingExpiration

前面提過，當新增一個快取物件時，不能同時設定 absoluteExpiration 和 slidingExpiration 屬性。  但是，如果有個需求希望資料可以暫存１０分鐘，但是，若持續２分鐘內都沒人使用就失效，那要怎麼辨？  
```c#
// １）設定一個１０分鐘後會過期的 cache
DateTime endTime = DateTime.Now.AddMinutes(10);
Cache.Insert("10MinCache", "10MinCache", null, endTime, Cache.NoSlidingExpiration);

// ２）利用上面 cache 物件建立一個相依物件
CacheDependency cd = new CacheDependency(null, new string[] {"10MinCache"});

// ３）設定一個 Sliding=2 的 cache ，而且相依步驟２中的相依物件
TimeSpan idleTime = TimeSpan.FromMinutes(2);
Cache.Insert("MainCache", "MainCache Data", cd, Cache.NoAbsoluteExpiration, idleTime);
```

上面例子 MainCache 設定為 SlidingExpiration = 2 min  
所以如果持續被讀取，則持續有效。  
但是，若到了 10 分鐘之後，就會隨著相依物件失效而跟著失效。

# 網頁輸出快取 (Page output caching)

一般狀況下，若瀏覽器連結到某個網頁時，瀏覽器就會在本機中，保留一份網頁的複本。  等到下一次連結相同網頁時，只要簡單的確認快取是否仍然有效，若還在有效時間內，就會使用快取中的網頁內容，這樣就可以提升網站展示的效率。  以下三種情況，是快取機制運用在不同狀態下的簡要說明：  

#### Client-site caching (用戶端快取)

使用「用戶端快取」，Web主機會對每一位使用者會產生不同的文件資料。  所以，在快取的有效時間內，假如有一個使用者連結一個特定的動態網頁100次，該網站主機只需要回應一次網頁內容。  若有 100 個使用者也都連結這個網頁 100 次，則網站主機也只需要回應網頁內容 100 次。  

#### Page output caching (網頁輸出快取)

為了更進一步減少 render 時間以增加效率，ASP.NET也支援「網頁輸出快取」。  ASP.NET 會在主機記憶中保留一份 render 後的備份網頁，當下次有使用者(即使是不同使用者)提出相同要求，ASP.NET 就會直接回應這份網頁內容，以減少回應時間。  這種做法，對於需要耗時查詢的網頁，是增加效率的好方法。  

#### Page fragment caching (網頁片段快取)

上述方法，最適合不常變更的資訊。但是如果一個網頁中若有部份資訊，必須針對不同使用者而異，或者包含隨時變動的資訊，比如說，使用者個人資訊或計數器等，這些資訊就不適合使用整個網頁的快取。  所以 ASP.NET 也提供替代更新快取，也就是只更新快取內容中的片段資訊。  

## 以宣告方法控制網頁輸出快取

要設定一個網頁的快取，必須使用 [@ OutputCache](http://msdn.microsoft.com/zh-tw/library/hdxfb6cy.aspx) 指示詞來設定。  

底下為 [@ OutputCache](http://msdn.microsoft.com/zh-tw/library/hdxfb6cy.aspx) 宣告的相關屬性：

| Attribute | Description |
| --- | --- |
| Duration | 設定快取到期時間，以**秒**為單位，這是唯一必要的屬性。 |
| Location | [OutputCacheLocation](http://msdn.microsoft.com/zh-tw/library/system.web.ui.outputcachelocation.aspx)列舉值  &lt;br&gt;  這個屬性是設定可以存放快取的裝置。  &lt;br&gt;  它是一個列舉值：Any、Client、Downstream、Server、ServerAndClient 或 None。  &lt;br&gt;  預設值為 Any，表示可以快取在所有具快取功能的網路裝置上，如：web 伺服器、Proxy 伺服器、瀏覽器。 |
| CacheProfile | 你可以在 web.config 設定一個快取設定檔，再使用 [CacheProfile](http://msdn.microsoft.com/zh-tw/library/system.web.ui.outputcacheparameters.cacheprofile.aspx) 屬性指定使用該設定檔中的設定值。 |
| NoStore | 決定是否防止敏感資訊的次要儲存區。 |
| Shared | 判斷使用者控制項輸出是否可以被多個頁面所共用。預設值為 false。 |
| VaryByParam | 這個屬性是用來設定變更輸出快取的字串清單。  &lt;br&gt;  根據預設值，這些字串對應至使用 GET 方法屬性傳送的查詢字串值或使用 POST 方法傳送的參數。  &lt;br&gt;  如果您不想指定參數變更快取的內容，請將這個值設定為 none。  &lt;br&gt;  如果希望所有參數值都可以變更輸出快取，請將這個屬性設定為星號 (\*)。   &lt;br&gt;  如果希望特定的參數值才可以變更輸出快取，請使用分號分隔參數名稱。 |
| VaryByControl | 用來變更使用者控制項輸出快取的分號分隔字串清單。  &lt;br&gt;  這些字串表示在使用者控制項中所宣告之 ASP.NET 伺服器控制項的 ID 屬性值。 |
| SqlDependency | 字串值，用以辨識網頁或控制項之輸出快取所相依的資料庫集和資料表名稱組。 |
| VaryByCustom | 如果您為這個屬性指定 "browser"，快取項目將根據瀏覽器類型和主要版本號碼而不同。 |
| VaryByHeader | 取得或設定一組以逗號分隔的標頭名稱，用來變更快取項目。 |

注意事項 

- 使用者控制項 (.ascx 檔案) 不支援：Location、CacheProfile、NoStore、VaryByHeader 屬性。
- ASP.NET網頁 (.aspx 檔案) 不支援：Shared、ProviderName 屬性。
- 使用 [@ OutputCache](http://msdn.microsoft.com/zh-tw/library/hdxfb6cy.aspx) 指示詞時，一定要指定一個快取有效時間
- 使用 [@ OutputCache](http://msdn.microsoft.com/zh-tw/library/hdxfb6cy.aspx) 指示詞時，VaryByControl 或 VaryByParam 至少必須使用其中一個屬性。

### 範例說明

假設原本有一個網頁 Customers.aspx ，程式會由 db 中取出客戶資料，反應到頁面上的 GridView 之中。

#### Case 1
```xml
<%@ OutputCache Duration="60" VaryByParam="none" %>
```

若這麼設定快取，則在 60 秒內，無論你在後面加入任何參數，如 ?region=WA 或 ?id=NM ，都會得到相同的結果。  即使這段時間內，資料庫中的資料有被異動，但是因為 ASP.NET 是以 [Cache](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cache.aspx) 中的資訊做回應，所以頁面上的資料就都會不變動。  即使是按了 Ctrl+F5 或者關閉瀏灠器後再開啟也是一樣。  除非在 60 秒之後，網頁才會重新由資料庫撈取資料，產出新的網頁內容。  

#### Case 2
```xml
<%@ OutputCache Duration="60" VaryByParam="id; region" %>
```

若這麼設定，則在60秒內，只要 Url 中的 id 或 region 參數值有變化，網頁才會重新由資料庫撈取資料，產出新的網頁內容。  

#### Case 3
```xml
<%@ OutputCache Duration="60" VaryByParam="\*" %>
```

若這麼設定，則在60秒內，只要任何參數值不同，網頁都會重新由資料庫撈取資料，產出新的網頁內容。  

#### Case 4
```xml
<%@ OutputCache Duration="60" VaryByParam="\*" Location="Client" %>
```

若這麼設定，快取是存放在用戶端，  在60秒內，只要 URL 中的參數沒有變化，就會優先使用用戶端中的 cache 資料。  不過如果使用者使用 Ctrl+F5 ，即使參數值都沒變，也會重新 render 網頁，因為 Server 端並沒有 cache ，只能執行程式才能輸出網頁內容。  

[Location](http://msdn.microsoft.com/zh-tw/library/system.web.ui.outputcachelocation.aspx) 是用設定存放 cache 的地方，有三種地方可以設定： Client | Server | Downstream (代理服務器) ，或者設成 None | Any | ServerAndClient 。

#### **註**

以上提到用在 VaryByParam 屬性中的設定名稱，可以是 Url 中的 [QueryString](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.querystring.aspx) 的名稱，也可以是 Form 內控制項的 ID 名稱。  
但是，用在 VaryByControl 屬性中的控制項 ID，不一定是 aspx 檔案中控制項的 ID, 要使用網頁 render 後的 ID，例如：ctl00$MainContent$TextBox1。

## 以程式碼控制網頁輸出快取

如果需要在執行階段才決定是否使用快取，那麼就可以透過 [Response.Cache](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.cache.aspx) 物件來加以設定。

#### 幾個控制快取屬性的方法：

- [SetExpires](http://msdn.microsoft.com/zh-tw/library/system.web.httpcachepolicy.setexpires.aspx) ：設定 [Expires](http://msdn.microsoft.com/zh-tw/library/system.web.httpcookie.expires.aspx) HTTP 標頭為絕對日期和時間。
- [SetCacheability](http://msdn.microsoft.com/zh-tw/library/system.web.httpcachepolicy.setcacheability.aspx)：相當於宣告方式中的 Location 設定，用來指明 cache 的行為。
- **NoCache**：可以透過欄位名稱，明確拒絕該項的快取回應；
- **Private**：預設值。只能在用戶端做快取。
- **Server**：只在原始伺服器進行快取。
- **Public**：可由用戶端和共用 (Proxy) 快取進行快取。
- **ServerAndNoCache**：套用 Server 和 NoCache 設定，指出在伺服器上快取內容，但明確拒絕所有其他項的快取回應能力。
- **ServerAndPrivate**：只能在伺服器和用戶端上快取回應。Proxy 伺服器不能快取回應。
- [SetValidUntilExpires](http://msdn.microsoft.com/zh-tw/library/system.web.httpcachepolicy.setvaliduntilexpires.aspx) ：指定 ASP.NET 快取是否應該忽略使快取失效之用戶端所傳送的 HTTP Cache-Control 標頭。

## 部份網頁快取（Partial-Page Caching）

前面提到的快取，都是針對整個網頁。若希望網頁中只有某一個區塊想要使用快取，有二個方法：控制項快取和快取後替換。（control caching and post-cache substitution）  

### 控制項快取 (control caching)

這個做法常用在網頁中有部份資訊必須耗時運算才可以取得，將它獨立出來，讓較少伺服器資源的網頁持續動態產生。  透過下面二個步驟，就可以將部份網頁的內容和主網頁分開而獨立運作。

1. 將須要使用快取的那部份網頁，利用**使用者控制項**包起來。
2. 接著在使用者控制項中，使用 [@ OutputCache](http://msdn.microsoft.com/zh-tw/library/hdxfb6cy.aspx) 宣示詞設定快取。

#### 使用 @ [OutputCache](http://msdn.microsoft.com/zh-tw/library/hdxfb6cy.aspx) 宣示詞設定

下面自訂控制項的設定，會將輸出快取設定成 120 秒。
```xml
<%@ OutputCache Duration="120" VaryByParam="None" %>
```

#### 使用 [PartialCaching](http://msdn.microsoft.com/zh-tw/library/system.web.ui.partialcachingattribute.aspx) 屬性設定

透過 [PartialCaching](http://msdn.microsoft.com/zh-tw/library/system.web.ui.partialcachingattribute.aspx) 屬性宣告使用者控制項如何使用快取輸出
```c#
[PartialCaching(120)]
public partial class CachedControl : System.Web.UI.UserControl
{
// Class Code
}
```

**注意：**

若 Page 和 UserControl 的快取時間設定不同

- 若 UserControl 的快取時間比較短，則 UserControl 的快取時間會成和 Page 一樣。
- 若 UserControl 的快取時間比較長，當 Page 重新產生，UserControl 會繼續使用取快，直到它自已的快取時間失效。

### 快取後替換 (post-cache substitution)

在一個快取的網頁中，有時候頁面上有一些簡單的元素，我們不希望使用快取，例如，時間、計數器等。  若網頁中有一區塊不想要快取，就可以用快取後替換的技巧。有三種方法可以達到快取後替換的須求：  

- 叫用 Response.[WriteSubstitution](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.substitution.aspx) 方法來插入回應內容。
- 使用 [Substitution](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.substitution.aspx) 控制項
- 使用 AdRotator 控制項

#### 使用 [WriteSubstitution](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.writesubstitution.aspx) 方法

底下例子在一個使用 [OutputCache](http://msdn.microsoft.com/zh-tw/library/hdxfb6cy.aspx) 的頁面中，同時輸出二個時間。  當程式執行時，使用 [WriteSubstitution](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.writesubstitution.aspx) 這個輸出只要 reflesh 頁面，時間值就會不停的變換。  使用 Write 的這個輸出，時間值就不會變換，必須等 cache 時間過了之後才會重新產生。  

使用 [WriteSubstitution](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.writesubstitution.aspx) 方法時，必須傳入一個回呼方法，該方法必須同 HttpResponseSubstitutionCallback 簽章。
```xml
<%@ Outputcache duration="10" varybyparam="none" %>

Non Cashe Time : <% Response.WriteSubstitution(new HttpResponseSubstitutionCallback(GetCurrentDateTime)); %>
Cashe Time : <% Response.Write(DateTime.Now.ToString()); %>
```
```c#
public static string GetCurrentDateTime(HttpContext context)
{
return DateTime.Now.ToString() + "<br/>";
}
```

#### 使用 [Substitution](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.substitution.aspx) 控制項

[Substitution](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.substitution.aspx) 控制項就類似一個 Label 控制項，但不受 [OutputCache](http://msdn.microsoft.com/zh-tw/library/hdxfb6cy.aspx) 限制。  它只有一個 [MethodName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.substitution.methodname.aspx) 屬性，用來指定產生內容的方法。  這個方法必須接受一個 HttpContext 參數，並回傳一個 String。  
```xml
<asp:Substitution ID="Substitution1" runat="server" MethodName="GetCurrentDateTime" />
```
```c#
static string GetCurrentDateTime(HttpContext context)
{
return DateTime.Now.ToString() + "<br/>";
}
```

# 手動讓快取網頁失效

當ASP.NET收到使用者的需求時，會自動根據網頁中定義的快取原則判斷快取是否仍然有效。  若有效，則將快取輸出給用戶端，若無效，則重新 render 該網頁。  

不過有些時候，我們必須透過一些額外的邏輯判斷來決定快取是否依然有效。  例如，判斷搜尋條件是否有變更，以決定原搜尋結果的快取是否依然有效；或者判斷某個檔案的修改時間，以判斷該檔案的快取是否依然有效  

ASP.NET 也提供以下方式，可以透過程式碼來控制快取的有效性。  

## HowTo: 以程式碼控制快取網頁的有效性

若要以程式設計方式設定快取網頁的有效性，可以如下步驟設定：

底下例子，我們根據 [QueryString](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.querystring.aspx) 中的 Status 變數值來回應 [HttpValidationStatus](http://msdn.microsoft.com/zh-tw/library/system.web.httpvalidationstatus.aspx) ，以自行控管快取的有效性。  

#### 1. 撰寫回應 ValidateCacheOutput 事件的程式碼

我們必須先撰寫一個 **ValidateCache** 方法，這個方法要符合 [HttpCacheValidateHandler](http://msdn.microsoft.com/zh-tw/library/system.web.httpcachevalidatehandler.aspx) 簽名，用來在網頁 render 前，判斷是否要以快取版本的網頁回應，或是重新產生新的網頁內容回應。  這個方法必須給定一個 [HttpValidationStatus](http://msdn.microsoft.com/zh-tw/library/system.web.httpvalidationstatus.aspx) 型態的變數，以決定快取的有效性。  然後再 Page\_Load 事件中，使用 [AddValidationCallback](http://msdn.microsoft.com/zh-tw/library/system.web.httpcachepolicy.addvalidationcallback.aspx) 註冊驗證回呼函式。  

####  [HttpValidationStatus](http://msdn.microsoft.com/zh-tw/library/system.web.httpvalidationstatus.aspx) 列舉

- [Invalid](http://msdn.microsoft.com/zh-tw/library/system.web.httpvalidationstatus.aspx) ：快取無效，使用重新處理的網頁內容，取代原有快取網頁內容。
- [IgnoreThisRequest](http://msdn.microsoft.com/zh-tw/library/system.web.httpvalidationstatus.aspx) ：本次需求使用重新處理的網頁內容，但原有快取網頁仍然有效。
- [Valid](http://msdn.microsoft.com/zh-tw/library/system.web.httpvalidationstatus.aspx) ：快取網頁有效。
```c#
protected void Page_Load(object sender, EventArgs e)
{
Response.Cache.AddValidationCallback(new HttpCacheValidateHandler(ValidateCache), null);
}

public static void ValidateCache(HttpContext context, Object data, ref HttpValidationStatus status)
{
if (context.Request.QueryString["Status"] != null)
{
string pageStatus = context.Request.QueryString["Status"];

if (pageStatus == "invalid")
status = HttpValidationStatus.Invalid;  //指示快取無效，使用重新處理的網頁內容，取代原有快取網頁內容。
else if (pageStatus == "ignore")
status = HttpValidationStatus.IgnoreThisRequest; //指示本次需求使用重新處理的網頁內容，但原有快取網頁仍然有效。
else
status = HttpValidationStatus.Valid; //指示快取網頁有效。
}
else
status = HttpValidationStatus.Valid;
}
```

#### 2. 在 Page\_Load 事件中，將步驟１定義的方法，傳遞給 [AddValidationCallback](http://msdn.microsoft.com/zh-tw/library/system.web.httpcachepolicy.addvalidationcallback.aspx) 方法
```c#
protected void Page_Load(object sender, EventArgs e)
{
Response.Cache.AddValidationCallback(new HttpCacheValidateHandler(ValidateCacheOutput), null);
}
```

#### 3. 在 aspx 網頁中加入以下設定
```xml
<%@ outputcache duration="1200" location="server" varybyparam="none" %>

<body>
<form id="form1" runat="server">
<div>
<%=DateTime.Now %>
</div>
</form>
</body>
```

## HowTo: 利用相依性物件控制快取網頁的有效性

當我們使用 **Page.Response** 方法時，可以設定相依物件，來判斷是否使用 cache 中的資料回應。

假設有個網頁的主要內容來自於一個 XML 文件，則該網頁只有在 XML 檔案變更時才需要重新處理，這個檔案就是快取網頁的相依物件。  必要時，相依物件允許一個以上。  

下列方法，都是 **HttpResponse** 用來設定相依性清單的方法：  

- [AddCacheDependency](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.addcachedependency.aspx) ：使用 [CacheDependency](http://msdn.microsoft.com/zh-tw/library/system.web.caching.cachedependency.aspx) 物件，建立相依性。
- [AddCacheItemDependency](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.addcacheitemdependency.aspx) ：使用一個快取項目，建立相依性。
- [AddCacheItemDependencies](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.addcacheitemdependencies.aspx) ：使用多個快取項目，建立相依性。
- [AddFileDependency](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.addfiledependency.aspx) ：使用一個 File 物件，建立相依性。
- [AddFileDependencies](http://msdn.microsoft.com/zh-tw/library/system.web.httpresponse.addfiledependencies.aspx) ：使用多個 File 物件，建立相依性。

#### AddCacheDependency 範例
```c#
private void Page_Load(object sender, System.EventArgs e)
{
// Create a Cache Dependency 
// using a CacheDependency object.
CacheDependency authorsDependency = new CacheDependency("authors.xml");

// Make the page invalid if either of the
// cached items change or expire.        
Response.AddCacheDependency(authorsDependency);

// Display the current time for cache reference
lblOutputCacheMsg.Text = DateTime.Now.ToString();
}
```

#### AddCacheItemDependency 範例

這個範例使用 bookData 
```c#
Response.AddCacheItemDependency("bookData");

//判斷
DataView source = (DataView)Cache["bookData"];
if (source == null)
{
source = DataHelper.GetBookData();
lblCacheMsg.Text = "Data generated explicitly.";
}
else
{
lblCacheMsg.Text = "Data retrieved from application cache.";
}
dgBooks.DataSource = source;
dgBooks.DataBind();
```

#### AddFileDependency 範例
```c#
String FileName = "C:\\Files\\F1.txt";
Response.AddFileDependency(FileName);
```

# 整體網站的快取設定

我們也可以將快取設定寫在 web.config 裡的 &lt;system.web&gt; 項目中，然後在網頁裡的 [OutputCache](http://msdn.microsoft.com/zh-tw/library/hdxfb6cy.aspx) 宣告中，透過 [CacheProfile](http://msdn.microsoft.com/zh-tw/library/system.web.ui.outputcacheparameters.cacheprofile.aspx) 屬性指定使用哪這個設定檔名稱。  這樣在參考或管理上都會比較方便。  
```xml
<system.web>
<caching>
<outputCacheSettings>
<outputCacheProfiles>
<add name="OneMinuteCache" enabled="true" duration="60" />
</outputCacheProfiles>
</outputCacheSettings>
</caching>
</system.web>
```

宣告時，CacheProfiles 沒有設定的屬性，也可以加在這裡。
```xml
<%@ OutputCache CacheProfile="OneMinuteCache" VaryByParam="none" %>
```

# 如何讓網頁自動 refresh

底下設定與快取無關，只是 cache 與網頁刷新有關，所以把這個問題放在這裡

要讓網頁可以在指定的時間自動刷新，必須使用 &lt;meta&gt; 標籤，content 屬性可用來設定刷新的間隔時間，單位秒。
```xml
<head id="Head1" runat="server">
<meta http-equiv="refresh" content="2">
</head>
```

底下設定當網頁自動刷新時，同時導向特定頁面。
```xml
<head id="Head1" runat="server">
<meta http-equiv="refresh" content="2; URL=http://www.xxx.com">
</head>
```
## 參考資料  

- [ASP.NET 快取概觀](http://msdn.microsoft.com/zh-tw/library/ms178597%28v=vs.100%29.aspx)
- [檢查快取網頁的有效性](http://msdn.microsoft.com/zh-tw/library/a5e5hdyz%28v=vs.100%29.aspx)
- [@ OutputCache](http://msdn.microsoft.com/zh-tw/library/hdxfb6cy%28v=VS.100%29.aspx)
- [ASP.NET 部分網頁快取](http://msdn.microsoft.com/zh-tw/library/h30h475z.aspx)
- [動態更新快取頁面的部分內容](http://msdn.microsoft.com/zh-tw/library/ms227429.aspx)
- [\[黃忠成\]DataSource Control 之Cache機制](http://www.dotblogs.com.tw/code6421/archive/2008/05/07/3868.aspx)