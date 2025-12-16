---
title: 相對路徑 vs 絕對路徑
layout: default
parent: ASP.NET WebSite
nav_order: 4
description: "相對路徑 vs 絕對路徑"
date: 2012-03-08
tags: [Web,ASPNET,ASP.NET WebSite]
postid: "7642044958230596732"
---
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiRwJ_ihG72fGFKkgl1aa1AqmoOP3G01TUSelxko3RkeO6dKQCFYhQO8OsbjtgS22w52jNfzdgmqz0rBU0KgU2RjJ0zXcEHYXwUgrHR23RMz_z-AgvVijUCEiIYccs064_E9RmehgHQ_n8/s850/image_path.jpg)

【dev environment】
Request.ApplicationPath : /
Request.Url             : http://localhost:55168/test/test_path2.aspx
Request.Url.AbsoluteUri : http://localhost:55168/test/test_path2.aspx
Request.Url.AbsolutePath: /test/test_path2.aspx
Service Uri             : http:

【iis environment】
Request.ApplicationPath : /VitoBlog
Request.Url             : http://127.0.0.1:8080/VitoBlog/test/test_path2.aspx
Request.Url.AbsoluteUri : http://127.0.0.1:8080/VitoBlog/test/test_path2.aspx
Request.Url.AbsolutePath: /VitoBlog/test/test_path2.aspx
Service Uri             : http://127.0.0.1:8080