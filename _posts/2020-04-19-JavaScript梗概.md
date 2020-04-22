---
layout:     post                    # 使用的布局（不需要改）
title:      JavaScript梗概          # 标题 
subtitle:    #副标题
date:       2020-04-19             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - JavaScript
---

## 前言

最近在看Mozilla的JavaScript的tutorial系列，好记性不如烂笔头，个中感悟通过blogs来记录一下。  

## 基础

JavaScript是一种借鉴了Java, C, and C++以及Python etc等的语言，默认使用*Unicode*字符集且大小写敏感。  

### Declarations

最著名的`let`,`var`,`const`的区别始自ECMAScript 2015，其中的核心是所定义关键字的作用域不同。  `var`的默认作用域是global,而`let`,`const`则作用域block之内。  

### Variable hoisting

意为在JavaScript中，变量的定义会被“抬升”到function的最上面。  
 `var` \ `let` 的默认值是`undefined`，但是在 `let`定义之前使用会导致`ReferenceError`.  

 ```
 var a;
console.log('The value of a is ' + a); // The value of a is undefined

console.log('The value of b is ' + b); // The value of b is undefined
var b;
// This one may puzzle you until you read 'Variable hoisting' below

console.log('The value of c is ' + c); // Uncaught ReferenceError: c is not defined

let x;
console.log('The value of x is ' + x); // The value of x is undefined

console.log('The value of y is ' + y); // Uncaught ReferenceError: y is not defined
let y;
 ```

## Data structures and types

JavaScript中有8种data type:
1. Boolean
2. null
3. undefined
4. Number
5. BigInt
6. String
7. Symbol
8. Object

## Literals

Literals指的是JavaScript中的value，分为：

1. Array literals
2. Boolean literals
3. Floating-point literals
4. Numeric literals
5. Object literals
6. RegExp literals
7. String literals

## String

建议使用String Literals 而不是String Object。

## Indexed collections

Indexed collections 指的是可以按照index排序的data collection,包括`Array`以及`TypedArray`.  

### Array

#### Initiating Array

JavaScript中Array初始化的方法：  
```
let arr = new Array(element0, element1, ..., elementN)
let arr = Array(element0, element1, ..., elementN)
let arr = [element0, element1, ..., elementN]
```
如果想要初始化不含元素的非0长度Array，可以如下：  
```
// This...
let arr = new Array(arrayLength)

// ...results in the same array as this
let arr = Array(arrayLength)


// This has exactly the same effect
let arr = []
arr.length = arrayLength
```
需要注意的是在Array初始化时要注意区分Array的长度以及单个元素的初始化：  
```
let arr = [42]       // Creates an array with only one element:
                     // the number 42.

let arr = Array(42)  // Creates an array with no elements
                     // and arr.length set to 42. 
                     //
                     // This is equivalent to:
let arr = []
arr.length = 42
```

在ES2015中，如果想要生成single item的array，可以使用如下方法：  
```
let wisenArray = Array.of(9.3)   // wisenArray contains only one element 9.3
```  

如果在Array中传入一个非零的值，将会设置一个Array的property而非Index:  
```
let arr = []
arr[3.4] = 'Oranges'
console.log(arr.length)                 // 0
console.log(arr.hasOwnProperty(3.4))    // true
```  
#### Iterating over arrays

最简单的自然是for/foreach循环：  
```
let colors = ['red', 'green', 'blue']
for (let i = 0; i < colors.length; i++) {
  console.log(colors[i])
}

let colors = ['red', 'green', 'blue']
colors.forEach(function(color) {
  console.log(color)
})
// red
// green
// blue
```
在ES2015之后,简化了foreach的调用：
```
let colors = ['red', 'green', 'blue']
colors.forEach(color => console.log(color))
// red
// green
// blue
```

#### Array Methods   

Array自带`concat() push() pop() foreach() map()`等方法，[详细的解释在这里](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Indexed_collections)。  



### Typed Array

Typed array是类似Array机制的结构，用来访问binary data。  
在具体实现中，Typed array具有`buffers` `views`两种结构，`buffers`用来存储具体的数据，不能直接被访问，如果需要直接访问数据，需要使用`views`。  


## Keyed collections

Keyed collections指的是按照key进行排序的数据组合，在JavaScript中有`Map` & `Set`。

### Map

`Map`是ECMAScript 2015引入的对象，可以使用`for ..of`进行遍历。  `Map`可以接受`Strings`或者`Symbols`作为Key。  

### Set  

`Set`是对象的集合，也可以遍历。`Set`中的`Value`必须是唯一的。  

## Working with objects

### Objects and properties

一个简单的Object的初始化如下：  
```
var myCar = new Object();
myCar.make = 'Ford';
myCar.model = 'Mustang';
myCar.year = 1969;
```

### Creating new objects

1. Using object initializers

```
var myHonda = {color: 'red', wheels: 4, engine: {cylinders: 4, size: 2.2}};
```  

2. Using a constructor function  

```
function Car(make, model, year) {
  this.make = make;
  this.model = model;
  this.year = year;
}

var mycar = new Car('Eagle', 'Talon TSi', 1993);

```  

3. Using the Object.create method

```
// Animal properties and method encapsulation
var Animal = {
  type: 'Invertebrates', // Default value of properties
  displayType: function() {  // Method which will display type of Animal
    console.log(this.type);
  }
};
// Create new animal type called animal1 
var animal1 = Object.create(Animal);
animal1.displayType(); // Output:Invertebrates

// Create new animal type called Fishes
var fish = Object.create(Animal);
fish.type = 'Fishes';
fish.displayType(); // Output:Fishes
```

### Indexing object properties

Object的property可以通过Index或者property name访问。需要注意的是，如果定义时是通过property name，则只能通过property name访问，同理适用于Index定义的property.

### Using this for object references

`this`使得我们可以在function内引用当前的对象。  

```
const Manager = {
  name: "John",
  age: 27,
  job: "Software Engineer"
}
const Intern= {
  name: "Ben",
  age: 21,
  job: "Software Engineer Intern"
}

function sayHi() {
    console.log('Hello, my name is', this.name)
}

// add sayHi function to both objects
Manager.sayHi = sayHi;
Intern.sayHi = sayHi; 

Manager.sayHi() // Hello, my name is John'
Intern.sayHi() // Hello, my name is Ben'
```

### Defining getters and setters

`getter` `setter`方法既可以在 object initializers中定义，也可以在初始化对象之后再加入。

