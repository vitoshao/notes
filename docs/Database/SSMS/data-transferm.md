---
title: 資料轉移
layout: default
parent: Database Manage
nav_order: 1
description: "資料轉移"
date: 2012-03-08
tags: [SQL,Database Manage]
postid: "8444910639005585899"
---
使用 SQL Server 要將資料由 A 主機轉到 B 主機，通常有以下幾種作法  

1. 使用 bcp.exe 由A匯出，再由B主機用 bcp.exe 匯入
2. 使用DTS (Data Transformation Services) 或 SSIS (SQL Server Integration Service)，將資料封裝成檔案，再由B主機匯入。
3. 備份成 .bak 檔，再由B主機還原。
4. 卸離資料庫(Detach)，再由B主機附加(Attach)

若沒有SSMS介面可以使用，但又要將某一資料表中的資料轉到B主機內呢？那就只能利用結構和資料的SQL Script，包含：  

a. Table
b. Index
c. Trigger
d. Data

在SQL2005以前，利用SSMS，可以轉出 a b c 三種資料，SQL2008 目前可以產生 d 的語法了

Step1:執行SSMS的產生指令碼

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgjRSw8xkUF6SFkRXhc5AfC_5d6pRemOc6NMJQKGMe4kXNIexO9ElXvXt6NaX_WB1z20xqn7CdspiRCOATUGILkbMJv16wD0gD18K6mBVnqbOuLedHyFPnqrDYsvWL_xvKGH6TUNuKwesA/s587/sql_gen_insert_script_1.png)

Step2:在此頁面點選下一步

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiwpVbnhqggEDg2tGnwLK6dSs4yejWIwj-hjDbUYyY5-_UZLMnPJqHqp6_ruSNhssJ7treqvYy_vQnYFnOnTLyd6OusBTSxW8oWKhM7g3rsBNNpyWVLakSLVpwiYbTbIppGAz484HFxOwE/s729/sql_gen_insert_script_2.png)

Step3:選擇要產生Script的物件

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgI_IBePOrPCSj26rAaSLbFaYe-5eq00yjRBCGdUPMpiTxDofVb5hzqOF8qGyrhZUqG1cKXtzMggJyLc37RS2jsiZX5NKeTgHDWZ9_JUoMkPxhwQrOvAqMHD7EHRKyB8cDu0AeWCFULe2o/s729/sql_gen_insert_script_3.png)

Step4: 選定輸出位置，並開啟進階設定

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhYd58PbwG7TLYXTV50qZknObo1_awnJhp0gXg3vsBP-4CzQVIkk6oEgV5kqLzAM48SyazqvEe8gzDm7lZTdXPCQNeJUVB-qle17rlQ6xLN4WEszTbTTg4zS-okJYZwstXPrQj-BLS0854/s729/sql_gen_insert_script_4.png)

Step5:在進階設定中，將編寫指令碼的類型，撰擇含有"資料"的選項，輸出內容才會有資料的 insert into 語法

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgpw_qz4vJolyUZvxw7G3YsGyAIQViN2-U67AifjTTpqbUeKup0hd3rPy23hON19KFoQMNdOCYAE5fq09k5zM1oRWuk2KWr74aYDOIpRpPjMr2eAfhjaorQGjCRMfRPOm2KrFE1OBfnyeY/s729/sql_gen_insert_script_5.png)

Step6:這個進階設定功能中，還有很多項目可以設定，例如，Table 的 Index 和 Trigger 部分，預設都是False，若有用到，要記得改成 True

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEixSA8O1sVJmMECz6l7p2I6xa5G8jA5S6ACAVm2VdI5Vxit2sbGNwWJF7QWugykqQDgKq1MElOPW7GF4AfmaOOT-C0mwxfFkc-dvhEL4xYzwbBM4s4JBYG97DEGCTG4s8tUFpxL0SxOwAs/s729/sql_gen_insert_script_6.png)

最後，這是要的結果。....Good

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi8KhpnqYyluSXsuzge-jzCsnEYgcjV7PcpgExhfNCBQ8NkICgDwkjUEDKOnM3QTcQr0dXRwNQE6sauU-Ef7Bzxrtd7OBXtFN-r456nb3xjTwtyAU4lNRF7CidwXI1sTAtY8RibnW_pi8w/s850/sql_gen_insert_script_7.png)