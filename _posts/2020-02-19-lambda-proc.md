---
layout: post
title:  "Lambda, Proc"
date:   2020-02-19 20:00
author: 賈蓋先
categories: Programming  
tags:	Ruby 
cover: 
---

>> The devil is in the details.

接續上一篇，這篇將透過 Lambda, Proc 將 block 物件化，以及物件化之後帶來哪些好處，如何使用。

開始之前我們先驗明正身：證明 block 不是物件，所以無法單獨存在 Ruby 中，
{% highlight ruby %}
{ puts "This is block" } # => SyntaxError 

# 當然也無法指派給變數
block = { puts "This is block" } # => SyntaxError 
{% endhighlight %}

驗完正身，可以準備將 block 物件化了。

## Proc, Lambda ~~物化~~ 物件化 block
#### Proc
{% highlight ruby %}
greeting = Proc.new { puts "What up" } # => #<Proc:0x00007fa95315cf58@(irb):1>
greeting = proc { puts "What up"} # => #<Proc:0x00007fa954008570@(irb):2>
{% endhighlight %}

可以看到 block 經由 Proc 將後面的 block 物件化為 Proc 實體。那物件化之後要幹嘛? 當然是摳出來使用

{% highlight ruby %}
greeting = { |n| puts "What up, #{n}!" }

greeting.call("Joe")
greeting["Joe"]
greeting.("Joe")
greeting.=== "Joe"
greeting.yield "Joe"
# 以上的範例在摳 greeting 同時，又傳入了一個參數("Joe")
{% endhighlight %}

#### Lambda 
{% highlight ruby %}
sasaya = lambda { |n| "sasaya + #{n}" } # => #<Proc:0x00007fa95316a478@(irb):12 (lambda)>
sasaya = -> (n){ "sasaya + #{n}" }  # => #<Proc:0x00007fa953179ab8@(irb):13 (lambda)>
{% endhighlight %}

從回傳值或使用 `class` 方法都可以得出物件化出來也是 Proc 實體，所以要檢查是不是 Lambda 可使用 `lambda?`
{% highlight ruby %}
sasaya.class # => Proc
sasaya.lambda? # => true
{% endhighlight %}

## Lambda & Proc 差異 
#### 1. 對於參數的處理方式：
{% highlight ruby %}
# Proc
p = Proc.new { |x, y| "x = #{x}, y = #{y}" }

p.call(1, 2) # => x = 1, y = 2 
p.call(1) # => x = 1, y = nil  # 沒代入參數，只會回傳 nil
p.call(1, 2, 3) # => x = 1, y = 2  # 多代入的，自動被忽略

# Lambda
l = lambda { |x, y| "x = #{x}, y = #{y}" }

l.call(1, 2) # => x = 1, y = 2 
l.call(1) # => ArgumentError
l.call(1, 2, 3) # => ArgumentError 
{% endhighlight %}

- 從回傳的訊息觀察到，Proc 對於接收的參數，多給沒給都不會回傳錯誤訊息，反正就是印啦! (回傳 `nil`)

- 反之，Lambda 對於接收到的參數會以一個蘿蔔一個坑的方式代入，如果不是預期的，就會回傳 `wrong number of arguments`。

實際運用上，Lambda 也因為處理參數更為嚴謹的特性，所以我們常在 Rails Model 定義 Scope 時這樣使用：
{% highlight ruby %}
# scopes
scope :available, -> { where(on_sell: true).where('list_price > 0') }
{% endhighlight %}

設定了一個 `:available` 的 scope, 條件是：1. 有上架 2. 售價大於 0。並且將 `available` 這方法應用於 Controller。 


#### 2. Return 方式:
{% highlight ruby %}
def test_lam
  -> { puts "exit from lambda" }.call
  return "exit from method"
end 

test_lam # => exit from method

def test_pr
  proc { puts "exit from proc"}.call
  return "exit from method"
end 

test_pr # => exit from proc
{% endhighlight %}
- Lambda return 的方式與 Method 方法一樣，會 exit the block 並繼續執行下一段 code。

- 而 Proc return 則直接中離這個方法，回傳 block 內容。

試著推論會回傳什麼? 
{% highlight ruby %}
def run_test 
  lambda { return "start" }.call
  proc { return "rest" }.call
  return "finish"
end 

run_test # => ?
{% endhighlight %}

答案是: `=> rest` 

執行過程：首先呼叫 lambda，return "start" 後 => 回來 method body => 往下執行呼叫 proc，但 proc 不乖，直接就 return "rest" 把內容丟出來 => end  

## `&` 是什麼?
**簡單一點的説，就是可以將 block, lambda, proc 之間作轉換，讓它們能像是 Anonymous function (匿名函式) 被運用。**
{% highlight ruby %}
block99 = proc { |x| puts "#{x} in test" }  # Proc.new
block99 = -> (x) { puts "#{x} in the test" }  # lambda 

def test(&block99)
  block99.call("car")
end 
# 另一種寫法
def test
  yield("car")
end 

# 兩種 callblock 方式一樣結果
test(&block99) 
# => car in test 
test do |x| 
  puts "#{x} in test"
end
# => car in test 
{% endhighlight %}

1. 一開始先將 block 物件化成 Proc 實體，指派給 block99 
2. 將 block99 傳入 test 方法，test 方法裡使用 `.call` or `yield` 去將執行權給 block99，並順手給了個伴手禮 "car"
3. block99 拿到 "car"，指定給 x 變數，再從 `||` 拿出 x 執行後段程式碼 
4. 返回 test 方法

**最後，要特別解釋兩種 callblock 方式：**

1. `test(&block99)` 是以 `&` 將 block99 轉換為 block 掛在 test 方法後面
2. `test do |x| ... end` 就是 test 方法直接接上 block，所以也不需要最一開始的 Proc 實體。(Block 篇有介紹到 `do...end` 也是 Block 形式。)

---
**有趣的來了～ 除了轉換成 block，也可以將 block 轉換成 Proc 或 Lambda**

在使用 Rails 開發時，或許對接著這行 code 似曾相似 `foo.map(&:bar)`。

揪竟為什麼會這樣寫呢? 

Ruby 的 Symbol, 其實有內建一個 `to_proc` 方法：[Ruby.doc] 

**Returns a Proc object which responds to the given method by sym.**
{% highlight ruby %}
(1..3).collect(&:to_s)  #=> ["1", "2", "3"]

# 可改寫成
(1..3).collect { |num| "#{num}" } 
{% endhighlight %}

Ruby 會是這麼解讀: 對(1..3) 使用 collect 方法，collect 方法會掛載定義好的 block，如果 `to_s` 還不是 Proc 實體，就實體物件化 `to_s`吧。

如果要追溯 Symbol 的 `to_proc` 方法是如何運作的，可以找到這篇 > [Stackoverflow](https://stackoverflow.com/questions/1217088/what-does-mapname-mean-in-ruby)

## To Thumbs up
- Lambda, Proc 可以將原本不是物件的 block 實體化
- Lambda return, 處理參數更像是 Ruby Method
- 而 `&` 的出現讓 Block, Lambda, Proc 運用上更貼近 Anonymous function (匿名函式)

### Reference 
- [method / block / yield / Proc / lambda 全面解釋]
- [Ruby 中的 Block、Proc、Lambda 是什麼?]
- [Ruby.doc]
- [What does mapname mean in Ruby]
- [What do you call &: operator in Ruby?] 


[method / block / yield / Proc / lambda 全面解釋]: https://devfun.tw/t/method-block-yield-proc-lambda/110
[Ruby 中的 Block、Proc、Lambda 是什麼?]: https://riverye.com/2019/11/15/Ruby-中的-Block、Proc、Lambda-是什麼？/
[Ruby.doc]: https://www.rubydoc.info/stdlib/core/Symbol
[What does mapname mean in Ruby]: https://stackoverflow.com/questions/1217088/what-does-mapname-mean-in-ruby
[What do you call &: operator in Ruby?]: https://stackoverflow.com/questions/2259775/what-do-you-call-the-operator-in-ruby