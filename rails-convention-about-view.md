## About View

* view 只處理「畫面顯示」相關的細節
* 不要在 view 中做任何「拉」資料的動作，至少不應在view中做任何 db 操作。(側邊欄的實作例外，因為要上 cache，此時不應該在 application controller 下 query，因為會多出超多無用 query ）
* 下拉式選單（select） 的 options 應該是屬於 helper 層級應該要準備的資料，不應該在 view 製作。
* link_to  的括號不要省略
* 如果你沒有把握這個頁面只會用到這個id一次，就不要用 id。
* css className 命名要以 dash(-) 分隔而非底線(_) 分隔，盡可能前面加上跟用途有關的修飾字。(will_pagination 或其他現成套件helper 如採用_分隔時當特例處理。）
* 儘量避免在 view 裡做 if , else 判斷，應用 helper 或 method 包裝邏輯。比如 @post.editable_by?(current_user)。而非直接在 view 寫 if / else 判斷 current_user 是否存在且是否作者。

* 善用 helper  
 - h(word) -> plain text
 - s(html) -> 消毒 html
 - u(url) -> urlencode
 - truncate(word,:length => length , "...") -> 斷句

 *** 請勿寫成 truncate( word , 70) ，而要寫成 truncate( word , :length => 70) 

* helper 也要用() 包起來 

* 如果有要寫在頁面的 javascript，可使用 content_for :page_specific_javascript ，這樣就可以達到在`</body>`前執行的效果。
 -  當然layout也要記得定義 yield(:page_specific_javascript) 在正確的位置，沒必要的話script盡量放`</body>`前。