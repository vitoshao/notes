---
title: Lab5：資料流程設計2
layout: default
parent: Labs
nav_order: 5
description: "Lab5：資料流程設計2"
date: 2013-12-19
tags: [SQL,Labs]
postid: "5214591545140035437"
---
前一個練習中，我們已經可以將資料 ETL 到倉儲資料庫。  在這個練習中，我們將變換一些轉換技巧，來提升整個 ETL 轉換過程的執行效率。  

# Enhancing Extract Data

在上一個Lab中，我們由二個 [OLE DB Source](http://technet.microsoft.com/en-us/library/ms141696.aspx) 取出資料，先由 [Sort Transformation](http://technet.microsoft.com/en-us/library/ms140182.aspx) 排序，再經由 [Merge Join Transformation](http://technet.microsoft.com/en-us/library/ms141775.aspx) 來合併二個資料來源。  我們現在將這些事，全部交由資料庫自已來處理，除了簡化步驟，也可以提升執行效率。  

### 1. 更新資料來源

1.1 將下圖紅色區塊整個刪除

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgnVsFqZPukjwSmpUnhI7V_7F1Qh8kjry_nQfacFt9534ZtTK5SInxJdopss77R8evbaM32TO5A1dbvGdtHMc_zU53Yeyfxzf8USOIMj_Zk6d-R-mPTBf8EgIiIuPbpmqRIuHq1dVp9mk0/s0/ssis-lab05-01.png)

1.2 加入新的資料來源

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjN2vSn1TdeGHYaBYugB36Xu4bpp1WeGTvn0V1ku93Gg-RSAlpV-1jst6oWeI4NFzcO1xoYoksa1w6ylxv4-XJyNKxtLRUUAPqJemtdWgota3UmHayJoHM_KmDBxjoZp6aC_XrAdGjNDLg/s0/ssis-lab05-03.png)

1.3 設定新的資料來源

開啟 OLE DB 資料來源的**編輯視窗**，輸入以下 SQL 命令：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiX_kDfWLL7xhd5Bg91yi0tu1j6gxA6nZeEIvMe1SSkh_2N7_O6UXBzGTG2uBAO8GOL5kr_7aMUDwDwq1ELx93Z06COL8PI6f2Znkyew8LFTvgQzxlXes0eKFYy3yUdRqn7b4EnElOCluc/s0/ssis-lab05-02.png)

### 2. 設定排序後再輸出

開啟 OLE DB 資料來源的**進階編輯視窗**，在 OLE DB 來源輸出節點上，將 IsSorted 屬性設為 True。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEid-vxM95ZanyR4c7GbeYW3YURKjYD9BuaCIkFv0ugxssTZ97mr44etkvyt3349Yhmh1qfwkPvhTU93l97pjRXLF4-_ajcK_oTMIMbLkhOr9TgILR3i09NxytusxX9S2CEp4lzkafuP3vc/s0/ssis-lab05-04.png)

### 3. 設定排序欄位

開啟 OLE DB 資料來源的**進階編輯視窗**，在輸出資料行中，找到你要排序的欄位，並將 **SortKeyPosition** 屬性設為 1。   

- 0：預設值，不使用該欄位排序。
- 1：該欄位當做第１個排序欄位，且使用升冪。
- -1：該欄位當做第１個排序欄位，且使用降冪。
- 2：該欄位當做第２個排序欄位，且使用升冪。
- -2：該欄位當做第２個排序欄位，但使用降冪。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjv7rlJ8SiDNDItakubR6q7M4SkJZVmQQxcMBeHMekSWnWkwYs87D2jZdicPDImCXPY6ywdVlzyGAeHnfr55LqbyKZ5nZ3epdxfese76zYXHkXN84xyrSXcJas16QGLwPBlINR4bS8hfAk/s0/ssis-lab05-05.png)

### 4. 設定流程方向

將流程方向重新設定過後，執行這個新的package，就可以得到和前一個練習相同的結果。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgmf0bwhTmeHMAfqPCIwmzIAIB296aHc46Rs8fVQMOzLyZfKE3Hwwv5AZAJ0fmheQfUutDQPt4qeTc9sZnLX8zNKTVUVLtxTp0Z-QJ8cFLlJWI5Kzw4jK-XtKas1qWZc5pocFSRDIgvh3w/s0/ssis-lab05-06.png)

# Enhancing Lookup Data

在 Lab4 中，我們用了一個 [Lookup Transformation](http://technet.microsoft.com/en-us/library/ms141821.aspx) 去查詢 stg.CustomerInformation ，然後分別將二個比對結果再 Union 起來。  你也可以直接用一個輸出結果來達到這樣子的效果。  

### 1. 調整流程圖

調整如下：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjwvV5-F1brO29or2MsJOYgj4erOuQ_vEHLAgbHyU1BKPQs8xljDNrvk1bBE71luo954k3Ln1-DfFPSJQR69xqawrOippJqrMj4iTvI9xDvdxmhTR7KxSCB6mwurcw3LbTrYcdM-Buygt8/s0/ssis-lab05-07.png)

### 2. 變更 Lookup Transformation 的設定

將查詢轉換的無相符資料列的處理方式改成「忽略失敗」。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjw8yvQjRE4qZXKUMe_a0X4Rs3Sn0c9Yc34UmwGZfyzIF1VujwYJn2HQM6z-wI9Dy8EOcgxAnNZO4qBSRqcuytMVPgnV7COqHXJQOcxikvFyA63MvMKpm-No3BsZsH1WHL3NIATeex5dqo/s0/ssis-lab05-08.png)

### 3. 設定衍生資料行轉換

由於無相符資料列的處理方式已改成「忽略失敗」，所以這些資料列的相關欄位都會被塞 NULL ，我們可以使用以下的判斷式來處理這些欄位。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEihUhqhyphenhyphencbcRE_c8JkjW16uii5RpIRIKPpYx9fUeX6deFVEdcFrm4ZqgwiaUdl4LODjk34Bgw6RzfI-4WSl6YebaBjukDbyFyAvsIum_d_MW4dn7jUW6Vx42LdD4yefNXjiTfXcnYEW1CQ/s850/ssis-lab05-09.png)

# Enhancing Update Performance（Using Execute SQL Task）

在 Lab4 中，我們用了一個 [OLE DB Command Transformation](http://technet.microsoft.com/en-us/library/ms141138.aspx) 來更新資料，  由操作過程可以看的出來，這個元件是依結果集的大小，跑迴圈去執行 Update 或 Delete 指令，結果集有幾筆，就執行幾次指令，這種方法就是所謂的「Procedural 」方式，這是一種效能較差的方法。  比較好的方法是使用「Set-Based」方式，這種方法是使用 TSQL 對整個資料庫進行批次更新或刪除。  

底下我們將練習如何採用 set-based update 方式來提升更新作業的效能。  

### 1. 建立儲存更新資料用的 stage table

在 MyDW 中建立一個用來儲存更新資料用的 stage table ，它的結構要類似 DimCustomers 。  只需要儲存要用的欄位即可，例如 DimCustomers 中的 surrogate key 在這裡沒有用，就可以不需要。  
```sql
CREATE TABLE stg.DimCustomersUpdate
(
CustomerKey INT NOT NULL,
FullName NVARCHAR(150) NULL,
EmailAddress NVARCHAR(50) NULL,
BirthDate DATE NULL,
MaritalStatus NVARCHAR(3) NULL,
Gender NVARCHAR(3) NULL,
Education NVARCHAR(40) NULL,
Occupation NVARCHAR(100) NULL,
City NVARCHAR(30) NULL,
StateProvince NVARCHAR(50) NULL,
CountryRegion NVARCHAR(50) NULL,
);
```

### 2. 調整資料流程

2.1 刪除前例中的 [OLE DB Command Transformation](http://technet.microsoft.com/en-us/library/ms141138.aspx) ，再加入一個 [OLE DB Destination](http://technet.microsoft.com/en-us/library/ms141237.aspx) 。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiW3LROpXzOszf1mR_TxEremFJPt_TpmE47lLcS2Etqg_AC04M3BFdS-aehEfznVM-O4n9PRed1GDqakvnjz90-LLRCt-h_S5_qXwslxQ5U_RdqN-Me7qS-Rl-NQzwjB_7ZjSvJLB96UcU/s0/ssis-lab05-10.png)

2.2 設定 [OLE DB Destination](http://technet.microsoft.com/en-us/library/ms141237.aspx) 連接

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjtEM3wkwLY__D9JhJpIJXIOrT4ggXKb0H3a900Z-d7SrouSX-yxOtNaajhcp0liknwYc8cUDGDPF_ZHNyAuMRidHwxcG6XvFm_LThHtGh6nihJ33oQOhkyZ7dRZcxJ60RnneJUadwGgvk/s0/ssis-lab05-11.png)

2.3 設定 [OLE DB Destination](http://technet.microsoft.com/en-us/library/ms141237.aspx) 對應

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjqZtqxLiBbgPyKag-pW6kpwrYV2Hx_HCx3GNseFvnqHQPSu3XO7457pMPYs89wCAb4ozbacAb87kRfCoyUcXyGr3jGXVLAjdpkieuWvW5X72aa0ZgCYbVNSXvxCTOPxJD-Dm6fzuVEPjM/s0/ssis-lab05-12.png)

### 3. 調整控制流程

上一步驟後完成後，若是新資料則會被轉移至 dbo.DimCustomer 資料表，而舊有的資料則會被轉移至 stg.DimCustomerUpdate 資料表。  現在，我們可以在 Control Flow 中，加入一個 [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) ，來進行更新資料庫操作。  

3.1 加入一個 [Execute SQL Task](http://technet.microsoft.com/en-us/library/ms141003.aspx) 

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhz9N_jQmG4lVxW3FW69K9uCDhvzkrK0aiQ6f8nw0HW2y0a2QUij0PotOb8rPVFLpErtF7pW6C2ZGnvJoyfVy8yFHMnx_qYsiF4WHA3NsNuPlYC07QOB8h6RcifPkErBbC9Tzb_2BrMwPk/s0/ssis-lab05-14.png)

3.2 設定連接與執行命令

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEinxTOzwpAGBT21UZRgCxNfLE0aKk4zlazNp4-L_83wh8RJz-aWVCc_0IPsTRH7uVV6NPeWaYqShzoAuwrJypGwyzm61FnXZxMaAAUQVcJg33BQ-8aVx93YtGr8jyRKK3X2N-3Ab-VoaBk/s0/ssis-lab05-13.png)

SQL Statement
```sql
UPDATE C
SET 
C.FullName = CU.FullName,
C.EmailAddress = CU.EmailAddress,
C.BirthDate = CU.BirthDate,
C.MaritalStatus = CU.MaritalStatus,
C.Gender = CU.Gender,
C.Education = CU.Education,
C.Occupation = CU.Occupation,
C.City = CU.City,
C.StateProvince = CU.StateProvince,
C.CountryRegion = CU.CountryRegion
FROM dbo.DimCustomers C
INNER JOIN stg.DimCustomersUpdate CU ON C.CustomerDwKey = CU.CustomerKey
;
TRUNCATE TABLE stg.DimCustomersUpdate
;
```

### 4. 執行轉換

以下是執行結果

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjA8M4nKD-8z2WyyeiK3ouW9Kfu7C4OWdrtdAuYgVS48fnsV0jEKIfuusl1kyOcYnXFgQ7fzeaKRZotLL-j9kpaUDG8BG6MTTyHdIY_e_sAbwPVKPFCR0b2ioUV2pPrJMej-MCYf3fjm_I/s0/ssis-lab05-15.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEigUcS49cb5EToeARVs26tJI9plEPsYUPoD93HGj4d6Nd4ykZd-2mM7iBNrs9XH03VJSENc33L9K2Ub_52heMIvSHGD1MU4ILQErUS5MaLbTdGTb4SsQ-K-teO70X0-kjRsuh_ZgZouQho/s0/ssis-lab05-16.png)