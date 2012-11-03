## Rails Convention About Controller

* application controller 每個頁面都會呼叫到，請特別小心使用 db query 。
* 會有三個controller 以上公用的函式 應該要放到 application controller 
* 如果是 model 自己的資料轉換 （如 year/month/date 的資料格式轉換），這責任應該在model ，而非 controller 
* link_to 的括號不要省略
* 禁用 link_to @resource, form_for @resource , redirect_to @resource 此種 magic method。請拆開來老老實實寫，不要貪圖求快用這種寫法。
** 這種寫法在多個專案內已被證明：(1) 當作 STI 時，在 Application 內會大幅爆炸 (2) resource 的 model 與 controller 同名的時候雖然沒事。但一但要改版拆開名字時，即使用 git grep 檢查關鍵字還是會檢查不乾淨，造成改版後大量爆炸（因為無法用 git grep "resource_path" ）。

* 警告訊息可以使用 `redirec_to xxx_path, :flash => { :error => ""}` 也可使用 notice, warning
* controller 裡嚴禁寫 view code
* 請用善用 before_filter 確保主要物件是存在的。

```ruby
before_filter :load_answer, :only => [......]
def load_answer
  @answer = Answer.find(params[:id])
rescue ActiveRecord::RecordNotFound
  render_404
end
```

* 如果不是使用 primary key 而是其他 column 去找，則要加上 @!@ 來確保找不到資料時會 raise exception。

```ruby
before_filter :load_answer, :only => [......]
def load_answer
  @answer = Answer.find_by_permalink!(params[:permalink])
  # ActiveRecord::RecordNotFound will be raised if no record is found
end
```

* 可以拆成 action 的就不要用 params 去做，如 論壇的精華區或 prefix https://github.com/techbang/forum/compare/bd263ff...d07cfcd
* controller 裡的 action 應該與裡面做的事是一致的，不要掛羊頭賣狗肉
  - https://github.com/techbang/forum/commit/8b3a063009f0d8870c945670518da56e11b03ba1
* controller 裡面不要直接寫 update_all 的 code, 應該用 class method 包起來, 在 controller 裡 call class method
  - https://github.com/techbang/forum/commit/39e9577429277b055a2bbfc37d1314f3066a516f
