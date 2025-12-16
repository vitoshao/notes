---
title: 如何變更 SQL Server 預設的資料檔路徑與備份檔路徑
layout: default
parent: HowTo
nav_order: 2
description: "如何變更 SQL Server 預設的資料檔路徑與備份檔路徑"
date: 2012-03-09
tags: [SQL,HowTo]
postid: "2397646534880212938"
---
在操作 SSMS 時，常會碰到二個預設目錄，一個是在新增資料庫時，資料庫存放的目錄，一個是在備份資料庫時，備份檔存放的目錄。  由於系統使用的預設的目錄為 C:\Program Files\Microsoft SQL Server\MSSQL10\_50.W7\_SQLSVR\_2008\MSSQL\Backup\ ，  這通常不是大家慣於使用的位置，每次新增或備份資料庫的時候，就得手動再切換到自已要使用的目錄。  如果你跟我一樣覺得這樣子很麻煩的話，底下的示範就是說明如何變更這個預設目錄的值，讓使用 SSMS 時可以方便一點點。  

## 變更預設的「資料檔路徑」

首先，在 SSMS 裡點選ServerName，在右鍵選單中選取「屬性」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjM458sZUgFKG8Nx7_WePSWphzla7N_tBck7wymYL73veciNEBMOhEQH0Z2KSHs5N2Uw1Z_BsIFxJdPSyOWS02XrLzBGilFeKHkL-CTv5Qq4m3cbFtz2wc9UruD3AxGOX4fbmTHUccufPw/s314/sql-default-folder-02.png)

選擇「資料庫設定」頁籤，在下方就會出現資料庫預設路徑的設定欄位，將他改掉即可：

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgsagE-Pys3ZI_21_zFDngQRPmlTYnQSD_s8b7sjPLRVZP7v9m1xwbd-akW9Frzhyphenhyphen7cVKqe-T1hDcfEksIv62RdKnofPz9N3wWxvSrOIZfDOmx1HtLjJ1XWkY0ui0DSC-jHWhq_yr3eiAs/s715/sql-default-folder-01.png)

## 變更預設的「備份檔路徑」

要修改預設的備份路徑就必須手動修改機碼

首先，先開啟登錄編輯程式(regedit.exe)，  並找到 HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\MSSQL10\_50.W7\_SQLSVR\_2008\MSSQLServer 機碼，  該機碼下有個 BackupDirectory 字串值，直接修改成你要的目錄路徑即可。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjLkixro5swaCMg_UnXLSe0wutg3jtS64f_FCYcOp-zMOr_YXAhphdr-aMIAaSGmds2EgfgdBITsRY1qAgaswrUErjfeLx96RXdx3a4m25bTAecZIFXP1jtYRbb3deVlmDyShTCh3nUuW4/s744/sql-default-folder-03.png)

上面 MSSQL10\_50.W7\_SQLSVR\_2008 這個值要看實際的 SQL 執行個體的版本名稱，每個人的環境可能有所不同。

**備註：**

SQL 2012 已經在「資料庫設定」中，加入預設備份路徑的設定。(2013/7/4)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiJ60rnPLL8tLT8CnZijy7PZCwxDGhaY5LRsP-QOW9nWI5DLahx3yjwxuiRaQJi1W-fGjoYOeEfPsQUYdQp9VdcqgaErPDgxKeK5K3QYcWAlfbrw6cZ9uhjnkgViWmZO4D8BhrsRdo7R_M/s0/sql-default-folder-04.png)


> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
> consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.
> Vestibulum enim wisi, viverra nec, fringilla in, laoreet vitae, risus.

> Donec sit amet nisl. Aliquam semper ipsum sit amet velit. Suspendisse
 id sem consectetuer libero luctus adipiscing.

> This is the first level of quoting.
>
> > This is nested blockquote.
>
> Back to the first level.

[id]: http://example.com/  "Optional Title Here"

> ## This is a header.
>
> 1.   This is the first list item.
> 2.   This is the second list item.
>
> Here's some example code:
>
>     return shell_exec("echo $input | $markdown_script");

This is [an example](http://example.com/ "Title") inline link.

[This link](http://example.net/) has no title attribute.

This is [an example][id] reference-style link.





*   Red
*   Green
*   Blue

+   Red
+   Green
+   Blue

*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
    Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
    viverra nec, fringilla in, laoreet vitae, risus.
*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
    Suspendisse id sem consectetuer libero luctus adipiscing.

1.  This is a list item with two paragraphs. Lorem ipsum dolor
    sit amet, consectetuer adipiscing elit. Aliquam hendrerit
    mi posuere lectus.

    Vestibulum enim wisi, viverra nec, fringilla in, laoreet
    vitae, risus. Donec sit amet nisl. Aliquam semper ipsum
    sit amet velit.

2.  Suspendisse id sem consectetuer libero luctus adipiscing.