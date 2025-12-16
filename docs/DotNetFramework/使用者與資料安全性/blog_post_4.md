---
title: 資料的加密與解密(1)-對稱金鑰加密演算法
layout: default
parent: 使用者與資料安全性
nav_order: 4
description: "資料的加密與解密(1)-對稱金鑰加密演算法"
date: 2012-05-10
tags: [DotNetFramework,使用者與資料安全性]
postid: "2395038289347547328"
---
當資料被永久性的儲存下來後或者於網路上傳輸的時候，都是很容易遭受攻擊的。  雖然有 CAS 可以用來控管應用程式的存取，或者 ACLs 可以用來保護資料，但是對有能力存取硬碟或網路的駭客而言，想要穿透軟體保護、截取資料或者修改資料是不難辦到的。  因此，對於一些隱私的資料，還必須使用加密的技術，進一步保護資料的完整性。  .Net Framewrok 提供數種加解密類別，可以用來支援各種型態的加解密需求，例如：對稱和非對稱加密、雜湊、數位簽章。  

- [一、對稱金鑰加密](http://vito-note.blogspot.com/2012/05/1.html)
- [二、非對稱金鑰加密](http://vito-note.blogspot.com/2012/05/2.html)
- [三、使用雜湊驗證資料的完整性](http://vito-note.blogspot.com/2012/05/3.html)
- [四、數位簽章](http://vito-note.blogspot.com/2012/05/4.html)

# 一、對稱金鑰加密 (Symmetric Key Encryption)

## 什麼是對稱金鑰加密

簡單講，對稱金鑰加密就是使用相同的金鑰(key)進行加密與解密的運算。這種對稱式加密演算法，因為執行速度較快，適合於加密大量的資料。  

加密  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiW8OXsV2EzY1f9tv3_TzKau1gKzeDQvyzj-p7TNK4KDO0AD238uxknMpKGUjWeDjDJl5Ij0Cj9_P41GI3lhoGG_H8Ebw_D0K_Rt83ZH2kaiZt7cRsp76V_bEmBDM8Ca34CmhWBxFY9Gq0/s651/Encryption.png)

解密  
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhrqA1oMOdqzX3Chq5ZWrsvMCN3dQkQou9oSyObFN4ORYdj4KlYnzVszJIEyJw4FTiSxj8-KhJIkrd7e1hb7nsnNBK1jfRKmkcWgREW7u573-47HPxzzv6N42G5qiNq5JQLwvW5L1A4Lds/s644/Decryption.png)

## 對稱演算法類別

| 類別 | 金鑰長度 | 說明 |
| --- | --- | --- |
| RijndaelManaged   &lt;br&gt;( AES ) | 128、192、256 bits  &lt;br&gt;16、24、32 bytes | &lt;ul&gt;      &lt;li&gt;又稱進階加密標準 ( Advanced Encryption Standard, AES )。&lt;/li&gt;      &lt;li&gt;由 Joan Daemen 和 Vincent Rijmen 二位學者共同設計。&lt;/li&gt;      &lt;li&gt;.NetFramkwork 中，唯一完全 Managed 的加密類別。&lt;/li&gt;      &lt;li&gt;讀做: rain doll&lt;/li&gt;      &lt;/ul&gt; |
| DES | 56+8 bits   &lt;br&gt;8 bytes  &lt;br&gt;      (其中 8 bits用於錯誤更正） | &lt;ul&gt;      &lt;li&gt;Data Encryption Standard 。&lt;/li&gt;      &lt;li&gt;使用較短的金鑰。&lt;/li&gt;      &lt;li&gt;但是每1 byte的LSB（Less Significant Bit）為同位元檢查碼（Parity Check），故實際有效金鑰長度為56 bits。  &lt;/li&gt;      &lt;/ul&gt; |
| RC2 | Variable | 類似DES，使用變動長度的key。 |
| TripleDES  &lt;br&gt;( 3DES ) | 156 bits | 使用 DES 加密演算法加密三次。 |

所有對稱演算法類別都衍生自 [System.Security.Cryptography.SymmetricAlgorithm](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.aspx) 基底類別，並共享下列屬性：

- [IV](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.iv.aspx) ：取得或設定對稱演算法的初始化向量 (initialization vector)。
- [Key](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.key.aspx) ：取得或設定對稱演算法的秘密金鑰。
- [BlockSize](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.blocksize.aspx) ：加解密作業運作的區塊大小，以位元為單位。也就是演算法單次處理的位元數。
- [KeySize](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.keysize.aspx) ：取得或設定對稱演算法使用之秘密金鑰的大小，以位元為單位。
- [Mode](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.mode.aspx) ：取得或設定對稱演算法的作業模式。為 [CipherMode](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.ciphermode.aspx) 列舉值，一般使用預計值(CBC)不用修改，若要變更，加密子與解密子都要同時變更。
- ......

除了共用屬性，還有共用下列方法：

- [CreateDecryptor](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.createdecryptor.aspx) ：使用目前的 [Key](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.key.aspx) 屬性和初始化向量 ( [IV](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.iv.aspx) )，建立對稱**解密子**物件。
- [CreateEncryptor](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.createencryptor.aspx) ：使用目前的 [Key](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.key.aspx) 屬性和初始化向量 ( [IV](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.iv.aspx) )，建立對稱**加密子**物件。
- [GenerateKey](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.generatekey.aspx) ：重新定義一組亂數金鑰 (Key)，只有在已定義一個金鑰，後續要使用另一個亂數金鑰時，才需要呼叫。
- [GenerateIV](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.generateiv.aspx) ：重新定義一組亂數的初始化向量 ( [IV](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.iv.aspx) )，同 [GenerateKey](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.generatekey.aspx) ，後續要使用另一組亂數時，才需要呼叫。
- [ValidKeySize](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.validkeysize.aspx) ：判斷指定的金鑰大小對目前的演算法是否有效。

## 如何建立對稱金鑰(key)和初始向量(IV)

#### 關於加密演算法，有幾點觀念必須要有：

- 每一種對稱加密演算法，其金鑰長度都是特定的，而不是隨便一段資料就可以。
- 加密與解密的操作，都是針對位元陣列 byte[]。然而，將文字轉換成位元陣列，會與編碼方式相關，所以加密時用什麼編碼方式，解密時也要用同樣的編碼方式。
- 加密後的 byte[] 資料，在輸出的時候，可轉為 [Base64](http://zh.wikipedia.org/wiki/Base64) 編碼格式，以方便使用文字編輯器開啟檢視。

#### 如何建立對稱金鑰

每一種演算法有自已金鑰長度的要求，金鑰的內容，除了自行定義外，也可以使用 [Rfc2898DeriveBytes](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rfc2898derivebytes.aspx) 類別，由它幫我們產生一組亂數金鑰。  利用 [Rfc2898DeriveBytes](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rfc2898derivebytes.aspx) 所提供的方法來產生 key，必須先提供一組 passward 和 salt。  這個方法最適合加解密雙方，早已存在一個共享的祕密時。透過這組 passward 和 salt，即可自行產生相同的金鑰。  這樣子，加解密雙方就不需要互相傳送 key ，可以透過這組 passward ，自行產生 key 和 IV。  

例如，ＡＢ二方要進行加解密，但是加解密前必須提供一個對稱金鑰，可是又怕這個 key 傳遞過程中被截取，所以利用 [Rfc2898DeriveBytes](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.rfc2898derivebytes.aspx) 所提供的方法來產生 key。  Ａ對Ｂ說，password 是我倆的「結婚紀念日」，salt 是「i love you」。  假設結婚紀念日是只有ＡＢ二人才知道的祕密，透過這樣子的訊息傳遞方法，就不怕傳遞過程中資料被截取，而且Ｂ也可以產生和Ａ加密時一樣的 key 了。  
```c#
// 定義 password、salt
string password = txtPassword.Text;
byte[] salt = Encoding.UTF8.GetBytes(txtSalt.Text);

// 依據 password、salt ，建立 Rfc2898DeriveBytes 物件
Rfc2898DeriveBytes Rfc2898 = new Rfc2898DeriveBytes(password, salt);
byte[] byteKEY = Rfc2898.GetBytes(8);    //這個數值可由各演算法的 KeySize 取得
byte[] byteIV = Rfc2898.GetBytes(8);     //這個數值可由各演算法的 BlockSize 取得

txtKey.Text = Convert.ToBase64String(byteKEY);  // LuJ+rn5JBVI=
txtIV.Text = Convert.ToBase64String(byteIV);    // PGBjNcIAp+k=
```
```c#
// 建立一個演算法物件
AesCryptoServiceProvider myAlg = new AesCryptoServiceProvider();

// 定義 password、salt
string password = txtPassword.Text;
byte[] salt = Encoding.UTF8.GetBytes(txtSalt.Text);

// 依據 password、salt ，建立 Rfc2898DeriveBytes 物件
Rfc2898DeriveBytes key = new Rfc2898DeriveBytes(password, salt);
myAlg.Key = key.GetBytes(myAlg.KeySize / 8);
myAlg.IV = key.GetBytes(myAlg.BlockSize / 8);

txtKey.Text = Convert.ToBase64String(myAlg.Key);
txtIV.Text = Convert.ToBase64String(myAlg.IV);
```
```c#
public class myRfc2898
{
public static void GenerateKey(SymmetricAlgorithm cryptoProvider, string password, byte[] salt)
{
// 依據 密碼 和 Salt 來衍生金鑰
Rfc2898DeriveBytes key = new Rfc2898DeriveBytes(password, salt);
cryptoProvider.Key = key.GetBytes(cryptoProvider.KeySize / 8);
cryptoProvider.IV = key.GetBytes(cryptoProvider.BlockSize / 8);
}
}

//使用 myKey.Generate 來產生 KEY 
private void button4_Click(object sender, EventArgs e)
{
// 定義 password、salt
string password = txtPassword.Text;
byte[] salt = Encoding.UTF8.GetBytes(txtSalt.Text);

//建立一個 DES 演算法
SymmetricAlgorithm symAlgorithm = new DESCryptoServiceProvider();

//1) 建立演算法物件時，本身已建立一組亂數的 Key, IV
txtKey.Text = Convert.ToBase64String(symAlgorithm.Key);     //hFYPyIK3uSQ=
txtIV.Text = Convert.ToBase64String(symAlgorithm.IV);       //oeZlJhiaZB0=

//2) 使用指定的 password, salt ，由 Rfc2898DeriveBytes 產生一組亂數的 Key, IV
myRfc2898.GenerateKey(symAlgorithm, password, salt);
txtKey.Text = Convert.ToBase64String(symAlgorithm.Key);     //LuJ+rn5JBVI=
txtIV.Text = Convert.ToBase64String(symAlgorithm.IV);       //PGBjNcIAp+k=

//3) 叫用 GenerateKey, GenerateIV，以取得另一組亂數的 Key, IV
symAlgorithm.GenerateKey();
symAlgorithm.GenerateIV();
txtKey.Text = Convert.ToBase64String(symAlgorithm.Key);     //NQTqVuHg7uM=
txtIV.Text = Convert.ToBase64String(symAlgorithm.IV);       //PGBjNcIAp+k=

//說明：
//步驟1中，每次執行， Key, IV 的值都是不一樣的。
//步驟2中，只要 password, salt 不變，都會建立相同的 Key, IV
//步驟3中，下一組 Key, IV 的值，也是亂數產生，每次不一樣。

//建立一個 AES 演算法
symAlgorithm = new AesCryptoServiceProvider();

myRfc2898.GenerateKey(symAlgorithm, password, salt);    //不同演算法， Key, IV 有不同的長度需求
txtKey.Text = Convert.ToBase64String(symAlgorithm.Key);     //LuJ+rn5JBVI8YGM1wgCn6TLDqnpD4qhA8KflTP0m/fQ=
txtIV.Text = Convert.ToBase64String(symAlgorithm.IV);       //3JqD0OWQRF3UuyVZvCdMEA==
}
```

#### 為什麼要有初始向量(initialization vector)

另外，有個概念我們要先知道。我們需要兩個元素去實行加密和解密：「初始向量」和「金鑰」。  你有可能會問初始向量有甚麼用途？  一般加密演算法，會將原始資料切成多個區塊進行加密動作，  如果我們只用一個金鑰值與每個明文區塊去做加密，這樣子所得到的每個密文區塊，都是相同的加密模式，會比較容易被破解。（如下圖）    
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjkcZcyiAmFaTYGb7i16kNwYTorYmjXeWkztQ81a267APkyIAsKIuicVi1ZToC6rjkiegfkJOkNdqQfaCvBPd2v0bGUGH1tntDeN1MGJ_bl0LGTSEzvWrjqZ_o-M5apsEJqzaUyxtrjpxY/s850/symmetric_encryption1.png)  

所以加密演算法使用一種類似遞回的方法將資料加密，它是由三個輸入去產生一個加密過的片段，分別是「上一個已加密片段」、「現在還沒加密的資料片段」、「金鑰」。  因為演算法在一開始時並沒有「上一個加密過的片段」，這就是為什麼要用到初始向量的原因了。（如下圖）    
![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiw-wBOJ7czovb6Gw_IDw5Cg2MVDodmqZTYX5fW5htYoCDBZ8fHyoSOKP5_LtTA8c5CvTW3iaXemij54YGGVTecVm3_17WSQTIN8Frvm8dQjoBdGoRuWtiqvjkTYBDnLxEPsB5Cdsy4vtg/s850/symmetric_encryption2.png)

## 如何使用對稱金鑰進行加密與解密

資料加密過程，和一般 stream 的用法很類似，其步驟如下：

1. 建立一個對稱加密演算法物件。
2. 指定演算法的 [Key](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.key.aspx) 和 [IV](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.symmetricalgorithm.iv.aspx) 。
3. 建立 [ICryptoTransform](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.icryptotransform.aspx) 加密子物件。（由演算法物件的 CreateEncryptor 方法取得）
4. 建立一個 Stream ，用來讀取或寫入資料。
5. 使用 Stream 和 [ICryptoTransform](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.icryptotransform.aspx) 建立 [CryptoStream](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.cryptostream.aspx) 。
6. 由 [CryptoStream](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.cryptostream.aspx) 讀取或寫入資料

先簡單認識一下 [CryptoStream](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.cryptostream.aspx) 類別
```c#
public CryptoStream(
Stream stream,                      //要在其上執行密碼編譯轉換的資料流。
ICryptoTransform transform,         //要在資料流上執行的密碼編譯轉換。
CryptoStreamMode mode               //其中一個 CryptoStreamMode 值
)
```
```c#
public override void Write(
byte[] buffer,
int offset,
int count
)
```
```c#
public override void Read(
byte[] buffer,
int offset,
int count
)
```

資料加密範例：
```c#
public static byte[] Encrypt(byte[] pPlainText, byte[] pKEY, byte[] pIV)
{
//1. 建立對稱加密演算法物件
DESCryptoServiceProvider providerDES = new DESCryptoServiceProvider();

//2. 指定演算法的 key & IV 值
//由參數傳入

//3. 呼叫 CreateEncryptor ，建立 ICryptoTransform 加密子物件
ICryptoTransform transform = providerDES.CreateEncryptor(pKEY, pIV);

//4. 建立執行密碼編譯轉換的資料流
MemoryStream memory_stream = new MemoryStream();

//5. 建立 CryptoStream 物件： ( stream, ICryptoTransform, R/W )
CryptoStream csEncrypt = new CryptoStream( memory_stream, transform, CryptoStreamMode.Write);

//6: 將資料由 buffer 寫到 CryptoStream 資料流
csEncrypt.Write(pPlainText, 0, pPlainText.Length);    //將資料由 PlainText 寫到 CryptoStream 資料流
csEncrypt.FlushFinalBlock();

csEncrypt.Close();
memory_stream.Close();

return msEncrypt.ToArray();
}
```

資料解密步驟，就依加密過程反向操作即可：
```c#
public static byte[] Decrypt(byte[] pCypherText, byte[] pKEY, byte[] pIV)
{
//1. 建立 AES 對稱加解密演算法物件
AesCryptoServiceProvider providerAES = new AesCryptoServiceProvider();

//2. 建立加密子物件。  AES 加解密過程中需要兩個位元組陣列（Key 及 IV），而且必須符合規定的大小
ICryptoTransform transform = providerAES.CreateDecryptor(pKEY, pIV);

//3: 建立執行密碼編譯轉換的資料流
MemoryStream memory_stream = new MemoryStream(pCypherText);

//4: 建立 CryptoStream 物件
CryptoStream crypto_stream = new CryptoStream(memory_stream, transform, CryptoStreamMode.Read);

//5: 將資料由 CryptoStream 寫到 buffer
byte[] buffer = new byte[pCypherText.Length];
crypto_stream.Read(buffer, 0, pCypherText.Length);

crypto_stream.Close();
memory_stream.Close();

return buffer;
}
```

下面範例，以 UTF8 的編碼方式，將文字轉成位元組陣列，以進行加密。
```c#
private void btnDES_Encrypt_Click(object sender, EventArgs e)
{
// 將明文資料以 UTF8 編碼方式，轉成相對等的 byte[]
byte[] buffer = Encoding.UTF8.GetBytes(txtSource.Text);

// 將 KEY 和 IV 以 UTF8 編碼方式，轉成相對等的 byte[]
byte[] byteKey = Encoding.UTF8.GetBytes(txtSecretKey.Text);
byte[] byteIV = Encoding.UTF8.GetBytes(txtIVector.Text);

// 將資料加密
byte[] returnVal = myDES.Encrypt(buffer, byteKey, byteIV);

// 將加密後的資料以 Base64 編碼方式，轉成相對等的字串
txtDES_CipherText.Text = Convert.ToBase64String(returnVal);     //f/tTWokcXA8RIWUjk8LCFw==
}

private void btnDES_Decrypt_Click(object sender, EventArgs e)
{
// 將密文資料，由 Base64 編碼的格式，轉成相對等的 byte[]
byte[] cipher = Convert.FromBase64String(txtDES_CipherText.Text);

// 將 KEY 和 IV 以 UTF8 編碼方式，轉成相對等的 byte[]
byte[] byteKey = Encoding.UTF8.GetBytes(txtSecretKey.Text);
byte[] byteIV = Encoding.UTF8.GetBytes(txtIVector.Text);

// 將資料解密
byte[] returnVal = myDES.Decrypt(cipher, byteKey, byteIV);

// 將解密後的資料以 UTF8 編碼方式，轉成明文輸出
string value = Encoding.UTF8.GetString(returnVal);
txtDES_PlainText.Text = value.Replace("\0", "");   //去掉多的\0。
}
```

上面範例，在 Decrypt() 方法中，我們建立一個同密文大小的 buffer ，用來存放解密後的資料。  因為加密後的文件通常比原始文件大一些，所以這個 buffer 通常就會比解密後的資料還大一些，所以使用上沒有問題，而且後面還會剩下一些多餘空間。  另外，我們也可以將解密後的資料直接輸出到 MemoryStream ，就沒有這個問題。方法如下：  
```c#
// Cipher Data
byte[] bCipherText = Convert.FromBase64String(sCipherText);

// KEY & IV
byte[] byteKey = Convert.FromBase64String("LuJ+rn5JBVI8YGM1wgCn6TLDqnpD4qhA8KflTP0m/fQ=");
byte[] byteIV = Convert.FromBase64String("3JqD0OWQRF3UuyVZvCdMEA==");

//1. 建立 AES 對稱加解密演算法物件
AesCryptoServiceProvider aesAlgrithm = new AesCryptoServiceProvider();

//2. 建立解密子物件。
ICryptoTransform transform = aesAlgrithm.CreateDecryptor(byteKey, byteIV);

//3. 建立一個 Stream , 用來存放加密資料用的資料流
MemoryStream memory_stream = new MemoryStream();

//4. 建立 CryptoStream 物件
CryptoStream crypto_stream = new CryptoStream(memory_stream, transform, CryptoStreamMode.Write);

//5. 將 buffer 中的資料解密後，透過CryptoStraem，寫到MemoryStream
crypto_stream.Write(bCipherText, 0, bCipherText.Length);
crypto_stream.FlushFinalBlock();

crypto_stream.Close();
memory_stream.Close();

string sPlainText = Encoding.UTF8.GetString(memory_stream.ToArray());
```

#### 補充１：TransformFinalBlock

前面加密或解密過程中，必須建立一個 MemoryStream 來搭配 [CryptoStream](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.cryptostream.aspx) ，用以讀取或寫入加密資料。  也可以直接使用 [ICryptoTransform.TransformFinalBlock](http://msdn.microsoft.com/zh-tw/library/system.security.cryptography.icryptotransform.transformfinalblock.aspx) 方法進行加密操作，  把這個步驟交由演算法內部自行處理，其寫法會比較簡單一點，各種演算法也都適用，以下以 AES 演算法做為範例：
```c#
public static byte[] Encrypt(byte[] pPlainText, byte[] pKEY, byte[] pIV)
{
//1. 建立 AES 演算法物件
AesCryptoServiceProvider alg = new AesCryptoServiceProvider();

//2. 建立加密子物件
ICryptoTransform transform = alg.CreateEncryptor(pKEY, pIV);

//3. 轉換所指定位元組陣列的指定區域
byte[] outputData = transform.TransformFinalBlock(pPlainText, 0, pPlainText.Length);

return outputData;
}
```
```c#
public static byte[] Decrypt(byte[] pCypherText, byte[] pKEY, byte[] pIV)
{
//1. 建立 AES 演算法物件
AesCryptoServiceProvider providerAES = new AesCryptoServiceProvider();

//2. 建立解密子物件
ICryptoTransform transform = providerAES.CreateDecryptor(pKEY, pIV);

//3. 轉換所指定位元組陣列的指定區域
byte[] outputData = transform.TransformFinalBlock(pCypherText, 0, pCypherText.Length);

return outputData;
}
```

#### 補充２：byte array to string

前面有提過，加密與解密的操作，都是針對位元陣列 byte[] 格式，所以加密前，要先將資料轉成 byte[]。  而上面範例中的程式碼區塊，只是在表達資料的加密與解密過程，所以，傳入的參數與回傳值的資料型態都是 byte[]。  加密後的資料當然也是 byte[] 格式，不過實際運作時，為了考量顯示的方便性，常會使用編碼原則，將 byte[] 再轉字串。例如：  

- [System.Text.Encoding.Unicode.GetBytes](http://msdn.microsoft.com/zh-tw/library/system.text.unicodeencoding.getbytes) ：string to byte[]。
- [System.Text.Encoding.Unicode.GetString](http://msdn.microsoft.com/zh-tw/library/system.text.unicodeencoding.getstring) ：byte[] to string。
- [Convert.ToBase64String](http://msdn.microsoft.com/zh-tw/library/zx2800z8) ：將 byte[] 轉換為使用 Base-64 編碼原則的字串。
- [BitConverter.ToString](http://msdn.microsoft.com/zh-tw/library/5ccbbf63) ：將 byte[] 轉換成對等的十六進位字串。
```c#
// returnVal 是加密後的 byte[]

//將 byte[] 轉成字串
txtDES_CipherText.Text = System.Text.Encoding.Unicode.GetString(returnVal); //ﭿ婓Ᲊཛྷℑ⍥슓ែ

//將 byte[] 轉換為使用 Base-64 編碼原則的字串
txtDES_CipherText.Text = Convert.ToBase64String(returnVal);     //f/tTWokcXA8RIWUjk8LCFw==

//將 byte[] 轉換成對等的十六進位字串
txtDES_CipherText.Text = BitConverter.ToString(returnVal);
//7F-FB-53-5A-89-1C-5C-0F-11-21-65-23-93-C2-C2-17

//將 byte[] 轉換成對等的十六進位字串(不帶'-'符號)
StringBuilder NewHashCode = new StringBuilder(returnVal.Length);
foreach (byte value in returnVal)
{
NewHashCode.AppendFormat("{0:X2}", value);
}
txtDES_CipherText.Text = NewHashCode.ToString();
//7FFB535A891C5C0F1121652393C2C217
```


- 
- 
- [逐步解說：建立和匯出 RSA 金鑰容器](http://msdn.microsoft.com/zh-tw/library/2w117ede.aspx)