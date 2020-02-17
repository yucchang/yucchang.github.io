---
layout: post
title:  "Block, Proc, Lambda"
date:   2020-02-17 18:00
author: 賈蓋先
categories: Programming  
tags:	Ruby 
cover: 
---

面試一定會被問的問題：請你解釋解釋 Block, Proc, Lambda (笑)

## Block  
**Ruby 是物件導向設計的程式語言。**所以，在 Ruby 裡所有東西幾乎都是物件，但 **Block** 卻是少數例外。

**Block** 就是 `code block` 程式碼區塊，以 { }, do...end 呈現。

通常單行程式碼使用 { }, 多行則使用 do...end 將程式碼包起來,

{% highlight ruby %}
[*1..10].map { |num| num * 2 }

[*1..10].map do 
  # do something
end
{% endhighlight %}

而在選擇使用 { }, do...end 還會影響先乘除後加減的計算, 
{% highlight ruby %}
# 大括號優先順序高 
p ([*1..10].map { |num| num * 2 })
=> [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]

# do ... end 優先順位較低不會執行 Block 裡的程式碼, 僅執行小括號內容
p ([*1..10].map) do |i| i * 2 end
=> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
{% endhighlight %}

### Block 特性 

- Block 不是物件
{% highlight ruby %}
# 無法單獨存在
{ puts "Aloha!" }  # => syntax error
# 之前提到 String 是物件 
"Aloha!" # => "Aloha!"

# 無法指定給變數 
greeting = { puts "Aloha!" } # => syntax error
{% endhighlight %}

- Block 不是參數
{% highlight ruby %}
# name 才是參數, 而 { puts "Aloha!" } 也不會執行
def greeting(name)
  puts "I'm #{name}."
end

greeting("kuma"){
  puts "Aloha!"   
}

greeting("kuma") # => I'm kuma. 
{% endhighlight %}

### 如何執行 Block?
#### 使用 `yield`,

{% highlight ruby %}
# yield 將執行權暫時交給 Block, 執行 Block 中的程式碼
def run_order
  puts 1  
  yield      # 先去執行 { puts 3 }, 再返回往下
  puts 2
end

run_order { puts 3 }
# => 1 
# => 3
# => 2
{% endhighlight %}

**方唐鏡:**

![yield](/assets/yield.jpeg)

#### 也可以傳參數給 Block
{% highlight ruby %}
def count
  yield 123
end

count { |x| puts x }
# => 123
{% endhighlight %}

#### Block 回傳值 
{% highlight ruby %}
# 使用 yield 做出 Ruby select 方法
def my_select(arr) 
  result = []
  arr.each do |i|
    result << i if yield(i)
  end
  result       # Block 回傳最後一行程式碼執行結果
end

p my_select([*1..10]) { |i| i.odd? }
# => [1, 3, 5, 7, 9]
{% endhighlight %}

#### 檢查 Block 是否正確帶入
Ruby 內建 `block_given?` 方法
{% highlight ruby %}
# 使用 block_given?
def is_block
   yield if block_given?  # return true exec 
end
is_block # => nil 
is_block { p "here is block" } # => here is block
{% endhighlight %}

### 小結：Block 附加於方法後面，裡面可以放進一些邏輯，傳入參數 argument

**上面有提到 Block 不是物件，不能單獨存在。但 Ruby 卻有 `Proc`, `Lambda` 能將 Block 物件化。**

哪泥???

