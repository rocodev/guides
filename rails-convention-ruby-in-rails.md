## Ruby in Rails

* 如果要判斷空字串或空集合等，請用 `xxx.blank?`   
  - 不要用 `xxx.empty?` or `xxx==""`
* 承上點的相反情形，如果要判斷字串或集合是否有任意成員，請用 `xxx.present?` 
  - `" xxx".present?` #=> true      
  - `["hi","hello"].present?` #=> true  
  - `[].present?` #=> false  
* 關於elsif
  - 使用elsif前先思考，處理的邏輯是否有關連性，若無請分成多個if去處理
  - 若有相關性，但必須使用到elsif，思考一下是否有更magic的方式去處理(ex: http://d.pr/i/xNqr)

* 禁止使用
```ruby
Post.where( :id => ids ).each do |post| ...
</code>
```
請改成兩行
```ruby
@posts = Post.where( :id => ids )
@posts.each  do |post| ...
```
* 注意以下兩種寫法topic_id為nil的狀況，
```
Topic.find(topic_ids) # error
Topic.find(:all, :conditions => { :id => nil}) # []
```

* recursive 的 method
https://gist.github.com/0b3acb50dd1554b1f937
要傳值回去的條件要擺在上面，不要擺在下面，會傳到你沒有預期的值。

* Rails 的 request object

 - request.host : 當前 request 的 host name `example : "http://localhost/" #=> localhost`
 - request.request_uri : 當前 request 的 uri （不含host）`example : "http://localhost/posts/1" #=> /post/1`
 - request.remote_ip : 當前 request 者的 ip


* 學習參考用投影片以及 application
 - Rails Best Practice http://www.slideshare.net/ihower/rails-best-practices
 - 禁止從 Redmine 學習寫 Rails Code，它的 code 太髒了，會造成 RD 走火入魔。適合學習的指南話應該是 [Refactoring Redmine](http://www.refactoringredmine.com/)
 - http://jetpackweb.com/blog/2009/10/14/high-quality-ruby-on-rails-example-applications/
 - http://github.com/engineyard/rails_dev_directory