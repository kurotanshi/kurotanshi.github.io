---
layout: post
title: '利用 Google Fusion Table，不用寫 code 也可以產生主題地圖'
date: 2015-10-15 02:16
comments: true
categories: [Google Map]
tags: [google map, Fusion Table]
---
感謝台北市政府以及[相關人士的努力](https://www.facebook.com/photo.php?fbid=10204828212414899&set=a.1357755313713.2044236.1526408898&type=3)，在十月中旬的時候，台北市 open data 平台又開放了「[台北市住宅竊盜點位資訊](http://data.taipei/opendata/datalist/datasetMeta?oid=68785231-d6c5-47a1-b001-77eec70bec02)」這樣的資料，雖然很多人戲稱房價又要下跌了，但是老話一句，身為<del>有良心的開發者，當然居住安全比房價什麼的還要重要得多</del>。

那麼，拿到這份資料我們可以怎麼玩呢?
剛好這份資料的格式是 CSV (Comma-Separated Values，一種由逗點分隔的純文字資料格式)，所以本篇就來介紹如何透過 [Google Fusion Table](https://support.google.com/fusiontables/answer/2571232) 來讓我們不必寫任何的 code ，也可以建立主題地圖。

首先先到[台北市住宅竊盜點位資訊](http://data.taipei/opendata/datalist/datasetMeta?oid=68785231-d6c5-47a1-b001-77eec70bec02)，點一下「使用資料」將所需的檔案下載下來，會得到一份 csv 檔案。

![螢幕快照 2015-10-15 上午10.16.22.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/UsvTpRmSDWhtT8junGNw_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.16.22.png)

接著打開 [Google Fusion Table](https://support.google.com/fusiontables/answer/2571232) 的頁面，看到下面這個畫面：
![螢幕快照 2015-10-15 上午10.33.31.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/vFIqF2IuSOuhKhR0mKUt_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.33.31.png)

接著選擇「CREATE A FUSION TABLE」，會看到這樣的畫面：
![螢幕快照 2015-10-15 上午10.37.48.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/QrsLmP03QNamaW0qrqjT_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.37.48.png)

現在試著把剛剛的 csv 直接餵給他，看看會發生什麼事
![螢幕快照 2015-10-15 上午10.05.47.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/Wf4cjXTQTrCXlw0UDWMl_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.05.47.png)

如果不意外，你應該會得到像這樣的亂碼資料 XDDDDD
![螢幕快照 2015-10-15 上午10.06.19.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/AUqRTPyFQX29z5TdLGaM_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.06.19.png)

不過沒關係，山不轉路轉，檔案格式當然也可以轉。只是需要多走幾步路。
這次我們將原始的 csv 資料先丟到 [Google Sheet](http://www.google.com/intl/zh-TW_tw/sheets/about/) 內，請他將我們的資料轉成正確的編碼。 在建立一個新的 Google Sheet 之後，我們選擇 File > Import 將剛剛的 csv 檔案匯入：
![螢幕快照 2015-10-15 上午10.07.40.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/fNpq4j4rTEuynwQAcxOO_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.07.40.png)

記得一樣要選擇 Comma，因為是透過逗點分隔的資料格式。
![螢幕快照 2015-10-15 上午10.08.07.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/bbUWtz7RXqdpZsizK5Lw_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.08.07.png)

然後就會得到正確編碼後的資料囉。<del>不過糖廍里一樣 GG...因為原始資料就 GG 了... orz</del>
![未命名.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/wYMPpnlHTRmrgoywrKGc_%E6%9C%AA%E5%91%BD%E5%90%8D.png)

ok, 到了這裡我們已經有一份正確的資料，這時再回到 Funsion Table，我們選擇 「Google Spreadsheets」，然後將剛剛建立的 Google Sheet 匯入進來:
![螢幕快照 2015-10-15 上午10.37.48.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/QrsLmP03QNamaW0qrqjT_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.37.48.png)

![螢幕快照 2015-10-15 上午10.11.18.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/66zfnteRQy6L50MJQUZ1_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.11.18.png)

匯入成功後，Funsion Table 會出現這樣的畫面：
![未命名.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/pDQ7qI4WSDub29jPiglU_%E6%9C%AA%E5%91%BD%E5%90%8D.png)

這個時候，因為我們要製作地圖，所以要告訴 Funsion Table 地點的欄位不是單純的字串，而是用來表示地理資訊的資料。點選 Edit > Change columns：
![螢幕快照 2015-10-15 上午10.11.57.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/thibCn9zRMO435hQgjFd_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.11.57.png)

然後將發生地點的 Type 改成 Location，然後點上面藍色的 Save 儲存。
![螢幕快照 2015-10-15 上午10.12.09.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/TK4RPNQtQNiXcQBclDeu_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.12.09.png)

資料都準備好了，然後我們建立地圖：
![螢幕快照 2015-10-15 上午10.58.53.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/csg65yBiQIyKcj1PAnNk_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.58.53.png)

不意外的話你應該會看到這樣的畫面，是因為我們傳入的是地址的文字資訊， Google 需要將它轉為經緯度後才能對應到地圖上：
![螢幕快照 2015-10-15 上午10.13.03.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/JJNbZ5DRRmehqBdoDytm_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.13.03.png)

然後等待一段時間後，Funsion Table 就會為我們產生地圖，像這樣：
![螢幕快照 2015-10-15 上午10.20.02.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/7cjQA7USzGy7nGrQRLDr_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8810.20.02.png)

除了地點標示外，他也提供了熱圖 (Heat Map) 的呈現，試著拉拉旁邊的捲軸調整參數吧：
![螢幕快照 2015-10-15 上午11.03.53.png](http://user-image.logdown.io/user/3292/blog/3340/post/304787/wMQb7zI8R72jFxu7a3g4_%E8%9E%A2%E5%B9%95%E5%BF%AB%E7%85%A7%202015-10-15%20%E4%B8%8A%E5%8D%8811.03.53.png)


當然你也可以做好的地圖 share 出來，像這樣：
<iframe width="800" height="400" scrolling="no" frameborder="no" src="https://www.google.com/fusiontables/embedviz?q=select+col3+from+1sAGtul5gWv1_yRpPTYI1ZvPaHQRPDod-B0MbOeS_&amp;viz=MAP&amp;h=false&amp;lat=25.05814459656792&amp;lng=121.63429174086912&amp;t=1&amp;z=12&amp;l=col3&amp;y=3&amp;tmplt=4&amp;hml=GEOCODABLE"></iframe>

透過 Fusion Table 我們可以不用寫任何的程式碼就生成一份資訊地圖，很簡單吧 :)