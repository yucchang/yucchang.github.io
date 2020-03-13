---
layout: post
title:  Class
tags:	[programming, Ruby]
---

在前幾篇都有提到 Ruby 是**物件導向程式語言 (Object-oriented programming)**。一直提到**物件導向**，但説到底**物件**在程式世界到底是什麼?

接下來會花 2-3 篇介紹實現物件導向程式設計的重要元素：**類別(Class)**、**物件(Object)**、**模組(Module)**

## Class

**物件(Object) = 狀態(State) + 行為(Behavior)**

那物件跟類別的關聯又是如何？

#### Object has class:

首先，我們可以對物件使用 `class` 或是 `is_a?` 判斷屬哪種類別，

```ruby
"string no.1".class # => String
[2, 5, 6].class # => Array

"string no.1".is_a?(String) # => true
[2, 5, 6].is_a?(Array) # => true
```

#### Class creates object:

在我們將 `"string no.1"` 輸入的時候，其實是 Ruby String 類別內建的 `new` 方法產生一個 `"string no.1"`實體。

```ruby
String.new("string no.1") # => "string no.1"
```

生活化一點的就像是龍哥[為你自己學 Ruby on Rails]提到的，雞蛋糕烤盤跟各造型雞蛋糕的例子。烤盤就是**類別(Class)**，將原料倒進去，翻轉個幾下就能做出又香又具各種造型的雞蛋糕，不管是皮卡丘、海綿寶寶的雞蛋糕在物件導向程式中都稱為**實體(Instance)**。

### How to Define Class?
要定義類別起手式就是使用 `class`，
```ruby
class Pikachu
end
```

定義一個類別 `Pikachu`。再來，我們要賦予皮卡丘『們』一些招式。

**小知識：** 皮卡丘屬於群體，不是只有小智旁邊那隻皮卡丘。

![pikachu](/img/pikachu.png)
（你在開玩笑嗎，這不是同一隻皮卡丘角色扮演）

皮卡丘s 會有一些招式(Behaviors)，在 Ruby 我們稱方法(Methods)，使用 `def` 定義, 比如：
```ruby
class Pikachu
...
  def thunderbolt
    puts "pikachuuuuuuu"
  end
...
end
```

這樣子，皮卡丘s 就學會 `thunderbolt` 十萬伏特了。所以皮卡丘群體裡的皮卡丘就都會使用十萬伏特了。
```ruby
Ryan = Pikachu.new
Ryan.thunderbolt # => pikachuuuuuuu

Aminu = Pikachu.new
Aminu.thunderbolt # => pikachuuuuuuu
```

使用 `new` 方法，可以從皮卡丘類別中產生一隻新的皮卡丘，一個實體(instance)。而新生的皮卡丘都能使用在類別裡賦予的十萬伏特。

在 Ruby 裡使用方法，就很像一個我們都很熟悉的畫面是，小智在對皮卡丘説：『皮卡丘，使出十萬伏特！』，皮卡丘回應：『 Pikachuuuuuuu 』，同時使出十萬伏特。

**大木博士：類別名稱一定要是常數，命名慣例上以 `CamelCase` 駝峰式命名。方法與變數名稱則使用 `snake_case`蛇式命名為慣例。**

#### Initialize(三分天註定)
假設要讓一隻新生的皮卡丘，出生就有名字、性別，在 `Pikachu`類別裡設定一個 初始化(Initialize)方法，再來 `new` 的時候設定名字、性別作為參數(argument)。但這樣還不足夠讓皮卡丘出生就有名字、性別。

```ruby
class Pikachu
  def initialize(name, gender)
    @name = name
    @gender = gender
  end
end

Pikachu.new("Ryan", "Male")
```

設定好參數傳進去，`initialize` 方法裡必須設定 `@name`, `@gender` 等實體變數(instance variables)去接這兩組參數。

現在 Ryan 知道他的名字跟性別了，但當你問雷恩：『請問你的性別是？』，雷恩卻回答不出來(笑)。為什麼？記得剛剛提到小智可以讓皮卡丘使用十萬伏特，是因為我們為 `Pikachu`類別賦予了 `thunderbolt`招式(Method)嗎？

同理，要讓 Ryan 能夠回答自己的性別，我們還需要賦予 `Pikachu` 這個行為，
```ruby
class Pikachu
  def initialize(name, gender)
    @name = name
    @gender = gender
  end

  def gender
    @gender
  end

  def name=(new_name)
    @name = new_name
  end
end

pikachu01 = Pikachu.new("Ryan")
pikachu01.gender = Male
```

這樣一來，我們就能從外部讀取實體的屬性(attributes)名字、性別了。

但這樣似乎缺乏彈性，在 `new` 實體時，就必須決定各項屬性，且無法再更改。(就真的什麼都天註定了)

舉個實際例子來說，一個網站，註冊成為使用者後，後續使用者應該可以再更改密碼，作法是設定 `password=(new_password)`。這邊的例子以 `name=(new_name)` 讓外部可以再設定名字這項屬性，這時我們就可以替 Ryan 改名了。

```ruby
pikachu01 = Pikachu.new("Ryan")
pikachu01.name("Kai")
pikachu01.name # => Kai
```

如果有理解上述的慨念，可以試著找找下面範例有什麼瑕疵
```ruby
class User
  def initialize(name, password)
    @name = name
    @passowrd = password
  end

  def name
    @name
  end

  def password
    @password
  end

  def name=(new_name)
    @name = new_name
  end
end
```

答：以這樣設定，使用者只能更改名字，而無法再更改個人密碼。

不過通常 `User` 不會只有 name, password 這兩項屬性，如果每項屬性都要這樣才能設定、讀取，程式碼僅不是會落落長？

所以 Ruby 提供了 `attr_reader`, `attr_writer`, `attr_accessor` 方法讓程式碼能更簡潔，使用這些方法來改寫看看吧！

```ruby
class User
  attr_accessor :name, :password

  def initialize(name, password)
    @name = name
    @password = password
  end  
end
```

`attr_reader` 作用是讓屬性可以讀取，`attr_writer` 讓屬性可以設定，而 `attr_accessor` 則綜合二者使屬性可讀取、設定。

---
#### Scope of Variables
設定實體變數(instance variables)，`@name`, `@password` 的目的是可以在 `User` 裡反覆使用，但記得 Scope 僅限於 `User`，那為什麼不用區域變數(local variables)？因為區域變數存取的範圍更限縮。

```ruby
number = 2
number # => 2

def add(num)
  num + 1
end
num # => NameError (undefined local variable or method 'number')

num = 1
def add(num)
  num + 1
end
num # => ?
add(2) # => ?
```

想像 `def...end` 是一堵牆，區域變數(local variables)無法在牆外生存，Ruby 在 `def...end` 外找不到，所以回傳 `undefined local variable`。

以這樣的設定，在 `add(2)` 就會去找 `add`方法，參數指定給 `add` 方法的區域變數 `num`。

而當我們輸入 `num` 時， Ruby 自然就會去找到在牆外的 `num = 1`，以此證明區域變數的作用範圍。

#### Instance Method & Class Method
根據作用的對象不同，Ruby 又分為實體方法或類別方法。照字面來看，實體方法作用對象為實體(instance)本身，
```ruby
ryan = Pikachu.new("Ryan")
ryan.thunderbolt
```

在這邊我們是以 `ryan` 實體為對象發送訊息『使出十萬伏特！』

在 Rails Controller，我們常見到：

```ruby
class BooksController <  ApplicationController
  def index
    @books = Book.all
  end
end
```

這裡 `all` 作用對象明顯為 `Book`類別。

要定義類別方法實際上有有幾種方式，
```ruby
# 1
class Pikachu
  self.all
    ...
  end
end

# 2
class Pikachu
  class << self
    def all
     ...
    end
  end
end

# 3
class Pikachu; end
def Pikachu.all
  ...
end
```

在需要設定許多類別方法時，第二個方法或許更適合。

#### Access Control
Ruby 有三種可以控制方法的存取，分別為 `public`, `private`, `protected`。沒特別限制的方法就會是 `public` 可以直接存取， 如果我們使用 `private`, `protected` 把剛剛的範例改成這樣：
```ruby
class Pikachu
  protected
  def quick_attack
    puts "pikaaa"  
  end

  private
  def thunderbolt
    puts "pikachuuuuuuu"
  end
end
```

你會發現現在不管怎麼求皮卡丘使用 `quick_attach` 或是 `thunderbolt` 都沒有用，取而代之的是 `NoMethodError` 彷彿遺忘了這些招式。不管是 `private`, `protected` 都限制了方法從外部使用。

`private`:
1. 控制方法僅能於作為同類別內部使用。
2. 不能有接收者(receiver)。還記得我們使用招式時的指令嗎？對皮卡丘說：『皮卡丘，使出十萬伏特！』。這段指令中，皮卡丘就是接收者，不過現在皮卡丘招式摳不出來，要被小茂打敗了，怎麼辦？

```ruby
 class Pikachu  
  def combo
    puts "discharge"
    thunderbolt   
  end

  private
  def thunderbolt
    puts "pikachuuuuuuu"
  end
end

pikachu = Pikachu.new
pikachu.thunderbolt # => NoMethodError
pikachu.combo
=> discharge
=> pikachuuuuuuu
```

在不違反這兩項條件下，使用 `public` 的 `combo`技讓皮卡丘充電完後，再使用十萬伏特。(原來是沒充電啊！) - 題外話：小時候玩被小茂打敗就會特別 7pupu，可能因為他太唱秋？

實際使用 Rails 在開發過程，我們一定會使用到的作法：
```ruby
class BooksController < ApplicationController
  ...
  def create
    @book = Book.new(book_params)
    if @book.save
      redirect_to admin_books_path
    else
      render :new
    end
  end
  ...
  private
  def book_params
    params.require(:book).permit(:title, :price, :author)
  end  
end
```

會設定 `book_params` 為 `private` 方法的目的在於：`book_params` 用於檢查使用者輸入的資料，避免被不預期的資料傳入影響資料庫，基於安全性，我們將此方法設為 `private`。(這個安全機制在 Rails 稱為 Strong Parameter)

至於 `protected` 從外部一樣無法存取，但能在不同類別間使用(可以有 receiver, 但只能是自己 `self`)

```ruby
class Pikachu
  protected
  def shake_tail
    puts "shakeshack"
  end
end

class Pichu < Pikachu
  def combo
    shake_tail
    quick_attack
  end

  private
  def quick_attack
    puts "DPS 12"
  end
end

pichu = Pichu.new
pichu.shake_tail # => NoMethodError
pichu.combo
=> shakeshack
=> DPS 12
```

這裡的 `Pichu < Pikachu` 是物件導向的繼承(Inheritance)，繼承的概念及運用會在討論物件導向時再提起。

**大木博士：其實我們平常使用的 `puts` 也是 `private`方法喔！**

很明顯的在使用 `puts` 時，前面並沒有作用對象(receiver)，屬於 `Object`類別的 `private`方法之一。試試看，
```ruby
puts "privacy" # => privacy
self.puts "privacy" # => NoMethodError
```

接下來會繼續討論到**模組(Module)**。

### Reference
- [為你自己學 Ruby on Rails](https://railsbook.tw/chapters/08-ruby-basic-4.html)
- [Rubymostas](http://ruby-for-beginners.rubymonstas.org/writing_classes/methods.html)
- [Ruby Access Control Basics: Public vs Private vs Protected methods](https://medium.com/@tjoye20/ruby-access-control-basics-public-vs-private-vs-protected-methods-7788b26e04a7)

##### 後話：寫這篇很危險，邊寫邊釐清觀念的過程中，一不注意就......飛太遠～ 但終究是分享學習紀錄後出現的好現象。因不想篇幅太冗長(閱讀友善)，有些觀念沒辦法一篇慨全。
