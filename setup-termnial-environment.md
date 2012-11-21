## 設置終端機


### 1. 安裝 zsh

根據 <http://blog.longwin.com.tw/2011/10/macosx-shell-zsh-2011/> 安裝 zsh

Mac 本身自帶 zsh，所以只要 `chsh -s /bin/zsh`即可

### 2. 安裝使用 oh-my-zsh

* `cd ~/`
* `git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh`
* `cp ~/.zshrc ~/.zshrc.orig`
* `cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc`
* `vim .zshrc`

#### 修改 theme

```
#ZSH_THEME="steeef"
ZSH_THEME="agnoster"
```

#### 啟用 plugin

`plugins=(git osx)` # 啟用 git, osx 的 plugin, ~/.oh-my-zsh/plugins 有更多可以參考

此時安裝完畢，可以新開 Tab 生效。

### 3. 安裝 agnoster 所需字型

由於 agnoster 需要[特殊字型](https://gist.github.com/3712874)。

所以必須安裝 patched 過的三個字體：<https://gist.github.com/1595572>。(下載後雙擊安裝這三個字體。)

### 4. 安裝 SOLARIZED 布景

[SOLARIZED](http://ethanschoonover.com/solarized) 布景是特殊調製的一個背景。按此 [下載](http://ethanschoonover.com/solarized/files/solarized.zip) 最新版本。

解壓縮後裡面有很多布景。

#### 替換掉 iTerm 布景

* `Preference` -> `Profiles` -> `Colors` -> `Load Presets` -> `Import`，載入 `iterm2-colors-solarized` 目錄下的兩個 itermcolors
* `Preference` -> `Profiles` -> `Colors` -> `Load Presets`，載入 `Solarized Dark`。



