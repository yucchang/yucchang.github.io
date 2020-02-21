---
layout: post
title:  "Class, Module"
date:   2020-02-21 10:00
author: 賈蓋先
categories: Programming  
tags:	Ruby 
cover: 
---

在前幾篇都有提到 Ruby 是**物件導向程式語言 (Object-oriented programming)**。一直提到**物件導向**，但説到底**物件**在程式世界到底是什麼? 

接下來就要介紹實現物件導向程式設計的重要元素：**類別(Class)**、**物件(Object)**、**模組(Module)** 

### Object
直覺看到物件，大概會想到房地產業，用來形容房子、房間、土地的『物件』吧。

物件在程式世界裡會是：

**物件(Object) = 狀態(State) + 行為(Behavior)** 

舉出現實生活中的例子，比如：有隻一貓名叫 “酷羅”, 毛色是 “黑的”、“眼白黃的”、“瞳孔黑的” 這些狀態，酷羅會 “跳”、會 “走路”、還會 “把阿嬤吃掉” ？！(什麼鬼？)這些行為。

![kuro](/assets/kuro.jpeg)

## Class 
那物件跟類別的關聯又是如何？

**Object has class:** 

首先，我們可以對物件使用 `class` 判斷是屬哪種類別，

Class create object:

既然我們知道 `"string no.1"` 屬於 字串(String) 類別，從 Ruby.doc 那裡得知 String 有個 `upcase` 的方法可以將英文字母全部變大寫。那便可以大膽推測，從 String 類別產生出來的 `string no.1` 物件可以使用這個方法。往回推，String 類別裡應該記載著這個 `upcase` 方法。



## Module