---
title: 環境變數
layout: default
parent: SSIS
nav_order: 15
description: "環境變數"
date: 2014-03-14
tags: [SQL,SSIS]
postid: "6407756307553168285"
---
# 如何將［環境變數］指定給［參數］

為了需求，我們有時會在封裝中加入**參數**，若這個參數同時有多個封裝都會使用到，就必須將參數設定成**專案層級**。  當專案部署到 SSIS Server 之後，為了方便，你可能使用**環境變數**將**值**指派給這個**參數**。  

以上情節是很常遇到的例子，如果你要在 SQL Agent 中建立一個自動執行的作業，並完成以上功能，大至的步驟如下：  

### 1. 建立環境

在［環境］節點上，新增一個**環境**，取名為［測試環境Ａ］。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhEbDkWNqZmxMhk90WgtjszUVFY5aNyAUHx9NRm6-mVpd_2xhm7ds6T4MS6F5iFZDi4WlieCLyufP4nMiPFBAqWkOrmI6BUEYdo_hho73xF1iGgsD5CRyxbBP220EInVroWTAyG5aYhJok/s0/ssis-para-env-agent-1.png)

### 2. 建立環境變數

開啟［測試環境Ａ］的屬性視窗，新增一個**環境變數**，取為名［msg］。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgXautbpvsdOe_yoWlv3nRkqq2IVSFuUtKxihWm06kAsuB-GH_Rib4Yl2PszbL5wxLu_jCq9fx1y4jdg-TANg-TkTzuO0hvNtnGuUE5NacuG5Aggwws6SCY135jO2wn42N9s9dN9_B3cxc/s0/ssis-para-env-agent-2.png)

### 3. 設定專案參數參考環境變數

接下來這個步驟就是要將專案中的參數，指向上面建立的環境變數。

3.1 在 SSIS 專案上，開啟［設定（configuration）］視窗。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg18eXJkXI3PZSQ4k7Gvq3QiVthjYfS5jiUpcZtRj6hUnLay0FJ-cYtMy7qKajUpdGfaZQjs-yCUi432BnwDeOQc7vgjXF0RiGvxmHOYm91rKGyiDUC6tikgIE7D6WUFME8P8VSa-Q9Nak/s0/ssis-para-env-agent-7.png)

3.2 在［參考］頁籤中，設定該專案將參考［測試環境Ａ］這個**環境**。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj9AvqfzLw8yOcG0Agd8TrBTrGjbFY1dh_SqWwtVuxnC4Y8uznVOaCSIs3IPdyCMhiLhqIPLk4hGefRAbXdZAeKFnQqadwYHSdSI0W3ixpS1MYH1_Ce2NKtJu0mNHlmNIsbZbTqoV89JWk/s0/ssis-para-env-agent-5.png)

3.3 在［參數］頁籤中，設定專案參數 mgs 的值來自於［測試環境Ａ］中的環境變數 msg 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEipmdClr90FJkQbSDYuaFOA9k6naJhrecGwJGvZVkkw3tdwzILBcPQPX9qSJO637WIQS7v_qppEuMNrsaWCFMpB7AEz5_TutE4DD_-EMqY0-P8Z_ERe97l_XbIcZxvFDFuLXkCangKYBc8/s0/ssis-para-env-agent-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgqqb0MijGadQlgArz6ghxsaxmhOzMhGY2xmBT6sSfTKCy7xyYtHphfH7RSZPQhT5SA9vUPPsfPYeccR-EQ_ghhdodUarwhGcNxGK1n1HoXlK_QOoLC64YjQDFkUK3ovX5FqjpP-VALsfA/s0/ssis-para-env-agent-4.png)

### 4. 設定 SQL Agent job 使用環境變數

在步驟中的［組態設定］頁籤中，勾選要使用的（環境］。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjx4SqPXQN6P4m7IlhwufLi5odIcwXCe3Lw0dsrnE0TUbkPVabUDjGT0vT3xOT-TBI4XHmGPa8RmZivuLR9Mj-b02AP9ty6imugSnAtpXgO_02w-4DUkSoD_T9Y5GCfOH3lAW0jrII9HNI/s0/ssis-para-env-agent-6.png)

# 如何將［環境變數］指定給［連線管理員］

若你設計了一個封裝程式，要對資料庫進行操作。  然而你有二台資料庫主機，分別是**測試機**與**開發機**。  你希望在封裝部署到 SSIS 主機後，能夠有個方便的設定，讓這個封裝在執行時，可以決定要使用哪一台資料庫主機連線。  

### 1. 建立環境

在 SSIS 主機上建立二個［環境］。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgd4uNlxh6iRVQ6xWZX2CWfFIPlXVsBvTCVDysj9U97GWzQetCY3gmDPSAgMX_AjEDl5_9skNOfSjAJlcGQOsLo__cnf1v0R4Wfh5sa-Ip_QnKe6SOAtxwuDM4g-MJdRyZoYw_NZwXqh6k/s0/ssis-conn-env-1.png)

### 2. 建立環境變數

在每一個環境中，都加入一個［環境變數］，並設定個自的連線字串值。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi42RXYSERP-voiekyCIJINyP_juS6e8z5KFVAZ5bTufdnmZ8dUKCH2WjlhUlyGlNYSF3Yh5Y5QFn3xDZdRC7ncHx-0rQ-KdOf33KwyGAFhLB__ZzbUVGQddUeqsIvl2hT_KwOPOl5-VgU/s0/ssis-conn-env-2.png)

### 3. 考入參考環境

如果你的連線是**封裝層級**，則開啟**封裝**的**設定**；  如果你的連線是**專案層級**，則開啟**專案**的**設**定。  接著將［環境］加入到［參考］。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgkEFifaSTmSHPSkttbFzfDw1gEK-lia966syptMjwYUMJQIBz4LYsMDpF39vlsTJJiLFegCW9jBUKhCqlm6Oiwyy-kfwbwMxQequ2gPA_g22p4xK4fz3mAb9i11ZEUH46l7yn8NLY0NRk/s0/ssis-conn-env-3.png)

### 4. 設定使用環境變數

在［參數頁籤］中，將 **ConnectionString** 的屬性值，更改成步驟２中建立的環境變數 **MyDwConn** 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhn5II-ZiHKJBK4Dd-4R06_7MECthpHqu2hrSb6AMHE9mAgKjBOBZLUJ9-LngT2Yo9ERz7ftzc_KUWzuTzyB4LFMC1_HWFWKXkBBeHVgH-n3memhUqk-U7r9Ot3iDhVjXqFPhdRQt7FfmA/s0/ssis-conn-env-4.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgID4FByNi9_gwz17LMYw0Gc0-QjH5H9eh_QxKTn0JkdxUpHIzFG_2z6l7E7CHqv7y2m8YCqNl5ZglUZqWBgVNSYZ2aYFxaB7HFicneMW9kY9a3YUurUC9U7eIoXuaQ_pPyGBoyxo7pYA4/s0/ssis-conn-env-5.png)

### 5. 執行封裝

當執行封裝時，你可以勾選環境，並選擇你要連線的那個環境。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhwFnCYjVLNI8TI7JTTywoCCF90z3sDa8hBEaE6Tt0UIOgckUUfmNkOFCnN3AqEns5Pyo9EEaT5vE3K-V72eGUYQg8-Kpk_15QxYgBDhDbDMsKTxPk49fmpLR15B641vnenteq4tl03uH8/s0/ssis-conn-env-6.png)