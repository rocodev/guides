## Rails on Mac 安裝最佳實務

`強烈警告：請絕對不要跳著裝！如果疏漏步驟有可能導致無法復原需要重灌。`

## 系統套件

1. 進行 Mac 系統更新
2. 從 Apple Store 上取得 Xcode 4.4 安裝
3. `Xcode -> Preferences -> Downloads tab then install the “Command Line Tools.” ` 裝完請重開機（保險起見）。

## 安裝 Homebrew
  
```
   $ /usr/bin/ruby -e "$(/usr/bin/curl -fsSL https://raw.github.com/mxcl/homebrew/master/Library/Contributions/install_homebrew.rb)"
   $ brew install git
   $ brew update
```

```
brew tap homebrew/dupes
brew install apple-gcc42
```

## 安裝 XQuartz

安裝 ImageMagick 需先有 X11 的 support，OSX 10.8 拿掉了...

http://xquartz.macosforge.org/landing


###  ImageMagick / MySQL

* 安裝 Imagemagick

```
   $ brew install imagemagick
```

*  安裝 MySQL

```
$ brew install mysql
$ unset TMPDIR
$ mysql_install_db --verbose --user=`whoami` --basedir="$(brew --prefix mysql)" --datadir=/usr/local/var/mysql --tmpdir=/tmp
$ mysql.server start
$ mysqladmin -u root password '123456'
$ mkdir -p ~/Library/LaunchAgents
$ find /usr/local/Cellar/mysql/ -name "homebrew.mxcl.mysql.plist" -exec cp {} ~/Library/LaunchAgents/ \;
$ launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist

```

若無法順利進行安裝，可換下載 [MySQL官網](http://dev.mysql.com/downloads/mysql/) 上的 Mac OS X ver. 10.6 (x86, 64-bit), DMG Archive 來安裝 MySQL。
  

### 安裝 RVM 與 Ruby 1.9.3

在建制 Rails 環境的時候，我們可能會有跑不同版本的 Ruby 或者不同的 getsemt 的需求。[Ruby Version Manager](https://rvm.beginrescueend.com/) 是一個能夠讓我們用很優雅的方式切換 Ruby 版本的工具。同時使用系統 Ruby，其實很容易弄髒環境和產生一些靈異現象的 bug。於是我們在建制環境時，通常第一時間就會裝起 RVM。

#### 安裝 RVM

```
$ bash -s stable < <(curl -s https://raw.github.com/wayneeseguin/rvm/master/binscripts/rvm-installer)
$ . ~/.profile
$ source ~/.profile
```
    
#### 安裝 Ruby 1.9.3

```
$ rvm install 1.9.3 --with-gcc=clang
$ rvm 1.9.3 --default 

```

#### 注意

使用 RVM 安裝 gem 和 passenger-install-apache2-module 不需要加上 sudo , 因為使用 sudo 會使用非 RVM 的 ruby 環境, 安裝目錄也不一樣.) 
    

### 安裝必要 Ruby gems

```
$ gem install rails
$ gem install mysql2
$ gem install capistrano
$ gem install capistrano-ext
$ gem install delayed_job
$ gem install hoptoad_notifier
$ gem install facebooker2
$ gem install factory_girl
```

## 設定 HTTP Server

### 使用 Pow

#### 使用 Pow 作為 HTTP Server

[Pow](http://pow.cx) 是 [37 Signals](http://37signals.com/) open-source 出來的一套 Rack Server。其標榜的就是 Zero Config。

Pow 的原理原理是攔截 routing，導到 Pow 上。所以新增 project 不需要更改 /etc/hosts 就會生效。也因為 Pow 是 rack-based，支援 [rack](http://rack.rubyforge.org/) 的 framework 掛了就能跑。


相較起來，以往的 [Passenger](http://www.modrails.com/) 搭配 Mac 本機端的 apache 的 solution 就顯得太笨重了。

#### Installation 

[Pow](http://pow.cx) 的安裝相當簡單。

```    
$ curl get.pow.cx | sh
```

即完成安裝。

#### Setting 

[Pow](http://pow.cx) 預設的目錄是在 ~/.pow 下。


因此若要讓 project 跑在 Pow 之下。以我的 wiki 為例 ：

`cd ~/.pow/`
`ln -s ~/projects/wiki`  

打開瀏覽器，輸入 http://wiki.dev 就完成了。以往的 http://localhost:3000/ 實在太噁心了，別再用它了！

#### Note

或者是直接在 project/wiki 下打 `powder link` 也可以。


#### 使用 Powder 管理 Pow

[Powder](https://github.com/Rodreegez/powder)  是後來衍生出來的一套管理工具。

因為 Pow 的管理有點不易，所有有人寫了這個工具把一些常用的功能包裝起來。

安裝方法：


`$ gem install powder`

通常我只拿來做 `powder restart` 和 `powder log` 而已。

##### 注意事項

若電腦預設非使用系統 Ruby 的開發者需注意此點。Pow 很可能會抓到系統 Ruby 及其  gemset 而無法啓動。我個人的解法是安裝 RVM 管控 Ruby，再在欲使用 Pow 之 project 目錄放置 .rvmrc 即可。

.rvmrc 內容如下：

`rvm 1.9.3`


解決使用 Pow 後，無法掛上 PHP project 的問題

請參考 [Legacy development with Pow](http://stuff-things.net/2011/05/16/legacy-development-with-pow/) 一文。原理是利用 rack-legacy 以及 rack-rewrite。
