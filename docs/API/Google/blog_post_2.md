---
title: Google Drive API
layout: default
parent: Google
nav_order: 2
description: "Google Drive API"
date: 2015-04-08
tags: [API,Google]
postid: "1126803153443600341"
---
##### Google Drive 的授權方式

要透過 Google API 存取 Google Drive ，不管存取的檔案權限設定為何，你的 App 都必須先獲得使用者的「開放授權」才可以讀取。  目前 Google Drive API 支援 [OAuth 2.0](https://developers.google.com/accounts/docs/OAuth2) 協定。  

##### Google Drive 的物件類別

在 Google Drive 中，不管是目錄或者檔案，都是使用 [File](https://developers.google.com/drive/v2/reference/files) （命名空間：Google.Apis.Drive.v2.Data）類別表示，所以針對檔案或目錄進行查詢、刪除、修改、權限設定等操作，都是使用相同的方式，所以底下的範例就不重覆說明。  

# 類別庫下載

透過 NuGet 下載以下類別庫套件：

- Google APIs Client Library
- Google APIs Auth Client Library
- Google.API.Drive.V2 Client Library

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEilu3G5a-R2gH7KauWdf9TH4Tilpl0QVj8IgVPPY53F5yRQnmiwKDWawubAwtXvrHSb2uJpzy1Y2Y6Eyz-TX91Q6tmeq5MlqqH9l0pInrPioG3ZNCSgLe1ewVW2XEQhvVU_D3uB0nLVA_s/s790/google-drive-01.png)

# 存取 Google Drive

## 如何取得檔案清單

##### 檔案與目錄的差別

在 Google Drive 檔案架構中，不管目錄或檔案都使用 File 物件表示。  唯一的差別是，若是目錄，它的 MimeType 屬性值必定等於 "application/vnd.google-apps.folder"。  

##### 父與子

在 File 的屬性中，你可以透過 Parents 屬性，取得其父層節點物件（ParentReference）。  如果檔案位於根節點下之下，那麼它的父層結點中的 IsRoot 屬性會等於 true 。  

##### 搜尋

要搜尋檔案，跟取得檔案清單都是使用相同的方法，差別在於搜尋條件的設定。  當執行 List() 方法時，若沒有設定任何條件，那麼回傳的清單也會包含垃圾筒中的檔案。  若要過濾這些內容，可以在 ListRequest 物件中設定 Q 屬性，用以加入篩選條件。  相關的篩選條件設定，可參考 [https://developers.google.com/drive/web/search-parameters](https://developers.google.com/drive/web/search-parameters) 。  

- [List()](https://developers.google.com/drive/v2/reference/files/list)：這個方法可以取得使用者的檔案清單。
```c#
/// <summary>
/// 取得檔案清單
/// Documentation List: https://developers.google.com/drive/v2/reference/files/list 
/// Documentation Search: https://developers.google.com/drive/web/search-parameters 
/// </summary>
/// <param name="searchPattern">搜尋條件</param>
/// <returns></returns>
public List<GData.File> List(string searchPattern = "\*")
{
List<GData.File> result = new List<GData.File>();
try
{
FilesResource.ListRequest request = service.Files.List();
request.MaxResults = 1000;
if (searchPattern != "\*")
{
request.Q = searchPattern;
}
GData.FileList filesFeed = request.Execute();

// 判斷資料是否回傳結束
while (filesFeed.Items != null)
{
// add to the list
result.AddRange(filesFeed.Items);

if (filesFeed.NextPageToken != null)
{
// 若有下一頁，繼續
request.PageToken = filesFeed.NextPageToken;

// 執行 NextPage 的 request
filesFeed = request.Execute();
}
else
{
// 若沒有下一頁，結束
break;
}
}
}
catch (Exception ex)
{
throw;
}
return result;
}
```

底下示範幾個篩選條件
```c#
public List<GData.File> GetList(string folderid = "")
{
string searchPattern = "trashed=false";
searchPattern += string.Format(" and '{0}' in owners", owner);

if (folderid != "")
searchPattern += string.Format(" and '{0}' in parents", folderid);
List<GData.File> result = List(searchPattern);
return result;
}
```
```c#
public List<GData.File> GetFolderList(string folderid = "")
{
string searchPattern = "trashed=false";
searchPattern += " and mimeType='application/vnd.google-apps.folder'";
searchPattern += string.Format(" and '{0}' in owners", owner);

if (folderid!="")
searchPattern += string.Format(" and '{0}' in parents", folderid);
List<GData.File> result = List(searchPattern);
return result;
}
```
```c#
//找出近一個月有修改過的檔案
DateTime date = DateTime.Now.AddMonths(-1);
string date_utc = date.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss");

string search = "trashed=false";
search += string.Format(" and '{0}' in owners", gstrUserEmail);
search += " and modifiedDate > '" + date_utc + "'";

List<GData.File>  files= service.List(search);
```

## 如何建立目錄

若是目錄，其 MimeType 屬性值必定等於 "application/vnd.google-apps.folder"，才可以當成其他檔案的父層成員。  
```c#
/// <summary>
/// 建立一個 File 物件
/// </summary>
/// <param name="parentid">父層目錄ID</param>
/// <param name="title">File's Name</param>
/// <param name="description">File's Description</param>
/// <param name="mimeType">File's MimeType</param>
/// <returns></returns>
private GData.File GenFileObject(string parentid , string title, string description="", string mimeType = "")
{
try
{
GData.File body = new GData.File();
body.Title = title;
body.Description = description;
body.MimeType = mimeType;
if (parentid != "")
body.Parents = new List<GData.ParentReference>() { new GData.ParentReference() { Id = parentid } };

return body;
}
catch (Exception ex)
{
throw;
}
}

public GData.File CreateFolder(string parentid , string title, string description = "")
{
try
{
// setting file
string mimeType = "application/vnd.google-apps.folder";
GData.File body = GenFileObject(parentid, title, description, mimeType);
FilesResource.InsertRequest request = service.Files.Insert(body);
GData.File folder = request.Execute();
return folder;
}
catch
{
throw;
}
}
```

## 如何上傳檔案

要上傳檔案到 Google Drive ，都必須指定一個 mime-type ，底下有個小函式，可以自動取得預設的 mime-type ，方便上傳資料時使用。  另外 Google Drive 同一個檔案上傳二次，即使檔案名稱都相同，它還是會認定為二個不同的檔案，擁有不同的檔案 ID.  如果要更新檔案內容，請看稍後的文章。  
```c#
/// <summary>
/// 上傳檔案
/// </summary>
/// <param name="fileSource">local 端的檔案路徑</param>
/// <param name="parentid">要上傳到 google drive 上的 folder id</param>
/// <param name="mimeType">指定 mimetype，若沒指定由系統看動判斷</param>
/// <returns></returns>
public GData.File Upload(string fileSource, string parentid = "", string mimeType = "")
{
FileInfo file = new FileInfo(fileSource);
if (file.Exists)
{
// setting file
if (mimeType=="")
mimeType=(mimeType == "" ? GetMimeType(fileSource) : mimeType);
GData.File body = GenFileObject(parentid, file.Name, file.Name, mimeType);

// read file to stream
byte[] byteArray = System.IO.File.ReadAllBytes(fileSource);
System.IO.MemoryStream stream = new System.IO.MemoryStream(byteArray);

try
{
FilesResource.InsertMediaUpload request = service.Files.Insert(body, stream, body.MimeType);
request.Upload();
return request.ResponseBody;
}
catch (Exception ex)
{
throw;
}
}
else
{
throw (new Exception("檔案不存在，無法上傳。"));
}
}

/// <summary>
/// 自動判斷檔案的 MimeType
/// </summary>
/// <param name="fileName"></param>
/// <returns></returns>
private string GetMimeType(string fileName)
{
string mimeType = "application/unknown";
string ext = System.IO.Path.GetExtension(fileName).ToLower();
Microsoft.Win32.RegistryKey regKey = Microsoft.Win32.Registry.ClassesRoot.OpenSubKey(ext);
if (regKey != null && regKey.GetValue("Content Type") != null)
mimeType = regKey.GetValue("Content Type").ToString();
return mimeType;
}
```

## 如何下載檔案

Google Drive 中，有一些檔案是由 Google 的其他服務自動建立的，例如 Google Map 或 Doc 文件等等，所以並不是所有檔案都可以下載。  不過，只要是使用者上傳的檔案都沒有問題，你都可以在 File 物件中找到 DownloadUrl 屬性，這個值就是用來下載檔案使用的。  
```c#
/// <summary>
/// 下載檔案
/// </summary>
/// <param name="DownloadUrl">要下載的檔案的 url </param>
/// <returns></returns>
public byte[] Download(string DownloadUrl)
{
try
{
Task<byte[]> request = service.HttpClient.GetByteArrayAsync(DownloadUrl);
byte[] result = request.Result;
return result;
}
catch (Exception ex)
{
throw;
}
}
```

## 如何更新檔案

這裡的「更新」指的是重新上傳檔案內容，這樣的更新不會變動檔案 ID ，可以避免因為檔案異動而導至一些舊有的連結失效。  
```c#
/// <summary>
/// 重新上傳檔案
/// </summary>
/// <param name="fileid"></param>
/// <param name="filepath"></param>
/// <param name="parentid"></param>
/// <param name="mimeType"></param>
/// <returns></returns>
public GData.File ReUpload(string fileid, string filepath, string parentid = "", string mimeType = "")
{
FileInfo file = new FileInfo(filepath);
if (file.Exists)
{
// setting file
if (mimeType=="")
mimeType=(mimeType == "" ? GetMimeType(filepath) : mimeType);
GData.File body = GenFileObject(parentid, file.Name, file.Name, mimeType);

// read file to stream
byte[] byteArray = System.IO.File.ReadAllBytes(filepath);
System.IO.MemoryStream stream = new System.IO.MemoryStream(byteArray);

try
{
FilesResource.UpdateMediaUpload request = service.Files.Update(body, fileid, stream, body.MimeType);
request.Upload();
return request.ResponseBody;
}
catch (Exception ex)
{
throw;
}
}
else
{
throw (new Exception("檔案不存在，無法上傳。"));
}
}
```

## 如何修改、刪除檔案

下面幾個方法可以用來修改刪除檔案或目錄。  

- [Patch](https://developers.google.com/drive/v2/reference/files/patch)：如果你只是想「修改」檔案的 MetaData ，可以使用這個方法。
- [Delete](https://developers.google.com/drive/v2/reference/files/delete)：刪除檔案。
- [Trash](https://developers.google.com/drive/v2/reference/files/trash)：將檔案移除到垃圾筒資料夾。
- [Untrash](https://developers.google.com/drive/v2/reference/files/untrash)：將檔案由垃圾筒復原。
```c#
/// <summary>
/// 修改檔案或目錄的 title 和 description 欄位資訊，若不修改，可傳入 null 值。
/// </summary>
/// <param name="fileid"></param>
/// <param name="newTitle"></param>
/// <param name="newDescription"></param>
/// <returns></returns>
public GData.File Patch(string fileid, string newTitle = null, string newDescription = null)
{
try
{
GData.File file = new GData.File();
if (newTitle != null)
{
file.Title = newTitle;
}
if (newDescription != null)
{
file.Description = newDescription;
}

FilesResource.PatchRequest request = service.Files.Patch(file, fileid);
return request.Execute();
}
catch (Exception ex)
{
Console.WriteLine(ex.Message);
return null;
}
}

/// <summary>
/// 刪除檔案或目錄
/// </summary>
/// <param name="fileid"></param>
/// <returns></returns>
public void Delete(string fileid)
{
try
{
service.Files.Delete(fileid).Execute();
}
catch (Exception ex)
{
throw;
}
}

/// <summary>
/// 將檔案或目錄移至垃圾筒
/// </summary>
/// <param name="fileid"></param>
/// <returns></returns>
public GData.File Trash(string fileid)
{
try
{
return service.Files.Trash(fileid).Execute();
}
catch (Exception ex)
{
throw;
}
}

/// <summary>
/// 將檔案或目錄由垃圾筒復原
/// </summary>
/// <param name="fileid"></param>
/// <returns></returns>
public GData.File Untrash(string fileid)
{
try
{
return service.Files.Untrash(fileid).Execute();
}
catch (Exception ex)
{
throw;
}
}
```

# 如何設定 Google Drive 的分享權限

在 Google Drive 中，不管檔案或是目錄，都是使用 ACL (Access Control List) 來管控分享設定，它可以針對不同類型(type)的人，設定不同的操作權限(role)。  例如：針對（anyone）給予（readder）權限；或者針對（特定使用者）給予（writer）權限。  詳細介紹可參考 [Drive REST API : Share Files](https://developers.google.com/drive/web/manage-sharing) 文件。  

底下示範幾個常用到的設定：  

## 如何將檔案設定為「公開的」
```c#
public void SetPermission_Public(string fileId)
{
GData.Permission permission = new GData.Permission();
permission.Role = "reader";
permission.Type = "anyone";
permission.Value = "";
driveService.Permissions.Insert(permission, fileId).Execute();
｝
```

## 如何將檔案設定為「僅限知道連結的使用者」

原則上，若要將檔案設定為「僅限知道連結的使用者」，那麼在設定 permission 時，只要多設定一道屬性 permission.WithLink = true　即可。  不過，因為 Permissions 本身是一個 List 物件，也就是你可以一次設定多個權限值，如果你同時針對 anyone 給予 public 和 withlink ，那麼這個檔案還是會以 public 為主，因為它包含的範圍較大。  所以，下面範例中，我們先去除 anyone 的權限設定，再加入新的 permission 。  
```c#
public void SetPermission_PublicWithLink(string fileId)
{
GData.Permission permission = new GData.Permission();
permission.Role = "reader";
permission.Type = "anyone";
permission.Value = "";
permission.WithLink = true;

RemovePermission(fileId, "anyone");
driveService.Permissions.Insert(permission, fileId).Execute();
}
private string RemovePermission(string fileId, string permissionId)
{
string result = "";
try
{
// 刪除 Permissions.items 集合中，id = permissionId 的項目。
result = driveService.Permissions.Delete(fileId, permissionId).Execute();
}
catch (Exception ex)
{
result = ex.Message;
}
return result;
}
```

## 如何將檔案設定為「私有的」

要將檔案設定為「私有的」，簡單來說，就是移除 anyone 和 anyoneLink 的權限設定。
```c#
public void SetPermission_Private(string fileId)
{
PermissionsResource.ListRequest request = driveService.Permissions.List(fileId);
GData.PermissionList list = request.Execute();

RemovePermission(fileId, "anyone");
RemovePermission(fileId, "anyoneWithLink");
}
```

# 如何直接在 browser 中瀏覽 Google Drive 中的檔案

在 Google.Apis.Drive.v2.Data 的 File 物件中，有幾個與**連結**有關的屬性，可用來取得該檔案的相關資訊，例如：  

- **AlternateLink**：A link for opening the file in a relevant Google editor or viewer.
- **WebContentLink**：A link for downloading the content of the file in a browser using cookie based authentication. In cases where the content is shared publicly, the content can be downloaded without any credentials.
- **SelfLink**：A link back to this file.
- 

上述列表中，WebContentLink 就是一個可以讓你透過 browser ，直接下載該檔案用的連結。  不過，如果你想要利用 Google Drive 來當做圖床或是網頁主機的話，那麼你必須使用底下格式的連結，才可以直接在 browser 中檢視：  
www.googledrive.com/host/{文件ID}  
例如，你有底下二個檔案，其中 helloworld1.htm 的 ID 為 0BwEj7exTMd2DMDd4X1JaUlFqR28 ，helloworld2.htm 的 ID 為 0BwEj7exTMd2DX21fOG1uLU9DWVU 。  
/Webs/W001/helloworld1.htm
/Webs/W002/helloworld2.htm  
你就可以使用底下連結，直接在 browser 中檢視它們。
www.googledrive.com/host/0BwEj7exTMd2DMDd4X1JaUlFqR28
www.googledrive.com/host/0BwEj7exTMd2DX21fOG1uLU9DWVU  
上述的文件ID，也可以是一個目錄 ID ，而且透過目錄 ID ，在該目錄下的子目錄或檔案的連結，都可以使用檔案的 Title 屬性直接串接取得。  例如，若 Webs 這個目錄的 ID 為 0BwEj7exTMd2DQ2wxRmdWSGpDV0U ，你就可以使用以下連結直接瀏灠：
www.googledrive.com/host/0BwEj7exTMd2DQ2wxRmdWSGpDV0U/W001/helloworld1.htm
www.googledrive.com/host/0BwEj7exTMd2DQ2wxRmdWSGpDV0U/W001/helloworld2.htm  
也就是說，你只要知道最上層子目錄的ID（必須設定公開屬性），那麼在該子目錄下的所有檔案，你就不用理會他們的ID為何，都可以輕鬆的由 title 名稱取得瀏覽連結。  所以，你只要注意底下二點，就可以將 Google 雲端硬碟，當做是網頁空間來使用。  

- 目錄或檔案都要設定為公開屬性。
- 不要使用中文檔名。
## 參考資料  

- [Integrate your app with Google Drive](https://developers.google.com/drive/web/)
- [Google Drive API C#](http://www.daimto.com/google-drive-api-c/)
- [使用雲端硬碟代管網頁](https://support.google.com/drive/answer/2881970?hl=zh-Hant)