## 非wording狀況

* `git pull `pull latest source code
* `git checkout -b t票號` checkout 到新開 branch t票號, 並開始解
* `git add FILES` 加入修改的檔案
* `git commit -m '修改資訊'` commit 修改資訊
* `git push origin t票號` 將完成的 t票號 push 到遠端 origin
* 上 Github 發 pull request
* 回 redmine，改狀態 [新建立 => 已解決]，貼 github link

wording
git pull
git checkout -b t票號
git checkout master
git merge t票號
git commit -a -m '修改資訊'
git push origin
pull request
redmine 貼 url