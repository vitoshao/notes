---
title: Sample
layout: default
parent: Sample
description: "...。"
date: 2025-02-14
tags:
  - authentication
  - authorization
nav_exclude: true
---

## 前言

- Cookie-based authentication
  
  Server 端在驗證後，將必要的識別資訊透過 cookie 發送至用戶端，用以識別使用者的做法。

- Session-based authentication
  
  Server 端在驗證後，將必要的識別資訊使用 Session 記錄下來，用以識別使用者的做法。

- Token-based authentication

  Server 端在驗證後，將必要的識別資訊，以 Token 型式傳送給用戶端。後續，如果客戶端想要存取資源時，都必需要發送此令牌。
  令牌通常包含有關使用者、用戶端、身份驗證時間戳記和其他具有唯一 ID 的有用資料的資訊。

- Claims-based authentication

  這與 Token-based 的身份驗證相同，只是它在 Token 中添加更多的用戶資料（如角色，權限等等），這些資料與授權相關，涉及客戶端是否有權限對資源進行操作。


記住密碼

AuthenticationProperties.IsIsPersistent
- True : 關閉瀏覽器時，自動清除 Cookie.
- False : Cookie 會等到有效期限過了或者手動刪除才會失效。

## 參考資料
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>