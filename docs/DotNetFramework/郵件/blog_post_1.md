---
title: 寄送郵件
layout: default
parent: 郵件
nav_order: 1
description: "寄送郵件"
date: 2012-03-27
tags: [DotNetFramework,郵件]
postid: "2252045118609306568"
---
從 .Net2.0 開始， [System.Net.Mail](http://msdn.microsoft.com/zh-tw/library/system.net.mail.aspx) 命名空間提供開發者建立及傳送郵件訊息的相關方法。  

- [MailMessage](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.aspx)：  
這個類別是用來建立電子郵件。支援簡單的純文字、HTML格式的電子郵件訊息、以及不同檢視方式、編碼標準及附加檔。
- [SmtpClient](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.aspx)：  
這個類別是用來寄送電子郵件。支援匿名網路連結、使用者驗證及SSL加密，另外也可以非同步寄送電子郵件，且允許使用者在寄送完成前取消寄送。

## MailMessage 類別

####  [MailMessage](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.aspx) 的建構子
```c#
public MailMessage()
public MailMessage(string from,string to)
public MailMessage(string from,string to,string subject,string body)
public MailMessage(MailAddress from,MailAddress to)
```

####  [MailMessage](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.aspx) 的屬性

- [Subject](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.subject.aspx) ：取得或設定這個電子郵件訊息的主旨。
- [Body](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.body.aspx) ：取得或設定訊息主體。
- [IsBodyHtml](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.isbodyhtml.aspx) ：取得或設定值，指出郵件訊息主體是否採用 Html 格式。
- [From](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.from.aspx) ：寄件者。
- [To](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.to.aspx) ：收件者。
- [CC](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.cc.aspx) ：副本收件者。
- [Bcc](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.bcc.aspx) ：密件副本收件者。
- [BodyEncoding](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.bodyencoding.aspx) ：主體內容所用的編碼方式。
- [SubjectEncoding](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.subjectencoding.aspx) ：主旨內容所用的編碼方式。
- [Attachments](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.attachments.aspx) ：取得附件集合。
- [AlternateViews](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.alternateviews.aspx) ：訊息主體的替代格式。

## SmtpClient 類別

####  [SmtpClient](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.aspx) 的建構子
```c#
public SmtpClient();
public SmtpClient(string host);
public SmtpClient(string host, int port);
```

####  [SmtpClient](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.aspx) 的屬性

- ClientCertificates ：
- [EnableSsl](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.enablessl.aspx) ：
- [Host](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.host.aspx) ：SMTP 交易的主機名稱或 IP 位址。
- [Port](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.port.aspx) ：SMTP 交易的連接埠。
- [Timeout](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.timeout.aspx) ： [Send](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.send.aspx) 呼叫逾時的時間。

####  [SmtpClient](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.aspx) 的方法

- [Send](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.send.aspx) ：傳送電子郵件訊息給 SMTP 伺服器進行傳遞。在傳送訊息時，會封鎖這些方法。
- [OnSendCompleted](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.onsendcompleted.aspx) ：引發 [SendCompleted](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.sendcompleted.aspx) 事件。
- [SendAsync](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.sendasync.aspx) ：傳送電子郵件訊息。 這些方法不會封鎖呼叫執行緒。
- [SendAsyncCancel](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.sendasynccancel.aspx) ：取消傳送電子郵件訊息的非同步作業。

## 範例一：示範建立 MailMessage 的相關方法

- 郵件地址可用 字串 或 [MailAddress](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailaddress) 物件表示。
- [DeliveryNotificationOptions](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.deliverynotificationoptions) ：設定發送結果通知 OnSuccess, OnFailure, Delay, None, Never
- [BodyEncoding](http://msdn.microsoft.com/zh-tw/library/system.net.mail.mailmessage.bodyencoding) ：郵件編碼方式。
```c#
//建立一個MailMessage，同時指明 MailForm, MailTo, Subject, Body
MailMessage mail = new MailMessage ("jane@contoso.com", "ben@contoso.com", "Mail Subject", "Mail Body Test");

//寄件者, 收件者, 主旨, 本文內容
MailMessage mail = new MailMessage();
mail.From = new MailAddress("jane@contoso.com", "DisplayName"); //email, 顯示名稱

mail.To.Add(new MailAddress("ben.test@contoso.com", "ben test"));
mail.To.Add(new MailAddress("ben test<ben.test@contoso.com>"));     //使用角括弧，可使用一個參數同時包含顯示名稱和email 
mail.To.Add("ben test<ben.test@contoso.com>");                      //這樣使用也行。 but..中文..(請看下面注意說明)

mail.Subject = "Mail Subject";
mail.Body = "Mail Body Test";

//副本、密件副本
mail.CC.Add(new MailAddress("ben4@contoso.com", "ben4"));
mail.Bcc.Add(new MailAddress("ben3@contoso.com", "ben3"));

//DeliveryNotificationOptions：通知狀態 OnSuccess, OnFailure, Delay, None, Never
//ReplyTo：設定一個回覆地址
//Priority：設定電子郵件的優先順序

mail.DeliveryNotificationOptions = DeliveryNotificationOptions.OnFailure | DeliveryNotificationOptions.OnSuccess;
mail.ReplyTo = new MailAddress("ben5@contoso.com", "ben5");
mail.Priority = MailPriority.High;

//html編碼  
mail.Body = "<html><body><h1>Picture</h1><br><img src=\"cid:Pic1\"></body></html>";
mail.IsBodyHtml = true;
mail.BodyEncoding = Encoding.UTF8; 
```

#### 注意:若顯示名稱含中文時

當使用 mail.To.Add("ben test&lt;ben.test@contoso.com&gt;"); 這個方式加入email時，  
若顯示名稱為中文時會產生錯誤，例如：mail.To.Add("測試&lt;ben.test@contoso.com&gt;"); 會跳出"指定字串不在電子郵件地址的必要表單中"的錯誤訊息.  
只能使用這個方式 mail.To.Add(new MailAddress("測試&lt;ben.test@contoso.com&gt;"))   
或將顯示名稱放在第二個參數 mail.To.Add(new MailAddress("ben.test@contoso.com", "測試"));

## 範例二：使用 SmtpClient 發送 MailMessage 的相關方法

- [NetworkCredential](http://msdn.microsoft.com/zh-tw/library/system.net.networkcredential) ：這個類別可用來提供密碼架構的驗證 (Authentication) 機制的認證。
- [SmtpClient.Credentials](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.credentials) ：這個屬性用來取得或設定寄件者的認證資料。
- [SmtpClient.EnableSsl](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.enablessl) ：是否啟用 SSL。
```c#
SmtpClient client = new SmtpClient("smpt.gmail.com");   //建立 SMTP ，並指定 SMTP Server
client.Credentials = new System.Net.NetworkCredential("user", "pwd");   //驗證寄件者
client.EnableSsl = true;                                                //是否啟用 SSL
client.Port = 25;                                                       //指定 SMTP 交易連接埠
SendMail(client, mail);

private void SendMail(SmtpClient client, MailMessage mail)
{
try
{
client.Send(mail);  //寄送郵件
MessageBox.Show("email send");
}
//Handle Mail Exceptions
catch (InvalidOperationException exInvalidOperation)            //沒有指定 SMTP Server
{
MessageBox.Show(exInvalidOperation.Message.ToString());   
}
catch (SmtpFailedRecipientException exSmtpFailedRecipient)      //指定錯誤的收件者
{
MessageBox.Show(exSmtpFailedRecipient.Message.ToString());
}
catch (SmtpException exSmtp)                                    //找不到 SMTP 或 其他的例外錯誤
{
MessageBox.Show(exSmtp.Message.ToString());
}
catch (Exception ex)
{
MessageBox.Show(ex.Message.ToString());
}
}
```

## 範例三：附加檔案

呼叫 [MailMessage.Attachments](http://msdn.microsoft.com/zh-tw/library/system.net.mail.attachment.aspx) 可取得附件的集合 AttachmentCollection 物件。  其組成項目為 [Attachment](http://msdn.microsoft.com/zh-tw/library/system.net.mail.attachment.aspx) 物件，可以透過該集合的 Add 方法加入新的項目。  
```c#
MailMessage mailmessage = new MailMessage();

mailmessage.From = new MailAddress(txtMailFrom.Text);
mailmessage.To.Add(new MailAddress(txtMailTo.Text));
mailmessage.Subject = txtSubject.Text;
mailmessage.Body = txtBody.Text;
mailmessage.IsBodyHtml = cbHTML.Checked;

SmtpClient smtpclient = new SmtpClient(cbSMTP.Text);
smtpclient.Credentials = new System.Net.NetworkCredential(txtAccount.Text, txtPassword.Text);
smtpclient.EnableSsl = cbSSL.Checked;
smtpclient.Port = int.Parse(txtSmtpPort.Text);

string sFilePath = @"D:\MCTS\MCTS2011\Practice\Practice\bin\Debug\cloud.jpg";
Attachment attachment = new Attachment(sFilePath);
mailmessage.Attachments.Add(attachment);

SendMail(smtpclient, mailmessage);
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEioTq33m2VzeXKYf2nthYmkIvbi5Rr3Z9m0EbAYxhSL_kOni5Ul7N3iZTUfZZRyy7gyMXVIPY7ijBvxhrZxwkKy-M9fEHqp3xyqepCbPXY0jnAE4N1rkN43CoxyryiGJNZSQ2VdEvQ4KX4/s383/mail03.png)

Attachment 有好幾個建構子，其中一個包含一個 stream ，也就是可以將 stream 變成附件形式寄出。
```c#
// create SmtpClient
SmtpClient smtpclient = new SmtpClient(cbSMTP.Text);
smtpclient.Credentials = new System.Net.NetworkCredential(txtAccount.Text, txtPassword.Text);
smtpclient.EnableSsl = cbSSL.Checked;
smtpclient.Port = int.Parse(txtSmtpPort.Text);

// create Attachment
string sContent = "將資料流轉成附加檔案";
byte[] byteArray = Encoding.UTF8.GetBytes(sContent);
MemoryStream stream = new MemoryStream(byteArray);
Attachment attm = new Attachment(stream, "myfile.txt", MediaTypeNames.Text.Plain);

// create MailMessage
MailMessage mailmessage = new MailMessage();
mailmessage.From = new MailAddress(txtMailFrom.Text);
mailmessage.To.Add(new MailAddress(txtMailTo.Text));
mailmessage.Subject = txtSubject.Text;
mailmessage.Attachments.Add(attm);

// Send
SendMail(smtpclient, mailmessage);
```

## 範例四：指定不同格式的電子郵件

因應某些收件者無法顯示 HTML 內容，也可以同時提供純文字和 HTML 版本的郵件內容。

- [AlternateView](http://msdn.microsoft.com/zh-tw/library/system.net.mail.alternateview.aspx) 類別：用來建立不同格式的郵件複本。
- [MediaTypeNames](http://msdn.microsoft.com/zh-tw/library/system.net.mime.mediatypenames.aspx) 類別：指定郵件複本的媒體類型資訊。{Html,Plain,RichText,XML...}
```c#
// 設定一個HTML格式的 AlternateView 
string htmlBody = "<body><html>This message support <b>HTML</b></html></body>";
AlternateView viewHtml = AlternateView.CreateAlternateViewFromString(htmlBody, null, MediaTypeNames.Text.Html);
avHtml.LinkedResources.Add(pic1);

// 設定一個文字格式的 AlternateView
string textBody = "This message doesn't support HTML";
AlternateView viewPlain = AlternateView.CreateAlternateViewFromString(textBody, null, MediaTypeNames.Text.Plain);

// 使用 alternate views 加入訊息主體，替代 MailMessage.Body
mailmessage.AlternateViews.Add(viewHtml);
mailmessage.AlternateViews.Add(viewPlain);

SendMail(smtpclient, mailmessage);
```

## 範例五：內嵌檔案(1)

透過 [LinkedResource](http://msdn.microsoft.com/zh-tw/library/system.net.mail.linkedresource.aspx) 類別，可用來建立資源連結物件，並將該物件加入 Html 格式的 [AlternateView](http://msdn.microsoft.com/zh-tw/library/system.net.mail.alternateview.aspx) 物件中。

給定每個 [LinkedResource](http://msdn.microsoft.com/zh-tw/library/system.net.mail.linkedresource.aspx) 一個唯一的 ContentId ，再將這個 ContentId 套用到 &lt;img＞ 標籤的 cid 屬性的值。
```c#
// 設定連結資源檔
string sFilePath = @"D:\MCTS\MCTS2011\Practice\PracticeMCTS\bin\Debug\cloud.jpg";
LinkedResource resource1 = new LinkedResource(sFilePath, MediaTypeNames.Image.Jpeg);
resource1.ContentId = "resource_id_1";      //定義這個資源的 ContentID。

// 設定一個HTML格式的本文內容，並加入連結資源檔
string htmlBody = @"
<html><body>

<h1>資源內嵌</h1>
<img src=""cid:resource_id_1"" />

</body></html>";

AlternateView viewHtml = AlternateView.CreateAlternateViewFromString(htmlBody, null, MediaTypeNames.Text.Html);
viewHtml.LinkedResources.Add(resource1);

// 使用 alternate views 替代 MailMessage.Body
mailmessage.AlternateViews.Add(viewHtml);

SendMail(smtpclient, mailmessage);
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgH3iGWsBPqE0aor5MbFwGN0GF75GR4o3mhifMzueGWBGSjzGRU0srdxeCOomK8c6Ga4PJdIKaX0Xqiaj6FmJOmxzQE3xOaGZOtP-fbVJQu-5lFm2RYu-olMKVNJq7duEIPM8kog_Dy3O0/s695/mail02.png)

## 範例六：內嵌檔案(2)

&lt;img&gt; 標籤中的 src 屬性，其值要等於 Attachment.Name 。
```c#
// 設定附件檔
string sFilePath2 = @"D:\MCTS\MCTS2011\Practice\PracticeMCTS\bin\Debug\cloud.jpg";
Attachment attm = new Attachment(sFilePath2);
attm.Name = Path.GetFileName(sFilePath2);
attm.NameEncoding = Encoding.GetEncoding("utf-8");
attm.TransferEncoding = TransferEncoding.Base64;

// 設定該附件為一個內嵌附件(Inline Attachment)
attm.ContentDisposition.Inline = true;                                      //附件中的內容是以內嵌方式呈現為電子郵件主體的一部分，則為 true
attm.ContentDisposition.DispositionType = DispositionTypeNames.Inline;      //附件類型 => 將附件顯示為電子郵件訊息主體的一部分
mailmessage.Attachments.Add(attm);

// 設定一個HTML格式的本文內容，並加入附件檔 
// img 標籤中的 src 屬性，其值要等於 Attachment.Name
string htmlBody = @"
<html><body>

<h3>附件內嵌(Inline Attachment)</h3>
<img src=""cloud.jpg"" />                       

</body></html>";
mailmessage.Body = htmlBody;

SendMail(smtpclient, mailmessage);
```

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhLfflil2VA3Q5pLyNW4Fu5v2ibR_kefoC0u1KCO_HdQG7G-iD0ElAoxq9XgCht_WP4OHHkc89AODStdoEWAZeNq8FuIvI7d-iH4zTtFjwHaq-EtB2K4McBFrB1hOF4CdaGL1WOP9kk_sQ/s648/mail01.png)

## 範例七：非同步傳送電子郵件

- [SmtpClient.SendAsync](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.sendasync) ：非同步寄送。
- [SmtpClient.SendAsyncCancel](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.sendasynccancel) ：取消非同步寄送
- [SmtpClient.SendCompleted](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.sendcompleted) ：完成非同步寄送事件。寄送完成或取消寄送都會引發 [SendCompleted](http://msdn.microsoft.com/zh-tw/library/system.net.mail.smtpclient.sendcompleted) 事件。
```c#
SmtpClient sc ;
private void btnSendAsync_Click(object sender, EventArgs e)
{
MailMessage mail = new MailMessage();

mail.From = new MailAddress(txtMailFrom.Text);
mail.To.Add(new MailAddress(txtMailTo.Text));
mail.Subject = txtSubject.Text;
mail.Body = txtBody.Text;
mail.IsBodyHtml = cbHTML.Checked;

sc = new SmtpClient(cbSMTP.Text);
sc.Credentials = new System.Net.NetworkCredential(txtAccount.Text, txtPassword.Text);
sc.EnableSsl = cbSSL.Checked;
sc.Port = int.Parse(txtSmtpPort.Text);

string sFilePath = @"D:\MCTS\MCTS2011\Practice\Practice\bin\Debug\cloud.jpg";
Attachment attm = new Attachment(sFilePath);
mail.Attachments.Add(attm);

sc.SendCompleted += new SendCompletedEventHandler(SendCompleted);
sc.SendAsync(mail, null);
}

private void btnCancel_Click(object sender, EventArgs e)
{
sc.SendAsyncCancel();
}

private void SendCompleted(object sender, AsyncCompletedEventArgs e)
{
if (e.Cancelled)
MessageBox.Show("Message Cancelled");
else
{
if (e.Error != null)
MessageBox.Show("Error : " + e.Error.ToString());
else
MessageBox.Show("Send Completed");
}
}
```