---
title: IIS設定
layout: default
parent: IIS
nav_order: 1
description: "IIS設定"
date: 2012-04-19
tags: [MISC,IIS]
postid: "8910016036252370432"
---
# 網站、應用程式、虛擬目錄

#### 網站與應用程式

在IIS中，當你建立一個網站，它本身就是一個應用程式，這個應用程式又稱根應用程式或預設應用程式。  在該網站中，你可以繼續加入其他應用程式（有子網站的意味），網站中的應用程式，和虛擬目錄一樣，並沒有限制必須位於網站的實體目錄底下，所以你可以將新增的應用程式指向網站根目錄以外的實體位置。  每一個應用程式都可以設定自已的組態檔，也可以指定自已的應用程式集區（application pool），各應用程式也就透過不同的集區來達到彼此隔離的效果（一個應用程式當掉不至於影響另一集區中的應用程式）。  

建立新網站

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgdKBusYmCCEv-6bmagnn3IWcC4KnzMmJxo-UsK97IqdUfNmdv74amkO9nk_vFFm0XgHWBfRulJbNaGWLkN5hAPMMhTyndQMAMT50xGwTmZayLVYaWF5joY4avlgtnaztZN8V7zUCdJCXY/s284/iis-setting-1.png)

在網站中加入其他應用程式

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhNxYwtJhorlloQ1c8QNr6JB3v0iKDv0esge5ONkM7g9VveKBCtz3jbpRPz1imlpaIrrWJfnV1BPlMFTqiG2mCDXJ1T4YscRJbEM6hyphenhyphenLUyr0iA0msbF1J9r5QIs-GcxCFr5uQL0plnrRLM/s362/iis-setting-2.png)

也可以將某個子目錄轉為應用程式

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiKR7hyphenhyphenEvKrHsu5fPZ2_c3NF5Wfv8U_fSUIYzezthUToA5zd8b28FD4hpYpo7CiD1-m3cswPGtJZ3Rat2ZrplrBCslROu_ys0laSc76mNfFBYrBzoGvEUdv6AdePsh85ktcgzfe4Eo4MJs/s288/iis-setting-3.png)

這是網站底下應用程式的樣子

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjwVo0Qwe0fJzHaM1gEO8w1n0yMwA5sDsQU6hr0TR9Su6YUhp8xfBViJ_FONK328xAgb_7xBkvsnaskQliRJUKgFtV66641pDFREp835vCXgUvWHqnR3BNfPyZ15TD2Mg09kbERDVjH_m4/s286/iis-setting-4.png)

#### 虛擬目錄

你可以在網站中，建立虛擬目錄，它沒有限制必須在網站的實體目錄中，所以你也可以將虛擬目錄指向網站根目錄以外的位置。  但是對用戶端而言，虛擬目錄就好像包含在 Web 伺服器的根目錄中一樣。   

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi-kji7fDAU1eV_2eYIhZ5OUwiyUi_EiT7O8n5xAVrlCt15NWtOprM9w6nY54EaaNdmRg0NVx7T5n74RcZyhU_zMCTdHbavfzj1HOT6YJSGvfNlwo1GjfDUuJp-L-subaoXcAVir-wnHYY/s281/iis-setting-5.png)

#### 應用程式 vs 虛擬目錄

- 同一個網站裡的所有應用程式都使用相同的埠，所以你無法在子應用程式中指定其他的埠。
- 所有的應用程式，都可以設定自已的應用程式集區和組態設定檔，所以不同的應用程式允許不同的.NET版本。
- 子應用程式或虛擬目錄，其實體位置都可以不用在根應用程式之下。
- 虛擬目錄的連結網址，一定由根網站的URL開始。
- 子應用程式的連結網址，若沒有特別設定，也是由根網站的URL開始。

，但其URL都是相同的，必須由網站的URL開始

# 基本設定

在 IIS 7 的「基本設定」中，主要有下列設定項目：

- 網站的實體路徑。
- 應用程式集區。
- 連線身分。

## 如何變更站台的應用程式集區

1. 開啟 IIS 管理員。 如需開啟 IIS 管理員的資訊，請參閱開啟 IIS 管理員 (IIS 7)。 如需巡覽至 UI 中之位置的相關資訊，請參閱 IIS 管理員中的瀏覽 (IIS 7)。
2. 在 [連線] 窗格的樹狀目錄中，展開 [站台] 節點，然後按一下以選取您要變更其應用程式集區的站台。
3. 在 [動作] 窗格中，按一下 [基本設定] 以開啟 [編輯站台] 對話方塊。
4. 按一下 [選取] 以開啟 [選取應用程式集區] 對話方塊，然後從 [應用程式集區] 清單選取應用程式集區，並按一下 [確定]。
5. 在 [編輯站台] 對話方塊中，按一下 [確定]。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhTUZysqYKX9-uB0n1AOVLhOAm73wo2scYWKyuMdGKxTfMyhGj3yQBSxmMO5_rRwFhqAZXKJbRoONTnm128txgGTRKWS1XwKZRnTkAFROayuu1gVaxIUuwn3MQS2wswvYAhA9L_4jgrEW8/s850/AppPool_Change.png)

# 進階設定

在 IIS 7 的「進階設定」，幾本上和「基本設定」沒多大差別。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi2SlFRYIK0Xbqj3tVE9QeHvy8z6J-6AWDwcQ7cBX4n65OcQSGm_PDpS4FLOLc52CQMFIXpC0dkPy0cKw5QSlvDhe9pKbENUcjkRoH88_p85Ep59Avbfw4-6DRNaa-9OcSRqrrqz0GfXbY/s598/iis-adv-setting.png)

# 繫結設定

若要設定「連接埠」或「IP位址」，則由[編輯站台]→[繫結...]

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhMwO7g1Rtd9JfzQ7AzshrmqkIopS58ML_atGpOK1IQJNIww03co-QuBchEqLVi-AkrTLhnwPQ2apJ1SKJiRwaS42fSklb9r9PBM6Zk8w593hiamwsZm5txbj0bGHkKeNr6RknX6gjPUds/s236/iis-binding-setting-2.png)

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgpK4ZeW99YH0YELSumdmUQDKBzn59f2-ruUM8h9S_lM-EJJKVNXS_u5Gc26FNoircQdEPjIIRWwtsSxW1hqVdGxFSXpN4R3uXp7Y3aTpxxYWSLr_rmn44tRkAtG-4xevUX2T68L9wTLd8/s587/iis-binding-setting.png)

# 防火牆設定

控制台\系統及安全性\Windows 防火牆

[進階設定]→[輸入規則]→[新增規則]

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjVtUZXWyBuq2LTlVddk4hfw9sXGlnwVJ8i-S-nwlfZpcvQ9G2yfEywiW8JlfwhUrF36pFfYy2c_5pSlm81ewQ1NBlkt-4Tn-KXw5osouZwiqGTgA7tnWi3PA3jYaip3BrvYwC0cRhfLzc/s458/iis-firewall.png)

# 虛擬主機設定

「虛擬主機」，簡單的講，就是在一台實體主機中，同時架設多個網站。  在IIS中，每個 Web 網站都可以由以下三個要素，構成一組唯一的識別，用來接收與回應請求：  

- IP位址
- 連接埠
- [主機標頭名稱](http://technet.microsoft.com/zh-tw/library/cc753195%28WS.10%29.aspx)（Host Header）：主機標頭也稱為網域名稱或主機名稱。

所以，若只有一個 IP 位址，要同時架設多個網站，當然就是利用其他二個要素來加以識別。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCPbc9H24PQ1dGmKHVSyjdsWXyhWezdztdogZHzxhxBOT2krtcDylyap821lA1kr3giW8d9d0RfNFMLLfC0r-Q5WHGUYdKSo4ZeJPgepjPQVB1OZOzoWA3duYPhnUpUJ5DrMkFfdpPClw/s174/iis-setting-9.png)

## 設定連接埠

要在一台實體主機中，建立多個站台，最簡單的方式，每個讓每個站台使用不同的連接埠。

在網站的繫結設定中，使用不同的連接埠。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiEDNu2rNQJiAkDx-vxg9kte0xyiz4JeEHeiYzciqECpj3hV6EUUPKubCCIuWEJW28EDIrHsqum6uI1oFWiyqfR9uLFGoJfL9MztRDncF08Yw-ALnbFYMmQvbzddObd7kyWpCieY9x3tk8/s850/iis-setting-6.png)

這樣你就可以使用以下方式來連結網站
http://169.254.115.20:80/
http://169.254.115.20:81/  
如果 169.254.115.20 對應到 domain：vito.com.tw ，就可以使用以下方式來連結網站
http://vito.com.tw:80/
http://vito.com.tw:81/

## 設定主機標頭

### 相同域名

相同域名的意思是說，你只有一個 domain name，  你希望這個 domain 同時可以給多個 web site 共同使用，且它們要使用相同的連接埠，如以下連結方式：  
http://site1.vito.com.tw/
http://site2.vito.com.tw/  
要達到這個目的，你只要在網站的繫結設定中，使用不同的主機名稱。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMI0esvDbh9xsArexZtcQAQE7Rjr_FyCjKfkAB4Z4V576vPh6r69_DEI7_86vjjRvrS-k4lDkGRnSzGMpKMgzRIJu9Iu0SpgLRjwFn8amxrfvmn64dJRF7m5g1054IsKx3nYJFuaCUPb0/s850/iis-setting-12.png)

### 不同域名

不同域名的意思是說，你有多個 domain name，  你希望每個 domain 都可以對應到一個 web site ，且它們都共用同一台主機，如以下連結方式：  
http://www.vito1.com.tw/
http://www.vito2.com.tw/  
在網站的繫結設定中，使用不同的主機名稱。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgXomDaLxpl5CEjynFiX-dYIF9Wlux2Y43VOO38Kx_i9WmfYd4FDG13jOZaK73IJnwFeD7GpOxRuoJvVibFxQHbabY_6UEqvxnGu0ZzzHGhgQjqnSGDsIWwwFM63N6uQafd5p0C5Dtanpw/s850/iis-setting-7.png)

設定完成後的畫面。

![](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgEwx5pPN8AHpL0UsPNjPReETAjosjXQpjCVeaYqA7WhLw2WiYUug1Xbek7Jv47RLp4R_oulAXlGWqdqESWuWvOZ7-bY75Q2g9n9WN4aYhi9JyKMc3cPbngVcB0wf-1V9LTJNbGAB5BBYg/s850/iis-setting-8.png)

使用這個設定方式，在完成以下設定後，你還必須在 DNS 中，將這些網域名稱（vito1.com.tw 和 vito2.com.tw）都指向同一個IP位址。  設定完成，這二個網站，將具有相同的 IP 位址，相同的連接埠，當需求送達時，IIS 就可以依據主機標頭名稱加以識別，以連接到正確的網站。

## 設定應用程式

在最前頭我們有提到，在同一個網站下，可以加入多個設定應用程式，例如：  

![](&lt;a target=)http://127.0.0.1:8080/\_images/cs/iis-setting-10.png" alt="" /&gt;

![](&lt;a target=)http://127.0.0.1:8080/\_images/cs/iis-setting-11.png" alt="" /&gt;

若這二個應用程式的網站名稱分別是 ChildSite1 與 ChildSite2 ，則其預設的連結網站如下：  
http://vito1.com.tw/ChildSite1
http://vito1.com.tw/ChildSite2  
你可以在 DNS Server 中，透過 CNAME Record 的設定，將網站名稱（ChildSite1 與 ChildSite2）對應到網域名稱（vito1.com.tw）。  設定完成之後，就可以使用以下連結樣式：  
http://ChildSite1.vito1.com.tw/
http://ChildSite2.vito1.com.tw/  
也就是說，若你輸入 http://ChildSite1.vito1.com.tw/ ，它其實是連結到 http://vito1.com.tw/ChildSite1 。  所以利用這個方式，也可以在一台主機下，設定多個虛擬網站。
## 參考資料  

- [Understanding Sites, Applications, and Virtual Directories on IIS 7](http://www.iis.net/learn/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis)
- [HOW TO：在 IIS 7.0 中建立和設定虛擬目錄](http://msdn.microsoft.com/zh-tw/library/bb763173%28v=vs.100%29.aspx)
- [Bind multiple sites on same IP address and Port in SSL](http://blogs.msdn.com/b/varunm/archive/2013/06/18/bind-multiple-sites-on-same-ip-address-and-port-in-ssl.aspx)