# Design Pattern - 原型模式

## 概念

**关键词 : 重复创建 , 性能优化**

**原型模式 (Prototype Pattern) :** 用原型实例指定要创建对象的种类 , 并通过拷贝这些原型的属性来创建新的对象

原型模式可以用来提升创建对象的性能 , 也可以简化相同对象的创建

## 实现

```python
# 注意深浅拷贝
from copy import copy, deepcopy
# 可克隆对象
class Cloneable:
    def clone(self):
        pass
      
class A(Cloneable):
    def clone(self):
        return copy(self) # deepcopy(self)
```

## 场景

### 重复创建

在Python 3.7中引入了`dataclasses` 库 , 可以通过`replace` 来创建修改后的副本

```python
from dataclasses import dataclass, replace

@dataclass
class Person:
    name: str
    age: int

original = Person("Alice", 30)
cloned = replace(original, age=31)

print(cloned.name == original.name)  # True
print(cloned.age == original.age) # False
```
