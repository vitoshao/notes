---
title: Lab16：在SSIS封裝中使用自訂程式碼
layout: default
parent: Labs
nav_order: 16
description: "Lab16：在SSIS封裝中使用自訂程式碼"
date: 2014-02-26
tags: [SQL,Labs]
postid: "9129510645104100205"
---
這個練習將會使用 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 去分析資料庫中客戶電子郵件資料的**樣版（Pattern）**。並將分析結果輸出到 XML 檔案中。  然後再使用 [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) 來開啟這個檔案，以讀取 XML 文件中**樣版**的資料。  

根據得到的**樣版**，再使用正規表達式去驗證另一個資料庫中的電子郵件資料，找出不符合這個**樣版**的郵件。  

# 使用 Data Profiling Task 分析資料

這個練習將會使用 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 去分析資料庫中客戶電子郵件資料的**樣版（Pattern）**。並將分析結果輸出到 XML 檔案中。

### 加入變數

新增一個封裝，加入二個 package-level 變數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjL0gP9jyLeEolTqPfIp-M2K1sUF-7ZP2MLWC5dGY5kj6IEsu1X16EebRkJ9DENBnDQvtBidiwDwDPGS1assYm_Bp-uwBo-NRvH98EU5n1BiYGWBNFZH6QYe3VWoCv7GglvmfZ52F1FLQo/s0/ssis-lab16-06.png)

### 加入連線

這個封裝將讀取 AdventureWorksDW2012.dbo.vTargetMail 檢視表中的資料，分析後再存到 XML 檔。  所以建立二個連線，一個是 ADO.NET Connection 連線到 AdventureWorksDW2012 資料庫，另一個是 File Connection ，連結到存放輸出的 EmailProfiling.xml 檔。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgotcsxRlrN0irRTXPHRPuCtLYSFlZeHFx-IT4DzMSIVIxuoy26cFkHhaGPxEHlexnY5zn2WjzcARZYSa5fIlNXg6F_MWxHtahhB1bhG-RaMbcZseUbflRhNKNE77XSsJWVZf0V4l4RhzQ/s0/ssis-lab16-01.png)

### 加入分析檔工作（Data Profiling Task）

加入 [Data Profiling Task](http://technet.microsoft.com/zh-tw/library/bb895263.aspx) 以便分析資料庫中客戶電子郵件。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi-pKm9qFCyawBH6rb6j8YeCnKRLBSzLESlrspL6TVJ9FJFdDEmw4FBoJat8FhMBll5ISO2NERHf2Y-1BIw6Q7Qf3vkSqgbVq8k8dYCKsdGCfZRJNOxPxCqbM6trz7UiuLAjkxHffXSB_8/s0/ssis-lab16-02.png)

設定 Destination 和 OverwriteDestination 屬性

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEguae9w-Gd7jLTrTZQEsEozX_UPIqS4DRThxURWEyznC7Dk5hFlD1qKxGnAZ0zbxKxaOAi35QJm-RPgQDlfIegQd4H5-cnTz2m_YO3hB9BZAY7PMioQ5yEN_J4CwfOthk_0k5FdOaqB27A/s0/ssis-lab16-03.png)

設定「分析檔要求」（Profile Request）

在「分析檔要求」頁面中加入「資料行模式分析檔要求」（Column Pattern Profile Request）

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj9OiDMXur3ht_3-vQFLCRJyKBD7tVCrRocdGpAFAS03ZDGiHY_MjSw16Tt1qi3uspS_omGoe_3Oqc3xPiMKxuXr5T-yxrbnqdlOBoHkdVSmqdebqvfw6jD83RyXHjv3tznoN6ET06yH04/s0/ssis-lab16-04.png)

並設定資料來源的連線及要分析的資料表欄位。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgTnsyE1le9sJU-i2Cj7yVXqyAxzgWLMgZC5q4i4qL8Gfnys7iWAc5TbBJerD5JMKvjQoycCu-RX8jJhiaCWqFGkO5mmfWHsi5JI7CQ5B0OYDbPc9XlN99GQxNPNbDJM3PbjRLz1232MiI/s0/ssis-lab16-05.png)

### 執行 ProfileEmail Task

在執行後，你可以開啟 XML 檔，查看執行結果。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhcdl-uvSsAzFMyx0nX-Shio31PHljB0EnRcTc5u7oeAH5u-94mNLbdrKeABxKYG2EFLrE7eLCUU6-c0fulMx_XNx4kEunkOf3nYFNnWejmasi-ar2PWV5NtceFPriBua60s2Km_1FPgSc/s0/ssis-lab16-07.png)

這個分析結果顯示，整個資料庫中的 email 總共可以歸納出二個正規表示式。

# 使用 Script Task 讀取資料

接下來我們將使用 [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) 來開啟這個 XML 檔案，並讀取文件中**樣版**的資料。

### 加入 Script Task

加入 [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) ，並連接上頭 ProfileEmail Task。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiUMKthanSUxmRiv9jxmKFTNa11K6r9BY4dfRGeTvYikcnJmzRhwH5FwAH7jmOt_RMdCXZ0rRDf0e8aj7D3WCWA0AKPDM1G5WChs3VOljyU2U76vaWid8X1iIKIi1R12iZ9qBIP4WUERxI/s0/ssis-lab16-08.png)

### 設定 Script Task

設定 [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) 的**進入點**及要**讀寫的變數**。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjRujLAstoSZUHrZ0DiphhhnLk3Q33Zhy233CjVY04mi_kJzuY4jQC8t5t59iM7eZdpSYCaIDF1SdcFwfs-fG0xO4FzpJjx1aTycz9pHkJmtSblciM2kH6OWzLGSAB-GeQnyTeeqjkKnDc/s0/ssis-lab16-09.png)

### 編輯指令碼

1. 加入 System.Xml Namespace

2. 在 ScriptMain 類別中加入變數的定義。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjIbXzHdpXS-r529y8m3fsJnImwuelL8Q7bthOOlUp-3H47IcoPIC66H8FGWw5IcQcufslF1IWwBmwhRdzHsf1SpUvjXLlRHxWZKLnx8613TPITu6gfcMpCltQEj8xQjYr86IiuHZuUWg8/s0/ssis-lab16-10.png)

3. 修改 Main Method 中的程式碼。
```c#
public void Main()
{
// TODO: Add your code here

string profilePath;
XmlDocument profileOutput = new XmlDocument();
XmlNamespaceManager profileNSM;
XmlNode regExNode1;
XmlNode regExNode2;

// Open output file.
profilePath = fileName;
profileOutput.Load(profilePath);
profileNSM = new XmlNamespaceManager(profileOutput.NameTable);
profileNSM.AddNamespace("default", profileNamespaceUri);

// Get regExNodes
regExNode1 = profileOutput.SelectSingleNode(erx1Path, profileNSM);
regExNode2 = profileOutput.SelectSingleNode(erx2Path, profileNSM);

// Assign variable values
Dts.Variables["User::EmailRegEx1"].Value = regExNode1.Value;
Dts.Variables["User::EmailRegEx2"].Value = regExNode2.Value;

// Show variable values
//MessageBox.Show(Dts.Variables["User::EmailRegEx1"].Value.ToString());
//MessageBox.Show(Dts.Variables["User::EmailRegEx2"].Value.ToString());

Dts.TaskResult = (int)ScriptResults.Success;
}
```

### 執行 Script Task

將 VSTA 存檔後關閉，並執行這個 Script Task，它會使用 MessageBox 回應二個訊息，並顯示由 XML 文件中讀取到的資訊。

上面例子，我們在 [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) 中，將資訊值儲存到變數中，  在運用上，你可以在 [Script Task](http://technet.microsoft.com/zh-tw/library/ms141752.aspx) 的輸出中，使用**運算式條件約束**來判別結果，以便將不同的結果接不同的處理工作。

# 使用 Script Component 驗證資料

接下來，我們將利用上面例中得到的二個Email格式資訊，使用 [Script Component](http://technet.microsoft.com/en-us/library/ms137640.aspx) 撰寫程式碼來驗證另一個郵件清單中的資料是否有符合這二個格式。

## Prepare the Environment

### 建立儲存驗證結果的資料表
```sql
CREATE TABLE dbo.EmailValidated
(
CustomerKey INT NOT NULL,
EmailAddress NVARCHAR(50) NULL,
EmailValid BIT NULL
);
```

### 停用前例中的顯示

在前例中，我們將讀取到的二個資料，由 MessageBox 顯示出來。我們必須註解掉這二行指令，以便程式可以繼續往下處理。  
```c#
//MessageBox.Show(Dts.Variables["User::EmailRegEx1"].Value.ToString());
//MessageBox.Show(Dts.Variables["User::EmailRegEx2"].Value.ToString());
```

## Use the Script Component

In this exercise, you use the [Script Component](http://technet.microsoft.com/en-us/library/ms137640.aspx) to validate email addresses against regular expressions.

### 加入 Control Flow

在 Control Flow 中加入 [Data Flow Task](http://technet.microsoft.com/en-us/library/ms141122.aspx) ，以便執行資料處理。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjPpkfEwPcVR-xZGuoIgjqvTdEmqKl0vh9SnT4-Y3i1mVthzLY3VofUDQy1M4Bvr5wvUoIiQpw35uCFDCeDajtq3DxYlIJv9nPT6OZsLzoJ7OK2a7s79K8otY26ivKCg8R1-oW0EAIUdws/s0/ssis-lab16-11.png)

### 加入資料來源

在 Data Flow Tab 中加入一個 [ADO NET Source](http://technet.microsoft.com/en-us/library/ms137897.aspx) 。這個資料來源就是要被驗證的郵件清單。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh8EOB4xPq5EPHk4-vFHor5rP1wiaQk0FCyJnPvGoUJLYekMrjg3CNmDHm8mqJZMp_7b6CqI1TmyfRWdxGuFcIIpKJh5Nn69UM3pkstojt0ATwM8htp2BhqAukz-TMNwbEfJLKkRb-OhWM/s0/ssis-lab16-12.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhcwh7zmbhBjn-_-HdenTKvrL7CBvi2w1pJ7rf39bT6JiZGEGtyyaXd5mnOPl6ZrnjVy01Yiwnhn14V6l7aIcVLYW7ZabSC7DQCE8NBvy3vlT7MY27ljiQBtr1Fx2uQemF351HcT-BtzCE/s0/ssis-lab16-13.png)

### 加入指令碼元件

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgVSkY4fqnmeisqCVLdNJFl0SBBPOFYFWKGwMtep1OuM0_-L8F_M1FA8j_KqZRzxPsKWivmKJ3yaGRUVroWk7C63Ih-sGquKT59H2X3Gw165w08B15LkMD-1qi6iRw55A2lxqS_l80OnYo/s0/ssis-lab16-14.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjy3SMumOcuxTKHX6NX6ciNqABCfzyGoLZhfHX8l-FpsEnwQKGCqwZ4WYz8y1mEppQiQZL76bykjMmb6FTmj21xqUVLozJOqzJ7xyX26-Z3fi9HOdk8kuQYkvb1s5pHrlyDwkUnFi-rbKE/s0/ssis-lab16-15.png)

### 設定指令碼元件

1. 在 Script 頁面中，設定 ReadOnlyVariables 屬性，指定要讀取的變數。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgiiq40ObC3aDvVrR1JtZ-YKCg65XbmlMRcL6PG6ILcjWFjtXoLBQoNqG0UvAnaqA-RL_l1xYleyDr1xfyCoDfBMXQHehsUeWlC_aJbfVy2mIRL_-WqR6YpwkgR2wFiPKpHcd8znDWc8UI/s0/ssis-lab16-16.png)

2. 在 InputColumn 頁面中，選擇以下二個欄位。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjw4j8ywhnPfiI1ZJ26igWx-IYmNb92uziQt4Il0V_ryc6rAhUEtGSq0L2eeJ_eHRlz7brqgFttmbPb9o2egIGnQKtyc3qCX8pVIfm8lYcazomrxERyheb2bKT9muNfnaZMBIfWN2Fvz58/s0/ssis-lab16-17.png)

3. 在 Inputs And Outputs 頁面中，展開 Output0 ，並加入一個輸出欄位 (EmailValied). 並將資料型別設定為 Boolean 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjT-jznI0NRBwhKLrRYDhQh472mUfHg-uMSE63PoZSYSwofp7nSkD9m-n2WQo44hc-_u7_objAFLGZlLAZZIPWoLZy5iqgnyK4ajBtmzbiWVgLxs1z3eer5lZbztkPcPTpUuSEodK7Sr4o/s0/ssis-lab16-18.png)

4. 回到 Script 頁面，執行「編輯指令碼」

### 撰寫指令碼

1. 在開啟 VSTA 編輯器後，你可以看到專案中包含三個檔案。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi197DYnK23Tm7YkjlCZ76Xw7MKubumig-WrkrUNgCCICoCwttvPWPsyVhz3Idz8ZRlYC97mhFvCUBMqXSqdMpXwblygAKUfRNvYDfsuFmxGd0uIaJ2s7BV6KB8g-CkcXy7gPfV_kV2yak/s0/ssis-lab16-19.png)

2. 在 Main.cs 中加入 Using System.Text.RegularExpressions 命名空間。

3. 在 Input0\_ProcessInputRow() 中加入以下指令碼。
```sql
public override void Input0_ProcessInputRow(Input0Buffer Row)
{
/\* Add your code here \*/
Row.EmailValid =
Regex.IsMatch(Row.EmailAddress, Variables.EmailRegEx1, RegexOptions.IgnoreCase | RegexOptions.Compiled) |
Regex.IsMatch(Row.EmailAddress, Variables.EmailRegEx2, RegexOptions.IgnoreCase | RegexOptions.Compiled);
}
```

4. Save the script and close VSTA

### 加入資料目的地

在 Data Flow Tab 中加入一個 [ADO NET Destination](http://technet.microsoft.com/en-us/library/bb895291.aspx) 。這個ADO.NET目的地是要用來存放已驗證的郵件清單。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkMAWKBh_3dGURGsgPkviHzeR9XOhhad9N0qkY2BUl_muliyU-m4lWjZzK_0YkTvKyvWzqbsJ9KmDCwy1sC3MWv_wZl49ZJqbRcneMqVra4hCIng_vSp-SDtPx8n-R4NMFass6zcLLxSo/s0/ssis-lab16-20.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEircjb0JCXWesquz0x5t4J-FqJO28otBKGSHo_6dTcorAWDJgZmqTyGESaINaBaN1agKgOhm1OzT160pQKgDkrxt65xuMK3HgAn_7Sx0qg5wWT1m5-LBxp3kdmF9E30L_Z-tXHyG6zmAKQ/s0/ssis-lab16-21.png)

### 執行封裝

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjFfzfFVkXcfZ9VuVnvkWAdPiYqYWXUgUJ92OzdogB2H7vNH_EoWuEW-hCEx4ORb7KSfgGe0EM1itBAz7vLt7FxzqCnoUyfB95NMr-NRHwkxiIUuuj9RIgshGjnc6_LbAOh35rnkVu9lvQ/s0/ssis-lab16-22.png)

### 檢驗結果

當封裝執行結束，你可以用以下 TSQL 找出驗證沒過的郵件清單。
```sql
SELECT \*
FROM dbo.EmailValidated
WHERE EmailValid = 0;
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMcvOZD5PY6rX7q6nvonuFA9LKjKxjIkfUGVsfrTR8S6RURzSV6VS9Qmzms1zDKP0yGpe_VT-ggV9ACD-3Ixzy0GBE8VVqAU7dZl1eNyDqv1LQWXcH114HgNXYxemYHgSkYaouw22EmXk/s0/ssis-lab16-23.png)