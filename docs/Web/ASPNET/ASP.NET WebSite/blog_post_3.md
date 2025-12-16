---
title: HttpContext 物件
layout: default
parent: ASP.NET WebSite
nav_order: 3
description: "HttpContext 物件"
date: 2012-03-07
tags: [Web,ASPNET,ASP.NET WebSite]
postid: "2302257320156368115"
---
寫WebPage時,有時候會用到 Request, Response...等等

例如:
HttpCookie cookie = new HttpCookie("Login");
cookie.Values.Add("id", txt_id.Text);
cookie.Values.Add("pwd", txt_pwd.Text);
cookie.Expires = DateTime.Now.AddDays(1d);
Response.Cookies.Add(cookie);

但如果我們想把一些常用功能寫到 Bussiness Class 之中, 就無法直接使用這些方法
原因是, 這些方法, 都屬於 Page 底下的成員, 若自訂類別沒有繼承Page, 就參考不到

那怎麼辨呢?
可利用 HttpContext 來取得我們所需要的網頁內建物件，並針對該物件來完成或取得所需資料

上面範例即可改寫成:
HttpCookie cookie = new HttpCookie("Login");
cookie.Values.Add("UserID", UserID);
cookie.Values.Add("Password", Password);
cookie.Expires = DateTime.Now.AddDays(3d);
HttpContext.Current.Response.Cookies.Add(cookie);

常用的方法還有:
Server
Application
Request
Response
Session
User
.......