# Design Pattern - 享元模式

## 概述

**关键词 : 共享单元 , 不可变对象**

**享元模式 (Flyweight Pattern) :** 运用共享技术有效地支持大量细粒度对象的复用

## 实现

```python
# 享元（Flyweight）
class Flyweight:
    def __init__(self, intrinsic_state):
        self._intrinsic_state = intrinsic_state

    def operation(self, extrinsic_state):
        print(f"Flyweight: Intrinsic State: {self._intrinsic_state}, Extrinsic State: {extrinsic_state}")

# 享元工厂（Flyweight Factory）
class FlyweightFactory:
    def __init__(self):
        self._flyweights = {}

    def get_flyweight(self, intrinsic_state):
        if intrinsic_state not in self._flyweights:
            print(f"Creating Flyweight for {intrinsic_state}")
            self._flyweights[intrinsic_state] = Flyweight(intrinsic_state)
        else:
            print(f"Reusing Flyweight for {intrinsic_state}")
        return self._flyweights[intrinsic_state]

# 客户端代码
if __name__ == "__main__":
    factory = FlyweightFactory()

    # 创建和使用享元对象
    flyweight1 = factory.get_flyweight("state1")
    flyweight1.operation("extrinsic_state1")

    flyweight2 = factory.get_flyweight("state2")
    flyweight2.operation("extrinsic_state2")

    flyweight3 = factory.get_flyweight("state1")
    flyweight3.operation("extrinsic_state3")
```

## 场景

### 共享单元

Python中的[字符缓冲池](https://attack-on-backend.github.io/python/#/memory/str?id=%e5%ad%97%e7%ac%a6%e7%bc%93%e5%86%b2%e6%b1%a0)

```python
import sys

# 手动驻留一些字符串
sys.intern("hello")

a = "hello"
b = "hello"
c = "hello"

# a, b, c地址相同
print(id(a), id(b), id(c))
```

