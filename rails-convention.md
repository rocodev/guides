## Rails 開發要點


### About Ruby Syntax

* 編輯器設定 soft tab (space=2)，以 2 格空白符號做為程式內縮距離（不分語言）。
* 函式如果只有一個參數，就不強制打 ()，但傳 object 進去處理時也是要有 () 
* 函式如果有二個以上的參數，通通都要有 ()   
**  (避免發生奇怪的paser bug跟保持專案一致性)
* 定義函式時一定要有 ()
* 字串限定用雙引號包覆
* 善用 "#{str1} #{str3} " 等字串改寫技巧取代不需要的字串加法。
* 變數名稱命名法需使用  my_favorite_game 這種 snake 型命名法，嚴禁駱駝 myFavoriteGame 這種命名方式。在 Ruby 中，使用駱駝法命名的只能是 Class name 或者是 Module name

* 可以用 if ，就避免用 unless ，除非 unless 較符合原意。
* map 要用 { |c| c.foo }，不要用 do end。

* [[ruby_method|基本函式介紹]]

###  About Ruby in Rails 

* 如果要判斷空字串或空集合等，請用 xxx.blank?   
** 不要用 xxx.empty? or xxx==""
* 承上點的相反情形，如果要判斷字串或集合是否有任意成員，請用 xxx.present? 
** " xxx".present? #=> true      
** ["hi","hello"].present? #=> true  
** [].present? #=> false  
* 關於elsif
** 使用elsif前先思考，處理的邏輯是否有關連性，若無請分成多個if去處理
** 若有相關性，但必須使用到elsif，思考一下是否有更magic的方式去處理(ex: https://github.com/techbang/playgame/commit/67d9da0ce00b810201fd9d4e68b7dcd4733e915e)
* 禁止使用
<pre>
<code class="ruby">
Post.find(:all, :contidions => {:id => ids}).each do |post| ...
</code>
</pre>
請改成兩行
<pre>
<code class="ruby">
@posts = Post.find(:all, :contidions => {:id => ids})
@posts.each  do |post| ...
</code>
</pre>
* 注意以下兩種寫法topic_id為nil的狀況，
<pre>
<code class="ruby">
Topic.find(topic_ids) # error
Topic.find(:all, :conditions => { :id => nil}) # []
</code>
</pre>
若要使用Topic.find(topic_ids)請排除nil狀況，否則請使用conditions去處理！

* recursive 的 method
https://gist.github.com/0b3acb50dd1554b1f937
要傳值回去的條件要擺在上面，不要擺在下面，會傳到你沒有預期的值。

* [[rails_request|request object detail]]
* 學習參考用投影片以及 application
** Rails Best Practice http://www.slideshare.net/ihower/rails-best-practices
** 禁止從 Redmine 學習寫 Rails Code，它的 code 太髒了，會造成 RD 走火入魔。要學習的話應該是 Refactoring Redmine 這本書 http://www.refactoringredmine.com/
** http://jetpackweb.com/blog/2009/10/14/high-quality-ruby-on-rails-example-applications/
** http://github.com/engineyard/rails_dev_directory

###  About View

* view 只處理「畫面顯示」相關的細節
* 不要在 view 中做任何「拉」資料的動作，至少不應在view中做任何 db 操作。(側邊欄的實作例外，因為要上 cache，此時不應該在 application controller 下 query，因為會多出超多無用 query ）
* 下拉式選單（select） 的 options 應該是屬於 helper 層級應該要準備的資料，不應該在 view 製作。
* link_to  的括號不要省略
* 如果你沒有把握這個頁面只會用到這個id一次，就不要用 id。
* css className 命名要以 dash(-) 分隔而非底線(_) 分隔，盡可能前面加上跟用途有關的修飾字。(will_pagination 或其他現成套件helper 如採用_分隔時當特例處理。）
* 儘量避免在 view 裡做 if , else 判斷，應用 helper 或 method 包裝邏輯。比如 @post.editable_by?(current_user)。而非直接在 view 寫 if / else 判斷 current_user 是否存在且是否作者。

* 善用 helper  
** h(word) -> plain text
** s(html) -> 消毒 html
** u(url) -> urlencode
** truncate(word,:length => length , "...") -> 斷句
*** 請勿寫成 truncate( word , 70) ，而要寫成 truncate( word , :length => 70) 

* helper 也要用() 包起來 

* 如果有要寫在頁面的 javascript，可使用 content_for :page_specific_javascript ，這樣就可以達到在</body>前執行的效果。
** 當然layout也要記得定義 yield(:page_specific_javascript) 在正確的位置，沒必要的話script盡量放</body>前。

h1. About Static Files (Javascript/css/image/html...)

* 應該要放在 public 資料夾下，依照javascripts/stylesheets/images項目分類，html則依request url 路徑置放。
* css 用到的圖片應該要放在 stylesheets/image 底下，也就是css所在當前位置的image子資料夾下。

* css 中我們有針對body上兩個指定瀏覽器才有的class類別(ie6 ,ie -> for ie7 and after )，也有針對不同controller 、action上不同的class類別，有需要針對特定版本或特定頁面設計時可多加利用。

## About Helper

* 主要是放跟 view 箱關的東西
* helper 裡不要放"太多" 純 html 的東西。如果要寫大片的 html，應該在 view 裡面寫 partial。如果真的要放 html，應使用 content_tag 而非寫 inline html，以免貼 html 因為雙引號問題，造成 syntax error。
* helper 不要放 instance variable，應把 variable 改寫為 parameter 由外部傳入。 
* 禁止在 helper 裡使用 yield 
* 只要牽扯到圖都應該寫 helper

## About Controller

* application controller 每個頁面都會呼叫到，請特別小心使用 db query 。
* 會有三個controller 以上公用的函式 應該要放到 application controller 
* 如果是 model 自己的資料轉換 （如 year/month/date 的資料格式轉換），這責任應該在model ，而非 controller 
* link_to 的括號不要省略
* 禁用 link_to @resource, form_for @resource , redirect_to @resource 此種 magic method。請拆開來老老實實寫，不要貪圖求快用這種寫法。
** 這種寫法在多個專案內已被證明：(1) 當作 STI 時，在 Application 內會大幅爆炸 (2) resource 的 model 與 controller 同名的時候雖然沒事。但一但要改版拆開名字時，即使用 git grep 檢查關鍵字還是會檢查不乾淨，造成改版後大量爆炸（因為無法用 git grep "resource_path" ）。

* notice_stickie 記得用 () 包起來，但 redirect_to 不需要
* controller 裡嚴禁寫 view code
* 請用善用 before_filter 確保主要物件是存在的。
<pre>
<code class="ruby">
before_filter :load_answer, :only => [......]
def load_answer
  @answer = Answer.find(params[:id])
rescue ActiveRecord::RecordNotFound
  render_404
end
</code>
</pre>

* 如果不是使用 primary key 而是其他 column 去找，則要加上 @!@ 來確保找不到資料時會 raise exception。
<pre>
<code class="ruby">
before_filter :load_answer, :only => [......]
def load_answer
  @answer = Answer.find_by_permalink!(params[:permalink])
  # ActiveRecord::RecordNotFound will be raised if no record is found
end
</code>
</pre>
* 可以拆成 action 的就不要用 params 去做，如 論壇的精華區或 prefix https://github.com/techbang/forum/compare/bd263ff...d07cfcd
* controller 裡的 action 應該與裡面做的事是一致的，不要掛羊頭賣狗肉
** https://github.com/techbang/forum/commit/8b3a063009f0d8870c945670518da56e11b03ba1
* controller 裡面不要直接寫 update_all 的 code, 應該用 class method 包起來, 在 controller 裡 call class method
**  https://github.com/techbang/forum/commit/39e9577429277b055a2bbfc37d1314f3066a516f

###  About Model

* model 吐出去的東西只能是純天然物質，如 字串、陣列、Hash 等。如果是製作 select option 需要塞字串，請至 helper 加料，嚴禁在 model 內加料。
* many to many 的 named_scope 如果有 order => "xxx DESC" 會造成 join 的 table scan ，解法是拆 query。
* 巢狀結構(同一 table 多對多），要用awesome_nested_set。
** script/plugin install git://github.com/collectiveidea/awesome_nested_set.git
** 這裡有簡單的說明：http://yearl.javaeye.com/blog/500929

* self的使用
** 狀況 1. Class method
<pre>
<code class="ruby">
# controller

@posts = Post.first_post # Post is a Class

# Post model 

def self.hidden # 要加 self
   first
end

</code>
</pre>
** 狀況 2. Instance method
<pre>
<code class="ruby">
# controller

@post = Post.first
@post.is_published? # @post is a Instance

# Post model 

def is_published? # 不加 self
   aasm_state == 'published' && published_at <= Time.zone.now
end
</code>
</pre>
** 狀況 3. assignment 
<pre>
<code class="ruby">
# Post model 

def set_time
   self.published_at = Time.zone.now # assign value to attribute, 一定要加self
end

</code>
</pre>
** 狀況 4. access 
<pre>
<code class="ruby">
# Post model 

def set_time
   published_at # access attribute, 統一不加self
end

</code>
</pre>

* method 的放置順序

<pre><code class="ruby">
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
</code></pre>

###  About Time 時區的細節

* 在 config/enviroment.rb 裡設定 config.time_zone = 'Taipei'
但是資料庫儲存的是 UTC 的時間
如果有需要用 conditions 下 SQL 語法撈資料
請檢查是否必須用 Time.now.utc 避免出錯

### About Mac

* TextMate
** 可以用 alt 按住做區域圈選， 搭配 # 鍵一次註解多行。
** 要在editor 的左邊顯示行號 請按 Option+Apple+L
** tab 要設為 Soft Tabs: 2
** 一些 trick 的介紹網站 http://wiki.macromates.com/Main/Howtos
*** 推薦 how to exclude files and foleders from project drawer 把 public/system加進去，就不會因為圖片太多因素導致開很慢。

