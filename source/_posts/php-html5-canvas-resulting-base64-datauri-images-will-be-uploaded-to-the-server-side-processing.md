---
layout: post
title: '[PHP] 將 HTML5 Canvas 產生的圖片上傳至伺服器端處理'
date: 2013-09-08 12:00
comments: true
categories: [HTML]
tags: [html, html5, canvas, php]
---
一般來說有兩種處理方式：

## 直接把 DataURI 字串儲存起來，等要用的時候直接輸出至 `<img>` 的 src 屬性或是 CSS 中：

  像這樣：

 ``` html
  <img src="<?php echo $base64_img_string; ?>" />
 ```

  實際執行的時候會長得像這樣：

``` html
  <img src="data:image/png;base64,......(後略)" />
```

  * 優點：
    簡單易用，幾乎不需要做任何處理。
    HTTP Request 變少，傳送一個大檔案會比連續傳送多個拆解的小檔更快速有效率，且節省頻寬。


  * 缺點：
    IE7 以前的版本不支援 DataURI 格式。 IE8 開始雖然有支援，但限制大小不可超過 32KB
    圖檔大的時候產生的 DataURI String 大得驚人(比原本的圖檔還大約 1/3)
    圖檔修改後必須要重新編碼，相對的嵌入 DataURI String 的網頁也都要跟著修正
    因圖片跟網頁綁再一起，不利快取

## 將 base64 DataURI 送到 PHP 端儲存成圖片後使用：

  如標題，應該不需要多加解釋了，就是利用 `base64_decode()` 將 data uri 反解，
  直接看 code。 PHP 的部分：

``` php
<?php
  // 設定圖檔上傳路徑
  define('UPLOAD_PATH', 'images/');

  // 接收 POST 進來的 base64 DtatURI String
  $img = $_POST['data'];

  // 轉檔 & 存檔
  $img = str_replace('data:image/png;base64,', '', $img);
  $img = str_replace(' ', '+', $img);
  $data = base64_decode($img);
  $file = UPLOAD_PATH . uniqid() . '.png';
  $success = file_put_contents($file, $data);

  // output string
  $output = ($success) ? '<img src="'. $file .'" alt="Canvas Image" />' : '<p>Unable to save the file.</p>';
```

  View 的部分 (HTML)：

``` html
<!DOCTYPE HTML>
<html>
  <head>
    <meta charset="UTF-8">
  <title>PHP base64 image decode demo</title>
  </head>
  <body>
    <!-- 成功存檔的話會秀出 img 標籤以及圖檔，失敗的話會出現 Unable to save the file 的訊息 -->
    <?php print $output; ?>
  </body>
</html>
```
這樣就完成了。