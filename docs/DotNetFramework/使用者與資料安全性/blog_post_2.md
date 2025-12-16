---
title: 資料的加密與解密(3)-雜湊
layout: default
parent: 使用者與資料安全性
nav_order: 2
description: "資料的加密與解密(3)-雜湊"
date: 2012-05-10
tags: [DotNetFramework,使用者與資料安全性]
postid: "4153266854946337071"
---
# 三、使用雜湊(Hash)驗證資料的完整性

由書上截取二句話：

- A hash is a checksum that is unique to a specific file or piece of data.
- A hash is a one-way operation.

所以說，雜湊(Hash)是將一堆資料進行一個總和運算後所得到的一個唯一檢查碼，又稱為雜湊值 (Hash Value) 。  
雜湊演算是單向作業，不可逆的，也就是無法將雜湊值反導出原始資料，不像先前的加密作業，還可以解密回去。  

例如：任何不同的檔案或資料，經過雜湊運算後，都會得到一個不同的雜湊值 (Hash Value) 。  因為這個雜湊值是唯一的，利用這個唯一的特性，所以可以用來驗證檔案的完整性。  只要檔案的內容有任何變動，經過相同的雜湊演算法所得到的雜湊值必定與原來的值不相同。  

## 雜湊演算法

#### 不需金鑰的雜湊演算法

.Net Framkwork 裡提供六種不需金鑰的雜湊演算法，這些類別都是衍生自 [System.Security.Cryptography.HashAlgorithm](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.hashalgorithm.aspx) 類別。  

| 抽象類別 | 實作類別 | 說明 |
| --- | --- | --- |
| MD5 | [MD5CryptoServiceProvider](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.md5cryptoserviceprovider.aspx) | Message Digest (MD)演算法。雜湊長度：128 bit (16 bytes) |
| RIPEMD160 | [RIPEMD160Managed](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.ripemd160managed.aspx) | MD160演算法。雜湊長度：160 bit (20 bytes)。(New in .Net2.0, 用於取代MD5 ) |
| SHA1 | [SHA1CryptoServiceProvider](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.sha1cryptoserviceprovider.aspx) | Secure雜湊演算法1。雜湊長度：160 bit (20 bytes) |
| SHA256 | [SHA256Managed](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.sha256managed.aspx) | Secure雜湊演算法256。雜湊長度：256 bit (32 bytes) |
| SHA384 | [SHA384Managed](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.sha384managed.aspx) | Secure雜湊演算法384。雜湊長度：384 bit (46 bytes) |
| SHA512 | [SHA512Managed](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.sha512managed.aspx) | Secure雜湊演算法512。雜湊長度：512 bit (64 bytes) |

#### 金鑰的雜湊演算法

金鑰雜湊演算法是為了預防雜湊值被修改的一種演算法，利用一個發送端和收件端都有的私密金鑰來加密雜湊。  .Net Framkwork 裡提供的兩種金鑰雜湊演算法，這些類別都是衍生自 [System.Security.Cryptography.KeyedHashAlgorithm](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.keyedhashalgorithm.aspx) 類別。  

| 抽象類別 | 實作類別 | 說明 |
| --- | --- | --- |
| HMAC | [HMACSHA1](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.hmacsha1.aspx) | 接收任何大小的金鑰，產生雜湊值長度：20 bytes。 |
| MACTripleDES | [MACTripleDES](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.mactripledes.aspx) | 接收長度為 8、16、24 bytes 的金鑰，產生雜湊值長度：8 bytes。 |

## 如何計算無金鑰雜湊 (NonKeyed Hash)

1. 建立雜湊演算法物件。
2. 將資料轉換成位元組陣列。
3. 呼叫 [HashAlgorithm.ComputeHash](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.hashalgorithm.computehash) 方法，執行雜湊演算法運算。
4. 由 [HashAlgorithm.Hash](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.hashalgorithm.hash.aspx) 取得計算出來的雜湊值。
```c#
// 1.建立雜湊演算法物件。
MD5 myHash = new MD5CryptoServiceProvider();

// 2.將資料轉換成位元組陣列。
byte[] byteSource = Encoding.Unicode.GetBytes(txtSource.Text);

// 3.呼叫 HashAlgorithm.ComputeHash 方法，計算出雜湊值。
byte[] byteHashed = myHash.ComputeHash(byteSource); 

// 4.取得計算出來的雜湊值。
byte[] byteHashed2 = myHash.Hash;  

// 5.將雜湊值轉 Base64 輸出
txtHash.Text = Convert.ToBase64String(byteHashed);  //6dXCyh3kC+EuZsxXnRG5/w==
```

所有不需金鑰的雜湊演算法都衍生自單一類別，所以用法也都一樣，只要變更演算法的宣告即可。另外，直接使用 HashAlgorithm 類別，也可以建立雜湊演算法。  
```c#
HashAlgorithm md5 = HashAlgorithm.Create("MD5");

byte[] message = Encoding.Unicode.GetBytes(txtSource.Text);
byte[] hash = md5.ComputeHash(message);

txtHash.Text = Convert.ToBase64String(hash);  //6dXCyh3kC+EuZsxXnRG5/w==
```

## 如何計算金鑰雜湊 (Keyed Hash)

金鑰雜湊演算法，使用方法同無金鑰雜湊演算法，只不過建立實體時，要額外提供一個金鑰資料。  若使用 [HMACSHA1](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.hmacsha1.aspx) 演算法，可接受任意長度的秘密金鑰，  若使用 [MACTripleDES](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.mactripledes.aspx) 則僅接受長度為 8, 16 或 24 個位元組的秘密金鑰。
```c#
// 使用 Rfc2898 產生一個 16 bytes 的金鑰
string password = "This is a password";
byte[] salt = Encoding.ASCII.GetBytes("This is my sa1t");
Rfc2898DeriveBytes passwordKey = new Rfc2898DeriveBytes(password, salt);
byte[] secretKey = passwordKey.GetBytes(16);

// 1.建立 HMACSHA1 金鑰雜湊演算法物件
HMACSHA1 myHash = new HMACSHA1(secretKey);

// 2.將資料轉成 byte[]
byte[] byteSource = Encoding.UTF8.GetBytes(txtSource.Text);

// 3.呼叫ComputeHash，計算出雜湊值
myHash.ComputeHash(byteSource);

// 4.由 HashAlgorithm.Hash 取得計算出來的雜湊值。
txtHash.Text = Convert.ToBase64String(myHash.Hash);
```


-