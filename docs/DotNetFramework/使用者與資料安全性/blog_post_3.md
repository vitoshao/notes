---
title: 資料的加密與解密(2)-非對稱金鑰加密演算法
layout: default
parent: 使用者與資料安全性
nav_order: 3
description: "資料的加密與解密(2)-非對稱金鑰加密演算法"
date: 2012-05-10
tags: [DotNetFramework,使用者與資料安全性]
postid: "7597328874587521508"
---
# 二、非對稱金鑰加密 ( Asymmetric Key Encryption ) 

對稱加密法有個不好的問題，就是加密子與解密子雙方都要共享同一個金鑰。  非對稱加密法主要就是要克服這個弱點，它將金鑰分成**公開金鑰** (public key) 與**私密金鑰** (private key)，所以一般也稱**公開金鑰加密法**。  該演算法中的公開金鑰是對外開放的，私密金鑰必須妥善保存，絕不可外洩；而使用公開金鑰加密的資料，必須使用相對應的私密金鑰才能解密，反之亦同。  所以，對稱加密法，其操作過程，通常雙方必須先交換公開金鑰，然後發送端使用對方的公開金鑰將明文轉成密文，接收端收到密文後，再以自已的私密金鑰進行解密。  

舉個例，假如 Alice 想要委託 Eve 送一封情書給 Bob，但是又不想讓 Eve 知道情書內容，所以，她使用 Bob 的公鑰，將情書內容加密，因此只有在 Bob 拿到加密後的情書後，以自已的私鑰解密後才能閱讀。  Eve 由於沒有 Bob 的私鑰，因此便無法窺得原文。  

非對稱加密演算法的速度不如對稱加密演算法，但更難破解，所以不適合用於加密大量的資料。  通常使用對稱演算法對實際的訊息內容加密，再使用非對稱演算法加密對稱演算法的金鑰和初始向量IV。  像 HTTPS 及 SSL 都是使用這樣子的技巧。  

為了管理金鑰，一般組織多會實作**公開金鑰基礎結構** (Public Key Infrastructure, PKI) ，例如：Windows Server 2003 的憑證服務。  

- PKI 是一種非對稱性密碼學、軟體和網路服務的整合技術，主要是用來提升保障網路通訊和電子交易的安全性。
- PKI 也是一種支援數位憑證和公開金鑰各項標準或協定的安全性整合服務與架構。
- PKI 提供了分送公開金鑰的實務技術。

## 非對稱演算法類別

| 類別 | 說明 |
| --- | --- |
| **RSA**CryptoServiceProvider | &lt;ul&gt;      &lt;li&gt;由 Rivest、Shamir、Adleman 三位學者於 1977 共同發展出來。&lt;/li&gt;      &lt;li&gt;RSACryptoServiceProvider 是一個 Managed 包裝函式，包覆住由 Cryptography API 提供的 UnManaged &lt;a target='_blank' href='http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsa.aspx'&gt;RSA&lt;/a&gt; 實作。&lt;/li&gt;      &lt;/ul&gt; |
| **DSA**CryptoServiceProvider | &lt;ul&gt;      &lt;li&gt;Digital Signature Algorithm 。&lt;/li&gt;      &lt;li&gt;數位簽章演算法。&lt;/li&gt;      &lt;/ul&gt; |

.Net Framkwork 提供二種非對稱演算法類別，二者都衍生自 [System.Security.Cryptography.AsymmetricAlgorithm](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.asymmetricalgorithm.aspx) 基底類別，並共享下列屬性：

- BlockSize：取得或設定密碼編譯作業的區塊大小，以位元為單位。
- KeySize：取得或設定對稱演算法使用之秘密金鑰的大小，以位元為單位。
- ......

但是 AsymmetricAlgorithm 不像 SymmetricAlgorithm，它不提供方法，所有方法都在實作的類別上。

## 如何匯出匯入非對稱金鑰

當建立一個 [RSA](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsa.aspx) 演算法的執行個體時，這個執行個體就會包含一對臨時的**金鑰組**，所以不需要額外建立金鑰組的作業。  也就是說當我們實例化一個 [RSACryptoServiceProvider](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsacryptoserviceprovider.aspx) 對象的時候，如果不指定具體的 [**CspParameters**](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.cspparameters.aspx) **(金鑰容器)** 對象，RSACryptoServiceProvider 執行個體會生成一個臨時的金鑰容器，並且在 [RSACryptoServiceProvider](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsacryptoserviceprovider.aspx) 執行個體銷毀的時候自動刪除這個臨時的金鑰容器。  公開金鑰，通常需要匯出，供交換使用。私密金鑰只有在需要重複使用時，才需要將它匯出。  

#### 匯出匯入的方法：

- [ExportParameters](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsacryptoserviceprovider.exportparameters.aspx) ：將金鑰組匯出成為一個 RSAParameters 結構的執行個體。
- [ToXmlString](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsa.toxmlstring.aspx) ：將金鑰組匯出成 XML 格式，方便金鑰的儲存與傳輸。
- [ImportParameters](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsacryptoserviceprovider.importparameters.aspx) ：匯入金鑰匯。
- [FromXmlString](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsa.fromxmlstring.aspx) ：匯入金鑰匯。
```c#
// 建立 RSA 演算法物件的執行個體
RSACryptoServiceProvider rsaProvider = new RSACryptoServiceProvider();

// 匯出金鑰組。 
RSAParameters publicKey = rsaProvider.ExportParameters(false);    //匯出金鑰，參數 false 表示不含私鑰。
RSAParameters privateKey = rsaProvider.ExportParameters(true);    //匯出金鑰

// 以 XML 格式匯出金鑰組。 
txt_PublicKey.Text = rsaProvider.ToXmlString(false);              //以 XML 格式匯出金鑰，參數 false 表示不含私鑰。
txt_PrivateKey.Text = rsaProvider.ToXmlString(true);              //以 XML 格式匯出金鑰
```
```c#
// 建立 RSA 演算法物件的執行個體，並使用之前匯出的公鑰
RSACryptoServiceProvider rsaProvider = new RSACryptoServiceProvider();
rsaProvider.FromXmlString(txt_PublicKey.Text);
```
```xml
<!--公鑰-->
<RSAKeyValue>
<Modulus>poXzZzQt883UzTN2Bi4ZGJpa2tFguxtKZ6BXFf3n2PKoOKtpO8b8Xan0eLcVcL9nvGe2hpjEFhX8+CKsWOaIb1Yi3ajyhAPaGRx4IZP5/JBGYSiPuv97jOWr3t9VbeXtzqSEaiQrcHaHz7u5t/QcdvI0KP7Y35QcIdjOELQcg2E=</Modulus>
<Exponent>AQAB</Exponent>
</RSAKeyValue>

<!--私鑰-->
<RSAKeyValue>
<Modulus>poXzZzQt883UzTN2Bi4ZGJpa2tFguxtKZ6BXFf3n2PKoOKtpO8b8Xan0eLcVcL9nvGe2hpjEFhX8+CKsWOaIb1Yi3ajyhAPaGRx4IZP5/JBGYSiPuv97jOWr3t9VbeXtzqSEaiQrcHaHz7u5t/QcdvI0KP7Y35QcIdjOELQcg2E=</Modulus>
<Exponent>AQAB</Exponent>
<P>2vaCCmnuQS2ktmdaZ8asZxDk1XZpl0lbnYfjhrX6W8DEu4G80jV23bHnQoBrZOhudktboJQKUsyiQTK+MYTBVw==</P>
<Q>wrC1qkBs/zeuH6UMLZqJomB6IGBs+hyo4Kqt2U5UBet/6ovtIMjvoKsDk/OWFyJnwPvDeE/kxaLKOvxeRrhWBw==</Q>
<DP>rGKehqxXUyEoKCK+Xi1f1dTLE/OhteWfJyqwyoHClTHysNy3V5eX+XnyY7zO0CbdsGwruwRSDFXm+AWHD11BCQ==</DP>
<DQ>Buy4GW0fM1ispc1+CGU1vgPGdD5Zwe+hBhSsCE2j6W39577AWWksNL/AadPBI+OCYzfRNQdKcMkgfkaZp9RIow==</DQ>
<InverseQ>pwHYkSXdT1k+wJCCyJ0TJzGHrfXk7s8wifBJU7Ta7D0FzGykXH1CB0L55cousJ3E8zGlD5CnYd83G4GcARR0JA==</InverseQ>
<D>jWqkUrjVbX8XgUx6prTvSx2KmtnFNCDxBx/71QlTo8IvajDnpYRPPZvfqcSRyLZD+pR5RcTmgXsa4f9QLg9ZPRS2rTDPNw8GVHvpcVxVkgNfUgaRTizOzJBTS6Ub15ARuIcJgh5BCX5frxOmKFe/YKheNVG8uZ3OBhcqRm+yRaE=</D>
</RSAKeyValue>
```

## 如何儲存金鑰組以供以後使用

上面例子中，我們使用將私鑰匯出成 XML 文件，進行法保存，日後只要匯入該金鑰即可用來解密。  關於保存私鑰，Windows 有提供一個電腦層級專門用來保存金鑰的機制，那就是使用 [金鑰容器 ( CryptoAPI )](http://msdn.microsoft.com/zh-tw/library/f5cs0acs)，它將金鑰保存在「密碼編譯服務供應者」(Cryptography Service Provider, CSP) 之中。  若日後有需要用到該金鑰的應用程式，只要藉由金鑰名稱，即可取出該金鑰，其處理步驟如下：。  

#### 儲存金鑰

1. 建立 [CspParameters](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.cspparameters.aspx) 物件
2. 指定 [KeyContainerName](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.cspparameters.keycontainername.aspx)
3. 使用步驟１的 [CspParameters](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.cspparameters.aspx) 物件，建立 [RSA](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsa.aspx) 演算法物件的執行個體。
4. 將 [PersistKeyInCsp](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsacryptoserviceprovider.persistkeyincsp.aspx) 屬性設為 true ，表示將金鑰儲存到 CSP。
```c#
// 建立 CspParameters 物件
CspParameters cspPara = new CspParameters();

// 指定 KeyContainerName
cspPara.KeyContainerName = "AsymmetricExample";

// 建立 RSA 演算法物件的執行個體
RSACryptoServiceProvider rsaProvider = new RSACryptoServiceProvider(cspPara);

// 設定 PersistKeyInCsp 屬性為 true。
rsaProvider.PersistKeyInCsp = true;       // 指出金鑰是否應保存 (Persist) 在密碼編譯服務供應者 (CSP) 中。

// 上面最後一行程式碼，.NET Framework 會自動建立金鑰容器並儲存金鑰。
// 若再度執行相同的應用程式，.NET Framework 會偵測同名的金鑰容器是否已存，若存在，則取出存於其中的私密金鑰
```

#### 刪除金鑰

若要刪除 CSP 中的金鑰，只要將 [PersistKeyInCsp](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsacryptoserviceprovider.persistkeyincsp.aspx) 設定為 false 即可。
```c#
CspParameters cspPara = new CspParameters();
cspPara.KeyContainerName = "AsymmetricExample";
RSACryptoServiceProvider rsaProvider = new RSACryptoServiceProvider(cspPara);
rsaProvider.PersistKeyInCsp = false;  //設定不保存
rsaProvider.Clear();                  //釋放 AsymmetricAlgorithm 類別使用的所有資源。
```

## 如何使用非對稱金鑰進行加密與解密

前面提過，非對稱加密演算法，其方法都由實作的類別提供，因此，  若使用 [RSA](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsa.aspx) 演算法，則叫用 [RSACryptoServiceProvider.Encrypt](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsacryptoserviceprovider.encrypt) 和 [RSACryptoServiceProvider.Decrypt](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rsacryptoserviceprovider.decrypt) 進行加密與解密。  下面例將示範如何使用非對稱加解密演算法。  
```c#
string messageString = txtSource.Text;

RSACryptoServiceProvider rsaProvider = new RSACryptoServiceProvider();

byte[] messageBytes = Encoding.Unicode.GetBytes(messageString);
byte[] encryptedMessage = rsaProvider.Encrypt(messageBytes, false);

byte[] decryptedBytes = rsaProvider.Decrypt(encryptedMessage, false);
Console.WriteLine(Encoding.Unicode.GetString(decryptedBytes));
```
```c#
private void Encrypt_Click(object sender, EventArgs e)
{
// 建立 CspParameters 物件，並指定 KeyContainerName
CspParameters cspPara = new CspParameters();
cspPara.KeyContainerName = "test secret key";

// 建立 RSA 演算法物件的執行個體，並將金鑰保存在 CSP 中
RSACryptoServiceProvider rsaProvider = new RSACryptoServiceProvider(cspPara);
rsaProvider.PersistKeyInCsp = true;         //將金鑰存到金鑰容器

// 將資料加密
byte[] bytePlain = Encoding.UTF8.GetBytes( txtSource.Text );
byte[] byteCipher = rsaProvider.Encrypt(bytePlain, false);

// 將加密後的資料，轉 Base64 格式輸入
txtRSA_CipherText.Text = Convert.ToBase64String(byteCipher);
}

private void Decrypt_Click(object sender, EventArgs e)
{
// 建立 CspParameters 物件，並指定 KeyContainerName
CspParameters cspPara = new CspParameters();
cspPara.KeyContainerName = "test secret key";

// 建立 RSA 演算法物件的執行個體，並將金鑰保存在 CSP 中
RSACryptoServiceProvider rsaProvider = new RSACryptoServiceProvider(cspPara);  //由金鑰容器取得金鑰

// 將資料解密
byte[] byteCipher = Convert.FromBase64String(txtRSA_CipherText.Text);
byte[] bytePlain = rsaProvider.Decrypt(byteCipher, false);

// 將解密後的資料，轉 UTF8 格式輸入
txtRSA_PlainText.Text = Encoding.UTF8.GetString(bytePlain);
}
```
```c#
private void Encrypt2_Click(object sender, EventArgs e)
{
// 建立 RSA 演算法物件的執行個體，並匯入先前建立的公鑰
RSACryptoServiceProvider rsaProvider = new RSACryptoServiceProvider();
rsaProvider.FromXmlString(txt_PublicKey.Text);

// 將資料加密
byte[] bytePlain = Encoding.UTF8.GetBytes(txtSource.Text);
byte[] byteCipher = rsaProvider.Encrypt(bytePlain, false);

// 將加密後的資料，轉 Base64 格式輸入
txtRSA_CipherText.Text = Convert.ToBase64String(byteCipher);
}

private void Decrypt2_Click(object sender, EventArgs e)
{
// 建立 RSA 演算法物件的執行個體，並匯入先前建立的私鑰
RSACryptoServiceProvider rsaProvider = new RSACryptoServiceProvider();
rsaProvider.FromXmlString(txt_PrivateKey.Text);

// 將資料解密
byte[] byteCipher = Convert.FromBase64String(txtRSA_CipherText.Text);
byte[] bytePlain = rsaProvider.Decrypt(byteCipher, false);

// 將解密後的資料，轉 UTF8 格式輸入
txtRSA_PlainText.Text = Encoding.UTF8.GetString(bytePlain);
}
```

#### 數位信封（Digital Envelop）

RSA公開金鑰系統同樣可運用於資料之加密，然而基於效能考量，通常不以RSA演算法直接對本文做加密，而是採用數位信封技術。  其特色是以快速的對稱式加解密演算法進行大量資料之加密運算，而以非對稱式加解密演算法解決對稱式金鑰交換之棘手問題。  傳送端首先任意產生用以加密此次本文資料之秘密金鑰（Session Key），對欲傳送之本文做加密，再以接收端之公開金鑰對此Session Key做加密，最後將密文與加密保護後之Session Key一併傳送至接收端。  接收端在取得訊息時，先以其私密金鑰解密取得正確的Session Key，再以此對密文做解密，即可取得完整之明文資料。


- [認識電腦層級和使用者層級的 RSA 金鑰容器](http://msdn.microsoft.com/zh-tw/library/f5cs0acs)
- [逐步解說：建立和匯出 RSA 金鑰容器](http://msdn.microsoft.com/zh-tw/library/2w117ede.aspx)