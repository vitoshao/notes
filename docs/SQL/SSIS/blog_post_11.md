---
title: 封裝執行
layout: default
parent: SSIS
nav_order: 11
description: "封裝執行"
date: 2014-01-08
tags: [SQL,SSIS]
postid: "8547338729596498314"
---
有數種方法都可以用來執行封裝，不過這些方法大抵可以被區分二個類別：

- **On-demand**：依需求使用人工操作的方法來啟動執行。
- **Automated**：由 SSIS Service 自動啟動執行。

# On-Demand SSIS Execution

有時候，有些封裝可能因為 depend on 其他的封裝，或者有特定的需求，所以無法設定自動執行，只有在有需求時才會被啟動執行。  在這種狀況下你就可以透過以下幾種方式來啟動：  

- 手動
- 在 SSMS 使用者介面中直接操作。
- 使用 DTExecUI 工具操作
- 程式
- 使用 [dtexec](http://msdn.microsoft.com/en-us/library/hh231187.aspx) 命令
- 使用 TSQL 陳述式
- 使用 Windows PowerShell
- 使用 SSIS Managed API

### SSMS

SSMS 同時支援新版（儲存在 SSIS Server）和舊版（儲存在 msdb 或 file system）的封裝。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjhQw4aHdZdSciGEXGiTlmgide8cUxQofd2mBQOuWCHDTpCXClYd3mWNDCli6BfNVpayo-WwYavSl5uZ3YpxDOy5_Rm3cVO0P56n30bATdV9XeqJ42eUi_aUdgdVjINrDuk79g-121ZxnM/s0/ssis-exec-ssms-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjFrhL_HZoAZ_E064XyK-XSx5NokYG1-YPrKt8xKr9_Bjrq1TzmGJCM9CgW6pyMKslDYn4Z9HvPV2kOqTQcKh1HJ346CXfdX8LQPXKLKWqZp2yItAr1iSOuENqjmYe2FfldMRoKiHjl3yw/s0/ssis-exec-ssms-2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjNsme4rMalZbLxJgGz3-7lRO1BwwkAxyh7hREERJArVUsGsGzENbyYgmoNewVkoidhiePM3uqRPHqu07G76FLHpXIkjex5mLwXo8MtTib5XJDknebH3pfp7CzVgpcnYv3ekkvSx2geWCE/s0/ssis-exec-ssms-3.png)

### DTExecUI

執行封裝公用程式（DTExecUI）是 SQL2012 提供的工具程式，用來執行舊版封裝，不支援新版(2012)的封裝。它的程式位置在：（僅有32-bit版本）  
C:\Program Files (x86)\Microsoft SQL Server\110\Tools\Binn\ManagementStudio  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjmk64vhihBo2tWIMHVgXNLc4oqoQTBQ_AnFHbwhd1ROr0hXRZcUSWNFM1-QjEgc_FIYuxUB56CUFZ9ElSzhfOXsBB458OpkNVUiimCalHvRB5f2JDNHKNJ9Sg2iQQVzOMUWg_957YwWcA/s0/ssis-exec-dtexecui-1.png)

程式介面：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh4hEfZODj4FKfApM0r38rtO0kt79SKu4mVKlHaxjlUj45UkTYBNZBhh5nmbXjSsfXikrC-IkcIgmoZwNL2FOvmEm2a1AeBmt2nSgs-6C37CfemO7-cT_Vefvf5YL2OJYcnaaGfTNfthVE/s0/ssis-exec-dtexecui-2.png)

DTExecUI.exe 這個工具只有 32 位元版，如果只能使用 64 位元的環境，必須使用另外一個工具（dtexec.exe），不過它是屬於命令列工具，沒有UI。

### TSQL

接下來的幾個方法都可算是可程式化的啟動封裝，使用可程式化啟動封裝，系統通常會經由以下幾個步驟：

1. create a new execution operation
2. set execution properties
3. start the execution : 預設系統會以非同步的方式啟動封裝，也就是封裝被呼叫後，控制權即回到原先呼叫者。
```sql
DECLARE @execution_id BIGINT;
DECLARE @use32bitruntime BIT = CAST(0 AS BIT);
DECLARE @logging_level INT = 1;

EXEC catalog.create_execution
@folder_name  = N'MyCatalog1',
@project_name = N'Chapter10',
@package_name = N'Staging.dtsx',
@use32bitruntime = @use32bitruntime,
@reference_id = NULL,
@execution_id = @execution_id OUTPUT;

EXEC catalog.set_execution_parameter_value
@execution_id,
@object_type = 50,
@parameter_name = N'LOGGING_LEVEL',
@parameter_value = @logging_level;

EXEC catalog.start_execution
@execution_id;
```

更多與 SSIS Catalog 相關的 Store Procedures 請參考 MSDN：[Stored Procedures (Integration Services Catalog)](http://msdn.microsoft.com/en-us/library/ff878099%28SQL.110%29.aspx)

### Windows PowerShell

Before SSIS packages can be executed by using [Windows PowerShell](http://www.microsoft.com/taiwan/technet/columns/profwin/28-monad.mspx), the SSIS management assembly must be loaded.  Then the operation is prepared and executed, as described in the TSQL section.   

執行 Windows PowerShell： 

- 在『開始』的『執行』交談窗輸入 powershell 或 Windows PowerShell。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEinhZCNSt6t-Y2nYMNdKUfDfDk92nk19JDLO8a06uOFSKT6J39p0JsWiaMaNv8B6qq9i9QRakLJQD1ksM0WrsT5CnwmsmsGzbfCgOFn9L7D8EWExQulFqwgh1einBljsfG5EjrJk9_dGtU/s0/ssis-powershell-1.png)
- 在任何『命令提示字元』視窗的命令提示符號輸入 powershell。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgq5-NzuZkkqnwsGpxbL2FQTWWSoNOFQC44n_QTxUJICXZ6C_JX5tMyWFF4CDvPgc7LuhOvRxTGJBpXgiL7Iuyq-mo7Zo8ACS3GgD2fqt-UHlf4U5aewsmPST3oYSh1iT8mvOGs-a6B778/s0/ssis-powershell-2.png)

範例：
#-- Load the SSIS Management Assembly
$assemblyLoad = [Reflection.Assembly]::Load("Microsoft.SqlServer.Management.IntegrationServices , Version=11.0.0.0,Culture=neutral, PublicKeyToken=89845dcd8080cc91")

#-- Create a connection to a SQL Server instance
$connectionString = "Data Source=VITO-2011W7\SQL2012_DB1; Initial Catalog=master; Integrated Security=SSPI; "
$connection = New-Object System.Data.SqlClient.SqlConnection $connectionString

#-- Instantiate the SSIS object
$ssis = New-Object Microsoft.SqlServer.Management.IntegrationServices.IntegrationServices $connection

#-- Instantiate the SSIS package
$catalog = $ssis.Catalogs["SSISDB"]
$folder = $catalog.Folders["MyCatalog1"]
$project = $folder.Projects["Chapter10"]
$package = $project.Packages["Staging.dtsx"]

#-- Set package parameter(s)
$package.Parameters["para1"].Set( [Microsoft.SqlServer.Management.IntegrationServices.ParameterInfo+ParameterValueType]::Literal, "parameter value")
$package.Alter()

#-- Execute SSIS package ($environment is not assigned)
$executionId = $package.Execute("false", $environment)

### SSIS Managed API

PowerShell 透過叫用來啟動封裝，若要直接存取 SSIS object 就必須透過 SSIS API ，這些 API 定義在[Microsoft.SqlServer.Management.IntegrationServices](http://technet.microsoft.com/zh-tw/library/microsoft.sqlserver.management.integrationservices.aspx) 命名空間  

範例：
```c#
using System;
using Microsoft.SqlServer.Management.IntegrationServices;
using SMO = Microsoft.SqlServer.Management.Smo;

…

// Create a connection to a SQL Server instance
SMO.Server ssisServer = new SMO.Server("VITO-2011W7\SQL2012_DB1");
Console.WriteLine("Connected to:\t\t" + ssisServer.Name);

// Instantiate the SSIS object
IntegrationServices ssis = new IntegrationServices(ssisServer);

// Instantiate the SSIS package
Catalog catalog = ssis.Catalogs["SSISDB"];
CatalogFolder folder = catalog.Folders["MyCatalog1"];
ProjectInfo project = folder.Projects["Chapter10"];
PackageInfo package = project.Packages["Staging.dtsx"];
Console.WriteLine("Selected package:\t" + System.IO.Path.Combine(catalog.Name, folder. Name, project.Name, package.Name));

// Set package parameter(s)
package.Parameters["para1"].Set( ParameterInfo.ParameterValueType.Literal, "parameter value" );
package.Alter();
catalog.ServerLoggingLevel = Catalog.LoggingLevelType.None;

// Execute SSIS package
package.Execute(false, null);
```

PS1.若是在 VS2010 底下，請自行加入以下參考組件。
C:\Windows\assembly\GAC_MSIL\Microsoft.SqlServer.Management.IntegrationServices\11.0.0.0__89845dcd8080cc91\Microsoft.SqlServer.Management.IntegrationServices.dll
C:\Windows\assembly\GAC_MSIL\Microsoft.SqlServer.Management.Sdk.Sfc\11.0.0.0__89845dcd8080cc91\Microsoft.SqlServer.Management.Sdk.Sfc.dll  
PS2.若是要存取 SQL Server 中的管理物件，可使用 Server Management Objects。((Microsoft.SqlServer.Management.Smo).)

### DTExec

DTExec utility is :

- a command-line utility
- can execute packages stored in the SSISDB catalog and the older SSIS service (that is, stored in the msdb system database or in the managed file system).

dtexec /f "C:\mypackage.dtsx"  
PS. **DTExecUI** cannot be used to execute packages stored in the SSISDB catalog.

# Automated SSIS Execution

most SSIS package are:

- scheduled for automatic execution
- performed at times when systems are not being used, for instance, at night

### SQL Server Agent

SQL Server Agent 是 SQL Server 中的一個元件，主要功用就是用來協助自動化執行在 SSIS Server 上的封裝作業。  SQL Server Agent 使用下列物件來定義要執行的工作，何時執行工作以及報告工作成功或失敗的方式。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgsKWkI3WHt5aI1QvAGaklNkESrWqPj9cexS9aNxYd0MurPbftAagrgCguS6MDiFUyLnw1fNlBBrAFINFJRg1W4B9KSkh-cWiJKpKk-f7EeEV63vVBP-6zWrDpN54cfDunQgNOlvdOsQ88/s0/ssis-sql-server-agent.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjoYITh_V_enaxeFSKNKQrVY3r7-V_e-gnQAorSiggR6DlIUbmaiq9eCnf2M14LMuskE9rHHvZMWr20H9yvM9H_NJOOoDPrJ1sSGT_6uLAs7KcWENR0vqXOjJvH8jjn-dygwgfcuDgIZGQ/s0/ssis-agent-job.png)

#### 作業與作業步驟（Jobs and Job Steps）

- Jobs is the principal unit of work in [SQL Server Agent](http://msdn.microsoft.com/zh-tw/library/ms189237.aspx) processes
- A Job serves as a container for job steps, which represent the actual operations.

作業步驟類型：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjbRLDdHAa5suYZOiRoI3IbaBMqwMinPtlNMsSsvXRm0G4WMTIVpENhxUFW6xXgKr_45X0j_Trf6j5h5sDKZ_XTYtP-JvgGM2_RmKhfyZnmI9XHwGrlMQKGbaADRmHPUxOzD_vmL1F_ptE/s0/ssis-agent-jobsteps-types.png)

#### 排程（Schedules）

在 [SQL Server Agent](http://msdn.microsoft.com/zh-tw/library/ms189237.aspx) 上的作業中，是透過「排程」(Schedule) 來指定作業的執行時間。  排程可使用以下定義來設定條件：   

- 每當 [SQL Server Agent](http://msdn.microsoft.com/zh-tw/library/ms189237.aspx) 啟動時
- 每當電腦的 CPU 使用率達到您定義為閒置的等級時。
- 某個特定的日期和時間。
- 執行循環排程時。

#### 警示（Alert）

「警示」是針對特定事件的自動回應。可用來回應下列條件之一： 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgBnfzKL48DII6S6FwwgMjXbQ3j0YtbbwRjRHkz3Wo_zh8YftIsCC-gWaZbjuEBCszbC2jfoWsKtkdphSH1WGRe3j1bAYU7aNoSlkh_8E-CUvuDczNU8Dgv7mQ9TYKylsl3eQ9pYAZ9ijg/s0/ssis-agent-alert-types.png)

#### 操作員（Operator）

「操作員」是警示設定中，用來接收通知的對象，SQL Server 可以透過下列一或多種方式，通知操作員發生警示： 

- 電子郵件
- 呼叫器 (透過電子郵件)
- net send

# Monitoring SSIS Execution

通常我們會針對部署到 SSIS 目錄中的專案或封裝進行**執行**或**驗證**。  這些過程都會被記錄下來，也可以透過系統檢視來進行追縱。  

### Operations

當 SSISDB 中的封裝或專案被執行或被驗證時，都會建立一筆作業(Operations），用來記錄這個操作行為。  這些作業的記錄都會被反應在 [catalog.operations](http://technet.microsoft.com/zh-tw/library/ff878094.aspx) 檢視表中。底下三個檢視表，都是用來檢視與作業相關的記錄：  

- [catalog.operations](http://technet.microsoft.com/zh-tw/library/ff878094.aspx) ：Displays the details of all operations in the Integration Services catalog.
- [catalog.operation_messages](http://technet.microsoft.com/zh-tw/library/ff877994.aspx) ：Displays messages that are logged during operations in the Integration Services catalog.
- [catalog.extended_operation_info](http://technet.microsoft.com/zh-tw/library/ff878141.aspx) ：Displays extended information for all operations in the Integration Services catalog.

#### 作業的狀態（Operation Status）

每個作業隨著不同的執行階段，其狀態包含以下九種可能：

- 1：已建立（Created）
- 2：執行中（Running）
- 3：已取消（Canceled）
- 4：失敗（Failed）
- 5：暫止（Pending）
- 6：意外結束（Ended unexpectedly）
- 7：成功（Succeeded）
- 8：停止（Stopping）
- 9：已完成（Completed）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhg0me1r3BB1Dyq5ZS4GBBl4mxt-MbgtuZkIjsZ1zLlAYuYqgZRpP0R22rZnSE-CWPtbx69-RhG-cJy6tj2aOk6uotc6eeq6vsFYLgpbxT0KzOXvBeo7cwY56VYBmZMFJrQHfMdZB2B-lw/s850/ssis-operations.png)

### Validations

在 SSIS 目錄中，你可以針對專案或封裝進行**驗證（Validations）**，用以檢驗專案或封裝中的種種設定。  你可以透過 [catalog.validations](http://technet.microsoft.com/zh-tw/library/ff878143.aspx) 來查看每次驗證的資料。  

專案驗證（project validations）：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjQSPcBwXLdgnrzN8hoA64qwrfaT44ckPvHkc-jIP49NamMa8QeVXcb0ilDoSDO7M6RySa8hSFcYfAxYvYrfKnBX24zU8uvH9p6OLc48Ra7Y2Niwgm7sExQI3AB2Vz-Q2PoiyHfBFXDa3c/s0/ssis-validate-1.png)

封裝驗證（package validations）：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjBpiAPRjsFPVOe5cRmpL1PjQFlewg0w0pC3OU3hoOkYz8LG1nYmHxXFUfewcca4sjhV-qvRsO3wESNectIlgN28IMtYR8eAmX7S4oPUqBlz9VFog2q7TM5q8DUnsm_DwQHxMd9Tj8FBhU/s0/ssis-validate-2.png)

### Executions

通常在 SSIS 目錄中的專案或封裝，最常被操作的行為就是**執行（Executions）**。  封裝在執行前，都會先進行驗證，驗證通常才會開始執行。

### Logging Levels

前面提過，在 SSIS 中的作業，都會被記錄下。你可以針對資訊的詳細度，來設定記錄模式。  SSISDB 支援以下幾種不同的記錄模式：

- None：關閉記錄功能。  只記錄封裝執行狀態。
- Basic：記錄所有事件，自訂和診斷事件除外。這是預設值。
- Performance：只記錄效能統計資料，以及 OnError 和 OnWarning 事件。
- Verbose：記錄所有事件，包括自訂和診斷事件。

### SSIS Monitoring in SSMS

若要監看 SSIS 的執行狀況，在 SSMS 之中就有方便的功能，有些使用報表，有些使用內建檢視表。  例如，除了上面提到的幾個檢視表外，你也可以使用以下幾個檢視表來查看與封裝執行相關的資訊：  

- [catalog.executions](http://technet.microsoft.com/zh-tw/library/ff878089.aspx) ：顯示 Integration Services 目錄中封裝的執行執行個體。  以 [封裝執行工作] 執行的封裝會使用與父封裝的相同執行執行個體來執行。
- [catalog.execution_data_statistics](http://technet.microsoft.com/zh-tw/library/hh230986.aspx) ：每當資料流程元件傳送資料至特定封裝執行的下游元件，此檢視就會顯示一個資料列。  此檢視中的資訊可用來計算元件的資料輸送量。
- [catalog.execution_data_taps](http://technet.microsoft.com/zh-tw/library/hh230985.aspx) ：顯示執行中定義之每個資料點選的資訊。
- [catalog.execution_component_phases](http://technet.microsoft.com/zh-tw/library/hh230981.aspx) ：顯示資料流程元件在每個執行階段所花費的時間。

若要查看報看，你只要先點選目錄中的節點（catalog, project, package, or environment），就可右鍵選單中找到報表。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgrJdOxcAlli10Bs4HynSCRdqcrVvjKPJf8bVvxlcYZNNQsfThCO9h1Kf1M5tpwo9W8LuK9zM2kMHKii73F9nYYJ20YhJCGSpU5dPIVjl-Xv04039aHluZBAdlCtqxpEoY_0kbj9AGquRE/s0/ssis-ssms-monitor-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjrKuyClQTtWZMJkO6LVt_rpBg9UISY6wQeFRVm16etIBiK7k30S50YmOtJVmc89Gkry3TfsctT-0PJ3lcvDdfk0bs1OHfOh5fPJVmFmVSBtK1J5w70xRWhKQVaQkctuwdQTuKdnyaaecc/s0/ssis-ssms-monitor-2.png)
## 參考資料  

- [SQL Server Agent](http://msdn.microsoft.com/zh-tw/library/ms189237%28SQL.110%29.aspx)
- [SQL Server Agent Jobs for Packages](http://technet.microsoft.com/zh-tw/library/ms141701.aspx)
- [Different ways to execute a SQL Server SSIS package](http://www.mssqltips.com/sqlservertip/1775/different-ways-to-execute-a-sql-server-ssis-package/)
- [Stored Procedures about Integration Services Catalog](http://msdn.microsoft.com/en-us/library/ff878099%28SQL.110%29.aspx)
- [在 SSIS 伺服器上啟用封裝執行的記錄功能](http://technet.microsoft.com/zh-tw/library/hh231191.aspx)
- [監視封裝執行](http://technet.microsoft.com/zh-tw/library/hh213294.aspx)