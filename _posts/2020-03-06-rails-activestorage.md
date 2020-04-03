---
layout: post
title:  Rails 6.0.0 active_storage
tags:	[programming, rails]
---
前陣子在做某公司初試題目，收到題目時還很開心題目類似自己練習過的小專案。壓根沒想到會碰到小麻煩。不過既然現在能輕描淡寫的寫文章紀錄下來，就表示問題最終是解決了，也有一點心得。

## So, What's the Problem?
現在回想，不知是自己想太多還是該公司出此題目的小用心? 題目要求使用 Rails 6.0.0 以上版本，且必須實作可以上傳多張圖檔功能。

問題呼之欲出，keyword 就是 **Rails 6.0.0** 以及實作功能 **上傳多個圖片**。

簡而言之就是 Rails 6.0 ActiveStorage `has_many_attached` 情況下，使用 `update` 更新資料時，預設會覆蓋所有的原圖檔，再上傳新的圖檔。まずいね！(真是太糟糕了)

>> 小腦補 - [ActiveStorage] 是 Rails 5.2 後，Rails 支援檔案上傳的功能。可以將檔案上傳至 Google Could Storage, Amazon S3, Microsoft Azure Storage 等雲端儲存服務，也可以在開發或測試環境中搭配 local based-disk service 使用。

## The Process
從 Github Rails 可以查到在討論 Rails 6.0.0 ActiveStorage `has_many_attached` 相關 issues，我是透過這些討論串找出解決方法的

#### Issue [#36374]: ActiveStorage has_many_attached attachemnts get destroyed when subsequent files attached
從 Issue 內容可知，預期 `has_many_attached` 的上傳檔案，既有的檔案不會被上傳的新檔案覆蓋。

不過，Rails 6.0.0.rc1 ActiveStorage 使用 `has_many_attached` 建立關聯，上傳多個檔案情況下，會先刪掉已存在的檔案，再重新上傳新的檔案。(rc = [Release Candidate])

![log](/img/log.png)

從 log 上可以看到執行 `product.update` 後，原有的檔案先被刪除，之後將新增的檔案再存進資料庫並建立與 Product 的關聯。

有人早在 Beta 之後的 rc 就發現了這個問題。

#### PR [#36716]: Preserve existing attachment assignment behavior for upgraded apps
這個 PR 則是 Rails Member 對此 issue 所做的修正。

Commit message 提到：
1. 此 PR 只修正 Rails 5.2 升上 Rails 6.0 的應用程式。
2. 使用 Rails 6.0 或更新版本的應用程式則維持 rc 的預設行為，刪除既有的檔案後上傳新的。Rails 6.0 的應用程式，可使用 `#attach` 方法，即可避免上傳新檔案時，刪除原有檔案。

**Great !!**  現在我們知道使用 `#attach` 絕對可行。[rails/activestorage] 就有示範如何在建立 `has_many_attached` 之後使用 `#attach`。

如果有興趣知道設定 `has_many_attached` 後的行為，建議進到 `/lib/active_storage/attached/model.rb/` 試著看看 source code，會發現裡面有 `purge` 方法 - 使用 `purge` 會刪除所有檔案。

啊哈！現在我們理解為何 `has_many_attached` 會先刪除既有檔案了。

#### ~~Laziest~~ Simplest way
其實上面那些已是我在 stackoverflow 上找到更 ~~偷懶~~ 簡單的解決方法之後的事了。這篇 [stackoverflow] 裡的留言也提到除了 `#attach` 方法外，還有一個大絕招......

就是到 `application.rb` 直接修改 ActiveStorage 預設設定。這樣一來直覺的使用 `update` 便不會再將原有的檔案先刪除了。- 實在太簡單，以至於我用了之後有點不好意思。

### Conclusion
總之，如何在 Rails 6.0 ActiveStorage `has_many_attached` 情況下，如不想上傳時覆蓋既有檔案：
1. 使用 `#attach`
2. 到 `application.rb`, `config.active_storage.replace_on_assign_to_many = false`  

在網路上的留言討論總是可以找到更多的訊息，雖然難免雜亂也可能繞一大圈，但印象會非常深刻。

## Reference
- [#36374]
- [#36716]
- [#36806](https://github.com/rails/rails/issues/36806)
- [stackoverflow]
- [Upgrade Guide: ActiveStorage in Rails 6 and ImageProcessing]

[#36374]:https://github.com/rails/rails/issues/36374
[#36716]:https://github.com/rails/rails/pull/36716
[stackoverflow]:https://stackoverflow.com/questions/58282845/rails-6-active-storage-could-not-find-or-build-blob-expected-attachable-got
[ActiveStorage]: https://guides.rubyonrails.org/active_storage_overview.html
[Release Candidate]: https://en.wikipedia.org/wiki/Software_release_life_cycle#Release_candidate
[rails/activestorage]: https://github.com/rails/rails/tree/master/activestorage
[Upgrade Guide: ActiveStorage in Rails 6 and ImageProcessing]: https://bloggie.io/@kinopyo/upgrade-guide-active-storage-in-rails-6
