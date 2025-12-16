---
title: 維護計劃
layout: default
parent: SSIS
nav_order: 2
description: "維護計劃"
date: 2013-07-10
tags: [SQL,Automatic,SSIS]
postid: "7884684777016654658"
---
使用 SQL 維護計畫時，DB Engine 會自動建立一個 Integration Services 封裝，  你可以手動執行這個維護計畫，或者使用排程交由 SQL Server Agent 自動執行。  

維護計畫常用來執行下列工作：

- 使用新的填滿因數重建索引。
- 移除空的資料庫頁面，進行資料檔案壓縮。
- 更新索引統計資料，以確保查詢最佳化。
- 對資料庫內的資料及資料頁執行內部一致性檢查。
- 備份資料庫及交易記錄檔。
- 執行SQL Server Agent作業

維護計畫必須透過 SQL Server Agent 服務來執行，所以請確認該服務是否已啟動。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiYH57JUkyoAfd0Rz5wyXlLnJKyRj4ibMTOI6iKPqLsAqPKqa7HBUdVCIhP6SClpttF4YaH8DpxFpRHc83zHBIbwPlljuOTyH3QNq7KrZv8AguwhpBH9jDjFSJSwtjsUtouYAXFtrXRpnU/s0/sql-start-agent.png)

# 建立自動備份

下面範例示範如何建立一個備份作業的維護計畫

## 1. 建立維護計畫

### 1.1) 建立維護計畫

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiepZ8ZFm6SUbALV5oH8ncs4VavHgNPxrZMh0zmCSqdnHzfYH3iM29BpuL1JF7GuRzTjhkVzUXJ4grDOSvilazJmUqcm0LnKiggmiDKCPc32wJXjOPHvMDOI7euLrsGkvDrOfklyf7Iaco/s0/sql-mp-backup-1.png)

### 1.2) 建立子計畫

先使用「加入子計畫」功能建立子計畫。再由工具箱中拖曳需要的工作類型。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjilwCTzmp1vb-N1KNvEqtuxb4_yfw6uK3HlqTP6vZVo5bAewHIUjKvXnrHXv_yOmJu1_nxN6vv-BOi5AOVWXzfZYPcNsbw5Jfvl26xy45nWN0ogCdL7sQG6ub8ooWCAxzYEsewJGs6I5o/s850/sql-mp-backup-2.png)

### 1.3) 設定執行排程

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj0EDjzexeaL2e52S0szC89XfU2uxLUIRfv2DO2WzBKMxLID2g7q-zq3XvYjPNCqL3vZ8u5TzqKFykm6NBp1dPtn8KRgcvDFY6SfDz0Stk5O9X-Svz9CaSIst0XtRQeLqRwPzgs4PIIsWU/s0/sql-mp-backup-3.png)

## 2. 檢視計畫內容

你可以在下圖看到你建立的「維護計畫」和「子計畫」

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjN7VHB3J0iYxa1jSdxiRSIxGmP_TBUeUp6pSpW2h74S221VbFyn8aLFfBQM-MGa2lcyJcYTHGtAYlYHVUoxJqSXRakQznxN7roVzh13tzSbcgVdU7d9FOoHbCBwwG1RqkledD5FbOsHmY/s0/sql-mp-backup-4.png)

## 3. 執行維護計畫

### 自動執行

正常狀況下，維護計畫內的子計畫會依據排程自動執行。

你也可以停用或啟用子計畫。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgNwneX30x9gz9PZ6onYET3SFw4MVLvsO_TXv7VABQketUH198eHNyLciisrQbk6IUn0dxqYpOmgN4x3WJDbd5WKhC8nh_GVsK-GNNtNeFSkix-JEumwqUXRUudYNjLXh3skiP-W_QXWyc/s0/sql-mp-backup-7.png)

### 手動執行

若是子計畫沒有使用個別的執行排程，則你可以手動執行整個維護計畫

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhvoa-OJ1w7QILoqlBVEIkUlCyMWyQUzwDa-OGWDpC6nK6e4gp-1mlj51yMm0z3kqY6qndDjondcftIGL_TcZwlYQ26YMEMctRboA0vmbJ_vQHa0rgtgGiiBWzORbDP3do3-HUuLtao3Mg/s0/sql-mp-backup-5.png)

若子計畫有個別的執行排程，則你必須在 SQL Server Agent 的作業中，個別執行子計畫。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhGGd-NnmvlaLxSvBMPO12UOeBse_kc_IsH600eElCzFZjh480Z4rUO3zT_i_-BLNZhjuAslJ-CDnm6cVyNxQ_UV9iw9XSrDKVGkCoVROsbDf9SV5zYhokSK-ecfgrsG4i6KdpxSqbVumI/s0/sql-mp-backup-6.png)

## 4. 檢視計畫執行狀況

點擊「作業活動監視器」可以查看所有計畫的執行狀況

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjHywMRLvgg0CaDwNY2ZzHS33DDqJdrxLwnpMhqakFAnQLQqADwcV5glOTl1NP2DpWBwwwbA-AgQwHkZ6_9_ORr-LByJsE3RXCsm1rk-6TdrLBQug5q6vvzKBPgNoqlx6oDfqr-_ugFFi4/s0/sql-mp-backup-8.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiTSbvniZDRqpxiMbm4GtI3FFV0Oq7jZmx01YuqzJgE6ggrM_tXfXPaU9YxrfvmVnrBkY8qQuTqp7pWEgPtjsAIiU9sLletJEv6Y3e9QWS2wl9SQBCEcaLEFFraHmpuRVwUrtlguJKPE38/s850/sql-mp-backup-9.png)

## 5. 計畫執行結果

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgh3FSL-JNY5Qi5-VVm7gGS-YcSbx2yREg2RSm4emBfRKUYX7uWU0w7PEHoqp_aVQIo0eHn-MSdMWYhE37wjH30GAUrUhpH_6LDPPvtGIqLD9EZY6IUTHhEeqloaiDKCDXVzc-13_IDck0/s850/sql-mp-backup-10.png)

## 6. 清除過時的備份檔

若想刪除過時的備份檔，可以使用「維護清除工作」，這個作業會透過 xp\_delete\_file 擴充預存程序來進行檔案刪除。  在這個範例中，我們可以將工作加進第一個子計畫中，也就是在建立完整備份作業後，進行檔案刪除工作。  

如下圖所示，總共建立了二個「維護清除工作」，分別刪除 .BAK 和 .TRN 檔。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiVX9SOSYeVcuwF-k1c3tENbVqVnmC-7orzI1_91ZzbnWUaHGPNjP8QM77J4X1lNVlPXrkZ8pFtSYws91AgzKCBD1OZzW3QJyAAhZV2MAj-RS2wryKQcUc5GNoP4L5tsF42CViMPfKyvjw/s850/sql-mp-backup-11.png)

## 7. 還原資料庫

若我們想將資料庫還原到某個特定時間，只要設定好「至某個時間點」項目，系統會自動幫我們帶出該時段之前的備份組資訊，方便我們進行還原作業。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjtszH-MEgRx8q9mYRj1ncPlLXoaltM-MF2q0IQ6Lkak_w2yhG7cH3bppmI4XMQeE5zn-hi0C0POWPv_zXbYPUew4vQV9N5RxRtYiW9NbNu4niLiGY4djUABpXlhXS4QIu5L_y37pyB-GI/s850/sql-mp-backup-12.png)