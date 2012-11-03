## 第一次 push
`git push -u origin master`

## [編輯] 開 remote branch

* 先 `git branch XXX` # 開 local branch
* `git push origin XXX` # 推到 remote 去
* `git branch -d XXX` # 刪掉 local branch
* `git checkout origin/XXX -b XXX` # 從 remote 再拉下來一份 (這樣做就不需要手動設定 .git/config 了)

## [編輯] 刪 remote branch
`git push origin :XXX` # 在 branch name 前面加 : 就行了
`git branch -d XXX` # 把 local branch 砍掉。

## [注意] 統一 remote branch 開法
大功能 remote branch 開成 t票號
ex: t123