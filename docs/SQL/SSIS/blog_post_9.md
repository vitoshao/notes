---
title: 動態封裝
layout: default
parent: SSIS
nav_order: 9
description: "動態封裝"
date: 2014-01-06
tags: [SQL,SSIS]
postid: "2657007612823337074"
---
有時候為了便方將封裝在不同環境下執行（例如開發、測試和產品等不同環境），這時就必須採用動態方式來設計SSIS封裝。  **動態封裝**指的就是不用開啟或變更封裝內容，就能夠直接變更執行封裝要使用的設定值。  要達到這樣子的目的，除了原本就有的 Package Configuration 功能外，  在 SQL 2012 中也新引入了 Parameter 和 peoject-level Connection Manager 觀念來達到這樣子的效果。  

下面幾個主題，都與動態封裝有關。  

# Connection Managers and Parameters

「連線」與「參數」的設定，都可以分成「專案層級」和「封裝層級」。  其中，「封裝層級連線」設定，是之前就有的設定，而「參數」和「封裝層級連線」都是 SQL 2012 新加入的功能。  這二個功能都必需使用「專案部署模型」才可以使用。功能如下：

- **參數**：可讓您在封裝執行時，將值指派給封裝內的屬性。
- **專案層級的連線管理**：可讓專案中的所有封裝共用相同的連線設定。

## 專案層級連線（Project-Level Connection Managers）

在專案樹狀目錄下，有個連接管理員，在這裡建立的連接，就會是**專案層級**（project-level）。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEie21mRItZHoYHQUp21we5dZ8ehs2Ck6XC3MMy7KXG_ArByysyJRC28BqKIwCnaLaJis4J6jpzFs0kUDfYslDqGYVLc0UUMboVX_yoNDMDAYmKFelAxrLnpw-VAU6wZvSsc-irYc-Ls9pM/s0/ssis-project-connection-1.png)

如果是在設計工作區下方的連接管理員中建立的連接，預設就是**封裝層級**（package-level）。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj3CWewODnK2b8CCKio2Hp5tAAMSrJ5UN3R537biqqn8TENA-Iu2B1hZOnj62umg0KIj1Qtwrq5RM0MqxhSggPhU0t3aQhsFzANVroClLLYM0fXXenVbedilo7KV6ELJDni0OBBlX352P8/s0/ssis-project-connection-2.png)

You can also convert an existing package-level connection to a project-level connection.  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjofzYbqXfAZObmCwDP5ujqsLc6R56SJmd4q788LAb4SqcgPvH-kulWjbYKkAlDaqWUauGRcW9f-05Kw2yP5WgdOgt5NI2pdPOjVZIGsL7ml6bzf-KiKm3SgFlVbgWDvNIVHgsuXh8OCvA/s0/ssis-project-connection-3.png)

## 參數（Parameters）

過去，在封裝中，若希望某個屬性值可以依環境不同而不同，大都必須使用「封裝組態」來實現。  現在(SQL2012)，則可以使用「參數」，輕鬆的來達到這個效果。  

- 參數可讓您在封裝執行時，將值指派給封裝內的屬性。
- 當屬性要套用參數時，一次只能套用一個參數值。但不同的屬性可以用時套用到同一個參數。
- 參數值是唯讀的，所以在封裝執行期間，參數值是不可以變更的。
- 參數資料可設定成 **Sensitive**（即參數編輯窗格中的「區分」屬性），它們就會被加密後再儲入 catalog 。
- 封裝執行時，參數值是無法變更的。

### 參數層級

參數的層級也是可以區分成**專案層級**和**封裝層級**。  

#### 專案層級參數

專案層級參數由專案底下的 **Project.params** 節點開啟。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjgFs40vYNHwDM-mqPoAnvVYrAH5K3NZ1kPGKUm4WRZj4T8Mo6EnjGqjmrcuOrGSOQIycn66iKRF5-D6Svm-tbp89ALtoPujwY37YS28Eo0g8lsNPxqjKJ__HtXdZCQct9-dEJUFk59B7k/s0/ssis-parameters-1.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgHFlCtVu1mS__qWcnoNWenK-hS4G0TF7q4hy_lc2PpdPZqgQd7VDgJliziyQUsULM-RMwHa4b065RwwHjNg7kWJK1cSofVfEjyyeMzQqHrSuL38LtbdGDezUGjpi0CCytL5KxQEhc1dEA/s0/ssis-parameters-2.png)

#### 封裝層級參數

封裝層級參數由封裝中的[參數]頁籤開啟。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiDhUyei9XZvcxk4m5mvxJC1Z5RoufclaZu561ODbjI22aKvh2-Zy23HU_5nomqXarA10GcqAOesoLEty_A9Y35uRyh_wLKcC53WVsSiF5FUJgcBySh2fQw14wNheSaybPaC5IHTlhyphenhyphen8Uk/s0/ssis-parameters-3.png)

### 參數預設值

每個參數可以有三個不同類型的值：

- **設計值（Design default value）**：This value assigned when the project is created in SQL Server Data Tools (SSDT), persists with the project.
- **伺服器值（Server default value）**：This value assigned during project deployment and persists in the SSIS catalog.
- **執行值（Execution value）**：This value that is assigned in reference to a specific instance of package execution.

## 建置組態 (Build Configurations)

「專案部署」是 SSIS 2012 新加入的功能，因為這個功能，讓很多的設定都變的更方便。  例如，對開發者而言，常常必須同時面對不同的環境，如開發，測試，產品等不同的環境。  因此你可以使用 Configuration Manager 針對不同的環境設定不同的「建置組態 (Build Configurations)」。  

每個建置組態，在建置(build)的時候，都會產生一個可用來部署的檔案(.ispac)，裡面除了包含封裝資訊之外，還有參數或連接等相關設定。  

### 如何建立「建置組態」

##### 1. 開啟「組態管理員（ Configuration Manager ）」

要建立或修改「建置組態」，都必需透過「**組態管理員（ Configuration Manager ）**」。  你可以點選方案總管中的專案名稱，再由右鍵選單中，點選［屬性］以開啟「屬性頁」視窗。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjCwxeS91BveqcobCmJ5d78F3BLc_z1W5B4r243-Q4fDdb3IAcMnQyanCjh9MzGCTD60zrxxP1A8kuzGQbLkNOVwUzbqnWAQUae1o7P6V2hazmcl5aNZezFTzU26sNiScQWsPqvL-qerbM/s0/ssis-build-config-1.png)

「屬性頁」視窗

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiahsTJoFaNSYY-reuCpuZq6Ko2-1pvT9lh6n3_zcskb3_eodGSkyNUSrt1JuTC54_Y9XHFFvM14MoMM9hVacQX_DSpJHITuKXIVcgu13kRyhyphenhyphenpWf5HhXyz6QK56ezK-Q99v2jq9WhTRsw/s0/ssis-build-config-2.png)

上圖中，當你點選「屬性頁」的「組態屬性」中的項目時，就可以點選「組態管理員」按鈕，以開啟「組態管理員」視窗。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjT8TsXKnIs4a5vP-UB2-pJLzaEIyotHl1hBHtMHf6rUmiWIOILQ93INPYAEUgUIoT3cIyLGB8MdUiUSoJ95-T_a0E7Me7FGnnMtTQ78A8E3a03if_0Rk2GBvZR-ggh-APbAHbgfYh3Ut0/s0/ssis-build-config-3.png)

##### 2. 新增組態

在「使用中的方案組態」下拉清單中，點選「新增...」項目。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgWh2UtM8_GzVG2J6x0Sv1KqbvDaeKlY-pmdgMChl1bPtuUdGT0KRJlSQLnD_dg9RXBppe1q_ZNJtdKjRWl14txH5JiDntY7DI-DpEaozOw8d8KhEEeqhqbGCwdokUeWXGSyKuRQyzXm8k/s0/ssis-build-config-4.png)

輸入新的方案組態名稱。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEihhLMBKLG_I3dekKIh509306__Ge-mNWULYELYtY24s7Ta8prsds9I23KmVIgDltgTjJM6tqISIiGUSnwYr6OD8sNx8dig6Oknim1trqsCEXn1EXxe-Brxio04kU_Kgx920y6JixGUNwg/s0/ssis-build-config-5.png)

##### 3. 設定專案的組態

最後，你就可以依據實際的需求，設定這個專案要使用哪一個「建置組態」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhcvNea0AZuM1-fQCeLcaz5MH5c1q5cbGAK5wC3PrsT0rn9NQ0AaarmdrkXvilmq7wxiGybS4TToDujS4NU2n4BAd6gwcm-LOLP9Y5B_lk19l63e_ZMwYvmB-z6yEu8wj7oEm7mOCM9Pp8/s0/ssis-build-config-6.png)

### 如何利用「建置組態」

如果你的封裝中有使用參數，你可以在參數視窗中，將這些參數值繫結到組態檔。  那麼當你需要在不同的環境中使用不同的參數值時，就可以直接變換使用的組態檔，而不用直接變更參數值。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgUJhp0OgueQqiDK1Z8Jt2b0m_ZVUtKsfHzww14gYN2Ue9cBAbyRG8SkDgDzkk-e7_cTs11Hy7KQcju6h8N-lSOj9rkU0Xvorr-qwFpzNIURWXiIm3IBfHukPieXawNw7JmS6zV7nMceXc/s0/ssis-build-config-7.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhqWFbL50JFz_YwT8FNM8axvH91j3dBF7oK9yRv-Nooa4fTyGuL_n2eBxG3FmAMFdqA-4zrZZ355yhiXRKTnUyxnf9cbABKQoXHGKWc-x1KCUXm_OsMByZCbn-fdGashGPdXbX77amK0ng/s0/ssis-build-config-8.png)

## 屬性運算式（Property Expressions）

由以上說明知道，在不同的組態檔中可以指定不同的參數值。  那麼一些 connection manager 或者 control flow task 的設定也就可以動態化了。  

若要將 Connection 或 Task 設定為動態，也就是設定值是在執行階段才決定，  就必需使用封裝的「**屬性運算式**」(Property Expression)，因為屬性運算式可以將參數值套用在屬性之中。  

下面二個方法，都可以開起設定「屬性運算式」的視窗：  

1. 在元件的屬性視窗中，點選[Expression]屬性旁的(...)按鈕，就會開起「屬性運算式編輯器」視窗。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgnsU7YbNc53bmCSV-_Tqdz8OhsreeCe-9srksEUojWMGe_omKo7iOMvrKvR2Pe0lLOncJQQwcgkmgTISsYEBPqFJgrUgFxLdbVQftCQFPitRIrWKeHgGlMyKR3ocd48-71c_EStAxDtn8/s0/ssis-property-expression-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiee68NinRK9-Dn-7wDdPs8QbNsB-pRysVoz5qfh9OXTo-zLqI1Lta3j448RdhwBzgDx-98N0pGjje3-_fadkUSgoIcSzH1NQk17XhmbJkOShYowGWv_iDaK68CaXhJGQsbG0rI77MyNSk/s0/ssis-property-expression-1.png)

2. 在工作或連接的編輯器視窗中，先點選[運算式]頁籤，就可以看到相同的[Expression]屬性設定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjwF6vLV7gbGDRgPpKDzEDN59W5rIHsrCbuvzanieODdXJgN0KcIyzRPRpsf2ZcZyaE1RtzYnUcxVi73iXb5jPSjZHWfEUHieGgsZtbhMEOxw_0Jg3yKuL7nW6OJ-mLfNTQFa0xfzaViWM/s0/ssis-property-expression-2.png)

例如：下圖中，我們設定一個**連接管理**的 **ConnectionString** 屬性，它會由參數 ConnString 來決定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh5GayKIUPV0-TWV6rb6vV6XiNDY5Ka8PWFrFYOekWKqvmFzIZ9loURTQ4-bXLSQHEJgBda0z7X7dDkFZ1cgwQciPQGDcA2BOTNZhMPIPYvODmjQQOSLy07ClRHauJQcuICOVPm5zX9b4A/s0/ssis-property-expression-4.png)

所以你只要先將參數加入至組態，然後對不同組態給與不同設定值即可。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjpIYidE5M-IW80YGFIPQZZSBRcc_egWEifZht7Rr5t7SzXshRukvowX1ly0kqwjf6MQ55vNbN1W99KXRfkTVITHSmJ5l-mjEGTjuq1Yk0TQ47aBlCGRugV-MNDCYh-T9v4gg6ss1OdD8M/s0/ssis-property-expression-5.png)

## 設定 SQL Agent job 使用環境變數

為了需求，我們有時會在封裝中加入**參數**，若這個參數同時有多個封裝都會使用到，就必須將參數設定成**專案層級**。  當專案部署到 SSIS Server 之後，為了方便，你可能使用**環境變數**將**值**指派給這個**參數**。  

以上情節是很常遇到的例子，如果你要在 SQL Agent 中建立一個自動執行的作業，並完成以上功能，大至的步驟如下：  

### 1. 建立環境

在［環境］節點上，新增一個**環境**，取名為［測試環境Ａ］。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhEbDkWNqZmxMhk90WgtjszUVFY5aNyAUHx9NRm6-mVpd_2xhm7ds6T4MS6F5iFZDi4WlieCLyufP4nMiPFBAqWkOrmI6BUEYdo_hho73xF1iGgsD5CRyxbBP220EInVroWTAyG5aYhJok/s0/ssis-para-env-agent-1.png)

### 2. 建立環境變數

開啟［測試環境Ａ］的屬性視窗，新增一個**環境變數**，取為名［msg］。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgXautbpvsdOe_yoWlv3nRkqq2IVSFuUtKxihWm06kAsuB-GH_Rib4Yl2PszbL5wxLu_jCq9fx1y4jdg-TANg-TkTzuO0hvNtnGuUE5NacuG5Aggwws6SCY135jO2wn42N9s9dN9_B3cxc/s0/ssis-para-env-agent-2.png)

### 3. 設定專案參數參考環境變數

3.1 在 SSIS 專案上，開啟［設定］視窗。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg18eXJkXI3PZSQ4k7Gvq3QiVthjYfS5jiUpcZtRj6hUnLay0FJ-cYtMy7qKajUpdGfaZQjs-yCUi432BnwDeOQc7vgjXF0RiGvxmHOYm91rKGyiDUC6tikgIE7D6WUFME8P8VSa-Q9Nak/s0/ssis-para-env-agent-7.png)

3.2 在［參考］頁籤中，設定該專案將參考［測試環境Ａ］這個**環境**。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj9AvqfzLw8yOcG0Agd8TrBTrGjbFY1dh_SqWwtVuxnC4Y8uznVOaCSIs3IPdyCMhiLhqIPLk4hGefRAbXdZAeKFnQqadwYHSdSI0W3ixpS1MYH1_Ce2NKtJu0mNHlmNIsbZbTqoV89JWk/s0/ssis-para-env-agent-5.png)

3.3 在［參數］頁籤中，設定專案參數 mgs 的值來自於［測試環境Ａ］中的環境變數 msg 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEipmdClr90FJkQbSDYuaFOA9k6naJhrecGwJGvZVkkw3tdwzILBcPQPX9qSJO637WIQS7v_qppEuMNrsaWCFMpB7AEz5_TutE4DD_-EMqY0-P8Z_ERe97l_XbIcZxvFDFuLXkCangKYBc8/s0/ssis-para-env-agent-3.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgqqb0MijGadQlgArz6ghxsaxmhOzMhGY2xmBT6sSfTKCy7xyYtHphfH7RSZPQhT5SA9vUPPsfPYeccR-EQ_ghhdodUarwhGcNxGK1n1HoXlK_QOoLC64YjQDFkUK3ovX5FqjpP-VALsfA/s0/ssis-para-env-agent-4.png)

### 4. 設定 SQL Agent job 使用環境變數

在步驟中的［組態設定］頁籤中，勾選要使用的（環境］。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjx4SqPXQN6P4m7IlhwufLi5odIcwXCe3Lw0dsrnE0TUbkPVabUDjGT0vT3xOT-TBI4XHmGPa8RmZivuLR9Mj-b02AP9ty6imugSnAtpXgO_02w-4DUkSoD_T9Y5GCfOH3lAW0jrII9HNI/s0/ssis-para-env-agent-6.png)

# Package Configurations

封裝組態（Package Configurations）是 SSIS 2012 之前就有的功能，  主要功能就是希望變數值或設定值不要在設計階段就寫死，而能夠在執行時，透過組態資訊中的設定值再來決定。  

## 設計封裝組態（Package Configuration）

### 轉換成「封裝部署模型（Package Deployment Mode）」

在 SQL 2012 中，SSIS 專案預設會使用「專案部署模型」，如果要使用**封裝組態**，則必需先確定專案是否使用「封裝部署模型」，你可以在專案的右鍵選單中找到轉換功能。  不過，若是專案中已存在任何的 project-level 的參數或連接設定，將無法進行轉換。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEihmfLdsLkyuu4FRpmPEiDQs7JZTgn6zz9Lp1ytcxsRNmHqAmQlB48zxx54IUOI-QScEWieYWShf4mzPySbmPqzpSdK1fMjMp1cVQ72BlQvyYA-ba5EUrrO5aAURNU5g2NNXgaEpfJu_GI/s0/ssis-package-config-1.png)![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg7afAGC1nYcAl_UbfCQZ7oKGcM8IxT4hofMrUKQKbR4ipZbBl8tWVEo1H69MvfpKIHLcmbhXyQ0JjjW2v0XNIh95zSyL-qXEJco1G9-_Z4wGN1OTDGYuqIrWy-UrmV9I67dqomLOwajiw/s0/ssis-package-config-2.png)

### 執行「封裝組態組合管理（Package Configuration Organizer）」

每一個封裝預設上都是停用組態檔的，你可以使用「封裝組態組合管理」來啟用與建立「封裝組態」。  

1. 你可以在SSIS選單中找到[封裝組態]功能，它會開啟「封裝組態組合管理」。(如果SSIS選單中沒看到[封裝組態]，請先點一下package的設計頁面)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiTwbgwqdlI3tozrN5-9YDxzPN73dJsvZf6vpM_22Mn7NuZA39yqwaScgcMOzIn_XJ5HabFIwQ-ck25YbUe_1U9IY-cLcV-vZD4JuHm_Ews3TG6P6YQ2jxxDteG5mKlEYcUlWHWm-ksDlk/s0/ssis-package-config-3.png)

2. 設定「啟用封裝組態」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEii1cc9NJD2LD3Idd2wZUdACuQ4HG_hpBkITBwk6AwcsyjfFRl6PhEh0Z-PaE4Z1ueOY9zMgGr8mt5xRg2bb_7o_J6_cjO6EMmIXSPCPL_x5AAhHUFI25-lCc4BNRluu2EJ0lLzcFDBZNQ/s0/ssis-package-config-4.png)

3. 封裝組態是透過「封裝組態精靈」來設定

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjStuaZccl4Z8w-YmtpeYtZj7fH6qfazXk_ldMKB_IXTj1MpXsQ67IX2A5gJD4KK2PMtEbERQjT9kPdiDqHiXu3SpTAh9bsVV8k5588axUaICf-EzMoCFk3PjgcTE6tJYn_BM6dPjYQlAk/s0/ssis-package-config-5.png)

### 設定組態類型（Configuration Type）

在設定封裝組態精靈的設定過程中，有個步驟必需設定「組態類型」，它是用來決定要將組態設定值儲存在什麼裝置。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg6RlC1igAB10Bu3q9Z2UtjeKGBsOPWBnxhP3CnZGaO_nqfEZW6mQcmJLSH-wfz4GrqhB3TqwHDrLjl0DXuxs5QWHUwRVAgC5y9xrBNL08nqcs9LR434VYgxTwXZuzAGIEo8XwvCzDQhDI/s0/ssis-package-config-6.png)

### 設定匯出屬性

這個步驟用來指定要記錄在組態中的項目。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEit0mKEKhrNtC72Y-mBwpnf5SknjTBhXSLncHBOSr1GIvUEOEQLJwfOejTCL5fuvAtUZq5Rs3HX2Xgzsxe4DfUdfBq-XeOCQmqbbKHv9s6uKTt8MKfJgiYrhinf-POoH7b-5WDflT8OvyI/s0/ssis-package-config-7.png)

### 完成組態設定

給定組態名稱，完成設定。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiqpgbuLxHtYtLATAS6cJVjjgCSeVihsDNiZk6lcuRwhKq1TFS8XXCJJ9tIh7bvYY8Q-qZWKLdbV3C-OBf8ch3YI6MdF5zjBmq2drErKaucrAMX5-L8Vz-DCTeS7WgyqjFAq6LYS2HZU_Y/s0/ssis-package-config-8.png)

## 組態的共用與排列順序

一個組態設定可以設定成共用，讓多個封裝一同時用，以減少相同資訊的維護。  

一個封裝也可以設定多個組態，並定義他們的使用的先後順序。  相同的屬性設定，在後面組態檔中的設定會蓋過前面的組態檔。

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

- [Integration Services (SSIS) 參數](http://technet.microsoft.com/zh-tw/library/hh213214.aspx)
- [Integration Services 連接](http://technet.microsoft.com/zh-tw/library/ms140203%28v=sql.105%29.aspx)
- [了解組建組態(Build Configurations)](http://msdn.microsoft.com/zh-tw/library/kkz9kefa.aspx)
- [封裝組態(Package Configurations)](http://technet.microsoft.com/zh-tw/library/ms141682.aspx)
- [建立封裝組態](http://technet.microsoft.com/zh-tw/library/ms141132.aspx)