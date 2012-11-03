## Rails 開發要點


### [[Ruby Syntax| rails-convention-ruby-syntax]]

### [[Ruby in Rails | rails-convention-ruby-in-rails]]

### [[Rails Convention About View| rails-convention-about-view]]

### [[Rails Convention About Asset| rails-convention-about-asset]]

### [[Rails Convention About Helper| rails-convention-about-helper]]

### [[Rails Convention About Controller| rails-convention-about-controller]]

## About Controller


##  About Model

* model 吐出去的東西只能是純天然物質，如 字串、陣列、Hash 等。如果是製作 select option 需要塞字串，請至 helper 加料，嚴禁在 model 內加料。
* many to many 的 named_scope 如果有 order => "xxx DESC" 會造成 join 的 table scan ，解法是拆 query。
* 巢狀結構(同一 table 多對多），要用awesome_nested_set。
 -  script/plugin install git://github.com/collectiveidea/awesome_nested_set.git
 - 這裡有簡單的說明：http://yearl.javaeye.com/blog/500929

* self的使用
  - 狀況 1. Class method
```ruby
# controller

@posts = Post.first_post # Post is a Class

# Post model 

def self.hidden # 要加 self
   first
end
```
  - 狀況 2. Instance method 
```ruby
# controller

@post = Post.first
@post.is_published? # @post is a Instance

# Post model 

def is_published? # 不加 self
   aasm_state == 'published' && published_at <= Time.zone.now
end
```
 - 狀況 3. assignment 
```ruby
# Post model 

def set_time
   self.published_at = Time.zone.now # assign value to attribute, 一定要加self
end
```
 - 狀況 4. access 
```ruby
# Post model 

def set_time
   published_at # access attribute, 統一不加self
end

```

#### method 的放置順序

```ruby
# 一律先把需要的 lib 都加入
require "nokogiri"

class Post < ActiveRecord::Base
  # 先把需要的 module 都加入
  include Techbang::Goodies

  # 宣告 class accessor、類別變數、常數
  cattr_reader :per_page
  @@per_page = 10
  DEFAULT_AUTHOR = "you"

  # 宣告 instance accessor、序列化的資料欄位、可以存取的欄位
  attr_reader :headline_image
  serialize :exif
  attr_accessible :title, :excerpt, :description

  # 宣告各式 validation 和 validation filter
  validates_presence_of :title, :published_at
  before_validation :ensure_title

  # 宣告 has_many、belongs_to、has_and_belongs_to_many 等 association 關係
  belongs_to :user, :counter_cache => true
  has_many :comments, :as => :resource

  # 如果有 delegate 則寫在那個 association 下
  has_one :profile
  accepts_nested_attributes_for :profile
  delegate :resume, :blog_url, :facebook_url, :plurk_url, :twitter_url, :to => :profile

  # 宣告 gem 的 class method，通常我會把這個 gem 相關的 method 放一起
  has_attached_file :image
  validates_attachment_content_type :image
  validates_attachment_size :image
  after_image_post_process  :get_exif
  delegate :url, :to => :image

  acts_as_taggable_on :tags
  chinese_permalink :title

  define_index do
    indexes title
    indexes description
  end

  # 宣告各式 filter/callback
  before_save :build_profile, :if => "profile.nil?"
  after_save :save_profile

  # 宣告各式 scope 定義
  scope :recent, lambda { |amount| order(""published_at DESC"").limit(amount || 5) }

  # 宣告 class method
  def self.published(post_types=[self.to_s])
    where(:type => post_types).where(:aasm_state => "published").where(["published_at <= ?", Time.zone.now]).order("published_at DESC")
  end

  # 宣告 instance method
  def is_published?
    aasm_state == "published" && published_at <= Time.zone.now
  end

end
```

###  About Time 時區的細節

* 在 config/enviroment.rb 裡設定 config.time_zone = 'Taipei'
但是資料庫儲存的是 UTC 的時間
如果有需要用 conditions 下 SQL 語法撈資料
請檢查是否必須用 Time.now.utc 避免出錯

### About Mac

* TextMate
  - 可以用 alt 按住做區域圈選， 搭配 # 鍵一次註解多行。
  - 要在editor 的左邊顯示行號 請按 Option+Apple+L
  - tab 要設為 Soft Tabs: 2
  - 一些 trick 的介紹網站 http://wiki.macromates.com/Main/Howtos
  - 推薦 how to exclude files and foleders from project drawer 把 public/system加進去，就不會因為圖片太多因素導致開很慢。

