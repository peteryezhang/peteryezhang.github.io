---
layout:     post                    # 使用的布局（不需要改）
title:      JavaScript中的对象          # 标题 
subtitle:    #副标题
date:       2020-04-26             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - JavaScript
---

## 前言

JavaScript作为一种基于对象的语言，与以往基于Class的语言有着较大不同。在JavaScript中一个对象就是一系列属性的集合，一个属性包含一个名和一个值。一个属性的值可以是函数，这种情况下属性也被称为方法。  

## 对象概述

在javascript中，一个对象可以是一个单独的拥有属性和类型的实体。  

## 对象和属性

一个 javascript 对象有很多属性。一个对象的属性可以被解释成一个附加到对象上的变量。  
对象中未赋值的属性的值为undefined（而不是null）。
```
myCar.noProperty; // undefined
```
JavaScript 对象的属性也可以通过方括号访问或者设置（更多信息查看 property accessors）. 对象有时也被叫作关联数组, 因为每个属性都有一个用于访问它的字符串值。例如，你可以按如下方式访问 myCar 对象的属性：  
```
myCar["make"] = "Ford";
myCar["model"] = "Mustang";
myCar["year"] = 1969;
``` 
一个对象的属性名可以是任何有效的 JavaScript 字符串，或者可以被转换为字符串的任何类型，包括空字符串。然而，一个属性的名称如果不是一个有效的 JavaScript 标识符（例如，一个由空格或连字符，或者以数字开头的属性名），就只能通过方括号标记访问。这个标记法在属性名称是动态判定（属性名只有到运行时才能判定）时非常有用。例如：  
```
// 同时创建四个变量，用逗号分隔
var myObj = new Object(),
    str = "myString",
    rand = Math.random(),
    obj = new Object();

myObj.type              = "Dot syntax";
myObj["date created"]   = "String with space";
myObj[str]              = "String value";
myObj[rand]             = "Random Number";
myObj[obj]              = "Object";
myObj[""]               = "Even an empty string";

console.log(myObj);
```  

## 枚举一个对象的所有属性

从 `ECMAScript 5` 开始，有三种原生的方法用于列出或枚举对象的属性：

1. for...in 循环
  该方法依次访问一个对象及其原型链中所有可枚举的属性。
2. Object.keys(o)
  该方法返回对象 o 自身包含（不包括原型中）的所有可枚举属性的名称的数组。
3. Object.getOwnPropertyNames(o)
  该方法返回对象 o 自身包含（不包括原型中）的所有属性(无论是否可枚举)的名称的数组。
    
## 创建新对象

JavaScript 拥有一系列预定义的对象。另外，你可以创建你自己的对象。从  `JavaScript 1.2` 之后，你可以通过:
1. 对象初始化器（Object Initializer）创建对象。
2. 或者你可以创建一个构造函数并使用该函数和 new 操作符初始化对象。  

### 使用对象初始化器

使用对象初始化器也被称作通过字面值创建对象。对象初始化器与 C++ 术语相一致。  
通过对象初始化器创建对象的语法如下：
```
var obj = { property_1:   value_1,   // property_# 可以是一个标识符...
            2:            value_2,   // 或一个数字...
           ["property" +3]: value_3,  //  或一个可计算的key名... 
            // ...,
            "property n": value_n }; // 或一个字符串
```

如果一个对象是通过在顶级脚本的对象初始化器创建的，则 JavaScript 在每次遇到包含该对象字面量的表达式时都会创建对象。同样的，在函数中的初始化器在每次函数调用时也会被创建。

### 使用构造函数

作为另一种方式，你可以通过两步来创建对象：

1. 通过创建一个构造函数来定义对象的类型。首字母大写是非常普遍而且很恰当的惯用法。
2. 通过 new 创建对象实例。
为了定义对象类型，为对象类型创建一个函数以声明类型的名称、属性和方法。例如，你想为汽车创建一个类型，并且将这类对象称为 car ，并且拥有属性 make, model, 和 year，你可以创建如下的函数：
```
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;
}
```
注意通过使用 this 将传入函数的值赋给对象的属性。  

### 使用 Object.create 方法

对象也可以用 `Object.create()` 方法创建。该方法非常有用，因为它允许你为创建的对象选择一个原型对象，而不用定义构造函数。

## 定义方法

一个方法 是关联到某个对象的函数，或者简单地说，一个方法是一个值为某个函数的对象属性。定义方法就像定义普通的函数，除了它们必须被赋给对象的某个属性。查看 method definitions了解更多详情例如：  
```
objectName.methodname = function_name;

var myObj = {
  myMethod: function(params) {
    // ...do something
  }
  
  // 或者 这样写也可以
  
  myOtherMethod(params) {
    // ...do something else
  }
};
```  

## 删除属性

你可以用 delete 操作符删除一个不是继承而来的属性。下面的例子说明如何删除一个属性：  
```
//Creates a new object, myobj, with two properties, a and b.
var myobj = new Object;
myobj.a = 5;
myobj.b = 12;

//Removes the a property, leaving myobj with only the b property.
delete myobj.a;
```  

如果一个全局变量不是用 var 关键字声明的话，你也可以用 delete 删除它：

```
g = 17;
delete g;
```  

## 比较对象

在 JavaScript 中 objects 是一种引用类型。两个独立声明的对象永远也不会相等，即使他们有相同的属性，只有在比较一个对象和这个对象的引用时，才会返回true.  

```
// 两个变量, 同一个对象
var fruit = {name: "apple"};
var fruitbear = fruit;  // 将fruit的对象引用(reference)赋值给 fruitbear
                        // 也称为将fruitbear“指向”fruit对象
// fruit与fruitbear都指向同样的对象
fruit == fruitbear // return true
fruit === fruitbear // return true
```  
