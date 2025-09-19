---
title: Program.cs
layout: default
parent: Dot Net
nav_order: 1
description: "...。"
date: 2025-07-21
tags:
  - tag1
  - tag2
nav_exclude: true
---





## 使用環境變數

```cs
if (app.Environment.IsProduction())
{
    app.UseStatusCodePagesWithReExecute("/StatusError/{0}");
    app.UseHsts();
}
```

### ENVIRONMENT

ASP.NET Core 可以透過環境變數值`Environment`的設定，來定義目前應用程式的運行環境。

預設內建 Development、Staging 以及 Production 三種設定值，若沒有特別設定，則預設環境變數值會是 Production。

### 環境設定 

如果需要在本機環境模擬不同的環境，在 .NET Core 中可以透過設定 Properties\launchSettings.json 檔案，定義目前應用程式運行的環境，.NET 應用程式執行啟動時會先讀取 launchSettings.json 檔案中的 environmentVariables 會覆寫系統環境的設定值。

```json
{
  "$schema": "http://json.schemastore.org/launchsettings.json",
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:60720",
      "sslPort": 44392
    }
  },
  "profiles": {
    "http": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": true,
      "applicationUrl": "http://localhost:5205",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "https": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": true,
      "applicationUrl": "https://localhost:7057;http://localhost:5205",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}

```

## 參考資料
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>