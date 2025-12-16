---
title: 文字設定
layout: default
parent: 圖形
nav_order: 1
description: "文字設定"
date: 2012-01-26
tags: [DotNetFramework,圖形]
postid: "5856728635822390534"
---
# 如何在圖形中加入文字

步驟如下：

- 1. 建立一個 [Graphics](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.aspx) 物件
- 2. 建立一個 [Font](http://msdn.microsoft.com/zh-tw/library/system.drawing.font.aspx) 物件
- 3. 建立一個 [Brush](http://msdn.microsoft.com/zh-tw/library/system.drawing.brush.aspx) 物件(optional)
- 4. 呼叫 [Graphics.DrawString](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.drawstring.aspx)，並且指定位置。
```c#
Graphics graphic = this.CreateGraphics();
Font font = new Font("標楷體", 18, FontStyle.Bold);
graphic.DrawString("生日快樂!", font, Brushes.Blue, 10, 10);
```

# 如何控制文字格式

[StringFormat](http://msdn.microsoft.com/zh-tw/library/system.drawing.stringformat.aspx) 類別封裝了文字的配置資訊，建立 [StringFormat](http://msdn.microsoft.com/zh-tw/library/system.drawing.stringformat.aspx) 執行個體，再將它指定給 [Graphics.DrawString](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.drawstring) 方法，即可達到控制文字格式。 [StringFormat](http://msdn.microsoft.com/zh-tw/library/system.drawing.stringformat.aspx) 的幾個重要屬性如下：

- [Alignment](http://msdn.microsoft.com/zh-tw/library/system.drawing.stringformat.alignment)：水平對齊方式
- StringAlignment.Center ：指定文字對齊配置矩形的中央。
- StringAlignment.Near ：指定文字應該靠近配置來對齊。 在由左至右的配置中，近端位置是左方。 在由右至左的配置中，近端位置是右方。
- StringAlignment.Far ：指定文字對齊配置矩形原始位置的遠端位置。
- [FormatFlags](http://msdn.microsoft.com/zh-tw/library/system.drawing.stringformat.formatflags)：設定格式資訊
- StringFormatFlags.DirectionRightToLeft ：文字會由右至左顯示。
- StringFormatFlags.DirectionVertical ：文字會垂直對齊。
- StringFormatFlags.NoWrap  ：停用在矩形中進行格式化時的行間文字換行功能。
- StringFormatFlags.NoClip ：允許顯示圖像的突出部分和超出格式化矩形外部的未換行文字。預設值會被裁剪
- [LineAlignment](http://msdn.microsoft.com/zh-tw/library/system.drawing.stringformat.linealignment)：垂直對齊方式
- StringAlignment.Center ：指定文字對齊配置矩形的中央。
- StringAlignment.Near ：定文字應該靠近配置來對齊。
- StringAlignment.Far ：指定文字對齊配置矩形原始位置的遠端位置。
- [Trimming](http://msdn.microsoft.com/zh-tw/library/system.drawing.stringformat.trimming)：文字超過配置區塊時的修剪樣式
- StringTrimming.Character ：指定將文字修剪為最接近的字元。
- StringTrimming.EllipsisCharacter ：指定文字修剪為最接近的字元，並且在修剪行的末端插入省略號。
- StringTrimming.None ：不指定修剪
```c#
Graphics g = this.CreateGraphics();
// Construct a new Rectangle.
Rectangle r = new Rectangle(new Point(40, 40), new Size(80, 80));

// Construct 2 new StringFormat objects
StringFormat f1 = new StringFormat(StringFormatFlags.NoClip);
StringFormat f2 = new StringFormat(f1);
StringFormat f3 = new StringFormat(f1);

// Set the LineAlignment and Alignment properties for
// both StringFormat objects to different values.

f1.LineAlignment = StringAlignment.Near;    //垂直置頂
f1.Alignment = StringAlignment.Center;      //水平置中

f2.LineAlignment = StringAlignment.Center;  //垂直置中
f2.Alignment = StringAlignment.Far;         //水平置右

f2.FormatFlags = StringFormatFlags.DirectionVertical;

f3.LineAlignment = StringAlignment.Center;  //垂直置中
f3.Alignment = StringAlignment.Far;         //水平置右

// Draw the bounding rectangle and a string for each StringFormat object.
g.DrawRectangle(Pens.Black, r);
g.DrawString("Format1", this.Font, Brushes.Red, (RectangleF)r, f1);
g.DrawString("Format2", this.Font, Brushes.Red, (RectangleF)r, f2);
g.DrawString("Format3", this.Font, Brushes.Blue, (RectangleF)r, f3);
```

![](http://127.0.0.1:8080/_images/cs/draw_font.jpg)
```c#
Bitmap bmap1 = new Bitmap("北大武山.jpg");
Bitmap bmap2 = new Bitmap("copyright.gif");

//將圖２貼到圖１左上角
Graphics graph = Graphics.FromImage(bmap1);
graph.DrawImage(bmap2, 0, 0);

//在圖１之右下角輸出文字
Font f = new Font("Brush Script MT", 24 ,FontStyle.Italic);
Brush b = new SolidBrush(Color.White);
Brush bb = new SolidBrush(Color.Black);
string ct = "北大武山";

int x = bmap1.Width - 300;
int y = bmap1.Height - 50;

graph.DrawString(ct, f, b, x, y);
graph.DrawString(ct, f, bb, x - 1, y - 1);
graph.DrawString(ct, f, bb, x - 1, y + 1);
graph.DrawString(ct, f, bb, x + 1, y - 1);
graph.DrawString(ct, f, bb, x + 1, y + 1);
graph.DrawString(ct, f, b, x, y);

bmap1.Save("北大武山2.jpg");
```

![](http://127.0.0.1:8080/_images/cs/NorthTawu.jpg)