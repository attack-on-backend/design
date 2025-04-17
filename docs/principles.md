# Attack on Design - 设计原则 🪼

设计原则是实现的思想方针 , 设计模式是实现的工具手段

## SOLID

### SRP单一职责原则

单一职责原则 (Single Responsibility Principle, SRP)

**原则 :** 一个类或者模块只负责完成一个职责 (A class or module should have a single reponsibility)

### OCP开闭原则

开放封闭原则 (Open Closed Principle, OCP)

**原则 :** 软件实体 (模块 , 类 , 方法等) 应该 "对扩展开放、对修改关闭" (Software entities (modules, classes, functions, etc.) should be open for extension, but closed for modification)

### LSP里氏替换原则

里氏替换原则 (Liskov Substitution Principle, LSP)

**原则 :** 子类对象能够替换程序中父类对象出现的任何地方 , 并且保证原来程序的逻辑行为不变及正确性不被破坏 (If S is a subtype of T, then objects of type T may be replaced with objects of type S, without breaking the program)

### ISP接口隔离原则

接口隔离原则 (Interface Segregation Principle, ISP)

**原则 :** 客户端 (接口调用者或者使用者) 不应该强迫依赖它不需要的接口 (Clients should not be forced to depend 
upon interfaces that they do not use)

### DIP依赖倒置原则

依赖倒置原则 (Dependency Inversion Principle, DIP)

**原则 :** 高层模块不要依赖低层模块 , 高层模块和低层模块应该通过抽象来互相依赖 , 除此之外 , 抽象不要依赖具体实现细节, 具体实现细节依赖抽象 (High-level modules shouldn’t depend on low-level modules. Both modules should depend on abstractions. In addition, abstractions shouldn’t depend on details. Details depend on abstractions)

## DRY原则

Don't Repeat Yourself, DRY

**原则 :** 不要写重复的代码

**逻辑重复、语义重复、执行重复**

## KISS原则

Keep It Simple and Stupid, KISS

**原则 :** 尽量保持简单

## YAGNI原则

You Ain't Gonna Need It, YAGNI

**原则 :** 不要过渡设计 (不要去设计当前用不到的功能)

**不要过渡设计并不是指不要预留扩展点 , 仅仅是表达不要提前编写当前用不到的代码**

## LOD法则

迪米特法则 (Law of Demeter, LOD)

**原则 :** 每个模块只应该了解那些与它关系密切的模块的有限知识 , 或者说 , 每个模块只和自己 
的朋友“说话” , 不和陌生人“说话” (Each unit should have only limited knowledge about other units: only units “closely” related to the current unit. Or: Each unit should only talk to its friends; Don’t talk to strangers)

**迪米特法则的目的是让我们实现代码的 "高内聚、低耦合"**

[六大原则细致版](/docs/six-principles.md)