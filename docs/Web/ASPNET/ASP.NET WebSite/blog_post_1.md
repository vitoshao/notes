---
title: Web Form 的事件簡介
layout: default
parent: ASP.NET WebSite
nav_order: 1
description: "Web Form 的事件簡介"
date: 2012-03-07
tags: [Web,ASPNET,ASP.NET WebSite]
postid: "1979801186563380969"
---
#### Web Form 事件順序

下圖為 Page Event 的執行的先後順序
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgmOOpz1dqrvfCM-5xAyZCAQgjhByXrA0MMa0AXgsJllzm1eL_1YsMO2r2B-fVjdtwePqdEwFvcc6UNs-ON4xHcvEobHAhp8AkcMclACLUX3XqQo_gP8GbKNGWkWys2kJwXFd6yPrrEaCk/s317/WebFormEventOrder-1.png)  
若你要在程式碼中建立控制項，你必須了解 LoadViewState 這件事情發生的順序，以免發生資料載入錯誤的情況。

- LoadViewState 的動作是介於 Page\_Init 事件之後及 Page\_Load 事件之前，所以在 LoadViewState 之前就要把控制項建立完成，才有辨法由 ViewState 中載入最新的屬性值。
- 因此，應該要在 Page\_Init 事件中把控制項建立完成，若你在這個階段讀取控制項的屬性值都會是初始值，也就是設計階段時，在屬性視窗中設定的值。
- PageLoad 事件是在 LoadViewState 動作之後才觸發，所以在 PageLoad 事件中讀到的控制項屬性值才會是最新的值。

&lt;ｈ4&gt;實例說明：&lt;/ｈ4&gt;  
下面這三張圖，分別是初始載入畫面，以及並按了２次 Button 按鈕後的顯示頁面。  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgmOOpz1dqrvfCM-5xAyZCAQgjhByXrA0MMa0AXgsJllzm1eL_1YsMO2r2B-fVjdtwePqdEwFvcc6UNs-ON4xHcvEobHAhp8AkcMclACLUX3XqQo_gP8GbKNGWkWys2kJwXFd6yPrrEaCk/s317/WebFormEventOrder-1.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5TSUh_xLlPE5H8RFO-VKoz6AG0Z6o7c58Js_16LrEfW1V6kUegUKOUtSvXAXAfJGC2r4plhqSQ4pd3C26Z-VuO-2fJsvQASyvTx5Q1ZYE5ntHuHq6EYHR47E7piLrCVmUuG1SaccN3nI/s317/WebFormEventOrder-2.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjETmuDkKDQggHNDihXMc42IyHw4IzawpQ7CEx2EFPheO_BHiRjCAh-cAMz-Codr2ceOjHpyS6ffjuNucqu6kVwYL-LafOAPpV5hyphenhyphenHxQM78UMLDMhx8fYcdqAZPV9CAJBMtHxNcCV2WGRU/s317/WebFormEventOrder-3.png)  

- 圖一：程式初始載入。  
各個階段讀到的 TextBox1 值都是 A 。
- 圖二：將 TextBox1 改成 B ，並按下 Button 。  
TextBox1 的內容，被記錄在 ViewStatus 中，postback 回去後，ViewState 的值會在 Page\_Load 之前載入完畢，所以在 Page\_Load 階段讀到 TextBox1 的值才會是 B。
- 圖三：再按下 Button 。  
上次沒有變更 TextBox1 內容，所以少了 TextBox1\_TextChange 事件。  不過，這個 B 值還是記錄在 ViewState 中的