---
layout: post
title:  "Rails 6.0.0 active_storage"
date:   2020-03-06 21:30
author: 賈蓋先
categories: Programming  
tags:	Rails 
cover: 
---
前陣子在做某公司初試題目，收到題目時還很開心(老實說就是**CRUD**)。壓根沒想到會碰到一點小麻煩。不過既然現在能輕描淡寫的寫文章紀錄下來，就表示問題最終解決了，也有一點小心得。(笑) 

## What's the Problem?
說來不知是自己想太多還是該公司出此題目的小用心? 題目要求使用 Rails 6.0.0 以上版本，且實作後台可以上傳多張圖片功能。

問題呼之欲出，`keyword` 就是 **Rails 6.0.0**, **ActiveStorage has_many_attached**。 

是的，憑藉 `keyword` 可以 google 到一些蛛絲馬跡。(但似乎都只有英文?)

### Issues on Github
在 Rails Github 上就有在討論 Rails 6.0.0 `ActiveStorage`, `has_many_attached` 相關 issues，

#### Issue [#36374]: ActiveStorage has_many_attached attachemnts get destroyed when subsequent files attached 
(rc 指的是 [Release Candidate])

從 Issue 內容可進一步知道，Rails 6.0.0.rc1 ActiveStorage 在 has_many_attached 欲上傳多個附件時，會刪掉已存在的附件，再重新上傳一組新的附件。重點是在 Rails 5.2.2.1 時，預設是不會刪除原有的檔案。
  
#### PR [#36716]: Preserve existing attachment assignment behavior for upgraded apps

再來 Rails 其中一名 Member 便去 fix Issue#36374，而他也在 commit message 提到：他僅修改從 Rails 5.2 升級至 Rails 6.0 的應用程式。

原生使用 Rails 6.0 的應用程式，使用 #attach 方法，即可避免在上傳新附件時，刪除原有的附件。

#### Before that 
不過這已經是我在 [Stackoverflow] 上找到解決方法之後的事了。裡面除了也提到 #attach 方法外，還有一個大絕招 - 到 `application.rb` 直接修改 ActiveStorage 預設設定。這樣一來直覺的使用 update 便不會再將原有的附件刪除了。

過程中還有看到相關 issues, 甚至是文章討論此次修改。

## Reference 
- [#36374]
- [#36716]
- [Stackoverflow]
- [Upgrade Guide: ActiveStorage in Rails 6 and ImageProcessing]
- [#36806]




[#36374]: https://github.com/rails/rails/issues/36374
[Release Candidate]: https://en.wikipedia.org/wiki/Software_release_life_cycle#Release_candidate
[#36716]: https://github.com/rails/rails/pull/36716
[Stackoverflow]: https://stackoverflow.com/questions/58282845/rails-6-active-storage-could-not-find-or-build-blob-expected-attachable-got
[Upgrade Guide: ActiveStorage in Rails 6 and ImageProcessing]: https://bloggie.io/@kinopyo/upgrade-guide-active-storage-in-rails-6
[#36806]: https://github.com/rails/rails/issues/36806