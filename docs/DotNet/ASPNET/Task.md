---
title: 使用 Task.Run 進行非同步處理
layout: default
parent: Dot Net
nav_order: 1
description: "...。"
date: 2025-03-05
tags:
  - Task
---


## 使用 Task.Run 注意事項

```csharp
public class MyTask
{
    public async Task Job2()
    {
        Debug.WriteLine($"{DateTime.Now} Job2 Start");
        await Task.Delay(3000);
        Debug.WriteLine($"{DateTime.Now} Job2 End");
    }

    public async Task Job3()
    {
        Debug.WriteLine($"{DateTime.Now} Job3 Start");
        await Task.Delay(3000);
        Debug.WriteLine($"{DateTime.Now} Job3 End");
    }
}
var task = new MyTask();

Debug.WriteLine($"{DateTime.Now} Before Job2");
Task.Run(() => task.Job2());
Debug.WriteLine($"{DateTime.Now} After Job2");
```

上面程式，會先執行 Before Job2，再執行 After Job2，最後才執行 Job2()。
這是因為 Task.Run() 是一種 fire-and-forget 非同步的方式，不會等待執行完畢，所以不會阻塞主執行緒。

要注意的是，如果主執行緒執行完畢，則整個應用程式也會結束，此時，若 task.Job2() 還沒執行完畢，也會跟著被結束。

若要等待 Job2 執行完畢，可以使用 await Task.Run()。或者直接 await 那個非同步方法。

```csharp
Debug.WriteLine($"{DateTime.Now} Before Job2");
await Task.Run(() => task.Job2());
Debug.WriteLine($"{DateTime.Now} After Job2");

Debug.WriteLine($"{DateTime.Now} Before Job2");
await task.Job2();
Debug.WriteLine($"{DateTime.Now} After Job2");
```


## 參考資料
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>
- <a target="_blank" href="">XXXXXXXX</a>