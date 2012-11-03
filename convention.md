## Project Convention

* Rails project 一律放在 `~/projects/project_name`
* 一律使用 Pow 掛起 project。
* 本機重開 server，使用 `touch tmp/restart.txt`
* 拉下來的 code 如果不能動，先 `bundle install` -> `rake db:migration` -> `touch tmp/restart.txt`
* 如果新增了 migration、新增了 gem、修改了 config/config.yml、需要跑 task 生資料，push 之後請主動到 Skype 上告知。
* deploy 到機器上請主動到 skype 喊 `deploy to production/staging`
* 禁止於 11:30 - 13:00 , 17:00 ~ 以後對 project 進行 deploy

## 解票慣例

* [[Ticket Convention]]

## Rails Convention

* [[Rails Convention]]

## Git 相關技巧

* [[開 remote branch| open remote branch]]