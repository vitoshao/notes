---
title: MDI Form 、 MenuStrip
layout: default
parent: WinForm
nav_order: 3
description: "MDI Form 、 MenuStrip"
date: 2016-01-28
tags: [WinForm]
postid: "486295900658574053"
---
## 如何建立 MDI 父視窗

- 先將主要表單的 [IsMdiContainer](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.ismdicontainer.aspx) 屬性設為 true 。
- 再將主要表單設定為應用程式的主要進入點。

## 如何加入 MDI 子視窗

載入時必須指定子視窗的 [MdiParent](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.form.mdiparent.aspx) 屬性
```c#
Form1 frm = new Form1();
frm.MdiParent = this;
frm.Show();
```

## 如何在 MenuStrip 中自動建立 MDI 子視窗的清單

若希望 MDI 子視窗建立時，可以在選單中自動建立清單，只要指定一個 MenuItem 給 [MenuStrip](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.menustrip.aspx) 的 [MdiWindowListItem](http://msdn.microsoft.com/zh-tw/library/system.windows.forms.menustrip.mdiwindowlistitem.aspx) 屬性即可。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhon9S6Vis-zFHtPawMq1dmtkX027Xo1y0x8K8ubDTXLdlvRfmDcnOyZDNBxoxg9TLsCN_-VI0JuDfniVXwcQV8RwxuV2d-ozE33sUF-pT1-fx6VcyQbJn6Dg0iP7yj7jkYVzOKX2Grgjc/s0/MdiWindowListItem.png)

子視窗建立時，就可以在選單中自動產生項目。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiPvHufWfzV3G7ZIz9R6IdoFr-UA_uiVcvnJvTc2AtIt04uIzCf3iMoL39ZV08rVUks9fWLiz64PogxPiqAl9cnK8ZVYkV1IX8nrweILI_P84jnFruWzDWRYeB8TaxnLCdD4TthWkOV0Ag/s0/MdiWindowListItem-2.png)