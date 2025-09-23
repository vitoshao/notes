title: 如何取得 SQL Server 資料庫正在執行的 T-SQL 指令與詳細資訊
layout: default
parent: HowTo
nav_order: 9
description: "如何取得 SQL Server 資料庫執行中的 T-SQL 指令與詳細資訊"
date: 2013-08-07
tags: [SQL,HowTo]
postid: "1181765935691327390"
---
使用 sp_who 或 sp_who2 這些系統預儲程序，雖然可以查出所有連線的狀況，也可以看到該連線被卡住 (Blocked) 的狀況，不過 Command 這個欄位卻只有查詢的摘要，看不出完整的查詢命令為何。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiAuWtoGqW4Lybq5i8q3EFutH47VFbCkuG2VrkSPoZhWXmNYIymKS1Zix71fsJCB26W4ZsjmP6WeKPy3cxkfU0yiwYVJjXb4jCoME9wm91wIgKtedgjvUjhO1reVeY07UvvkXDmDiiRrNc/s0/sql-sp_who2.png)

這時可以利用幾個 DMVs (Dynamic Management Views) 來查詢 SQL Server 資料庫中即時的運作資訊，包括正在執行的完整 T-SQL 命令句：  
```sql
SELECT 
	r.scheduler_id as 排程器識別碼, 
	status   as 要求的狀態, 
	r.session_id   as SPID, 
	r.blocking_session_id as BlkBy, 
	substring( 
	ltrim(q.text), 
	r.statement_start_offset/2+1, 
	(CASE 
	WHEN r.statement_end_offset = -1 
	THEN LEN(CONVERT(nvarchar(MAX), q.text)) * 2 
	ELSE r.statement_end_offset 
	END - r.statement_start_offset)/2) 
	AS [正在執行的 T-SQL 命令], 
	r.cpu_time   as [CPU Time(ms)], 
	r.start_time as [開始時間], 
	r.total_elapsed_time as [執行總時間], 
	r.reads     as [讀取數], 
	r.writes    as [寫入數], 
	r.logical_reads   as [邏輯讀取數], 
	-- q.text, /\* 完整的 T-SQL 指令碼 \*/ 
	d.name      as [資料庫名稱], 
	c.client_net_address as [用戶端IP地址], 
	c.client_tcp_port as [用戶端Port] 
FROM 
	sys.dm_exec_requests r 
	CROSS APPLY sys.dm_exec_sql_text(sql_handle) AS q 
	LEFT JOIN sys.databases d ON (r.database_id=d.database_id) 
	LEFT JOIN sys.dm_exec_connections c ON ( r.connection_id = c.connection_id ) 
ORDER BY 
r.total_elapsed_time desc
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgje6XSCzuF4NZWeozBHFLuWNJjwSNsXRW1F3RiwuNXJt_j6OiCSHan-q4M7d-a0KEssXL7iz4h8w4mbgiN-zocyqBWHxkcq-05YV18zmJRCuGqksBeAszhIQXygQLwvLJE_WAICxBOgHc/s0/sql-dm_exec_requests.png)