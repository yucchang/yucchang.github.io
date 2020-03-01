---
layout: post
title:  "Quack - Duck Typing"
date:   2020-02-29 20:30
author: 賈蓋先
categories: Programming  
tags:	Ruby 
cover: 
---
前幾篇都在提構成物件導向的特性。除物件導向特性外，Ruby 也是**動態型別(Dynamically Typed)**的程式語言，蛤!蛤? 啥小朋友... (是的。在學習程式語言時，我大多數時候的第一反應) 

但好險，這不在這篇討論範圍，不過遲早要面對 : ) 

## Duck Typing
目前只需知道，Ruby 是動態型別的物件導向程式語言就好，而**鴨子型別(Duck Typing)**則是為了實現這兩大特性的實踐，也可以說是 Ruby 設計原則。

這裡也不免俗地要引用一行解簡述何謂鴨子型別：
>>If it walks like a duck and quacks like a duck, then it must be a duck. 

上面這段話，可以綜觀鴨子型別的慨念，接下來我們用 code 翻譯看看，
{% highlight ruby %}
class Duck
  def quacking
    puts "quack"
  end 

  def walking
    puts "tida tida"
  end
end 

class Penguin
  def quacking
    puts "quack like duck"
  end 

  def walking 
    puts "tida like duck"
  end
end 

def go_quacking(duck)
  duck.quacking
end

puts go_quacking(Duck.new)
=> quack
puts go_quacking(Penguin.new)
=> quack like duck
{% endhighlight %}

有一個類別 `Duck`，有 `quacking`, `walking` 方法(行為)，另個類別 `Penguin`，同樣模仿了 `quacking`, `walking` 等行為。現在請他們都表演 `go_quacking`，結果企鵝意外的可以學鴨子叫? 這不是巧合，這正是鴨子型別的特性：

在執行 `go_quacking` 方法時，不管傳的是 `Duck` 還是 `Penguin` 實體，只要他們都有 `quacking`，企鵝也可以學鴨叫。

由此可以推導，『只要行為跟鴨子一樣，不管實體是什麼，都會被當成鴨子。』不考慮一個物件是哪個類別的實體，只在乎物件具備怎麼樣的行為(方法 Method)，就是 **Duck Typing**。

**Duck Typing in Real Life**
![fake_outlet](/assets/fake-outlet.jpg)

### Conclusion 
可以看得出 Duck Typing 的設計，讓型別變得~~很隨便~~更有彈性，讓開發者設計時有更高自由度。

### Why Duck Typing?
在這篇 [Ruby.Codemy] 文章中，作者提到他問 Matz：

Why encouraging the use of Duck Typing in Ruby? 
>>Unlike static and dynamic typing of variables (and expressions), **duck typing is not a language feature, but a design principle which is a combination of dynamic typing and object-oriented programming.** Since Ruby is a dynamically typed object-oriented programming language, it is natural for the Ruby community to encourage duck typing. - Matz 

### Reference 
- [Why the Ruby community encourages Duck Typing][Ruby.Codemy]
- [Duck Typing]
- [Duck, Duck, Ruby: Duck Typing in Ruby]



[Ruby.Codemy]: https://medium.com/rubycademy/why-ruby-community-encourages-duck-typing-2e5fb529fca1
[Duck Typing]: http://rubylearning.com/satishtalim/duck_typing.html
[Duck, Duck, Ruby: Duck Typing in Ruby]: https://revelry.co/duck-typing-with-ruby/