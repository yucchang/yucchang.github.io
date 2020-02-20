---
layout: post
title:  "Compare Symbol to String"
date:   2020-02-14 18:00
author: 賈蓋先
categories: Programming  
tags:	Ruby
cover: 
---

趁著再釐清觀念，打一篇加深自己的印象，順便整理一下學習隨筆。其實，龍哥在上課期間就不斷強調的是學習過程中必須去思考 **” 這個是什麼？“**、**“ 為什麼要使用? ”**、 **“ 什麼時候使用？”**、**“ 使用了有什麼好處？”**。所以思緒脈絡大致會遵循這幾個原則，撩落去～

## 什麼是 String (字串) 什麼是 Symbol (符號)?
**在 Ruby 裡, String (字串), Symbol (符號) 都是 Class (類別) 的一種。下面使用`class`這個方法驗證。**

**String:** A string object typically representing *characters*.

白話：任何以單引號或雙引號包起來的，在 Ruby 裡就是字串。例如：`"cube"`, `'cube'` 甚至是 `":)"` 

{% highlight ruby %}
>> "cube".class  
>>'cube'.class
>> ":)".class
=> String    # 類別都是 String 
{% endhighlight %}

**Symbol:** Symbol object represents names and some strings in Ruby. 

白話：在文字前面加上冒號，就是符號。例如：`:cube` 其實英文也夠白話，但就是第一次看會：蝦?! 

{% highlight ruby %}
>> :cube.class
=> Symbol 
{% endhighlight %}

## String (字串) & Symbol (符號) 差別?

#### String 內容可以改變，但 Symbol 不行 
{% highlight ruby %}
# 使用中括號填入數字 (index) 來取得字元
>> "cube"[1]
=> "u"
# Symbol 也 OK
>> :cube[1]
=> "u"

# 再來試著修改 String 裡面內容
>> "cube"[2] = "t"
=> "t"
# 那要怎麼知道 "b" 真的被改成 "t" 
>> qq = "cube"
>> qq[2] = "t"
>> qq  # => "cute" 
# Symbol 
>> :cube[2] = "t"
=> NoMethodError (undefined method `[]=' for :cube:Symbol)
{% endhighlight %}

#### Symbol 效能比較好
- 以下以 `object_id` 實測。(`object_id` 代表物件在 Ruby 裡的 `id`，不同的物件想當然有不同 `id` 就像你跟我身份證號碼不一樣。)

{% highlight ruby %}
>>"cube".object_id
=> 70264457789100
>>"cube".object_id
=> 70264457789020
>>"cube".object_id
=> 70264457788960

>> :cube
=> 1524828
>> :cube
=> 1524828
>> :cube
=> 1524828
{% endhighlight %}

由此可知，每次輸入 `"cube"` 其實代表著不同物件。 (就跟你喊 `"怡君"` 很多人轉頭，但每個 `"怡君"` 是不一樣的個體啊! ) 不過這又能證明什麼? (笑) 其實這個 object_id 也表示物件於記憶體的位置，每個 String 明顯佔了不同位置，在 Ruby 要取用時也都得再產生一組 object_id 佔一個位。反之，Symbol 從一而終都是同個物件。

- Symbol 比對速度更快 

我們使用函式庫 [Benchmark] 來量測程式碼執行效能：
{% highlight ruby %}
require 'benchmark'  # => true
looping = 1000000000 

Benchmark.measure do 
  looping.times do 
    "foo" == "foo"
  end
end.total 
=> 14.128060999999999  

Benchmark.measure do 
  looping.times do 
    :foo == :foo
  end
end.total
=> 5.308579999999999
{% endhighlight %}

kerker... 依照我這樣打，量測真的都要等到 total 秒數到。

最終可看出 Symbol 的處理速度快得多，因為 Symbol 單純以 object_id 來比對，而 String 則是會以字元來逐一比對。就比對方式可以推論：Symbol 的處理速度會是比較固定的，但 String 的處理時間會隨著字元的長度而增加。

## String, Symbol 可以互相轉換 

{% highlight ruby %}
# String to Symbol 
>> "cube".to_sym 
>> %s(cube) 
>> "cube".intern # 這方法用起來比較不直覺，但結果都一樣
=> :cube

# Symbol to String 
>> :cube.to_s 
>> :cube.id2name 
=> "cube"
{% endhighlight %}

## Symbol 特性及使用時機
#### Symbol 特性:
1. 不可變更 (Immutable), object_id 有唯一性
2. 效能比 String 好

#### 什麼時候該使用 Symbol:
適合拿來當作 Hash 中的 Key: 

{% highlight ruby %}
>> person = { name: "Rocky", age: "40"}  
=> {:name => "Rocky", :age => "40"}  
{% endhighlight %}

又或者是設定 `attr_accessor`:

{% highlight ruby %}
class Cat  
attr_accessor :name  # 這裡把 :name 傳給 attr_accessor 方法
end

cat1 = cat.new  
cat1.name = "Tuna"
{% endhighlight %}

**BUT...** String 也不是被打假的，String 在 Ruby 中內建的方法比較多，操作上會更靈活。使用上有疑慮，詳閱公開說明書 [Ruby doc][Ruby.doc]


### Reference 
- [龍哥 - 為你自己學 Ruby on Rails][Longer]
- [Ruby.doc]
- [What are Symbols in Ruby?][PHILIP BROWN]
- [Ruby Symbols vs. Strings][Lindsay Criswell]



[Benchmark]: https://ruby-doc.org/stdlib-1.9.3/libdoc/benchmark/rdoc/Benchmark.html 
[Ruby.doc]: https://ruby-doc.org
[Longer]: https://railsbook.tw/chapters/06-ruby-basic-2.html#symbol_class
[Lindsay Criswell]:https://medium.com/@lcriswell/ruby-symbols-vs-strings-248842529fd9
[PHILIP BROWN]: https://culttt.com/2015/04/22/what-are-symbols-in-ruby/