## Rails Convention About Helper


* 主要是放跟 view 箱關的東西
* helper 裡不要放"太多" 純 html 的東西。如果要寫大片的 html，應該在 view 裡面寫 partial。如果真的要放 html，應使用 content_tag 而非寫 inline html，以免貼 html 因為雙引號問題，造成 syntax error。
* helper 不要放 instance variable，應把 variable 改寫為 parameter 由外部傳入。 
* 禁止在 helper 裡使用 yield 
* 只要牽扯到圖都應該寫 helper


#### TODO Section

* link_to 的 blick
* content_for 的 block
