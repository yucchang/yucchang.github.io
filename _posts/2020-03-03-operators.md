---
layout: post
title:  "Logical Operators with Truthy & Falsey"
date:   2020-03-03 22:50
author: 賈蓋先
categories: Programming  
tags:	Ruby 
cover: 
---
## Logical Operators 
### 1.  `&&`, and 
只有兩個值皆為 true，才成立。

Ex: `true && true => true`, `false && true => false`

### 2.  `||`, or 
只要有一個值為 true，就成立並回傳該值。

Ex: `false || true => true`, `true || false => true` 

### 3.  `!`, not 
就是個唱反調的，如果該值為 Truthy，則回傳 false，反之亦然。

Ex: `!true => false`, `!nil => true`

## Truthy and Fasley value
接續上一篇 Ruby 中的 Truthy, Falsey value，試著回答看看以下的 code 執行會回傳什麼?
{% highlight ruby %}
str = "" 
str || str.length 
{% endhighlight %}

首先假定我們已經都知道邏輯運算子的用法，且對上一篇還有印象。

Ruby 會是這樣判斷的：
1. `str` 空字串是 Truthy value
2. `||` 一者為 true，
3. 既然 `str` 已為 true 
4. 便直接回傳 `str`，也就是 `""`

{% highlight ruby %}
str = ""
str && str.length 
{% endhighlight %}

1. 前面一樣為 Truthy value
2. `&&` 二者須為 true
3. `str` true，`str.length` true => 成立 
4. 空字串長度為 0  

參考 [這篇]["truthy" and "falsey" (or "falsy") in Ruby] 的方法，建議進入 irb， 帶入參數自己玩看看，印象會更深刻。
{% highlight ruby %}
def and_op(a, b)
  a && b 
end 

def or_op(a, b)
  a || b 
end 

def not_op(a)
  !a 
end
{% endhighlight %}

甚至讓回傳的結果更完整可以嘗試看看：
#### 1. `&&`, and   
{% highlight ruby %}
def print_and_op(a, b)
  return_value = and_op(a, b)
  if return_value 
    puts "#{a.inspect} && #{b.inspect} => #{return_value.inspect}, is truthy"
  else 
    puts "#{a.inspect} && #{b.inspect} => #{return_value.inspect}, is falsey"
  end 
end 

print_and_op(nil, true)
=> ?
{% endhighlight %}

#### 2. `||`, or 
{% highlight ruby %}
def print_or_op(a, b)
  return_value = or_op(a, b)
  if return_value 
    puts "#{a.inspect} || #{b.inspect} => #{return_value.inspect}, truthy"
  else 
    puts "#{a.inspect} || #{b.inspect} => #{return_value.inspect}, falsey"
  end 
end 

print_or_op(false, true)
=> ?
{% endhighlight %}

#### 3. `!`, not 
{% highlight ruby %}
def print_not_op(a)
  return_value = not_op(a)
  if return_value 
    puts "!#{a.inspect} => #{return_value.inspect}, truthy"
  else 
    puts "!#{a.inspect} => #{return_value.inspect}, falsey"
  end 
end 

print_not_op(0)
=> ?
{% endhighlight %}

這是基本中的基本，花一點時間加深印象絕對值得。至少不必像我一樣在面試時回答的支支唔唔，一方面心裡也心虛...共勉之。

### Reference 
- [Logical operators] 
- ["truthy" and "falsey" (or "falsy") in Ruby]





[Logical operators]: http://ruby-for-beginners.rubymonstas.org/operators/logical.html
["truthy" and "falsey" (or "falsy") in Ruby]: https://gist.github.com/jfarmer/2647362