---
layout:     post                    # 使用的布局（不需要改）
title:     Design Patterns in C#   # 标题 
subtitle:     #副标题
date:       2020-08-15             # 时间
author:     Peter                      # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - C#
    
---

首先我必须把这张图放在这里。  

![avatar](https://refactoring.guru/images/patterns/languages/csharp-2x.png)  

# 创建型模式

## 抽象工厂模式 - Abstract Factory

抽象工厂模式是一种创建型设计模式， 它能创建一系列相关的对象， 而无需指定其具体类。  

+ 首先， 抽象工厂模式建议为系列中的每件产品明确声明接口 （例如椅子、 沙发或咖啡桌）。 然后， 确保所有产品变体都继承这些接口。 例如， 所有风格的椅子都实现 椅子接口； 所有风格的咖啡桌都实现 咖啡桌接口， 以此类推。  

+ 接下来， 我们需要声明抽象工厂——包含系列中所有产品构造方法的接口。 例如 create­Chair创建椅子 、 ​ create­Sofa创建沙发和 create­Coffee­Table创建咖啡桌 。 这些方法必须返回抽象产品类型， 即我们之前抽取的那些接口： ​ 椅子 ， ​ 沙发和 咖啡桌等等。

+ 对于系列产品的每个变体， 我们都将基于 抽象工厂接口创建不同的工厂类。 每个工厂类都只能返回特定类别的产品， 例如， ​ 现代家具工厂Modern­Furniture­Factory只能创建 现代椅子Modern­Chair 、 ​ 现代沙发Modern­Sofa和 现代咖啡桌Modern­Coffee­Table对象。  

![avatar](https://refactoringguru.cn/images/patterns/diagrams/abstract-factory/structure-2x.png)  


## 工厂方法模式 - Factory Method

工厂方法模式是一种创建型设计模式， 其在父类中提供一个创建对象的方法， 允许子类决定实例化对象的类型。  

![avatar](https://refactoringguru.cn/images/patterns/diagrams/factory-method/structure-2x.png)



## 工厂模式比较

[工厂模式比较](https://refactoringguru.cn/design-patterns/factory-comparison)
[Differences between Abstract Factory and Factory Method]
### 简单工厂模式

简单工厂模式 描述了一个类， 它拥有一个**包含大量条件语句**的构建方法， 可根据方法的参数来选择对何种产品进行初始化并将其返回。  

### 工厂方法模式

工厂方法 是一种**创建型设计模式**， 其在父类中提供一个创建对象的方法， 允许子类决定实例化对象的类型。  

如果在基类及其扩展的子类中都有一个构建方法的话， 那它可能就是工厂方法。  

```
abstract class Department {
    public abstract function createEmployee($id);

    public function fire($id) {
        $employee = $this->createEmployee($id);
        $employee->paySalary();
        $employee->dismiss();
    }
}

class ITDepartment extends Department {
    public function createEmployee($id) {
        return new Programmer($id);
    }
}

class AccountingDepartment extends Department {
    public function createEmployee($id) {
        return new Accountant($id);
    }
}
```

### 抽象工厂模式

抽象工厂是一种**创建型设计模式**， 它能创建**一系列**相关或相互依赖的对象， 而无需指定其具体类。  

什么是 “系列对象”？ 例如有这样一组的对象： ​ 运输工具 + 引擎 + 控制器 。 它可能会有几个变体：  

+ 汽车 + 内燃机 + 方向盘
+ 飞机 + 喷气式发动机 + 操纵杆
如果你的程序中并不涉及产品系列的话， 那就不需要抽象工厂。



## 生成器模式/建造者模式 Builder

生成器模式建议将对象构造代码从产品类中抽取出来， 并将其放在一个名为生成器的独立对象中。  

*只有当产品较为复杂且需要详细配置时，使用生成器模式才有意义。*  
*使用生成器模式可避免 “重叠构造函数 （telescopic constructor）” 的出现.*  



![avatar](https://refactoringguru.cn/images/patterns/diagrams/builder/solution1-2x.png)  


## 原型模式 -  Clone \ Prototype

原型模式是一种创建型设计模式， 使你能够复制已有对象， 而又无需使代码依赖它们所属的类。  

原型模式将克隆过程委派给被克隆的实际对象。 模式为所有支持克隆的对象声明了一个通用接口， 该接口让你能够克隆对象， 同时又无需将代码和对象所属类耦合。 通常情况下， 这样的接口中仅包含一个 克隆Clone()方法。  

![avatar](https://refactoringguru.cn/images/patterns/diagrams/prototype/structure-2x.png)

## 单例模式 - Singleton

单例模式是一种创建型设计模式， 让你能够保证一个类只有一个实例， 并提供一个访问该实例的全局节点。  

![avatar](https://refactoringguru.cn/images/patterns/diagrams/singleton/structure-zh-2x.png)  


# 结构型模式

## 适配器模式 - Wrapper Adapter

实现时使用了构成原则： 适配器实现了其中一个对象的接口， 并对另一个对象进行封装。 所有流行的编程语言都可以实现适配器。  

![avatar](https://refactoringguru.cn/images/patterns/diagrams/adapter/structure-object-adapter-2x.png)

## 组合模式 - Object Tree \ Composite

组合模式是一种结构型设计模式， 你可以使用它将对象组合成树状结构， 并且能像使用独立对象一样使用它们。  

![avatar](https://refactoringguru.cn/images/patterns/diagrams/composite/structure-zh-2x.png)  

## 装饰模式 - Wrapper \ Decorator

装饰模式是一种结构型设计模式， 允许你通过将对象放入包含行为的特殊封装对象中来为原对象绑定新的行为。  

 许多编程语言使用 `final`最终关键字来限制对某个类的进一步扩展。 复用最终类已有行为的唯一方法是使用装饰模式： 用封装器对其进行封装。  


## 代理模式 - Proxy

代理模式是一种结构型设计模式， 让你能够提供对象的替代品或其占位符。 代理控制着对于原对象的访问， 并允许在将请求提交给对象前后进行一些处理。  

装饰和代理有着相似的结构， 但是其意图却非常不同。 这两个模式的构建都基于组合原则， 也就是说一个对象应该将部分工作委派给另一个对象。 两者之间的不同之处在于代理通常自行管理其服务对象的生命周期， 而装饰的生成则总是由客户端进行控制。  

# 行为模式

行为模式负责对象间的高效沟通和职责委派。  

## 责任链模式 - Chain of Responsibility

中间件

## 迭代器模式 - Iterator

迭代器模式是一种行为设计模式， 让你能在不暴露集合底层表现形式 （列表、 栈和树等） 的情况下遍历集合中所有的元素。  

## 观察者模式 - Event-Subscriber、 Listener、 Observer

## 状态模式 - State














## Reference

1. [DESIGN PATTERNS in C#](https://refactoring.guru/design-patterns/csharp)

