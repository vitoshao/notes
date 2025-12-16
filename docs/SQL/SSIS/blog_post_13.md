---
title: 封裝部署
layout: default
parent: SSIS
nav_order: 13
description: "封裝部署"
date: 2014-01-09
tags: [SQL,SSIS]
postid: "2267912435533138207"
---
# SSISDB Catalog

當封裝設計好之後，你可以將含有那些封裝的專案部署到 SSIS Server 上。  

在舊版的 SQL Server 中，封裝必須匯入到 SSIS Servie 的**檔案系統**或**MSDB系統資料庫**中。  在 SQL Server 2012 中，除了原先的模式之外，更引進了全新的 [SSISDB Catalog](http://technet.microsoft.com/zh-tw/library/hh479588.aspx) 機制，專門用來存放封裝部署的相關資訊(metadata)。  例如專案、封裝、參數、環境和作業記錄都會包含在 SSISDB 目錄中。   

### 建立 SSISDB

將將封裝專案部署至 SSIS Server 之前，必須先確認 SSISDB 目錄是否已存在，如果不存在則必須先建立 SSISDB 目錄。  建立 SSISDB 目錄，實際上 SSIS Server 會在 SQL Server 上掛載一個 SSISDB 資料庫，專門用來儲存封裝、專案、參數、權限、伺服器屬性與作業歷程記錄等物件。  

1.建立 [Integration Services 目錄] 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhavN_WayiN08B69VEs0CDjWK1pC0_j4q65gKhPraa3_go1ZOde-aYA3OufJDImFHueiQRfqkb0nzUVXDSQkzvIPp7c97Xa3uDWLV8gyUvL3V1m5wq_sP26NNb_SQnKMiQbepQq3xYFtDI/s0/ssis-create-ssisdb-1.png)

2.設定目錄選項

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgAuf5vh9G993q8qZBMCOZE2tkgugxYWnn8tzl1xTFe_YhZ5bRWE-NblFwbjDjZlVf1uVjeo3wWMqUP-tMNhaDySMmCAcaIN_EYnJIVre8LY1IIu7MMSGz4SkwsxmMlz7vhfp7xyIrPz7I/s0/ssis-create-ssisdb-2.png)

##### PS. Enable CLR Integration

CLR must be enabled on the SQL Server instance to create and use the SSISDB catalog.  CLR integration allows a SQL Server instance to use user assemblies, such as userdefined SQL CLR types, user-defined SQL CLR functions, SQL CLR procedures, or triggers. CLR integration is disabled by default.   

If you restored the SSISDB catalog to a new SQL Server , you can enable the **ClrIntegrationEnabled** option in **Surface Area Configuration** property. 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjzNGih2p02TtfJxTY4JJIHS23I0EJ_tLVMBd3fsbdyFjPjxzN5QjCQt6-FljEZTStqz_Q9Luws4lwFWPipGsuDk8ScUdGKuSWG6Xcs9YbBdZ28TtPa8MsTyfd-c8bC4Kw1G1T1APFJaRw/s0/ssis-create-ssisdb-6.png)

3.輸入密碼。完成後你就可以看到如下畫

SSISDB 目錄

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh_ASKSeuMyki1BztJk-besEPys55RAZDgj0AhbbWRkujkKQkMN_SrFEQoWazbLVdXHWU_9B32KIf-XtoMiNrQhSiZFvIQQSGECkvhQjcTQF7LOluPw3sMg5pTf1gGqDL-Imk1ao2Fr18k/s0/ssis-create-ssisdb-3.png)

SSISDB 資料庫

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg3v0P-CthXKP_PHmaeK6i9U7NR8v7rOp1ZmSWB8DT9EGsg8PihyxWSWIk4HustuilXOpdlqg9O8VE9iQjwsctwBmq9W8-vrTfcBrzFVF0zxKA8xe6Dt64ZcDXJ5V3OIcAOJNOrZmPpA_s/s0/ssis-create-ssisdb-5.png)

### 設定 SSISDB

你可以在 SSISDB 的屬性功能中，看到以下資訊。你也可以使用 [catalog.catalog_properties](http://technet.microsoft.com/zh-tw/library/ff878147.aspx) 系統檢視來查詢這資訊。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhH0A78iWJ0fFXs022WIzyseqYkFu8OPFakXJ_6mbrHHwm-gZOOs19wRiKndEZKvHtO1ceRZ_Cip6TNqQv6FJ8Z80lthWRcO-XltUMq9vysE_M49wpJ_Vk1uX4C6k61YcobIEagi1S5aFU/s0/ssis-create-ssisdb-7.png)

這些屬性值，你可以直接在屬性頁中修改，或者透過系統預存程序 [catalog.configure_catalog](http://technet.microsoft.com/zh-tw/library/ff878042.aspx) 來處理。

關於這些屬性值，有幾點要注意：

- 「加密演算法」只能透過預存程序來變更，且必須在「single-uer mode」模式底下。
- 「清除記錄檔」的作業是由 [SQL Server Agent](http://msdn.microsoft.com/zh-tw/library/ms189237.aspx) 的工作負責執行，預設是每日執行一次。

# SSISDB Object

當你建立 SSISDB 目錄後，系統會自動建立 SSISDB 資料庫，用來儲存封裝部署後的相關資料。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgYap2FmcRKV62A8wySlhKLoRyGy7k9wsIteGhp1VG0ubpzx57U-DM9e6K5NmqIC4lomC_v1cQTCdTAgpwykgCNhCBmnL-neCZelNqpsaK5fLiQY16CW6KBZFkZfs6coBKsk4kG7m-UaNM/s0/ssis-create-ssisdb-4.png)

當 SSIS 專案或封裝被部署到 SSIS Server 之後，其相關資料會被存放在以下物件之中。

### Folders

- When SSIS projects are deployed to the [SSISDB Catalog](http://technet.microsoft.com/zh-tw/library/hh479588.aspx), they are placed in folders.
- a common security context
```sql
SELECT \* FROM catalog.folders;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEisCT_YCPf9_rneq3oWaFEeYzstPlEGKsejFObM-qXXBN8ewVUfi-a4FB2emoJOkHjBFSXi8Va51yLYtHtRhVw_d4SheL9OR-Y9-w63LTNArRG6W9fMYP76n5kKCQoYsSAtjpcGzz9ntoU/s0/ssis-ssisdb-folders.png)

### Projects and Packages
```sql
SELECT \* FROM catalog.projects;
SELECT \* FROM catalog.packages;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNktqjL0proBZDhXmhEG8fbJKLX2Us3MxsfP0_S7lObZksB0Wf4NlTD68itOWI5OUyX3XPaYYaPqATv67opGdsYGlcSHvsZN8mODPdhrdJ-M3dbP5GXE_ynVDPK0uepr_TnIuwQKqJHFE/s0/ssis-ssisdb-projects-packages.png)

### Parameters

您在封裝執行時，可使用參數將值指派給封裝屬性。
```sql
SELECT \* FROM catalog.object_parameters
SELECT \* FROM catalog.execution_parameter_values
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhK9uZO2-njkrZQ1IKT76hf5Vtjwbr7EEr8iOjRHpbWOdFOMH5PeoKEK9HTUXDM46wMcfydd9xCLBwNU_nCu_l5Fx5KxcFUipEL583x3FhCa5ftz-xUQHI0bLKdMzQc_keH8RKsyhZbKb4/s850/ssis-ssisdb-parameters.png)

- catalog.object\_parameters ：顯示所有封裝和專案的參數
- catalog.execution\_parameter\_values ：顯示用於執行執行個體的參數值。
- catalog.set\_object\_parameter\_value ：設定封裝或專案的參數值
- catalog.clear\_object\_parameter\_value ：清除封裝或專案的參數值
- catalog.set\_execution\_parameter\_value ：To set the value of a parameter for an instance of execution
- catalog.get\_parameter\_values ：擷取預設參數值。

### Server Variables, Server Environments, Server Environment References

Parameter values can be assigned directly,   but they can also refer to environment variables that are defined and stored in the [SSISDB Catalog](http://technet.microsoft.com/zh-tw/library/hh479588.aspx).  
```sql
SELECT \* FROM catalog.environments;                 /\*已設定的環境\*/
SELECT \* FROM catalog.environment_variables;        /\*環境中的變數\*/
SELECT \* FROM catalog.environment_references;       /\*專案參考到環境\*/
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjND7YWPxl43RLfpqt2tIleDlkswRcNBI72qiD__PC-uSXiUUdLZ9IRqYSPsXn5nCtgeJWsXP3OYUK897Mw2DbgqVkBchR5-9bq4gefsHGouqR0WYgqaZflUB4TvyrcBon6pSiZIT9byQw/s850/ssis-ssisdb-environment-1.png)

若要建立環境參考，你也可以直接使用 catalog.create\_environment\_reference 預存程序。

### 作業（Operations）

Operations represent actions performed against the [SSISDB Catalog](http://technet.microsoft.com/zh-tw/library/hh479588.aspx) (deployments and　catalog-level configurations)   and against SSISDB projects and packages (validations and executions).  

Operations are created when an SSIS process is prepared for execution.  As operations are created, information about their creation is written to the [SSISDB Catalog](http://technet.microsoft.com/zh-tw/library/hh479588.aspx) and is accessible through the catalog.operations catalog view.  
```sql
SELECT \* FROM catalog.operations;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjsow7njoOCOL20NJadpRaglA2B97kYN7Blv74IqZ0Hl78btViwv_mRXeZUL7XnZW2xKtw1XQbfF71-DGHd1PlWxGu4KOtyckpijI7ocKnQ6EjcbmhbM2lLuAZAdShm_yM1mXdXZf50oTQ/s850/ssis-ssisdb-operations.png)

### Execution and Validation

Execution 物件指的就是一個封裝執行時的執行個體

- catalog.create\_execution ：建立執行
- catalog.start\_execution  ：啟動執行
- catalog.stop\_operation ：停止執行

# Deployment

SSIS 部署，就是將 package 由開發環境移到線上環境，SSIS 支援兩種部署模型：

- 專案部署模型（project deployment model）：透過「專案部署精靈」將 SSIS 專案部署到 SSISDB 目錄。
- 封裝部署模型（package deployment model）：將封裝檔案安裝到 Integration Service 伺服器的檔案系統或 SQL Server 的執行個體。

## 專案部署

- 舊版的 SSIS package 無法直接部署到 SSIS Server。必需先使用 SSIS Project Conversion Wizard 轉換成 SSIS 專案，才可以部署到 SSIS Server。
- **專案部署模型**的中心是一個**專案部署檔案** (副檔名 .ispac)。  如果專案中設定了多個**組建組態**（Build Configuration），在建置專案之後，每個組建組態都會產生一個**專案部署檔案**。  每個**專案部署檔案**都是一個獨立的 (self-contained) 部署單位，包含該專案的封裝、參數、連接等相關資訊。  當 SSIS 專案被部署到 SSIS Server ，實際上也就是部署到 SQL Server 執行個體上的 SSISDB 目錄。
- 專案部署是透過「專案部署精靈（SSIS Deployment Wizard）」來進行部署。你有以下方式可以叫起這個功能：
- 由 SSDT 開發環境，直接叫用**部署**功能。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhileHguDo4YZQaqUbHvqYsqbCRO_SUuBlwYzNjfePPcuVKe-YYUkQWq9sLAYRc78sFg3d16idUUIMdUKlZxbzADFdRUJW4B4Bi267OIRfgF6Emy9iO6v3RptnKIKtnF_OiTTZOQJyF7LM/s0/ssis-prj-deployee-1.png)
- 由 SSM 中，在 SSISDB 目錄中，執行**部署**功能。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhwJwZqykfOYZQkg7U6eErrvBZoGV2SCmFYDwSf3HZHpICRcG2ao4kxe3HN_TBIjc548oJl9X8V5TvHF6FtV0TvjFARY02WVkZE0DrPADbp9nnAwhqmWLnETabG_qKrq9FU76Igsd8GxnM/s0/ssis-prj-deployee-3.png)
- 直接執行建置後的**專案部署檔案** (副檔名 .ispac)。  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhPdq76sng8gYR811Z-hUOuUr4iO_qZArXLVkM-pN_LpM3yZpfYum-4gEKSvCMwfSHZXPYjwETag0MnVACCjkiRANAgaKy3dHDniUB5gqN0D0ClJJNeWdWjJPQtKBReExK2jWElQtdfnZQ/s0/ssis-prj-deployee-2.png)

### 在 SSTD 中使用精靈部署 SSIS 專案

在 SSTD 中，你只要開啟專案之後，在其右鍵選單中執行「部署」就可開啟部署精靈。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi_n00rsmZvY7ZFV1G0OdmoyBzE5DoF3-osZ99IS1lARD74hSTf6p0b9zQI9ZEcQENf34UdxUKRx8eiClCnIFIf9IEpHGjIAltN23snZgCZQqLKB5-S2xAlz7kyUeoXANPeFg-c01Jf_K0/s0/ssis-deployee-2.png)

專案部署精靈共有四個步驟，一步一步設定即可。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEil2ENHPBUYuMQtOWUZwv2AjrN7bSXjFFsHgrgoBsiHR8PmxDwAKOGtkYBndBWCXFgZRLuW2OBcsPHkSIvdA1A3zivzvaGKp_uDWry5yMIZC4ibZFWs47fdxKq6WaTrzbma8GlQ0kR07f8/s0/ssis-deployee-4.png)

完成設定後，會看到如下的結果：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhT8HN3vz444rnZ3bYx5DdC4ylJET7AmGwUNH459up5pTqWVbAB0MORS7sOQvVonlbmOi_6QdvDMKLrDn7FDJ1t4jE1xXE_woI-S1te5KLsYDZOyrG7Rljqwr3a3FU5_yM6ma77fYxmclc/s0/ssis-deployee-1.png)

### 在 SSMS 中部署 SSIS 專案

若要由 SSMS 中部署 SSIS 專案，你必需先建置該專案，然後在專案的 /bin 目錄中找到建置子的封裝檔(.ispac)。 

在 SSMS 中，由[目錄]的[專案]節點的右鍵選單，執行「部署專案」就可開啟部署精靈。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhEvg3RQfZrjxJC195-qR5awnp73fUpMbk14Cq2kN2MZsfOyXq1xD05Hkg7xKCn2naTnV1dcS5X3HT5vmp6G0Vh4UfUD1KCGDKGiEbBGLanrWsZKOSF_joBkV4uyF6k1VtZi4pHsn-44aY/s0/ssis-deployee-3.png)

## 封裝部署

不管是舊版或新版的封裝，你都還可以採用**封裝部署模型**，將**封裝**部署到 Integration Service 伺服器的檔案系統，或者儲存至 SQL Server 執行個體上的 MSDB 資料庫。   

封裝部署處理有四個步驟：

### 1. 建立封裝組態

這步驟是選擇性的，若不使用就不用建立

更多相關內容請參考 MSDN：[建立封裝組態](http://technet.microsoft.com/zh-tw/library/ms141132.aspx)

### 2. 建立封裝部署公用程式

「建立封裝部署公用程式」就是透過「建置（build）」，產生部署封裝所需的檔案。

不過在建置之前，你必須先將 [CreateDeploymentUtility] 選項設為 True。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjMR_MLzWl0St1VFETtaDvwYA0d2KkvFhOFy9S3IBOX0wshkabweeQtAYdquo7DYPWul1eLc4LWI8Pi0jCMmkKtW-5mZs8LT1xRh3bKQa2bYkTIR8qLYo9rLpwmpdSI4Ab6c7qC7M704mo/s0/ssis-create-deploy-utility.png)

建置結束，你可以在輸出視窗中，看到建置結果。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj2lpLp0_LYlCMbyFGipnPvQ1YwJxDJ7KsM6ObJ-ev3awNmAMQ0SXLPpb0H7EJwa69WyMe8Jcz3OjLgLY7rmSPPy1O2pbJ-_41Ih_yDrGF3rMxGJCqx-1zb6rmgcDJYvgBOXC8t4TaNXDQ/s0/ssis-create-deploy-utility-2.png)

最後，你可以在指定的輸出路徑中，查看建置完成的檔案。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj2lpLp0_LYlCMbyFGipnPvQ1YwJxDJ7KsM6ObJ-ev3awNmAMQ0SXLPpb0H7EJwa69WyMe8Jcz3OjLgLY7rmSPPy1O2pbJ-_41Ih_yDrGF3rMxGJCqx-1zb6rmgcDJYvgBOXC8t4TaNXDQ/s0/ssis-create-deploy-utility-2.png)

更多相關內容請參考 MSDN：[建立封裝部署公用程式](http://technet.microsoft.com/zh-tw/library/ms137952.aspx)

### 3. 複製封裝檔案

這步驟主要就是將步驟２建置的檔案複製到 SSIS 目標電腦。

### 4. 封裝安裝

要安裝 package ，你必須在 SSIS 目標電腦上，執行 [封裝安裝精靈]，就可以將 package 安裝到 Integration Service 伺服器上的檔案系統或 SQL Server 的執行個體。。  

更多相關內容請參考 MSDN：[封裝安裝](http://technet.microsoft.com/zh-tw/library/ms141693.aspx)
## 參考資料  

- [Stored Procedures about Integration Services Catalog](http://msdn.microsoft.com/en-us/library/ff878099%28SQL.110%29.aspx)
- [將專案部署至 Integration Services 伺服器](http://technet.microsoft.com/zh-tw/library/hh231102.aspx)
- [封裝部署 (SSIS)](http://technet.microsoft.com/zh-tw/library/ms137592.aspx)