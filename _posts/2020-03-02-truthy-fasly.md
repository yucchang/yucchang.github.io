---
layout: post
title:  "Truthy & Falsy"
date:   2020-03-02 23:20
author: 賈蓋先
categories: Programming  
tags:	Ruby 
cover: 
---
還記得在第一次面試時，被問了一個這樣的問題：請問 **Truthy**, **Falsey** value 在 Ruby 代表什麼? 

當時我腦袋突然一片空白，因為從來沒有思考過這個基本到不行，卻是程式邏輯、流程控制的基礎觀念。一陣緊張焦慮後，我試著回答出 `布林值` 這個答案，嘛～ 算是只答對一半(甚至不到)。

之後，面試官便問了幾題加入運算子(operator)並建構在此基礎上的程式邏輯判斷，想也知道我回答的哩哩落落，且信心受挫。不過也因為這次，讓我重新體認到基本的重要。

**Truthy**, **Falsey** value 影響著程式語言的邏輯判斷，主要與 `if..else`, `while` 等條件語句或是運算子(operator)相關聯。

## What is Truthy and Falsey value in Ruby?

在 Ruby 中，除 `fasle`，`nil` 會被視為 Falsey value 外，其餘的皆被視為是 Truthy value。

{% highlight ruby %}
def truthy_or_falsey(value)
  if value 
    puts "#{value.inspect} is truthy"
  else 
    puts "#{value.inspect} is falsey" 
  end 
end 

[true, false, nil, 0, 1, "", {}, Object].each do |value|
  truthy_or_falsey(value)
end 
{% endhighlight %}

參考 [這篇]["truthy" and "falsey" (or "falsy") in Ruby] 設定 `truthy_or_falsey` 方法，並傳入 `value` 參數讓條件語句 `if..else` 判斷，結果為 `true` 則回傳 `#{value.inspect} is truthy`。反之，為 `false` 則執行 `#{value.inspect} is falsey`。

可以進入 irb 試試看，會更清楚每個物件的回傳值。

下一篇將結合**運算子(operator)**，瞧瞧 Ruby 如何判斷。

### Reference 
- ["truthy" and "falsey" (or "falsy") in Ruby]
- [Truthy and Falsy values]



["truthy" and "falsey" (or "falsy") in Ruby]: https://gist.github.com/jfarmer/2647362
[Truthy and Falsy values]: https://riptutorial.com/ruby/example/2092/truthy-and-falsy-values