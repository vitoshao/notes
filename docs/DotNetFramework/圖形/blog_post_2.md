---
title: 使用影像(Images)
layout: default
parent: 圖形
nav_order: 2
description: "使用影像(Images)"
date: 2012-01-26
tags: [DotNetFramework,圖形]
postid: "3028733783444831126"
---
# Image class

[Image](http://msdn.microsoft.com/zh-tw/library/system.drawing.image.aspx) 類別是 GDI 用來處理影像的類別，但是 [Image](http://msdn.microsoft.com/zh-tw/library/system.drawing.image.aspx) 是一個抽象基底類別，必須藉由 [Image.FromFile](http://msdn.microsoft.com/zh-tw/library/system.drawing.image.fromfile) 或 [Image.FromStream](http://msdn.microsoft.com/zh-tw/library/system.drawing.image.fromstream) 方法，才可建立 instances。  或者由它的二個繼承類別來處理： [Bitmap](http://msdn.microsoft.com/zh-tw/library/system.drawing.bitmap.aspx) 和 [Metafile](http://msdn.microsoft.com/zh-tw/library/system.drawing.imaging.metafile.aspx) 。  

- [Bitmap](http://msdn.microsoft.com/zh-tw/library/system.drawing.bitmap.aspx) 類別：靜態影像。透過提供載入、儲存和管理點陣影像的其他方法，增強 [Image](http://msdn.microsoft.com/zh-tw/library/system.drawing.image.aspx) 類別的功能。
- [Metafile](http://msdn.microsoft.com/zh-tw/library/system.drawing.imaging.metafile.aspx) 類別：動能影像。透過提供記錄和檢視向量影像的其他方法，增強 [Image](http://msdn.microsoft.com/zh-tw/library/system.drawing.image.aspx) 類別的功能。
```c#
// 使用 Image.FromFile() 讀取圖形資料
Image img = Image.FromFile(@"D:\VitoBlogPic\reg_1.jpg");
pictureBox1.BackgroundImage = img;
pictureBox1.Refresh();

// 使用 Image.FromStream() 讀取圖形資料
FileStream file1 = File.Open(@"D:\VitoBlogPic\reg_1.jpg", FileMode.Open, FileAccess.Read);
Image img2 = Image.FromStream(file1);
pictureBox2.BackgroundImage = img2;
pictureBox2.Refresh();
file1.Close();

// 使用 Bitmap() 讀取圖形資料
Bitmap bitmap = new Bitmap(@"D:\VitoBlogPic\reg_1.jpg");
pictureBox1.BackgroundImage = bitmap;

Bitmap bitmap5 = new Bitmap(@"D:\VitoBlogPic\reg_1.jpg");
Graphics graph5 = Graphics.FromImage(bitmap5);
graph5.DrawLine(new Pen(Color.Red, 7),1, 1, 100, 100);
```
**補充：GDI+**   
GDI+ 是 Microsoft® Windows® XP 作業系統的子系統，負責在螢幕和印表機上顯示資訊。從名稱便可得知，GDI+ 是 GDI 的後續產品，GDI 是舊版 Windows 隨附的繪圖裝置介面 (Graphics Device Interface，GDI)。GDI+ 是應用程式發展介面 (Application Programming Interface，API)，它是由一組部署為 Managed 程式碼的類別所公開。這組類別稱為 GDI+ 的 Managed 類別介面。   

繪圖裝置介面 (例如 GDI+) 可讓應用程式設計人員不需注意特定顯示裝置的詳細資料，便可在螢幕或印表機顯示資訊。應用程式設計人員會呼叫 GDI+ 類別所提供的方法，然後這些方法會適當地呼叫特定的裝置驅動程式。GDI+ 可將應用程式和繪圖硬體分隔，使開發人員得以建立與裝置無關的應用程式。

# 如何顯示圖片

- 建立 [Image](http://msdn.microsoft.com/zh-tw/library/system.drawing.image.aspx) 或 [Bitmap](http://msdn.microsoft.com/zh-tw/library/system.drawing.bitmap.aspx) 的執行個體。
- 將建立的 image 物件指定給控制項的 [Image](http://msdn.microsoft.com/zh-tw/library/system.drawing.image.aspx) 屬性。  ```c#
Image img = Image.FromFile(@"D:\VitoBlogPic\reg_1.jpg");

// 將建立的 image 物件指定給 Button 控制項
button1.Image = img;

// 將建立的 image 物件指定給 PictureBox 控制項
pictureBox1.BackgroundImage = img;
```
- 或者使用 [Graphics.DrawImage](http://msdn.microsoft.com/zh-tw/library/system.drawing.graphics.drawimage) 方法將圖片繪到指定 DC 。  ```c#
Image img = Image.FromFile(@"D:\VitoBlogPic\reg_1.jpg");

// 使用 Graphics.DrawImage 方法，將圖片繪到指定 DC 
Graphics graph = pictureBox1.CreateGraphics();
graph.DrawImage(img, 1, 1, this.Width, this.Height);
```

# 如何建立與儲存圖片

使用 [Image.Save](http://msdn.microsoft.com/zh-tw/library/system.drawing.image.save) 方法，可以將 [Image](http://msdn.microsoft.com/zh-tw/library/system.drawing.image.aspx) 以指定的格式儲存至檔案。
```c#
// 建立一個空白影像，並繪製一個多邊型後存檔
Bitmap bmp = new Bitmap(600, 600);
Graphics graph = Graphics.FromImage(bmp);
Brush brush = new LinearGradientBrush(new Point(1, 1), new Point(600, 600),Color.White, Color.Red);
Point[] points = new Point[]{new Point(10, 10),new Point(77, 500),new Point(590, 100),new Point(250, 590),new Point(300, 410)};
graph.FillPolygon(brush, points);
bmp.Save("bm.jpg", ImageFormat.Jpeg);

// 開啟圖檔，並繪製一個方框後存檔
Bitmap bitmap = new Bitmap("pie.jpg");          // 開啟影像檔
Graphics g2 = Graphics.FromImage(bitmap);       // 在圖形上畫一個方框
g2.DrawRectangle(new Pen(Color.Blue, 5.0F), new Rectangle(0, 0, bitmap.Width, bitmap.Height));
bitmap.Save("pie_2.Jpeg", ImageFormat.Jpeg);  // ImageFormat 指定檔案格式
```

# 如何使用Icons

Icons are transparent bitmaps of specific sizes that are used by Windows to convey  status. The .NET Framework provides standard 40-by-40 system icons as properties  of the SystemIcons class, including icons for exclamation, information, and question.  
```c#
// 載入檔案圖示
Icon ico = new Icon("04.ico");
Graphics g = this.CreateGraphics();
g.DrawIcon(ico, 0, 0);

// 載入系統圖示
g.DrawIcon(SystemIcons.Question, 100, 0);

// 將圖示轉換成 Bitmap
Bitmap bitmap = Icon.ToBitmap();
bitmap.Save("04_2.Jpeg", ImageFormat.Jpeg);
```