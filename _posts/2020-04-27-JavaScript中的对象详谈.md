---
layout:     post                    # 使用的布局（不需要改）
title:      JavaScript中的对象详谈         # 标题 
subtitle:    #副标题
date:       2020-04-27             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - JavaScript
---

*本篇摘选自Mozilla的JavaScript文档： https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Details_of_the_Object_Model*

## 前言

JavaScript 是一种基于原型(Prototype)而不是基于类的基于对象(object-based)语言。正是由于这一根本的区别，其如何创建对象的层级结构以及对象的属性与属性值是如何继承的并不是那么清晰。  

## 基于类 vs 基于原型的语言

基于类的面向对象语言，比如 Java 和 C++，是构建在两个不同实体之上的：**类和实例**。

1. 一个类(class)定义了某一对象集合所具有的特征性属性（可以将 Java 中的方法和域以及 C++ 中的成员都视作属性）。类是抽象的，而不是其所描述的对象集合中的任何特定的个体。例如 Employee 类可以用来表示所有雇员的集合。
2. 另一方面，一个实例(instance)是一个类的实例化。例如， Victoria 可以是 Employee 类的一个实例，表示一个特定的雇员个体。实例具有和其父类完全一致的属性，不多也不少。  

基于原型的语言（如 JavaScript）并不存在这种区别：它只有对象。基于原型的语言具有所谓原型对象(prototypical object)的概念。原型对象可以作为一个模板，新对象可以从中获得原始的属性。**任何对象都可以指定其自身的属性，既可以是创建时也可以在运行时创建。而且，任何对象都可以作为另一个对象的原型(prototype)，从而允许后者共享前者的属性。**  

### 定义类

在基于类的语言中，需要专门的类定义(class definition)来定义类。在定义类时，允许定义被称为构造器(constructor)的特殊的方法来创建该类的实例。在构造器方法中，可以指定实例的属性的初始值并做一些其他的操作。你可以通过使用 new 操作符来创建类的实例。

JavaScript 大体上与之类似，但并没有专门的类定义，你通过定义构造函数的方式来创建一系列有着特定初始值和方法的对象。任何JavaScript函数都可以被用作构造函数。你也可以使用 new 操作符来创建一个新对象。  

### 子类和继承

基于类的语言是通过对类的定义中构建类的层级结构的。在类定义中，可以指定新的类是一个现存的类的子类。子类将继承父类的全部属性，并可以添加新的属性或者修改继承的属性。例如，假设 Employee 类只有 name 和 dept 属性，而 Manager 是 Employee 的子类并添加了 reports 属性。这时，Manager 类的实例将具有所有三个属性：name，dept和reports。  

JavaScript 通过将构造器函数与原型对象相关联的方式来实现继承。这样，您可以创建完全一样的 Employee — Manager 示例，不过需要使用略微不同的术语。首先，定义Employee构造函数，在该构造函数内定义name、dept属性；接下来，定义Manager构造函数，在该构造函数内调用Employee构造函数，并定义reports属性；最后，将一个获得了Employee.prototype(Employee构造函数原型)的新对象赋予manager构造函数，以作为Manager构造函数的原型。之后当你创建新的Manager对象实例时，该实例会从Employee对象继承name、dept属性。  

## Employee 示例

### JavaScript中对象的层级

![avatar](https://mdn.mozillademos.org/files/3060/figure8.1.png)

通过一个实例来深入浅出JavaScript中对象的层级结构。具体的UML图如上。  

Manager 和 WorkerBee 的定义表示在如何指定继承链中上一层对象时，两者存在不同点。在 JavaScript 中，您会添加一个原型实例作为构造器函数`prototype` 属性的值，然后将该构造函数原型的构造器重载为其自身。这一动作可以在构造器函数定义后的任意时刻执行。而在 Java 中，则需要在类定义中指定父类，且不能在类定义之外改变父类。  

```
function Manager() {
  Employee.call(this);
  this.reports = [];
}
Manager.prototype = Object.create(Employee.prototype);

function WorkerBee() {
  Employee.call(this);
  this.projects = [];
}
WorkerBee.prototype = Object.create(Employee.prototype);
```  

在对Engineer 和 SalesPerson 定义时，创建了继承自 WorkerBee 的对象，该对象会进而继承自Employee。这些对象会具有在这个**链**之上的所有对象的属性。另外，它们在定义时，又重载了继承的 dept 属性值，赋予新的属性值。  

```
function SalesPerson() {
   WorkerBee.call(this);
   this.dept = 'sales';
   this.quota = 100;
}
SalesPerson.prototype = Object.create(WorkerBee.prototype);

function Engineer() {
   WorkerBee.call(this);
   this.dept = 'engineering';
   this.machine = '';
}
Engineer.prototype = Object.create(WorkerBee.prototype);
```  

## 对象的属性

本节将讨论对象如何从原型链中的其它对象中继承属性，以及在运行时添加属性的相关细节。

### 继承属性

假设您通过如下语句创建一个mark对象作为 WorkerBee的实例：

```
var mark = new WorkerBee;
```  

当 JavaScript 执行 new 操作符时，它会先创建一个普通对象，并将这个普通对象中的 [[prototype]] 指向 WorkerBee.prototype ，然后再把这个普通对象设置为执行 WorkerBee 构造函数时 this  的值。该普通对象的 [[Prototype]] 决定其用于检索属性的原型链。当构造函数执行完成后，所有的属性都被设置完毕，JavaScript 返回之前创建的对象，通过赋值语句将它的引用赋值给变量 mark。  

这个过程不会显式的将 mark所继承的原型链中的属性作为本地属性存放在 mark 对象中。当访问属性时，JavaScript 将首先检查对象自身中是否存在该属性，如果有，则返回该属性的值。如果不存在，JavaScript会检查原型链（使用内置的 [[Prototype]] ）。如果原型链中的某个对象包含该属性，则返回这个属性的值。如果遍历整条原型链都没有找到该属性，JavaScript 则认为对象中不存在该属性，返回一个 undefined。这样，mark 对象中将具有如下的属性和对应的值：
```
mark.name = "";
mark.dept = "general";
mark.projects = [];
```  

mark 对象从 mark.__proto__ 中保存的原型对象里继承了 name 和 dept 属性。并由 WorkerBee 构造函数为 projects 属性设置了本地值。 这就是 JavaScript 中的属性和属性值的继承。  

### 添加属性

在 JavaScript 中，您可以在运行时为任何对象添加属性，而不必受限于构造函数提供的属性。添加特定于某个对象的属性，只需要为该对象指定一个属性值，如下所示：  
```
mark.bonus = 3000;

```
这样 mark 对象就有了 bonus 属性，而其它 WorkerBee 则没有该属性。  
如果您向某个构造函数的原型对象中添加新的属性，那么该属性将添加到从这个原型中继承属性的所有对象的中。例如，可以通过如下的语句向所有雇员中添加 specialty 属性：  
```
Employee.prototype.specialty = "none";
```

