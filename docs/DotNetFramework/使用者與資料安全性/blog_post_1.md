---
title: 資料的加密與解密(4)-數位簽章
layout: default
parent: 使用者與資料安全性
nav_order: 1
description: "資料的加密與解密(4)-數位簽章"
date: 2012-05-10
tags: [DotNetFramework,使用者與資料安全性]
postid: "5243832598112844647"
---
當我們去銀行提款時，通常都需要蓋個章，用以保證這是你本人送交的東西。  在網路上傳送資料給對方，為了確保這資料是由你本人送出的，所以也需要在資料中蓋個章，這就是「數位簽章」的概念。  數位簽章相當於個人之印鑑，憑證相當於此印鑑之印鑑證明。電子化網路環境下，可利用憑證來驗證個人之數位簽章，以確認個人身分。   

# 四、檔案簽章

## 什麼是數位簽章

數位簽章（Digital Signature）是一種針對資料進行加密的技術，主要目的在防止非法第三者冒名傳送或竄改傳輸中的資料，以確保資料之來源辨識性與完整性。  

#### 簽章憑證

若要使用數位簽章，必須要有一個可用來識別身份用的「簽章憑證」。  「簽章憑證」由憑證授權單位發行，有效期限通常是一年，到期時，必須透過更新或取得新的簽章憑證。   憑證內容包括：憑證序號、用戶名稱、公開金鑰、憑證有效期限及憑證管理中心之數位簽章等。  

#### 憑證授權單位 (CA，Certificate Authority)

憑證授權單位是類似公證處的單位。該單位會發出數位憑證、簽章憑證以驗證有效性，以及追蹤哪些憑證已遭撤銷或已過期。  

由「憑證授權單位」所發出的「簽章憑證」，就像是公鑰的保證書，可以用來保證此公開金鑰是某人所擁有。  

PS.  
我們也可以自行架設 CA Server，發給憑證，但接收端將無法驗證憑證的真實性，就只能以手動的方式選擇是否信任該憑證。   
電腦中存放個人憑證的地方  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiRI7vE78QLmwfF7Kx3rZrLy6xk5uSJQ3greuAYQhr6Xn2Auf-Q-RSDyA8T6NcjrLx3mFhFjmdPLF-tqIcVVQaNZJWHrqqk64bxP-JPW9WV9NExL1WLY_qWirnfOAnRTCJxSbFGTeJml_w/s850/personal_ca.png)  

#### 數位簽章

所以數位簽章指將電子文件以數學演算法或其他方式運算為一定長度之數位資料，以簽署人之私密金鑰對其加密，形成電子簽章，並得以公開金鑰加以驗證者。  例如，利用雜湊演算法可以取得各個資料的 hash 值，再使用私密金鑰加密，即形成數位簽章。

## 簽署與驗證

其實數位簽章流程使用的就是[公開金鑰加密(非對稱加密)](http://zh.wikipedia.org/wiki/%E5%85%AC%E9%92%A5%E5%8A%A0%E5%AF%86)的技術。  也就是每個發送端都有一對「鑰匙」，一個是只有他本人知道的（私鑰），另一個公開的（公鑰）。  當發送資料的時候，會先使用自已的私密金鑰進行加密；接收端收到資料的時候，會使用發送端的公開金鑰進行解密。  只不過數位簽章流程必須額外透過「憑證授權單位」來核發「簽章憑證」，讓未來的接收資料端，能夠有個公證的查詢單位，證明該簽章的確是某人所擁有。  

所以數位簽章的傳輸過程可分為簽署(sign)與驗證(verify)二個階段。  它會定義兩種相對的運算，一個是用來簽署資料，一個是用來驗證資料。  其過程如下圖所示：  

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgpS8OpVIVbvkut_bKJVjKQ3xJdFxagrRyVtSrt3YfHqMk7beeWEilwFS6I4scPfvknzKO6hySgjLycKGHRsfHPR369CHHYxWrEhWubxnGgtKAVL7-CRUTRZKVTYDnkejiddbrlv-ufH4M/s850/digital_signature.png)

### 簽署資料

- Ａ以［公鑰Ａ］向信認的ＣＡ單位，申請憑證，之後ＣＡ核發一個加過密的憑證。
- Ａ用自已的私鑰將資料的hash值加密，做成數位簽章。
- Ａ將[憑證][公鑰Ａ][數位簽章][資料]一同傳送給Ｂ。

其中，當資料傳送前，經由雜湊運算所得到的雜湊值，通常稱為**訊息摘要**（Message Digest），  也就是數位簽章就是以**私密金鑰**對**訊息摘要**進行加密運算後的結果。

### 驗證資料

當接收端取得訊息後，先使用公開金鑰對數位簽章作解密，並取得裡面的雜湊值。  再取出訊息中的本文資料，並使用相同雜湊運算，得到一個新的雜湊值。  最後比對二個訊息摘要，即可判斷訊息之正確性。  

- Ｂ拿收到的憑證向ＣＡ詢問送出憑證者的公鑰，以確認收到的公鑰確實為送出憑證者所擁有。
- Ｂ以收到公鑰解開數位簽章，以取得裡面的hash值。
- Ｂ將資料雜湊，以得到一個新的hash值，與上一步驟驗證是否一致。若一致表示資料沒被篡改。
-

## 數位簽章類別

如同非對稱加密演算法，.Net Framkwork 提供 [DSACryptoServiceProvider](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.dsacryptoserviceprovider.aspx) 和 [RSACryptoServiceProvider](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsacryptoserviceprovider.aspx) 兩種類別以產生數位簽章。並實作用於數位簽章的四種方法：

- [SignData](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.dsacryptoserviceprovider.signdata.aspx) ：這個方法會先產生本文資料的雜湊值 (Hash Value)，再對雜湊值加密以產生簽章資料。
- [VerifyData](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.dsacryptoserviceprovider.verifydata) ：這個方法會使用公開金鑰將簽章資料解密以取得簽章中的雜湊值，再與本文資料的雜湊值進行比對。
- [SignHash](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.dsacryptoserviceprovider.signhash.aspx) ：這個方法會使用私密金鑰將本文的Hash值加密，用來建立數位簽章。
- [VerifyHash](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.dsacryptoserviceprovider.verifyhash) ：這個方法會使用簽署所用的公開金鑰，從簽章取得雜湊值，並與所提供之資料的雜湊值進行比較。

## 如何建立與驗證數位簽章

### 使用 DSA 演算法，建立與驗證數位簽章

#### 建立數位簽章的步驟：

1. 建立數位簽章演算法物件
2. 將要簽章的資料，轉成 byte[]
3. 呼叫 [SignData](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.dsacryptoserviceprovider.signdata.aspx) 方法, 以建立簽章
4. 輸出公開金鑰
```c#
private void btn_DSA_SignData_Click(object sender, EventArgs e)
{
// 1) 建立DSA數位簽章演算法物件
DSACryptoServiceProvider signer = new DSACryptoServiceProvider();
txt_PrivateKey.Text = signer.ToXmlString(true);     //暫存私密金鑰 (用不到，含在DSACryptoServiceProvider物件中）
txt_PublicKey.Text = signer.ToXmlString(false);     //暫存公開金鑰

// 2) 讀取本文資料
byte[] content = Encoding.UTF8.GetBytes(txtSource.Text);

// 3) 呼叫 SignData 方法, 對本文進行簽章
byte[] signature = signer.SignData(content); 

// 輸出簽章 (使用 Base64 編碼）
txtSignature.Text = Convert.ToBase64String(signature);
}
```
```c#
private void btn_DSA_VerifyData_Click(object sender, EventArgs e)
{
// 1) 建立DSA數位簽章演算法物件
DSACryptoServiceProvider verifier = new DSACryptoServiceProvider();
verifier.FromXmlString(txt_PublicKey.Text);        //讀取公開金鑰

// 2) 讀取接收到的本文資料
byte[] content = Encoding.UTF8.GetBytes(txtSource.Text);

// 3) 讀取接收到的簽章資料
byte[] signature = Convert.FromBase64String(txtSignature.Text);

// 4) 呼叫 VerifyData 方法, 驗證本文與簽章是否相符
if (verifier.VerifyData(content, signature))
MessageBox.Show("Verify successful");
else
MessageBox.Show("Verify failed");
}
```

### 使用 RSA 演算法，建立與驗證數位簽章

在 DSA 物件中，當叫用 SignData 或 [VerifyData](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.dsacryptoserviceprovider.verifydata.aspx) 方法時，它會使用自已預設的雜湊演算法去計算本文的雜湊值，然後再對雜湊值簽署。  若使用 [RSA](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsa.aspx) 物件，則必須明確指定一個雜湊演算法。  

#### 驗證數位簽章的步驟：

1. 建立數位簽章演算法物件
2. 讀取發送端的公開金鑰
3. 讀取發送端的數位簽章
4. 讀取發送端的資料，並轉成 byte[]
5. 呼叫 [VerifyData](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.dsacryptoserviceprovider.verifydata) 方法
```c#
private void btn_RSA_SignData_Click(object sender, EventArgs e)
{
// 1) 建立RSA數位簽章演算法物件
RSACryptoServiceProvider signer = new RSACryptoServiceProvider();
txt_PublicKey.Text = signer.ToXmlString(false);     //暫存公開金鑰

// 2) 讀取本文資料
byte[] content = Encoding.UTF8.GetBytes(txtSource.Text);

// 3) 呼叫 SignData 方法, 對本文進行簽章
byte[] signature = signer.SignData( content, new SHA1CryptoServiceProvider());  //指定一個雜湊法

// 輸出簽章 (使用 Base64 編碼）
txtSignature.Text = Convert.ToBase64String(signature);
}
```
```c#
private void btn_RSA_VerifyData_Click(object sender, EventArgs e)
{
// 1) 建立RSA數位簽章演算法物件
RSACryptoServiceProvider verifier = new RSACryptoServiceProvider();
verifier.FromXmlString(txt_PublicKey.Text);     //讀取公開金鑰

// 2) 讀取接收到的本文資料
byte[] content = Encoding.UTF8.GetBytes(txtSource.Text);

// 3) 讀取接收到的簽章資料
byte[] signature = Convert.FromBase64String(txtSignature.Text);

// 4) 呼叫 VerifyData 方法, 驗證本文與簽章是否相符
if (verifier.VerifyData(content, new SHA1CryptoServiceProvider(), signature))
MessageBox.Show("Verify successful");
else
MessageBox.Show("Verify failed");
}
```

### 使用SignHash簽章與VerifyHash驗證

SignHash 和 [VerifyHash](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.dsacryptoserviceprovider.verifyhash.aspx) 幾本上和 SignData 和 [VerifyData](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.dsacryptoserviceprovider.verifydata.aspx) 是等價的。  只不過前者是直接使用資料當參數傳入，後者則是使用資料的Hash值進行簽署或驗證。  所以在叫用 SignData 和 [VerifyData](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.dsacryptoserviceprovider.verifydata.aspx) 方法前，必須先自行計算資料的Hash值。  

另外，這二個方法僅支援 SHA1 和 [MD5](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.md5.aspx) 雜湊演算法，你必須在再用時告知你的資料使用哪一個演算法。  不過，它必須待合雜湊演算法識別項(OID)格式，這可以透過 MapNameToOID 方法取得。  
```c#
private void btnSignHash_Click(object sender, EventArgs e)
{
// 1) 建立DSA數位簽章演算法物件
DSACryptoServiceProvider signer = new DSACryptoServiceProvider();
txt_PublicKey.Text = signer.ToXmlString(false);     //暫存公開金鑰

// 2) 讀取本文資料,並計算本文的雜湊值
byte[] content = Encoding.UTF8.GetBytes(txtSource.Text);
byte[] content_hash = myHash.get_Hash_using_SHA1(content);

// 3) 呼叫 SignHash 方法, 對雜湊值進行簽章
string OID = CryptoConfig.MapNameToOID("SHA1");  //取得OID (OID是指名演算法物件識別項）
byte[] signature_hashed = signer.SignHash(content_hash, OID);

txtSignature_Hash.Text = Convert.ToBase64String(signature_hashed);
txtOID.Text = OID;
}
```
```c#
private void btnVerifyHash_Click(object sender, EventArgs e)
{
// 1) 建立DSA數位簽章演算法物件
DSACryptoServiceProvider verifier = new DSACryptoServiceProvider();
verifier.FromXmlString(txt_PublicKey.Text);

// 2) 讀取接收到的本文資料,並計算本文的雜湊值
byte[] content = Encoding.UTF8.GetBytes(txtSource.Text);
byte[] content_hash = myHash.get_Hash_using_SHA1(content);

// 3) 讀取接收到的簽章資料
byte[] signature_hashed = Convert.FromBase64String(txtSignature_Hash.Text);
string OID = txtOID.Text;

// 4) 呼叫 VerifyHash 方法, 驗證本文的雜湊值與簽章過雜湊值的是否相符
if (verifier.VerifyHash(content_hash, OID, signature_hashed))
MessageBox.Show("Verify successful");
else
MessageBox.Show("Verify failed");
}
```


- [數位簽章](http://zh.wikipedia.org/wiki/%E6%95%B8%E4%BD%8D%E7%B0%BD%E7%AB%A0)