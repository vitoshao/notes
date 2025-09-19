---
title: MagickSettings SetFont
layout: default
parent: Dot Net
description: "Unable to find an entry point named 'MagickSettings_SetFont' in DLL 'Magick.Native-Q16-x64.dll'."
date: 2025-02-17
tags:
  - Magic.Net
---

今天將 Magic.Net 由 13.5 升級到 14.4 ，結果部署程式到 IIS 之後出現以下錯誤.

![Magic Image Error](images/magic-image-error.png)

後來才發覺 runtime 目錄底下的 DLL 也要更新。

明明根目錄下的 Magick.Native-Q16-x64.dll 已是新版，為何跑的卻是 runtime 目錄下的 DLL。
對這領域不是很清楚，我想是因為與誇平台相關的元件，程式執行時，是以對應平台目錄中的 DLL 為主。
先記錄一下，避免下次忘記。

![Magic Image Error 2](images/magic-image-error2.png)


PS. GhostScript 同步更新，由 gs9.54.0 至 gs10.04.0 .
