---
layout: post
title:  Lambda, Proc
tags:	[programming, Ruby]
---

>> The devil is in the details.

接續上一篇，這篇將透過 Lambda, Proc 將 block 物件化，以及物件化之後帶來哪些好處，如何使用。

開始之前我們先驗明正身：證明 block 不是物件，所以無法單獨存在 Ruby 中。這樣 Lambda, Proc 才有存在意義嘛！
```ruby
{ puts "This is block" } # => SyntaxError

# 當然也無法指派給變數
block = { puts "This is block" } # => SyntaxError
```

## Proc, Lambda ~~物化~~ 物件化 block
### Proc:
```ruby
greeting = Proc.new { puts "What up" } # => #<Proc:0x00007fa95315cf58@(irb):1>
greeting = proc { puts "What up"} # => #<Proc:0x00007fa954008570@(irb):2>
```

可以看到 block 經由 Proc 將後面的 block 物件化為 Proc 實體。那物件化之後要幹嘛? 當然是摳出來使用

```ruby
greeting = { |n| puts "What up, #{n}!" }

greeting.call("Joe")
greeting["Joe"]
greeting.("Joe")
greeting.=== "Joe"
greeting.yield "Joe"
# 以上的範例在摳 greeting 同時，又傳入了一個參數("Joe")
```

### Lambda:
```ruby
sasaya = lambda { |n| "sasaya + #{n}" } # => #<Proc:0x00007fa95316a478@(irb):12 (lambda)>
sasaya = -> (n){ "sasaya + #{n}" }  # => #<Proc:0x00007fa953179ab8@(irb):13 (lambda)>
```

從回傳值或使用 `class` 方法都可以得知物件化後也是 Proc 實體，所以要檢查是不是 Lambda 可使用 `lambda?`
```ruby
sasaya.class # => Proc
sasaya.lambda? # => true
```

## Lambda & Proc 差異
#### 1. 對於參數的處理
```ruby
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
```

- 從回傳的訊息觀察到，Proc 對於接收的參數，多給沒給都不會回傳錯誤訊息，反正就是印啦! 很隨便。

- 反之，Lambda 對於接收到的參數會以一個蘿蔔一個坑的方式代入，如果不是預期的，就會回傳 `wrong number of arguments`。

實際運用上，Lambda 也因為處理參數更為嚴謹的特性，所以我們常在 Rails Model 定義 Scope 使用 Lambda：
```ruby
# scopes
scope :available, -> { where(on_sell: true).where('list_price > 0') }
```

設定了一個 `:available` 的 Scope, 範圍是**有上架且售價大於0的商品**。並且將 `available` 這 Scope 用於 Controller。


#### 2. `return`
```ruby
#Proc
pc = Proc.new { return 10 }
puts "result = #{pc.call}" # => LocalJumpError(unexpected return)

def return_in_proc
  a = Proc.new { return 10 }.call
  puts "This will never be printed."
end
```

`return_in_proc` 將控制權交給 Proc 之後就要不回來了，Proc 直接回傳 10

```ruby
#Lambda
lam = -> { return 10 }
puts "result = #{lam.call}" # => result = 10

def return_in_lambda
  a = lambda { return 10 }.call
  puts "The lambda return #{a}, and this will be printed."
end
```
`return_in_lambda` 將控制權交給 Lambda，回傳 10 並交回控制權。 `return_in_lambda` 拿回控制權後，繼續執行下一行 code  

## `&` 是什麼?
**簡單一點的説，就是可以將 block, lambda, proc 之間作轉換，讓它們能像是 Anonymous function (匿名函式) 被運用。**
```ruby
block99 = proc { |x| puts "#{x} in test" }  # Proc.new
block99 = -> (x) { puts "#{x} in test" }  # lambda

def test(&block99)
  block99.call("car")
  puts "Holding..."
end
# 另一種寫法
def test
  yield("car")
  puts "Holding..."
end

# 兩種 callblock 方式一樣結果
test(&block99)

test do |x|
  puts "#{x} in test"
  puts "Holding..."
end
# => car in test
# => Holding...
```

1. 一開始先將 block 物件化成 Proc 實體，指定給 `block99`
2. 將 `block99` 傳入 `test` 方法，方法裡使用 `.call` or `yield` 將執行權暫時交給 `block99`，並順手給了個伴手禮 `"car"`
3. `block99` 拿到 `"car"`，指定給 x 變數，再從 `||` 拿出 x 執行後段程式碼
4. 返回 `test`，執行 `puts "Holding..."`

**最後，要特別解釋兩種 callblock 方式：**

1. `test(&block99)` 是以 `&` 將 block99 轉換為 block 掛在 test 方法後面
2. `test do |x| ... end` 就是 test 方法直接接上 block，所以也不需要最一開始的 Proc 實體。(Block 篇有介紹到 `do...end` 也是 Block 形式。)

**有趣的來了～ 除了轉換成 block，也可以將 block 轉換成 Proc 或 Lambda**

在使用 Rails 開發時，或許對接著這行 code 似曾相似 `foo.map(&:bar)`。

揪竟為什麼會這樣寫呢?

Ruby Symbol 其實有內建一個 `to_proc` 方法：

**Returns a Proc object which responds to the given method by sym.**
```ruby
(1..3).collect(&:to_s)  #=> ["1", "2", "3"]

# 可改寫成
(1..3).collect { |num| "#{num}" }
```

Ruby 會是這麼解讀: 對 (1..3) 使用 `collect` 方法，`collect` 會掛載定義好的 block，如果 `to_s` 還不是 Proc 實體，就實體物件化 `to_s`。

如果要追溯 Symbol 的 `to_proc` 方法是如何運作的，可以找到這篇 [stackoverflow](https://stackoverflow.com/questions/1217088/what-does-mapname-mean-in-ruby)

## To Thumbs up
- Lambda, Proc 可以將原本不是物件的 block 實體化
- Lambda 在處理參數及使用 `return` 時的行為更接近 regular method
- 而 `&` 的出現讓 Block, Lambda, Proc 運用上更貼近 Anonymous function (匿名函式)

### Reference
- [Ruby.doc - Symbol]
- [What does mapname mean in Ruby]
- [What do you call &: operator in Ruby?]




[Ruby.doc - Symbol]:(https://www.rubydoc.info/stdlib/core/Symbol)
[What does mapname mean in Ruby]:(https://stackoverflow.com/questions/1217088/what-does-mapname-mean-in-ruby)
[What do you call &: operator in Ruby?]:(https://stackoverflow.com/questions/2259775/what-do-you-call-the-operator-in-ruby)
