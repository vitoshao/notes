---
title: 加密連線字串
layout: default
parent: ADO.NET and XML
nav_order: 3
description: "加密連線字串"
date: 2012-09-16
tags: [Web,ASPNET,ADO.NET and XML]
postid: "5561713503533641920"
---
## 使用加密演算法將連線字串加密

## 使用加密工具將連線字串加密

若要加解密組態檔，最簡單的方法就是使用 ASP.NET IIS 註冊工具 (Aspnet\_regiis.exe) ，可以針對 Web 組態檔的區段進行加密或解密。

#### 加密
```c#
aspnet_regiis.exe -pef connectionstrings C:\Inetpub\wwwroot
```

#### 解密
```c#
aspnet_regiis.exe -pdf connectionstrings C:\Inetpub\wwwroot
```
## 參考資料  

- [加密和解密組態區段](http://msdn.microsoft.com/zh-tw/library/zhhddkxy.aspx)
- 
-