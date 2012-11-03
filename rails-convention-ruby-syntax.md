## Ruby Syntax

* 編輯器設定 soft tab (space=2)，以 2 格空白符號做為程式內縮距離（不分語言）。

```
Sublime Text2 設定, Settings Default => 
    // The number of spaces a tab is considered equal to
    "tab_size": 2,

    // Set to true to insert spaces when tab is pressed
    "translate_tabs_to_spaces": true,
```

* 函式如果只有一個參數，就不強制打 ()，但傳 object 進去處理時也是要有 () 
* 函式如果有二個以上的參數，通通都要有 ()  
* 定義函式時一定要有 ()
* 字串限定用雙引號包覆
* 善用 `"#{str1} #{str3} "`等字串改寫技巧取代不需要的字串加法。
* 變數名稱命名法需使用  `my_favorite_game `這種 snake 型命名法，嚴禁駱駝 `myFavoriteGame` 這種命名方式。在 Ruby 中，使用駱駝法命名的只能是 Class name 或者是 Module name

* 可以用 `if` ，就避免用 `unless` ，除非 `unless` 較符合原意。
* map 要用 `{ |c| c.foo }`，不要用 do end。

* [[ruby_method|基本函式介紹]]