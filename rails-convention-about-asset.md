## Rails Convention About Asset

* 應該要放在 public 資料夾下，依照 javascripts/stylesheets/images項目分類，html則依 request url 路徑置放。
* css 用到的圖片應該要放在 stylesheets/image 底下，也就是css所在當前位置的image子資料夾下。

* css 中我們有針對body上兩個指定瀏覽器才有的class類別(ie6 ,ie -> for ie7 and after )，也有針對不同controller 、action上不同的class類別，有需要針對特定版本或特定頁面設計時可多加利用。
