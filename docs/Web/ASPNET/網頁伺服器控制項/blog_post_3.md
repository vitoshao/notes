---
title: 特殊 Server Controls
layout: default
parent: 網頁伺服器控制項
nav_order: 3
description: "特殊 Server Controls"
date: 2012-10-26
tags: [Web,ASPNET,網頁伺服器控制項]
postid: "8670895765642903419"
---
特定型(specialized)的伺服器控制項，指的是 [Image](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.aspx) 、 [Calendar](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.calendar.aspx) 、 [Panel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.panel.aspx) 、 [FileUpload](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.fileupload.aspx) 、 [View](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.view.aspx) 這類控制項，它們通常具有專門的功用。  

# Literal

The [Literal](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.literal.aspx) control is useful when you need to add text to the output of the page dynamically  (from the server) but do not want to use a Label.  

#### Mode Property

[Literal](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.literal.aspx) 控制項的 [Mode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.literal.mode.aspx) 屬性是用來設定 [Literal](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.literal.aspx) 內容如何呈現。

- PassThrough：內容不會加以修改。
- Encode：內容會轉換成 HTML 編碼的字串。例如： &lt;hr&gt; 就會先轉成 &lt;hr&gt;
- Transform：與 PassThrough 同，只是把瀏覽器不支援的 tag 移除掉。（預設）
```xml
<asp:Literal ID="Literal1" runat="server" Text="<i>這是Literal控制項</i>" Mode="Transform"></asp:Literal><br />
<asp:Literal ID="Literal2" runat="server" Text="<i>這是Literal控制項</i>" Mode="Encode"></asp:Literal><br />
<asp:Literal ID="Literal3" runat="server" Text="<i>這是Literal控制項</i>" Mode="PassThrough"></asp:Literal><br />
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjhaHpG90tTtTAVZcKAiE-7rl6YC_DOBGxJOPsweVniqAKUTCFfk0f5rcNAdFhyphenhyphenpD1ombm-L-U2awKfpZKVurp7_1t1aFbJaHCKhTBM617Mb25qFYQzQUwVQuXSXOoK0MtRB51zvac04Uw/s191/ctl-literal-mode.png)

# Table、TableRow、TableCell

[Table](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.table.aspx) 控制項適合需要由程式端建立表格的時候使用，尤其是簡單表格，若大型複雜的表格，可考慮使用 Repeater 或 GridView 等控制項。  
```xml
<asp:Table ID="Table1" runat="server">
<asp:TableHeaderRow>
<asp:TableHeaderCell>H1</asp:TableHeaderCell>
<asp:TableHeaderCell>H2</asp:TableHeaderCell>
<asp:TableHeaderCell>H3</asp:TableHeaderCell>
</asp:TableHeaderRow>
<asp:TableRow>
<asp:TableCell>Cell 1</asp:TableCell>
<asp:TableCell>Cell 2</asp:TableCell>
<asp:TableCell>Cell 3</asp:TableCell>
</asp:TableRow>
</asp:Table>
```
```c#
protected void btnTable_Click(object sender, EventArgs e)
{
Table1.BorderWidth = 1;
for (int row = 0; row < 3; row++)
{
TableRow tr = new TableRow();
for (int column = 0; column < 3; column++)
{
TableCell cell = new TableCell();
cell.Text = string.Format("Row:{0} Cell:{1}", row, column);
cell.BorderWidth = 1;
cell.Font.Name = "標楷體";
tr.Cells.Add(cell);
}
Table1.Rows.Add(tr);
}
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgxbKnsXNpgxdDEo9TgOI6CdZkh0_0w0ChmCwLP7EDsJGy45-DPMM1UPCXfG0EaHbMLy4mMwJ-VxfpnA7OstPbWix0yfPgng4utwlGNMCs7-JHHuUrk1dHut-KIPzFhJMqct_sjHIadZb0/s350/ctl-table.png)

# Image

[Image](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.aspx) 控制項直接繼承 WebControl 類別，而 [ImageButton](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagebutton.aspx) 和 [ImageMap](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagemap.aspx) 則繼承 [Image](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.aspx) 類別。  

使用 [Image](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.aspx) 要知道的是，圖檔資料本身並不是內嵌在網頁之中，而是當 browser 讀到 &lt;img&gt; 標籤時，才會根據 href 屬性，再對網站主機送出另一個 request，以取得圖檔資料。  

- [ImageUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.imageurl.aspx) ：影像的路徑。
- [AlternateText](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.alternatetext.aspx) ：顯示於 [Image](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.aspx) 控制項的替代文字。
- [ImageAlign](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.imagealign.aspx) ：設定 [Image](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.aspx) 控制項相對於 Web 網頁上其他項目的對齊方式。
- [DescriptionUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.descriptionurl.aspx) ：取得或設定影像詳細說明的位置。
- [GenerateEmptyAlternateText](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.generateemptyalternatetext.aspx) ：若值為 true ，則當沒有指定 [AlternateText](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.alternatetext.aspx) 時，會加上 alt="" 。
```xml
<asp:Image ID="Image1" runat="server" 
ImageUrl="http://127.0.0.1:8080/_images/cs/fill_style.jpg"
AlternateText = "fill_style"
DescriptionUrl = "ImageDescription.htm"
GenerateEmptyAlternateText = "true"
/>
```

# ImageButton

The [Image](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.image.aspx) control does not have a [Click](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagebutton.click.aspx) event.   In situations in which a [Click](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagebutton.click.aspx) event is necessary, you can use [ImageButton](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagebutton.aspx) or [ImageMap](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagemap.aspx) instead.  

The [ImageButton](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagebutton.aspx) control has a [Click](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagebutton.click.aspx) and [Command](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.button.command.aspx) event that functions like the Button  control. The second argument of the [Click](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagebutton.click.aspx) event has a data type of **ImageClickEventArgs**, which lets you retrieve the x- and y-coordinates of the user's click.  
```xml
<asp:ImageButton ID="ImageButton1" runat="server" 
ImageUrl="http://127.0.0.1:8080/_images/cs/fill_style.jpg" 
onclick="ImageButton1_Click" />
```
```c#
protected void ImageButton1_Click(object sender, ImageClickEventArgs e)
{
myDebug.ResponseBR("(x,y)=({0},{1})", e.X, e.Y);
}
```

# ImageMap

The [ImageMap](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagemap.aspx) control is used to display a clickable image on a Web page that can be used to PostBack to the Web server when the image is clicked.   This control differs from the [ImageButton](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagebutton.aspx) control in that the [ImageMap](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagemap.aspx) control allows you to define regions or "**hot spots**"   that cause a PostBack, whereas clicking anywhere on an [ImageButton](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagebutton.aspx) causes a PostBack.  

#### HotSpot & HotSpotMode

#### HotSpot

[HotSpot](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.hotspot.aspx) 是用來定義圖片區塊，共有三種型態可以使用，它們都是繼承 [HotSpot](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.hotspot.aspx) 類別。  

- RectangleHotSpot
- CircleHotSpot
- PolygonHotSpot

#### HotSpotMode

[HotSpotMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.imagemap.hotspotmode.aspx) 用來定義當發生 click 事件時，HotSpot 物件的行為。  

- Inactive ：indicates that the [HotSpot](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.hotspot.aspx) does not have any behavior when it is clicked.
- NotSet ：NotSet
- Navigate ：causes the [HotSpot](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.hotspot.aspx) to navigate to a URL when clicked.
- PostBack ：causes the [HotSpot](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.hotspot.aspx) to generate a PostBack to the server when the [HotSpot](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.hotspot.aspx) is clicked.
```xml
<asp:ImageMap ID="ImageMap1" runat="server" 
ImageUrl="http://127.0.0.1:8080/_images/cs/fill_style.jpg" 
HotSpotMode="PostBack" 
onclick="ImageMap1_Click" >
<asp:CircleHotSpot PostBackValue="2" Radius="30" X="180" Y="58" />
<asp:PolygonHotSpot PostBackValue="1" Coordinates="5,5,5,95,45,60,95,95,80,35" />
<asp:RectangleHotSpot PostBackValue="3"  Left="5" Top="5" Right="20" Bottom="20" 
AlternateText = "RectangleHotSpot" 
AccessKey="D" 
HotSpotMode="Navigate" 
Target="_blank" 
NavigateUrl="new.html"
/>
```

# Calendar

The [Calendar](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.calendar.aspx) control allows you to display a calendar on a Web page.   The calendar can be used when asking a user to ***select a given date or series of dates***.  

The [SelectionMode](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.calendar.selectionmode.aspx) property controls this. It can be set to one of the following settings:  

#### 選取模式(SelectionMode)：

- Day ：可以選取單一日期
- DayWeek ：可以選取單一日期或整週。
- DayWeekMonth ：可以選取單一日期、週或整月。
- None ：沒有可選取

#### 常用事件：

- [SelectionChanged](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.calendar.selectionchanged.aspx) ：發生於使用者按一下 [Calendar](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.calendar.aspx) 控制項以選取日、 週或整個月份。
- [VisibleMonthChanged](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.calendar.visiblemonthchanged.aspx) ：發生於使用者按一下 [下一步]，或標題上的往上月份巡覽控制項。
- [DayRender](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.calendar.dayrender.aspx) ：發生於建立每一天的控制項時。
```xml
<asp:Calendar ID="Calendar1" runat="server" Caption="我的日曆" 
onselectionchanged="Calendar1_SelectionChanged" 
SelectionMode="DayWeek"></asp:Calendar>
```
```c#
protected void Calendar1_SelectionChanged(object sender, EventArgs e)
{
SelectedDatesCollection selectedDdays = Calendar1.SelectedDates;
string msg = "";
foreach (DateTime day in selectedDdays)
{
msg += string.Format("{0}/{1} ", day.Month.ToString(), day.Day.ToString());
}
myMessage.Show(this, msg);
Calendar c;
}
```

下面這個例子，我們在 [DayRender](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.calendar.dayrender.aspx) 事件中讀取行事曆資料，並將訊息顯示在 Calerdar 控制項上，  這時候 Calerdar 控制項就變成類別行事曆的樣子，而不單單只能當做日期選擇器。  
```xml
<asp:Calendar ID="Calendar1" runat="server" Caption="我的日曆" 
onSelectionChanged="Calendar1_SelectionChanged"
onDayRender="Calendar1_DayRender"
SelectionMode="DayWeek"></asp:Calendar>
```
```c#
private Hashtable GetSchedule()
{
Hashtable schedule = new Hashtable();
schedule["2012/11/01"] = "My Birthday";
schedule["2012/11/02"] = "My GF's Birthday";
schedule["2012/11/03"] = "My Dog's Birthday";
schedule["2012/12/05"] = "Sleeping Day";
schedule["2012/12/25"] = "X'mas Day";
schedule["2012/12/31"] = "Last day in this year";
return schedule;
}

protected void Calendar1_DayRender(object sender, DayRenderEventArgs e)
{
// Show something in cell
Hashtable schedule = GetSchedule();

if (schedule[e.Day.Date.ToString("yyyy/MM/dd")] != null)
{
Literal lit = new Literal();
lit.Text = "<br />";
e.Cell.Controls.Add(lit);
Label lbl = new Label();
lbl.Text = (string)schedule[e.Day.Date.ToString("yyyy/MM/dd")];
lbl.Font.Size = new FontUnit(FontSize.Small);
e.Cell.Controls.Add(lbl);
}

// 一些常用判斷
bool selected = e.Day.IsSelected;
bool weekend = e.Day.IsWeekend;
bool today = e.Day.IsToday;

// 將假日設成不可點選
if (e.Day.IsWeekend)
{
e.Day.IsSelectable = false;
}
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgfLzJxlhVSLOoW9ffQE8sFAP-c56zXG7XySO4fDHF13t90gUmJLWHS_DPJsD27o-XENNJ6McKodqcvG4ZprBncPlRuGN3tqvZBAxn0g4EAJ-nI0q8x2wx5o6yu1dVsBZxL4vfYf1CRtPw/s850/ctl-calendar.png)

# FileUpload

The [FileUpload](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.fileupload.aspx) control is used to allow a user to select and upload a single file to the server.  The control displays as a text box and Browse button.   The user can either type a file name and path into the text box or click the Browse button and select a file.  

#### FileUpload 常用屬性與方法：

- [SaveAd](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.fileupload.saveas.aspx) ：將上載檔案的內容儲存至指定的路徑中，Web 伺服器上。
- [FileBytes](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.fileupload.filebytes.aspx) ：取得 byte array 格式的檔案內容。叫用前，可以先用 [HasFile](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.fileupload.hasfile.aspx) 屬性判斷 [FileUpload](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.fileupload.aspx) 控制項是否包含一個檔案。
- [FileContent](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.fileupload.filecontent.aspx) ：取得 Stream 格式的檔案內容。
- [FileName](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.fileupload.filename.aspx) ：取得用戶端上檔案的完整名稱。
- [PostedFile](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.fileupload.postedfile.aspx) ：取得 [HttpPostedFile](http://msdn.microsoft.com/zh-tw/library/system.web.httppostedfile.aspx)型別的檔案內容。這個類別也有以下屬性或方法可以取用：
- [ContentType](http://msdn.microsoft.com/zh-tw/library/system.web.httppostedfile.contenttype.aspx) ：取得用戶端所送出檔案的 MIME 內容類型。
- [ContentLength](http://msdn.microsoft.com/zh-tw/library/system.web.httppostedfile.contentlength.aspx) ：取得上載檔案的大小，以位元組為單位。
- [FileName](http://msdn.microsoft.com/zh-tw/library/system.web.httppostedfile.filename.aspx) ：取得用戶端上檔案的完整名稱。
- [SaveAs](http://msdn.microsoft.com/zh-tw/library/system.web.httppostedfile.saveas.aspx) ：儲存上載檔案的內容。

### 範例 1
```xml
<asp:FileUpload ID="Upload1" runat="server" />
<asp:Button ID="btnUpload" runat="server" Text="Upload" 
onclick="btnUpload_Click" />
```
```c#
protected void btnUpload_Click(object sender, EventArgs e)
{
if (Upload1.HasFile) //判斷是否有檔案上傳
{
try
{
//指定檔案儲存路徑
Upload1.SaveAs(MapPath("~/_Uploads/" + Upload1.FileName));

//檢視檔案資訊
string msg = string.Format(@"
File Name = {0} <br>
File Size = {1} <br>
ContentType = {2} <br>
ContentLength = {3}",
Upload1.FileName, 
Upload1.FileBytes.Length, 
Upload1.PostedFile.ContentType,
Upload1.PostedFile.ContentLength);
myDebug.ResponseBR(msg);

//File Name = LINQPad4.zip 
//File Size = 2941604 
//ContentType = application/x-zip-compressed 
//ContentLength = 2941604

}
catch (Exception ex)
{
//若是檔案大小超過限制，是不會引發 Exception 的，因為整個 Page 就停掉了
//該 Error 必須在 Globalasax 處理 Application_Error
//可參考http://jessewth.blogspot.tw/2007/10/aspnet-20.html

myMessage.Show(this, ex.Message);
}
}
else
{
myMessage.Show(this, "File not fount");
}
}
```

### 範例 2

若你同時置放多個 FileUpload 控制項，除了逐一取得每個控制項的上傳檔案外，你也可以直接透過 [Request.Files](http://msdn.microsoft.com/zh-tw/library/system.web.httprequest.files%28v=vs.100%29.aspx) 屬性，取得用戶端所上載的檔案集合。  
```c#
protected void Button1_Click(object sender, EventArgs e)
{
string Location = Server.MapPath("~/Uploads/");

HttpFileCollection Files = Request.Files;
foreach (string FileKey in Files.AllKeys)
{
HttpPostedFile f = Files[FileKey];
f.SaveAs(Location + name);
}
}
```

### 調整可上傳檔案大小

ASP.NET 預設可上傳檔案大小為 預設值為 4096 (4 MB)。  若要改變這個值，可以調整 Web.config 中的 [httpRuntime](http://msdn.microsoft.com/zh-tw/library/e1f13641.aspx) 項目  
```xml
<configuration>
<system.web>
<httpRuntime maxRequestLength="40960" />
</system.web>
</configuration>
```

# Panel

The [Panel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.panel.aspx) control is used as a control container.   It can be useful when you need to group controls and work with them as a single unit.  A common example is the need to display and hide a group of controls.  

#### 常用屬性：

- [BackImageUrl](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.panel.backimageurl.aspx) ：取得或設定面板控制項背景影像的 URL。
- [HorizontalAlign](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.panel.horizontalalign.aspx) ：設定在面板中內容的水平對齊。
- [Wrap](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.textbox.wrap.aspx) ：specifies whether items in the [Panel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.panel.aspx) automatically continue on the next line when a line is longer than the width of the [Panel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.panel.aspx) control.
- [DefaultButton](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.panel.defaultbutton.aspx) ：specifies the button that is clicked when the [Panel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.panel.aspx) control has focus and the user presses Enter on his or her keyboard.
```xml
<asp:Panel ID="Panel5" runat="server" 
BackImageUrl="http://127.0.0.1:8080/_images/cs/fill_style.jpg" 
HorizontalAlign="Left"
GroupingText=" Panel" >
...
</asp:Panel>  
<div class="source_code"><pre class="brush:c#;" title="cs">
protected void btnShow_Click(object sender, EventArgs e)
{
panel_FileUpload.Visible = true;
}

protected void btnHidden_Click(object sender, EventArgs e)
{
panel_FileUpload.Visible = false;
}

protected void btnRightMove_Click(object sender, EventArgs e)
{
Unit left = new Unit(panel_FileUpload.Style["left"]);
left = new Unit((double)left.Value - 10, left.Type);
panel_FileUpload.Style["left"] = left.ToString();
}

protected void BtnLeftMove_Click(object sender, EventArgs e)
{
Unit left = new Unit(panel_FileUpload.Style["left"]);
left = new Unit((double)left.Value + 10, left.Type);
panel_FileUpload.Style["left"] = left.ToString();
}
```

# MultiView & View

Like the [Panel](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.panel.aspx) control, the [MultiView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.multiview.aspx) and [View](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.view.aspx) controls are also container controls;   that is, they are used to group other controls.   Again, this is useful when you want to treat and manage a group of controls as a single unit.   A [MultiView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.multiview.aspx) exists to contain other [View](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.view.aspx) controls.   A [View](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.view.aspx) control must be contained inside a MultiView. The two controls are meant to work together.  

You can use the [ActiveViewIndex](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.multiview.activeviewindex.aspx) property or the [SetActiveView](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.multiview.setactiveview.aspx) method to change the [View](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.view.aspx) programmatically.  If the [ActiveViewIndex](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.multiview.activeviewindex.aspx) is set to **-1**, no [View](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.view.aspx) controls are displayed.  
```xml
<asp:Button ID="btnView1" runat="server" Text="View1" onclick="btnView1_Click" />
<asp:Button ID="btnView2" runat="server" Text="View2" onclick="btnView2_Click" />
<asp:Button ID="btnView3" runat="server" Text="View3" onclick="btnView3_Click" />
<asp:MultiView ID="MultiView1" runat="server" ActiveViewIndex="-1">
<asp:View ID="View1" runat="server">view 1</asp:View>
<asp:View ID="View2" runat="server">view 2</asp:View>
<asp:View ID="View3" runat="server">view 3</asp:View>
</asp:MultiView>
```
```c#
protected void btnView1_Click(object sender, EventArgs e)
{
MultiView1.SetActiveView(View1);
}

protected void btnView2_Click(object sender, EventArgs e)
{
MultiView1.ActiveViewIndex = 1;
}

protected void btnView3_Click(object sender, EventArgs e)
{
MultiView1.SetActiveView((View)FindControl("View3"));
}
```

# Wizard

[Wizard](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.wizard.aspx) 控制項大都用來輸入資料使用，只要搭配 [WizardStep](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.wizardstep.aspx) 控制項，可以將資料輸入調整成一步一步的方式。  在過去，相同功能大都使用不同網頁來承接資料，使用 [Wizard](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.wizard.aspx) 控制項就可以在一個網頁中直接完成。  

[WizardStep](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.wizardstep.aspx) 控制項有一個 [StepType](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.wizardstepbase.steptype.aspx) 屬性，是用來控制巡覽按鈕的類型。  

- Auto：預值設。由 [WizardStep](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.wizardstep.aspx) 物件宣告的先後順序自動判斷。
- Start：此步驟為第一個出現，不會呈現 [上一步] 按鈕。
- Step：該步驟是 [Start] 和 [Finish] 步驟之間的任意步驟。會呈現 [上一步] 和 [下一步] 巡覽按鈕。
- Finish：此步驟是收集資料的最後一個步驟，為巡覽呈現的 [完成] 和 [上一步] 按鈕。
- Complete：此步驟為最後一個出現，不會呈現巡覽按鈕。
```xml
<asp:Wizard ID="Wizard1" runat="server" 
onFinishButtonClick="Wizard1_FinishButtonClick" 
onNextButtonClick="Wizard1_NextButtonClick" 
onPreviousButtonClick="Wizard1_PreviousButtonClick">
<WizardSteps>
<asp:WizardStep ID="WizardStep1" runat="server" 
Title="Step 1" StepType="Auto">
<asp:RadioButtonList ID="RadioButtonList1" runat="server">
<asp:ListItem Text="Yes" Value="Yes"></asp:ListItem>
<asp:ListItem Text="No" Value="No"></asp:ListItem>
</asp:RadioButtonList>
</asp:WizardStep>
<asp:WizardStep ID="WizardStep2" runat="server" Title="Step 2">
<asp:RadioButtonList ID="RadioButtonList2" runat="server">
<asp:ListItem Text="Yes" Value="Yes"></asp:ListItem>
<asp:ListItem Text="No" Value="No"></asp:ListItem>
</asp:RadioButtonList>
</asp:WizardStep>
<asp:WizardStep ID="WizardStep3" runat="server" Title="Step 3">
<asp:RadioButtonList ID="RadioButtonList3" runat="server">
<asp:ListItem Text="Yes" Value="Yes"></asp:ListItem>
<asp:ListItem Text="No" Value="No"></asp:ListItem>
</asp:RadioButtonList>
</asp:WizardStep>
</WizardSteps>
</asp:Wizard>
```
```c#
//輸入檢驗
private bool VerifyStep(WizardNavigationEventArgs e)
{
int iCurrentIndex = e.CurrentStepIndex;
WizardStep step = (WizardStep)Wizard1.WizardSteps[iCurrentIndex];

foreach (Control c in step.Controls)
{
if (c is RadioButtonList)
{
if (((RadioButtonList)c).SelectedIndex == -1)
{
myMessage.ShowAjax(this, "步驟 " + (iCurrentIndex + 1).ToString() + " 尚未完成，請選擇！");
return false;
}
}
}
return true;
}

protected void Wizard1_PreviousButtonClick(object sender, WizardNavigationEventArgs e)
{

}

protected void Wizard1_NextButtonClick(object sender, WizardNavigationEventArgs e)
{
if (!VerifyStep(e))
e.Cancel = true;
}

protected void Wizard1_FinishButtonClick(object sender, WizardNavigationEventArgs e)
{
if (!VerifyStep(e))
e.Cancel = true;
else
{
string a1 = RadioButtonList1.SelectedItem.Text;
string a2 = RadioButtonList2.SelectedItem.Text;
string a3 = RadioButtonList3.SelectedItem.Text;
string msg = string.Format("你的選擇：\r\nStep1:{0} \r\nStep2:{1} \r\nStep3:{2}", a1, a2, a3);
myMessage.ShowAjax(this, msg);
}
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhDpeV77XirzzhZzRPtghTWA4gkiEn6GFQ_LzKkNnZz-Z7bWvau5nuvh4ejasea8Pu_CGZkf7PqhKEf3MLveXMoVHnHLpcBYryzhAsz_vR6oSn_NjJlDEt8UazG5A5_JPZtijZAf0Kp8AM/s350/ctl-wizard1.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg83dJi8RrWECaqbeNP91I4e_c_Cw4pgGO3mJNo-JIgLGQzmttS0SBIqcCWpy0OU-0F6MfA3FKEF4wcvnGluCD4EaTAPVoyCUohJJXB6DXLDuW9vzqepHMknaM9PeTsv8hpCimIymZcQbc/s350/ctl-wizard2.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjWec9Ucd-VuYTzoGe1Ti06gQY04NAZVorSthxeYypRiG_icmmI2sBO6Kl6uaJssIQIDG04CHbm4ncr16ojACp4-E2juOFSqe4AZfSojLlOWGXf3yNBTPfNydgeq3pNNIFxyVnrR2CRHow/s350/ctl-wizard3.png)  ![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhXPKbI8Sx1W3Xp8vohcu6e8pZ4eldBWYbVNVvW296qxxD_BABDmM-sCzplQuWwWwvNC0f7HWRdXei-nnNH5JXAkiqgwFdco-zADrh2h7osu24TqKxT9zxWJ0ZIMKMTXdU6ZZTzTLbHp30/s190/ctl-wizard4.png)

# Xml Control

The [Xml](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xml.aspx) control is used to display the contents of an XML document.   This control is useful if your data is stored in XML and you need to execute an Extensible Stylesheet Language (XSL) transform.  

底下二個屬性都是 [Xml](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xml.aspx) 控制項用來設定 XML 資料來源用的，若同時都有使用，以最後一個參考者為依據。

- [DocumentSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xml.documentsource.aspx) ：將 XML 文件的路徑指定給 [Xml](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xml.aspx) 控制項。
- [DocumentContent](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xml.documentcontent.aspx) ：若 XML 資料存在字串中，可以用這個屬性指定給 [Xml](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xml.aspx) 控制項。

另外 [TransformSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xml.transformsource.aspx) 屬性是用來設定 XSL 轉換樣式表檔案的路徑。如果沒有指定，就會以預設的 XML 文件格式輸出。  
```xml
<asp:Xml ID="Xml1" runat="server"></asp:Xml>
```
```c#
protected void btnShowXML_Click(object sender, EventArgs e)
{
//指定 XML 文件檔案的路徑
Xml1.DocumentSource = "~/_Uploads/CarList.xml";
//指定 XSL 轉換樣式表檔案的路徑
Xml1.TransformSource = "~/_Uploads/CarList.xsl";
}
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhTXD4-bTtfBkBHyDa3jwLUHQxJc9j46dzwz_okYp8-0yEuJVo6ey0uGY-IjZHOSQBkJIY88cNF0nqsikFBUxSpFhJm37eJVoWAKT61sqzqCN9kDuYmnTjMmkig2e2lc5eDUxyRQzGIV8Q/s365/ctl-xml.png)

上面這個例子，因為有指定 [TransformSource](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.xml.transformsource.aspx) ，所以 XML 文件被轉換成一個 [Table](http://msdn.microsoft.com/zh-tw/library/system.web.ui.webcontrols.table.aspx) 格式的資料，如果沒有指定，會直接輸出 XML 文件格式內容。