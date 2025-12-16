---
title: 繪製圖形
layout: default
parent: 圖形
nav_order: 3
description: "繪製圖形"
date: 2012-01-26
tags: [DotNetFramework,圖形]
postid: "7207245267214112808"
---
命名空間 [System.Drawing](http://msdn.microsoft.com/zh-tw/library/system.drawing) 提供建立或修改影像 (image) 的方法。  例如：  

- add circles, lines, and other shapes
- create charts from scratch
- edit and resize pictures
- change the compression ratios of pictures
- crop and zoom pictures
- add copyright logos or text to pictures

####   下表列出幾個 [System.Drawing](http://msdn.microsoft.com/zh-tw/library/system.drawing) 命名空間中幾個重要的類別或結構：  

| 類別 | 說明 |
| --- | --- |
| [Graphics](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.aspx) | 封裝 GDI+ 繪圖介面。 |
| [Bitmap](http://msdn.microsoft.com/zh-tw/library/system.drawing.bitmap.aspx) | 點陣圖 |
| [Image](http://msdn.microsoft.com/zh-tw/library/system.drawing.image.aspx) | 提供功能給 [Bitmap](http://msdn.microsoft.com/zh-tw/library/system.drawing.bitmap.aspx) 和 [Metafile](http://msdn.microsoft.com/zh-tw/library/system.drawing.imaging.metafile.aspx) 子代類別的抽象基底類別 |
| [Icon](http://msdn.microsoft.com/zh-tw/library/system.drawing.icon.aspx) | Windows 圖示 |
| [ImageAnimator](http://msdn.microsoft.com/zh-tw/library/system.drawing.imageanimator.aspx) | 顯示具有以時間為基礎的框架 (Frame) 的影像動畫 |
| [Pen](http://msdn.microsoft.com/zh-tw/library/system.drawing.pen.aspx) | 繪製直線與曲線的物件 |
| [Brush](http://msdn.microsoft.com/zh-tw/library/system.drawing.brush.aspx) | 填滿圖形形狀內部的物件 |
| [Font](http://msdn.microsoft.com/zh-tw/library/system.drawing.font.aspx) | 定義文字格式的物件 |
| [StringFormat](http://msdn.microsoft.com/zh-tw/library/system.drawing.stringformat.aspx) | 封裝文字配置資訊 ( Encapsulates text layout information ) |

| 結構 | 說明 |
| --- | --- |
| [Color](http://msdn.microsoft.com/zh-tw/library/system.drawing.color.aspx) | 表示 ARGB (Alpha、紅、綠、藍) 色彩 |
| [Point](http://msdn.microsoft.com/zh-tw/library/system.drawing.point.aspx) | 表示二維平面(X,Y)的點 |
| [Rectangle](http://msdn.microsoft.com/zh-tw/library/system.drawing.rectangle.aspx) | 表示矩形的位置和大小 |
| [Size](http://msdn.microsoft.com/zh-tw/library/system.drawing.size.aspx) | 表示大小 |

# HOW TO：建立繪製的圖形物件

## 如何建立 Graphics 物件

使用圖形的兩個步驟如下：  

- 1.建立 [Graphics](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.aspx) 物件。
- 2.使用 [Graphics](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.aspx) 物件來繪製線條和形狀、呈現文字或顯示和管理影像。

要使用 GDI+ 來畫畫之前，您必須先建立 [Graphics](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.aspx) 物件，  因為 [Graphics](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.aspx) 物件才有提供繪圖的方法。  想要在什麼裝置上繪圖，就必需要先由該裝置取得 [Graphics](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.aspx) 物件。  取得 [Graphics](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.aspx) 物件之後，就可以開始在上面繪製各種圖形資料。  例如：  
```c#
//Form 控制項的 Graphics
Graphics graph1 = this.CreateGraphics();

//PictureBox 控制項的 Graphics
Graphics graph2 = pictureBox1.CreateGraphics();

//Button 控制項的 Graphics
Graphics graph3 = button1.CreateGraphics();

//使用指定的 Image 建立 Graphics
Image img = Image.FromFile(@"D:\VitoBlogPic\reg_1.jpg");
Graphics graph4 = Graphics.FromImage(img);
```

## 如何繪製線條、形狀

當使用 [Graphics](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.aspx) 繪圖時，底下二個與繪圖相關的工具是最常使用到的：

- [Pen](http://msdn.microsoft.com/zh-tw/library/system.drawing.pen.aspx) ：畫筆，用來繪製直線與曲線的物件。
- [Brush](http://msdn.microsoft.com/zh-tw/library/system.drawing.brush.aspx) ：筆刷，用於填滿圖形形狀內部的物件，例如矩形、橢圓形、派形、多邊形和路徑。
```c#
Graphics graph = this.CreateGraphics();

//建立畫筆
Pen pen = new Pen(Color.Blue,5);
//建立筆刷
Brush brush = new SolidBrush(Color.Red);

graph.DrawRectangle(pen, 11, 11, 100, 100);  //畫一空心矩型
graph.FillRectangle(brush, 14, 14, 95, 95);  //畫一實心矩型
```

## 如何設定畫筆 (Pen 物件)

[Pen](http://msdn.microsoft.com/zh-tw/library/system.drawing.pen.aspx) 是用來繪製線條的物件，例如直線，框線，曲線...。  這個物件常用的屬性有：顏色、大小、線條樣式 ( [DashStyle](http://msdn.microsoft.com/zh-tw/library/system.drawing.drawing2d.dashstyle) ) 、端點樣式 ( [LineCap](http://msdn.microsoft.com/zh-tw/library/system.drawing.drawing2d.linecap) )。例如：  
```c#
//建立畫筆 (大小:7, 顏色:紅)
Pen pen = new Pen(Color.Red, 7);

//設定線條樣式
//DashStyle.Dash:虛線
pen.DashStyle = DashStyle.Dash;

//設定端點樣式
pen.StartCap = LineCap.NoAnchor;
pen.EndCap = LineCap.ArrowAnchor;
```

#### 1. 使用預設屬性設定線條樣式

####  [Pen](http://msdn.microsoft.com/zh-tw/library/system.drawing.pen.aspx) 物件的樣式

- [DashStyle](http://msdn.microsoft.com/zh-tw/library/system.drawing.drawing2d.dashstyle) ：虛線樣式。
- [LineCap](http://msdn.microsoft.com/zh-tw/library/system.drawing.drawing2d.linecap) ：線條端點樣式。
```c#
p.DashStyle = DashStyle.Dot;        //設定點線
g.DrawLine(p, 50, 25, 400, 25);

p.DashStyle = DashStyle.Dash;       //設定虛線
g.DrawLine(p, 50, 50, 400, 50);

p.DashStyle = DashStyle.DashDot;    //設定點虛線
g.DrawLine(p, 50, 75, 400, 75);

p.StartCap = LineCap.ArrowAnchor;   //起點Cap設為 ArrowAnchor 樣式
p.EndCap = LineCap.DiamondAnchor;  //起點Cap設為 DiamondAnchor 樣式
g.DrawLine(p, 10, 225, 400, 225);

p.StartCap = LineCap.Flat;          //起點Cap設為 Flat 樣式
p.EndCap = LineCap.Triangle;        //起點Cap設為 ArrowAnchorTriangle 樣式
g.DrawLine(p, 10, 250, 400, 250);
```

![](http://127.0.0.1:8080/_images/cs/line_style.jpg)

#### 2. 自訂屬性設定線條樣式
```c#
//若 DashPattern 有指派任何值,則 DashStyle 屬性自動變為 Custom。
//DashOffset  : 設定直線開端至虛線圖樣開端的距離
//DashPattern : 設定虛線和間距大小的陣列
//              陣列元素分別表示：第1虛線的長度、第1間距的長度、第2虛線的長度、第2間距的長度.....
Graphics g = this.CreateGraphics();

Pen redPen = new Pen(Color.Red);
redPen.Width = 6.0F;
redPen.DashStyle = DashStyle.Dot;
g.DrawLine(redPen, 0, 70, 500, 70);

Pen greenPen  = new Pen(Color.Green);
greenPen.Width = 6.0F;

//設定 Offset=12F 距離
greenPen.DashOffset = 12.0F;

//設定 長度=3,間距=2,長度=2,間距=2,長度=1,間距=2,.......
greenPen.DashPattern = new float[] { 3.0F, 2.0F, 2.0F, 2.0F, 1.0F, 2.0F };

g.DrawLine(greenPen, 0, 100, 500, 100);
```

![](http://127.0.0.1:8080/_images/cs/line_style_custom.jpg)

#### 下表是幾個 [Graphics](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.aspx) 物件常用畫線的方法：

- [Clear](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.clear.aspx) ：
- [DrawLine](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.drawline.aspx) ：
- [DrawRectangle](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.drawrectangle.aspx) ：
- [DrawEllipse](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.drawellipse.aspx) ：
- [DrawPolygon](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.drawpolygon.aspx) ：
- [DrawPie](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.drawpie.aspx) ：
- [DrawPath](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.drawpath.aspx) ：
- [DrawIcon](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.drawicon.aspx) ：
- [DrawImage](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.drawimage.aspx) ：
- [DrawString](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.drawstring.aspx) ：
```c#
Graphics graph = this.CreateGraphics();

Point[] points = new Point[] {new Point(10, 10),new Point(10, 100),new Point(50, 65),new Point(100, 100),new Point(85, 40)};

//建立畫筆 (大小:7, 顏色:紅)
Pen pen = new Pen(Color.Red, 7);

//線條樣式:
pen.DashStyle = DashStyle.Dash;     //虛線

//端點樣式:
pen.StartCap = LineCap.NoAnchor;    //不指定
pen.EndCap = LineCap.ArrowAnchor;   //箭頭型狀

//1. DrawLine
graph.DrawLine(pen, 1, 1, 100, 100);

//2. DrawRectangle
pen.Color = Color.MintCream;
graph.DrawRectangle(pen, 100, 100, 80, 60);

//3. DrawPolygon
pen.Color = Color.Blue;
graph.DrawPolygon(pen, points);

//4. DrawEllipse
pen.Color=Color.Purple;
graph.DrawEllipse(pen, 100, 100, 80, 60);

//5. DrawPie
pen.Color = Color.Yellow;
graph.DrawPie(pen, 1, 1, 100, 100, -30, 60);

//6. DrawPath  表示一系列連接的直線和曲線。
GraphicsPath graphPath = new GraphicsPath();   //NameSpace：System.Drawing.Drawing2D
graphPath.AddEllipse(0, 0, 200, 100);
graphPath.AddRectangle(new Rectangle(50, 50, 50, 50));
graphPath.AddLine(new Point(5, 5), new Point(15, 15));
pen.Color = Color.Black;
graph.DrawPath(pen, graphPath);
```

## 如何設定筆刷 (Brush 物件)

繪製線條時，必會使用到 [Pen](http://msdn.microsoft.com/zh-tw/library/system.drawing.pen.aspx) 物件，若要填滿一個範圍，則必須使用 [Brush](http://msdn.microsoft.com/zh-tw/library/system.drawing.brush.aspx) 物件。  由於 [Brush](http://msdn.microsoft.com/zh-tw/library/system.drawing.brush.aspx) 是一個抽象類別，所以必須使用底下的衍生類別以建立實體：  

- System.Drawing.Drawing2D.[HatchBrush](http://msdn.microsoft.com/zh-tw/library/system.drawing.drawing2d.hatchbrush) ：花紋筆刷( HatchStyle )。透過前景和背景色彩的變化，以產生不同花紋效果。
- System.Drawing.Drawing2D.[LinearGradientBrush](http://msdn.microsoft.com/zh-tw/library/system.drawing.drawing2d.lineargradientbrush) ：線形漸層筆刷。漸層是沿著由矩形的寬度或兩個點指定的線條來定義漸層。
- System.Drawing.Drawing2D.[PathGradientBrush](http://msdn.microsoft.com/zh-tw/library/system.drawing.drawing2d.pathgradientbrush) ：路徑漸層筆刷。漸層是從路徑的中心點到路徑的外部界線邊緣。
- System.Drawing.[SolidBrush](http://msdn.microsoft.com/zh-tw/library/system.drawing.solidbrush) ：純色筆刷，定義單一色彩的筆刷，用來填滿圖形形狀。
- System.Drawing.[TextureBrush](http://msdn.microsoft.com/zh-tw/library/system.drawing.texturebrush) ：影像材質筆刷，使用影像來填滿形狀的內部。

[LinearGradientBrush](http://msdn.microsoft.com/zh-tw/library/system.drawing.drawing2d.lineargradientbrush) ：這個類別是線形漸層筆刷物件，  也就是漸層樣式是以線性變化的，透過 LinearGradientMode 屬性，可以設定漸層的樣式：  
```c#
public enum LinearGradientMode
{
Horizontal = 0,             //從左至右的漸層
Vertical = 1,               //從上至下的漸層
ForwardDiagonal = 2,        //從左上至右下的漸
BackwardDiagonal = 3,       //從右上至左下的漸層
}
```
```c#
Graphics graph = pictureBox1.CreateGraphics();

//左上右下漸層
Rectangle rectangle = new Rectangle(0, 0, 200, 100);
LinearGradientBrush linGrBrush = new LinearGradientBrush(
rectangle,
Color.AliceBlue, Color.CornflowerBlue,
LinearGradientMode.ForwardDiagonal
);
graph.FillEllipse(linGrBrush, 0, 0, 200, 100);      

//30 度角漸層
LinearGradientBrush linGrBrush2 = new LinearGradientBrush(
rectangle,
Color.Crimson, Color.LemonChiffon,
30
);
graph.FillRectangle(linGrBrush2, 0, 0, 200, 100);     
```

![](http://127.0.0.1:8080/_images/cs/fill_style_linear-gradient.png) ![](http://127.0.0.1:8080/_images/cs/fill_style_linear-gradient2.png)

## 如何填滿區塊

#### 下表是幾個 [Graphics](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.aspx) 物件中，幾個常用填滿區塊的方法：

- [FillClosedCurve](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.fillclosedcurve.aspx) ：
- [FillEllipse](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.fillellipse.aspx) ：
- [FillPath](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.fillpath.aspx)  ：
- [FillPie](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.fillpie.aspx) ：
- [FillPolygon](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.fillpolygon.aspx) ：
- [FillRectangle](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.fillrectangle.aspx) ：
- [FillRegion](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.fillregion.aspx)  ：
```c#
Graphics graph1 = this.CreateGraphics();
Pen pen = new Pen(Color.Maroon, 2);
Brush lg_brush = new LinearGradientBrush(new Point(1,1), new Point(100,100),Color.White, Color.Red);
Point[] points = new Point[]{new Point(10, 10),new Point(10, 100),new Point(50, 65),new Point(100, 100),new Point(85, 40)};

graph1.DrawPolygon(pen, points);         //在指定位置內, 繪一個線框
graph1.FillPolygon(lg_brush, points);    //在指定位置內, 以指定之 brush 樣式填滿

//指定一個網狀筆刷(Hatch Brush)
HatchBrush hBrush = new HatchBrush(HatchStyle.Horizontal, Color.Red, Color.Blue); 
graph1.FillEllipse(hBrush, 0, 100, 100, 60);

Graphics graph2 = this.button5.CreateGraphics();
graph2.DrawPolygon(pen, points);
```

![](http://127.0.0.1:8080/_images/cs/fill_style.jpg)