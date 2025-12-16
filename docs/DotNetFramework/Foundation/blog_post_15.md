---
title: Byte 與 MemoryStream 轉換
layout: default
parent: Foundation
nav_order: 15
description: "Byte 與 MemoryStream 轉換"
date: 2013-03-12
tags: [DotNetFramework,Foundation]
postid: "2356928925594217593"
---
MemoryStream 是將資料存放在記憶體中的一種資料流，有時在執行資料處理時，必須將其轉換成陣列以方便處理。  

# 如何將 MemoryStream 轉成 Byte[]
```c#
public byte[] imageToByteArray(System.Drawing.Image imageIn)
{
MemoryStream ms = new MemoryStream();
imageIn.Save(ms,System.Drawing.Imaging.ImageFormat.Gif);
return  ms.ToArray();
}
```

# 如何將 Byte[] 轉成 MemoryStream
```c#
public Image byteArrayToImage(byte[] byteArrayIn)
{
MemoryStream ms = new MemoryStream(byteArrayIn);
Image returnImage = Image.FromStream(ms);
return returnImage;
}
```