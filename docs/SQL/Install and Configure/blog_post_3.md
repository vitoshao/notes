---
title: SQL Server Management Studio
layout: default
parent: Install and Configure
nav_order: 3
description: "SQL Server Management Studio"
date: 2013-07-05
tags: [SQL,Install and Configure]
postid: "992104479701715049"
---
SQL Server Management Studio 是一個整合式環境，您可以用以存取、設定、管理及開發 SQL Server 的所有元件。  SQL Server Management Studio 利用許多豐富的指令碼編輯器來組合一群非常廣泛的圖形工具，使所有開發人員和管理員 (不管他們的技術水準如何) 都能夠存取 SQL Server。   

## 已註冊的伺服器

SSMS 提供一個「已註冊的伺服器」視窗，可以用來管理 SQL Server 執行個體。  還可以設定＂伺服器群組＂，以分類管理。更多詳細介紹與使用方法，請參考：[MSDN:註冊伺服器](http://msdn.microsoft.com/zh-tw/library/ms190631.aspx)。  

### 新增伺服器註冊

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiVZd3GwwEiTJuFsY3nxLf6EA1Dm9tmTAGz0fvAHW0EqQ3tlX_Cc2sz8AgUfUgjBb47zL8R_k6aygLN32f221N0OFK9CpPbd0rOgjGvZnWU9tzFJDYuk_hAOc1m5VS5Okg_r3YAMXkfux8/s0/ssms-registered-server-1.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj_vhepKLkc8IbATNTZygzYjmNHcy7mI8BdVDgPe7Zw72pCL0FaiyZN4ckl3og1FAveNW_JpxpW4VBo0JHB_NPj2y67j71Nn30mg_vu315bY47VokPVeVP6R4FxeQfKlJyBGbyJ2pC2RDk/s0/ssms-registered-server-2.png)  

### 新增伺服器群組

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg3-xSY1NGMXhKUYVjaiXr55zgxl-HCUwxJKFoHY4YIm7fP0C17VIY7mZmjtg2J7p2T3Jlm1_T2CRC8bzIyvFr-q78B0ULQM5N_VnS4WLSPVP7_VmCECUIfNKlLoCG_qO6luh3Z4dmZ5us/s0/ssms-registered-server-3.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj-ymvg5T8koDFRLmxtfElL6abGfFmOrPl4uhBI8Rm_Q2W5KIS-7JnvByxTufwfGauSw5UvSzttmCxnxqbJWzqXNQ9t0z_faun7-3i31nnBCiZnjb-AcJbKHwVpP-YHg0uDIKyLtgZUVqo/s0/ssms-registered-server-4.png)

## SQL Server 組態管理員

SQL Server 組態管理員包含以下三個管理項目：

### SQL Server 服務

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi3sNcTOx1DMdGu4IHIA4h37FcLZFPSGHJAhy7jTebZP6z4l4uYKD_WrYh7RhpnIz4Kb-1DiravxbEXqvteUrTpsTtqGK3TIR-xPxa1mgnNx7895PRK6rv7MAmtKU541C7HLyxaJp32hH0/s850/sql-config-mgr-service-1.png)

### SQL Server 網路組態

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjmnX2InTX0DYF7d4xgPFBiJwxg58uM_7poJ4YdI8yMTM8fiBIoqjWFzvTM-6XesMz9LE01A0iAr2wKqnq_N8V_XgQai3s-1k9v17a_5J03955rw1sOb-7qmBIiCiz8BFpsD94nZRSXDyU/s0/sql-config-mgr-net-1.png)

SQL Server 2008 支援四種網路通訊協定：  

- Shared Memory：僅限本機連線。
- Named Pipes：使用專為區域網路設計的 IPC 通訊協定。
- TCP/IP：使用網際網路的 TCP/IP 通訊協定。
- VIA：此通訊協定需搭配 VIA 硬體使用。SQL 2012 不再支援。

### 設定與使用 TCP/IP 通訊協定

#### 啟用 TCP/IP 通訊協定

要使用 TCP/IP 通訊協定，必須啟用該協定，並且設定動態通訊埠號碼(port)。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhqwUCHEVaDaDX2gaqBpcv2Ui1_ZVNQ1Hi3BFfNkGKmSdgOqxtuDeMjyGIx0Pc7qwprtvT6KHN4F62zatK7cUNMo1D4Rk6U3g-6per0UxOR3HqRPM4-53kKpufZveXmgjEyZrx-kSfum7o/s0/sql-config-mgr-net-2.png)

#### 使用 TCP/IP 通訊協定連線

你可以使用以下格式指定伺服器名稱：
&lt; IP Address &gt;, &lt; PortNum &gt;  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj9XG5Ps9OPEymRRIQolFN2xB8fmnmCd1I1BG96tWa8W1j2weiCaOn3Y_gaxxa65Uq92RSP_inIlZctWNeLznLrMdjiWmDCRFEPrllPKf3ZWT8O73UsKtX_y-FzP6MZJgVV17LkI3bIjk4/s0/sql-config-mgr-net-3.png)

## SQL Server Client 組態

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjrJz7PI_sUvRLTXPWiODif2DcZwcB45rxgU_UZYD7bfTq371AoL86dofu-Jq4yqUUSnlJgqWkZLn6AWdTjXDMUXreQJAXxqkpE1x61hsA2EjQ-aD00DzaRtmiFNIMHg6Um5QwqWUlF_qc/s0/sql-config-mgr-client-1.png)

### 客戶端通訊協定

「SQL Server Client 組態」可設定允許客戶端使用的連線通訊協定，以及通訊協定使用的優先順序。  若允許連線的通訊協定，必須先啟用該通訊協定才會有效。

### 別名

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh6xlMyGQ8prIJzma7kqcu1EqId1NGJE9VLWKSyGhHORaPIQSqI5uGYXY_WWmtW2aI1-_E99dsK7BPQbf04VsC8hYzL1pCyNekqgk5uv0mLDFBGOu-R7thU2ZU34W5C4g4ic7s7WJfKT28/s0/sql-config-mgr-client-2.png)


## 參考資料  

- [\[Will保哥\]善用 SQL Server Management Studio 選項設定DB作業環境](http://blog.miniasp.com/post/2009/08/05/SQL-Server-Management-Studio-SSMS-Options-configuration.aspx)
- [使用 SQL Server Management Studio](http://msdn.microsoft.com/zh-tw/library/ms174173.aspx)
-