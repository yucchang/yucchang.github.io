---
layout: post
title:  Truthy & Falsey  
tags: [programming, Ruby]
---
還記得在第一次面試時，被問了一個這樣的問題：請問 **Truthy**, **Falsey** value 在 Ruby 代表什麼?

當時我腦袋突然一片空白，因為從來沒有思考過這個基本到不行，卻是程式邏輯、流程控制的基礎觀念。一陣緊張焦慮後，我試著回答出 `布林值` 這個答案，嘛～ 回答得很不完整。

之後，面試官便問了幾題加入運算子(operator)與條件語句並建構在此基礎上的程式邏輯判斷，想也知道我回答的哩哩落落，且信心受挫。不過也因為這次，讓我重新體認到基本的重要。

**Truthy**, **Falsey** value 是電腦運算、程式語言的邏輯判斷的基礎。

## What are Truthy and Falsey values in Ruby?

在 Ruby 中，除 `fasle`，`nil` 會被視為 Falsey value 外，其餘的皆被視為是 Truthy value。(僅需記得什麼是 Fasely value 足矣)

```ruby
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
```

參考 [這篇]["truthy" and "falsey" (or "falsy") in Ruby] 設定 `truthy_or_falsey` 方法，並傳入 `value` 參數讓條件語句 `if..else` 判斷，結果為 `true` 則回傳 `#{value.inspect} is truthy`。反之，為 `false` 則執行 `#{value.inspect} is falsey`。

可以進入 irb 試試看，會更清楚每個物件的回傳值。
```ruby
=> true is truthy
=> false is falsey
=> nil is falsey
=> 0 is truthy
=> 1 is truthy
=> "" is truthy
=> {} is truthy
=> Object is truthy
```

透過這個方法你可以帶入任何物件為參數(value)，檢查為 Truthy or Falsey。

### What the hack is `false`, `nil`?
有趣的是，雖然 `false`, `nil` 都是 Falsey value，但可都是真真實實的存在 Ruby 世界。
```ruby
false.class
=> FalseClass
nil.class
=> NilClass
```
`false` 是 `FalseClass` 的實體，而 `nil` 是 `NilClass` 的實體。

在 Ruby，`nil` 實體用來代表 『不存在』、『空的』。存在的實體代表『不存在』，好繞口，但不是黑白豪洨。

**It is waht it is.**
```ruby
nil.nil?
=> true
nil.to_s
=> ""
nil.to_a
=> []
```

下一篇將結合**運算子(operator)**，更進一步了解 Truthy, Falsey value。

### Reference
- ["truthy" and "falsey" (or "falsy") in Ruby](https://gist.github.com/jfarmer/2647362)
- [Truthy and Falsy values](https://riptutorial.com/ruby/example/2092/truthy-and-falsy-values)
- [為你自己學 Ruby on Rails](https://railsbook.tw/chapters/05-ruby-basic-1.html#flow-control)
