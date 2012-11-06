# CSS Tricks

教材：<http://www.codeschool.com/courses/css-cross-country>

<hr>

### Style 生效的位置

* inline style
* 在 `<head> </head>` 裡面
* external link: 如 `link rel="stylesheet" href="style.css"`

### CSS Selectos

* Element selector
* Class selector
* ID selector
* Compound selector : `h1#header`

### Advanced Selector

ref: [Taming Advanced CSS Selectors](http://coding.smashingmagazine.com/2009/08/17/taming-advanced-css-selectors/)

* `#sidebar h2 `— 0, 1, 0, 1
* `h2.title` — 0, 0, 1, 1
* `h2 + p` — 0, 0, 0, 2
* `#sidebar p:first-line` — 0, 1, 0, 2

### cascade order

優先權照

* external `<link>`
* `<head>`
* inline style
* !important
* 相同的 selector 重複的屬性，後寫的會覆蓋前面的。沒有重複的則會合併。

### Float

#### 定義

* 把元素從傳統的 document flow 中移除，然後浮動掛在指定的邊界上。
* 其他在 parent 元素的內容會繞著這個 float 的元素排列。

#### 種類

* float: left
* float: right
* float: none

#### Stacking order

* 浮動的元素從 parent 的左/右邊界開始排，不夠放的話會找另下一個可以停泊的邊界繼續排
* 如果浮動的元素高度不同，例如左邊的特別長，旁邊的元素的比他短，則下一個停泊的不會是左邊界，而是會停在左邊元素的右邊。（下一個可以停泊的邊界原則）

#### floating left & right

同時使用 float:left 與 float:right，寬度夠的話，會分列兩邊。如果寬度不夠，以先 claim 的會是第一排(靠右），後 claim 的會被扔到下一排（靠左）去。

``` html
<div>
  <div class="content1"> </div>
  <div class="content2"> </div>
</div>
```

``` css
.content1 {
  float:right;
}
.content2 {
  float:left;
}
```

<hr>

### clearfix

#### 為什麼要使用  cleafix

* 狀況一：float 元素可能比旁邊的非 float 元素高。（因為 float 沒有高度）所以下一段會黏上來。
* 狀況二：所有的 children 都是 float。（因為 float 沒有高度）所以 container 看起來會太短。

##### 常用  clearfix 技巧

###### clear with a subsequent elemet ( 在下面的元素放 `clear: both` )

* 但這一招移動 div 順序就會爆炸
* 背景和邊界不會被展延

失敗！

###### manual clearing （手動在下面塞一個空的 div，對它下 `clear: both`)

* 需要塞一個空的 element
* 看起來很礙眼

#### 正解技巧 ( self-cleared )

```css
 
.group:before, .group:after { 
   content: "";
   display: table; 
}
.group:after {
   clear: both; 
}
.group { 
   zoom: 1; /* IE6&7 */ 
}
```

##### .group 要下在 parent node

``` html
<div class="group">
   <div class="float-left"> </div>
   <p> blah </p>
</div>
```

### Inheritance & Specificity

* 巢狀元素會自動繼承 parent 的 style
* 可以 override parent 的 style
* id 比 class 的優先權高



#### 優先權算法

0,0,0,0

* 第一個數字：inline style
* 第二個數字：of ID selectors
* 第三個數字：of class selectors
* 第四個數字：of element seletors

###### example

* `p { color: #fff; }`  # 0,0,0,1
* `.intro { color: #98c7d4; }` # 0,0,1,0
* `#header { color: #444245; }` # 0,1,0,0
* `<h1 style="color: #000;">Mogul</h1>` # 1,0,0,0
* `p { color: #fff !important; }`

越大的可以把祖先蓋掉。

延伸閱讀：[Don’t use ID selectors in CSS](http://screwlewse.com/2010/07/dont-use-id-selectors-in-css/)

<hr>

### box model

由內往外是

* content area
* padding area
* border area
* margin area

寬度計算

box width =  content width + padding width + border width

### overflow

* visible: the default value, which allows content to extend beyond container boundaries
* auto:  adds a scrollbar as needed when content overflows
* hidden: hides content that extends beyond the container
* scroll:  adds a scrollbar at all times, even if unneeded

### positioning

有四種

* static
* relative
* absolute 
* fixed

定義

* Elements have a position value of static by default
* Using a value other than static causes an object to become a positioned element
* Positioned elements may use the top, left, bottom, and right properties for placement

#### Relative positioning

Renders in the normal flow, then shifted via positioning properties

#### Absolute positioning

Takes an element out of the normal flow for manual positioning

#### 定位技巧

parent element 下 relative，內部需要定位的元素下 absolute，就不會超過邊界。

#### Fixed positioning

Affixes an element to a specific place in the window, where it will stay regardless of scrolling。（在螢幕上永遠保持不動，釘住）

### z-index

* No z-index or equal z-index = overlap determined by placement in DOM （後放的疊在先放的上面）
* Higher values appear above lower values（ z-index 比較大的在比較上面）
* Elements must be positioned for z-index to take effect. Use relative if you're not interested in moving the object （必須先被「定位」，才能使用 z-index)

延伸閱讀： [Understanding CSS z-index](https://developer.mozilla.org/en/Understanding_CSS_z-index)

<hr>

### 整理 CSS code

#### 用 `,`

``` html
p .content h3{
   color: red;
}
```
#### padding/margin 上右下左

``` html
.content {
   margin: 15px 10px 0 20px; /* top right bottom left */ }
}

```

#### 其他綜合

``` css

   font: 16px/18px bold italic sans-serif; /* size/line-height weight style family */
   background: #000 url(image.jpg) no-repeat center top; /* color image repeat x-pos y-pos */
   list-style: disc inside none; /* style position image */
   margin or padding: 0 10px 0 10px / 0 10px 0 / 0 10px; /* top right bottom left / top right&left bottom / top&bottom right&left */
   border: 3px solid #ccc; /* width style color */

```

### Display types

#### block 

* Stretch the full width of their container ( container 有多寬就延伸到多寬)
* Behave as though there is a line break before and after the element （有斷行效果）
* Full box model can be manipulated￼￼Display Types

Block elements: Tags that are block-level by default: `<div>`, `<p>`, `<ul`>, `<ol>`, `<li>` and `<h1>` through `<h6>`.

#### inline

* Typically found within block-level elements (通常可以在 block 元素裡找到)
* Only take up the space of the content inside （只有內容的寬度）
* Do not generate a line break before and after the content （沒有斷行效果）

Tags that are inline by default include `<span>`, `<a>`, `<em>`, `<img>`

#### inline-block

* Same flow as an inline element but behave as a block element (可以玩 box model)

### 水平置中技巧

#### Centering a block-level element

* Define a width, and the element width must be less than that of the parent container
* margin: 0 auto;

### Centering inline and inline-block elements

* text-align: center


<hr>

### margin 重疊問題

多個 margin 疊在一起，會有 margin 重疊問題（ margin 會合併計算，取最大的。假設第一個 div margin-bottom: 40px，第二個 div margin-top: 20px。則他們的距離會是 40px ，而不是 60px）。


#### 解決重疊技巧：

Collapsing margins will not occur when one or more block element has:

* Padding or border
* Relative or absolute positioning 
* A float left or right

延伸閱讀： [Collapsing margins](http://www.w3.org/TR/CSS2/box.html#collapsing-margins)

### rest & normalize

[Eric Meyer's Reset CSS](http://meyerweb.com/eric/tools/css/reset/)
[Normalize.css](http://necolas.github.com/normalize.css/)

<hr>

### Content Image 與 Layout image

* Content should be marked up as inline images
* Layout elements should be defined as background images

### Image Cropping

``` html

<li class="crop">
  <img src="snowboard.jpg" alt="Snowboard" />
</li>

```

``` css
crop {
  height: 300px; 
  width: 400px; 
  overflow: hidden;
}
.crop img {
  height: 300px; 
  width: auto;
}
```

其實沒有多少好的方法，建議取代方案：

* Resize images to a square < height and width of all of your images 
* Resize them server-side
* Provide image-uploading instructions in your CMS

延伸閱讀：[Experiments with wide images](http://clagnut.com/sandbox/imagetest)

<hr>

### 圖片取代文字技巧

使用 text-indent: -9999px;

``` css
.logo {
    background: url(logo.png);
    display: block;
    height: 100px;
    width: 200px;
    text-indent: -9999px;
}
```  

### css spirite 技巧

#### 為什麼要使用 CSS spirite

Issues with swapping background images:

* Adds an extra HTTP request 
* Image is not preloaded （網路不夠快時，hover 感覺畫面會閃一下）

Advantages to the sprite approach:

* Reduces number of HTTP image requests 
* Removes loading flash / need for preload


#### 使用方法

Multiple images & states:

``` 
 .twitter, .github {
     background: url(social.png);
     display: block;
     height: 100px;
     width: 100px;
     text-indent: -9999px;
}
.github {
    background-position: -100px 0;
}
.twitter:hover, .twitter:focus {
   background-position: 0 -100px;
}
.github:hover, .github:focus {
   background-position: -100px -100px;
}
```      

延伸閱讀: [Spritecow](http://www.spritecow.com/)

<hr>

## psuedo class

Allow you to conditionally select an element based on state or position

* last-child
* nth-child (an+b)


延伸閱讀：

* [Meet the Pseudo Class Selectors](http://css-tricks.com/pseudo-class-selectors/)
* [CSS Reference](https://developer.mozilla.org/en/CSS_Reference) # 支援瀏覽器列表


## psuedo element

``` html
<article>
   <p>Coffee? Hah! Our cocoa is far better.</p>
   <p>Visit from 4-5 for cocoa happy hour!</p>
</article>
```

``` css
article p:last-child:after {
   content: '\2744';
}
```

### 利用 before, after 技巧 

#### 取代掉無用 element

``` html
<blockquote>
Coffee? Hah! Our cocoa is far better.
<span></span>
</blockquote>
```

原本是對 `blockquote span` 下 styling。

改成對 `blockquote:before` 下 styling。

html 可以砍成
``` html
<blockquote>
Coffee? Hah! Our cocoa is far better.
</blockquote>
```

#### 利用 before, after 實作縮排

延伸閱讀：[A Whole Bunch of Amazing Stuff Pseudo Elements Can Do](http://css-tricks.com/pseudo-element-roundup/)

