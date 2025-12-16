---
title: 常用網路指令
layout: default
parent: MISC
nav_order: 2
description: "...。"
date: 2025-01-15
tags:
  - tag1
  - tag2
nav_exclude: true
---

## nbtstat

這個指令可以用來查詢 NetBIOS over TCP/IP 的相關資訊，例如遠端主機的 NetBIOS 名稱、MAC 位址等。

```bash
nbtstat -A 192.168.20.139
```

![Nbtstat 01](images/nbtstat_01.png)


## netstat

這個指令可以用來顯示目前系統的網路連線狀態，包括開啟的連接、監聽的埠號、路由表等資訊。
```bash
netstat -an
```