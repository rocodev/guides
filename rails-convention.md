## Rails 開發要點


### [[Ruby Syntax| rails-convention-ruby-syntax]]

### [[Ruby in Rails | rails-convention-ruby-in-rails]]

### [[Rails Convention About View| rails-convention-about-view]]

### [[Rails Convention About Asset| rails-convention-about-asset]]

### [[Rails Convention About Helper| rails-convention-about-helper]]

### [[Rails Convention About Controller| rails-convention-about-controller]]


###  About Time 時區的細節

* 在 config/enviroment.rb 裡設定 config.time_zone = 'Taipei'

但是資料庫儲存的是 UTC 的時間，如果有需要用 conditions 下 SQL 語法撈資料，請檢查是否必須用 Time.now.utc 避免出錯。

### About Mac

* TextMate

  - 可以用 `alt` 按住做區域圈選， 搭配 `#` 鍵一次註解多行。
  - 要在editor 的左邊顯示行號 請按 `Option+Apple+L`
  - tab 要設為 Soft Tabs: 2
  - 一些 trick 的介紹網站 http://wiki.macromates.com/Main/Howtos
  - 推薦 how to exclude files and foleders from project drawer 把 public/system加進去，就不會因為圖片太多因素導致開很慢。

