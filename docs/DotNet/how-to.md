---
title: How to ...
layout: default
parent: Dot Net
description: "這裡記錄一些 Dot Net 常見的問題與解決方法"
date: 2025-02-14
tags:
  - HttpContext
  - tag2
---

## 這裡記錄一些 Dot Net 常見的問題與解決方法
<br>


{: .highlight}
> 如何將 filter 中的資料，提供給外部程式碼使用？

可以在 filter 中將資料儲存在 HttpContext.Items ，然後由外部程式碼取得使用。
```
public void OnAuthorization(AuthorizationFilterContext filterContext)
{
	...
	filterContext.HttpContext.Items["key"] = permission;
}
```
之後在外部(Controller或View)，都可以透過 HttpContext.Items 取得資料。
```
var permission = HttpContext.Items["key"];
```



## 參考資料
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>