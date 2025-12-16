---
title: Health Monitoring 網站
layout: default
parent: 除錯與系統監控
nav_order: 3
description: "Health Monitoring 網站"
date: 2012-12-24
tags: [Web,ASPNET,除錯與系統監控]
postid: "6302052034634611710"
---
當網站還在開發階段，我們隨時都可以使用 debug 或 trace 來除錯或追蹤網頁資訊。  如果網站在部署之後，我們想要監控系統的錯誤訊息或效能等問題，  就可以透過 ASP.NET 提供的「[健康監視 (Health Monitoring)](http://msdn.microsoft.com/zh-tw/library/bb398933%28v=vs.100%29.aspx)」功能，  將這些訊息輸出到指定事件記錄器或者資料庫之中。  

# 健康監視類別 (Health Monitoring)

ASP.NET 2.0 的「健康監視」功能，是將每一種 Web 事件以一個類別來包裝，並且提供相對應的 Provider 來讀取.  我們只要透過 web.config 設定，不用寫程式碼就可以使用。  只要設定好想要追蹤的事件，就可以將訊息記錄在事件記錄器、轉寄到 e-mail或者儲存在 SQL Server 資料庫。  

[System.Web.Management](http://msdn.microsoft.com/zh-tw/library/system.web.management.aspx) 命名空間包含數種健檢事件的型別，以及負責處理這份資料的提供者型別。

## 內建 Web 事件 (ASP.NET Web Event)

不同的健檢事件，以不同的類別包裝，如下表所示：

- [WebBaseEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.webbaseevent.aspx) ：定義 ASP.NET 健康監視事件的基底類別。
- [WebManagementEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.webmanagementevent.aspx) ：定義包含應用程式和處理序資訊之事件的基底類別。
- [WebHeartbeatEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.webheartbeatevent.aspx) ：定義定期引發的健康監視事件。
- [WebRequestEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.webrequestevent.aspx) ：The base class that contains web request information
- [WebApplicationLifetimeEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.webapplicationlifetimeevent.aspx) ：表示應用程式存留期中的重大事件。
- [WebBaseErrorEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.webbaseerrorevent.aspx)：所有健康監視錯誤事件的基底類別。
- [WebErrorEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.weberrorevent.aspx) ：Used to provide information about an error when it occurs in your application
- [WebRequestErrorEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.webrequesterrorevent.aspx) ：Contains request data for request errors
- [WebAuditEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.webauditevent.aspx)：所有 ASP.NET 健康監視稽核事件的基底類別。
- [WebSuccessAuditEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.websuccessauditevent.aspx)：成功的安全性事件之相關資訊。
- [WebAuthenticationSuccessAuditEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.webauthenticationsuccessauditevent.aspx) ：
- [WebFailureAuditEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.webfailureauditevent.aspx)：關於安全性失敗的資訊。
- [WebAuthenticationFailureAuditEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.webauthenticationfailureauditevent.aspx) ：Used to provide information when a failed attempt at user authentication occurs on the site
- [WebViewStateFailureAuditEvent](http://msdn.microsoft.com/zh-tw/library/system.web.management.webviewstatefailureauditevent.aspx) ：Raised when the view state fails to load (typically as a result of tampering)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh8D5bK4KsmpKfQ0cLehujnNat9Ezcnh8qjLG-4s-r1xERhVLArUMU_rbBLWdaIgOCdtwAoIqYvEkSdwpsNlocki3JuHkKudAv6k71mNJJTq6Lqny6DKm7Oywuw_tuJ7NVlasqiwdWLLFo/s850/WebManagementEvent-class-diagram.png)

## 事件提供者 ( ASP.NET Event Provider (Listeners) )

Provider (Listeners) 是用來收集 Web event 的資訊，並 log 這些資訊。  不同的提供者(Provider)，會將訊息記錄到不同的位置，下表是預設的 Provider ，  我你可以直接使用這些 Provicer 或者使用繼承後的自訂類別。  

- [EventLogWebEventProvider](http://msdn.microsoft.com/zh-tw/library/system.web.management.eventlogwebeventprovider.aspx) ：將 Web 事件寫入 Windows 事件記錄檔。
- [SqlWebEventProvider](http://msdn.microsoft.com/zh-tw/library/system.web.management.sqlwebeventprovider.aspx) ：將 Web 事件寫入 SQL Server 資料庫。
- [WmiWebEventProvider](http://msdn.microsoft.com/zh-tw/library/system.web.management.wmiwebeventprovider.aspx) ：將 Web 事件傳送至 WMI。
- [SimpleMailWebEventProvider](http://msdn.microsoft.com/zh-tw/library/system.web.management.simplemailwebeventprovider.aspx) ：透過 email 傳送 Web 事件。
- [TraceWebEventProvider](http://msdn.microsoft.com/zh-tw/library/system.web.management.tracewebeventprovider.aspx) ：將 Web 事件傳送至網頁追蹤系統。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEilMI3TKdaFtmlS70yHvqHyPMXxk5oOYv0AZY5UwzlNP4bUzC4Wxyjuh33zRnNIcZXBjH02TCKbVhptwiya3IP0y1VS1S2XTQRFRmYlhGJopSX91Fde2ZJAh7lZFcrKRIhr1p96IkeDB2w/s850/WebManagementProvider-class-diagram.png)

# 啟用健康監視

要使用健康監視功能，就必須先在 Web.config 中先啟用 Web Events 功能。  這個功能使用 &lt;healthMonitoring&gt; 標籤來表達。  

## &lt;healthmonitoring&gt; 區段設定說明
```xml
<healthMonitoring Enabled="true|false" heartbeatInterval="10">
<bufferModes>...</bufferModes>
<providers>...</providers>
<eventMappings>...</eventMappings>
<profiles>...</profiles>
<rules>...</rules>
</healthMonitoring>
```

- [providers](http://msdn.microsoft.com/zh-tw/library/zaa41kz1%28v=vs.100%29.aspx) ：定義負責處理事件的健康監視提供者。  
您可以依應用程式的需要加入多個提供者，提供者會處理健康事件。
- [eventMappings](http://msdn.microsoft.com/zh-tw/library/yc5yk01w%28v=vs.100%29.aspx) ：對應 Web Event 類別，用來訂閱要監控的事件。  
這個標記項目可以讓您設定一個易記名稱來表示事件的來源。  它會被使用在 rules 標記中，以便程 Provider 產生關聯。
- [rules](http://msdn.microsoft.com/zh-tw/library/fe5wyxa0%28v=vs.100%29.aspx) ：將事件對應至提供者。  
定義提供者與事件的關聯規則，也就是指定你所訂閱的事件該由哪個 Provider 處理。
```xml
<system.web>
<healthMonitoring enabled="true">
<providers>
<clear />
<add name="SqlWebEventProvider"               
type="System.Web.Management.SqlWebEventProvider"
connectionStringName="AspNetSqlConnectionString" />
</providers>
<eventMappings>
<clear />
<add name="All Errors" 
type="System.Web.Management.WebBaseErrorEvent" />
</eventMappings>
<rules>
<clear/>
<add name="All Errors Default" 
eventName="All Errors"               
provider="SqlWebEventProvider"               
minInstances="1" 
maxLimit="Infinite"               
minInterval="00:00:00" />
</rules>
</healthMonitoring>
</system.web>
```

上面這個範例使用到&lt;eventMappings&gt;區段，但為什麼前一個範例沒有？而且前一個範例中的 eventName 又是哪來的呢？  
這是因為全域的 web.config 檔中已經有一個定義好的&lt;eventMappings&gt;區段，所以若沒有特別需求就可以直接使用裡面定義的 eventName 來訂閱該類型的事件。  

底下是由 C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\Web.config 中截取出來的設定
```xml
<!--預設的三個 Provider -->
<providers>
<add name="EventLogProvider" type="System.Web.Management.EventLogWebEventProvider,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />
<add connectionStringName="LocalSqlServer" maxEventDetailsLength="1073741823"
buffer="false" bufferMode="Notification" name="SqlWebEventProvider"
type="System.Web.Management.SqlWebEventProvider,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />
<add name="WmiWebEventProvider" type="System.Web.Management.WmiWebEventProvider,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />
</providers>

<!--預設的 rules -->
<rules>
<add name="All Errors Default" eventName="All Errors" provider="EventLogProvider"
profile="Default" minInstances="1" maxLimit="Infinite" minInterval="00:01:00"
custom="" />
<add name="Failure Audits Default" eventName="Failure Audits"
provider="EventLogProvider" profile="Default" minInstances="1"
maxLimit="Infinite" minInterval="00:01:00" custom="" />
</rules>

<!--預設的 eventMappings -->
<eventMappings>
<add name="All Events" type="System.Web.Management.WebBaseEvent,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />
<add name="Heartbeats" type="System.Web.Management.WebHeartbeatEvent,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />
<add name="Application Lifetime Events" type="System.Web.Management.WebApplicationLifetimeEvent,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />
<add name="Request Processing Events" type="System.Web.Management.WebRequestEvent,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />
<add name="All Errors" type="System.Web.Management.WebBaseErrorEvent,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />
<add name="Infrastructure Errors" type="System.Web.Management.WebErrorEvent,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />
<add name="Request Processing Errors" type="System.Web.Management.WebRequestErrorEvent,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />
<add name="All Audits" type="System.Web.Management.WebAuditEvent,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />
<add name="Failure Audits" type="System.Web.Management.WebFailureAuditEvent,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />
<add name="Success Audits" type="System.Web.Management.WebSuccessAuditEvent,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />
</eventMappings>
```

#### healthMonitoring 規則的 add 項目
```xml
<add name="rule's name"
eventName="event name"
custom="String"
profile="profile name"
provider="provider name"
minInstances="number"
maxLimit="number"
minInterval="00:01:00"/>
```

- name ：此規則的名稱。
- provider ：套用至此規則的設定檔。
- eventName ：指定事件的名稱。
- minInterval ： 指定兩個事件之間的最小時間間隔。

## 如何啟用健康監視
```xml
<healthMonitoring enabled="true" heartbeatInterval="1">
```

例中的 [heartbeatInterval](http://msdn.microsoft.com/zh-tw/library/2fwh2ss9.aspx) 是用來指定引發 WebHeartbeatEvent 事件的間隔頻率 (以秒為單位)。預設值為 "00:00:00"，表示不會引發任何 WebHeartbeatEvent 事件。  

原則上，如果只做這樣的設定，就已經啟用健康監視功能。因為它會繼承全域 Web.config 的預設組態設定，這個檔案位於以下目錄：

%windir%\Microsoft.Net\Framework\v2.0.\*\config\Web.config

在這個預設組態中已經加入了 "All Errors" 和 "Failure Audits" 事件  若要訂閱其他符合自已需求的事件和提供者，可以參考以下示範

## 如何訂閱事件與指定提供者

下面這個例子示範如何訂閱 Application Lifetime 事件，並儲存到 SQL Server 
```xml
<connectionStrings>      <add name="aspnetdb"           connectionString="Data Source=myDB; Initial Catalog=aspnetdb; ..."           providerName="System.Data.SqlClient" />        </connectionStrings>      <system.web>      <healthMonitoring enabled="true" heartbeatInterval="1">
<providers>          <add name="sqlProvider"                type="System.Web.Management.SqlWebEventProvider"                connectionStringName="aspnetdb"                buffer="false"                bufferMode="Notification" />        </providers>          <rules>          <add  name="App Lifetime"                eventName="Application Lifetime Events"                provider="sqlProvider"/>        </rules>        </healthMonitoring>      </system.web>  ```

下面這個例子示範如何訂閱 All Errors 事件，並寄送到 email 
```xml
<system.web>    <healthMonitoring enabled="true" heartbeatInterval="1">
<providers>          <add name="SimpleMailWebEventProvider"             type="System.Web.Management.SimpleMailWebEventProvider"             maxEventsPerMessage="10" maxMessagesPerNotification="1"             buffer="true" bufferMode="Critical Notification"             from="test@gmail.com" to="test@gmail.com"             subjectPrefix="My Web Events"/>        </providers>          <rules>          <add  name="App Errors"                  eventName="All Errors"                provider="SimpleMailWebEventProvider"                profile="Default"                minInstances="1"                maxLimit="Infinite"                minInterval="00:01:00"                custom="" />        </rules>        </healthMonitoring>    </system.web>      <system.net>      <mailSettings>        <smtp from="test@gmail.com">          <network host="smtp.gmail.com"                   userName="test@gmail.com"                   password="test1234"                   port="587"                   enableSsl="true" />        </smtp>      </mailSettings>    </system.net>  ```

# 實例演練

這個範例會示範如何接聽 WebBaseErrorEvent ，並將 log 記錄到 EventLog 、 SqlServer 並且 Mail 給指定帳戶。  

## ASP.NET 健康監視處理過程

#### 下圖是健康監視的處理過程。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgyLjK-EEFYaUAhCPp7I_9IjHkbG4sSxr43OQJx_loxbKErM0_5LQODRAIKESGVz-CWJM2CrH82HYmD0AhM9j4omO_FGabSmzpL7fxJa0ja7F993F31eFCvA__9LJ-9midbG7Swa4_vy1M/s439/health-monitoring-process.gif)

#### 下列步驟描述上圖中 ASP.NET 健康監視是如何將設定的監視的事件資料傳送至 WMI 。

1. ASP.NET 會根據組態設定引發健康事件。
2. ASP.NET 會根據組態設定，將事件分派至 WmiWebEventProvider 提供者
3. WmiWebEventProvider 提供者會透過 Unmanaged 呼叫將事件資訊傳遞至 WMI 系統。
4. 最後，WMI 會將相關資料傳遞至自訂應用程式。

## 設定健康監視
```xml
<!--１。啟用健康監視--><healthMonitoring enabled="true" heartbeatInterval="1">
<!--２。加入 eventMappings , 設定要偵測網頁事件 , 並給定一個易記名稱-->  <eventMappings>      <add name="My All Errors" type="System.Web.Management.WebBaseErrorEvent,System.Web,Version=4.0.0.0,Culture=neutral,PublicKeyToken=b03f5f7f11d50a3a" />  </eventMappings>    <!--３。加入 providers , 設定負責處理事件的提供者-->  <providers>      <add name="mySqlProvider"              type="System.Web.Management.SqlWebEventProvider"              buffer="true"               bufferMode="Critical Notification"              connectionStringName="aspnetdb" />              <add name="myEventLogProvider"              type="System.Web.Management.EventLogWebEventProvider" />        <add name="myMailProvider"               type="System.Web.Management.SimpleMailWebEventProvider"              maxEventsPerMessage="10" maxMessagesPerNotification="1"              buffer="true" bufferMode="Critical Notification"              from="vxxxxxxo@gmail.com" to="vxxxxxxo@gmail.com"              subjectPrefix="WebError"/>  </providers>  ```

圖：Log to EventLog  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgKrWQWkHqiEWju8lW9ZczaW64M5FDXapgTkUqubmA70gHYZV1JphPVL3lhCD9C9-iG0vEyBxUYJx-_DeaB1vJNyZ10r6F37Z6j5VEpkscpFgYPlfWD5z7FicgIwUew0JiOpDIZ0IUoU-4/s779/health-monitor-sample-pic-1.png)

圖：Log to SQL Server  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEghAO7h2wtoFxI5WWMwvwrTUJsIanzayLQvc2GcZxovkv0IzD7KsAax_LDISc-HGFvf7SiUpNC9gRbi7XP4Hw3VHL5qgJebJ8op4L-tCBqkKm2qmfaFpTFgjU8I6ZiEd4ONukDg55umxsw/s850/health-monitor-sample-pic-2.png)

圖：Log to Email  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiBqnd73V4vk0_orJj4-cqGdoW1-7lQu9qIJw1MqxEdz-6772KbwJJu7kIMoPoBSDLn65X9sO0YrZlQZsnZDdjm6QhCif_I-69JFeS8iclNF8mDgz5lrZk36_D9BAbj4LWpx37ahR0IEZ0/s850/health-monitor-sample-pic-3.png)
## 參考資料  

- [MSDN：ASP.NET 健康監視事件概觀](http://msdn.microsoft.com/zh-tw/library/bb398933%28v=vs.100%29.aspx)
- [保哥：利用健康監視(healthMonitoring)監控你的 ASP.NET 網站狀況](http://blog.miniasp.com/?tag=/health+monitoring)
- [ASP.NET 2.0 Health Monitoring](http://renjin.blogspot.tw/2008/01/aspnet-health-monitoring.html)
- [healthMonitoring 項目 (ASP.NET 設定結構描述)](http://msdn.microsoft.com/zh-tw/library/2fwh2ss9%28v=vs.100%29.aspx)