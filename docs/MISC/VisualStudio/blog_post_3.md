---
title: 如何在除錯時允許修改程式碼
layout: default
parent: VisualStudio
nav_order: 3
description: "如何在除錯時允許修改程式碼"
date: 2013-03-13
tags: [MISC,VisualStudio]
postid: "498607016112216674"
---
## 如何在除錯時允許修改程式碼

VB6 有這個功能，VS2003 則完全停用這類功能。   

VS 2005 以後，雖然加回部分的除錯時允許修改程式碼，但是並不是每種狀況都可以，  一般來說下列狀況通常不能修改，或是修改的時候會跳出重新執行專案對話框。  
1. 多緒  
2. 涉及邏輯或物件結構  
3. 被引用的元件或自訂控制項  

若想在執行中斷時修改，請打開「編輯後繼續 (Edit and Continue)」功能  

![](http://127.0.0.1:8080/_images/cs/vs-continue-after-edit.png)