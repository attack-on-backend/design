# Design Pattern - 模板模式

## 概述

**关键词 : 复用 , 扩展**

**模板模式 (Template Pattern) :** 在一个方法中定义一个算法骨架 , 并将某些步骤推迟到子类中实现 , 模板模式可以让子类在不改变算法整体结构的情况下 , 重新定义算法中的某些步骤

## 实现

```python
# 定义抽象类，包含模板方法
class Template:
    def template_method(self):
        self.primitive_operation1()
        self.primitive_operation2()
        self.primitive_operation3()

    def primitive_operation1(self):
        pass

    def primitive_operation2(self):
        pass

    def primitive_operation3(self):
        pass

# 定义具体类，实现抽象方法
class ConcreteClassA(Template):
    def primitive_operation1(self):
        print("ConcreteClassA: Implementing primitive_operation1")

    def primitive_operation2(self):
        print("ConcreteClassA: Implementing primitive_operation2")

    def primitive_operation3(self):
        print("ConcreteClassA: Implementing primitive_operation3")

class ConcreteClassB(Template):
    def primitive_operation1(self):
        print("ConcreteClassB: Implementing primitive_operation1")

    def primitive_operation2(self):
        print("ConcreteClassB: Implementing primitive_operation2")

    def primitive_operation3(self):
        print("ConcreteClassB: Implementing primitive_operation3")

# 使用示例
if __name__ == "__main__":
    concrete_a = ConcreteClassA()
    concrete_a.template_method()

    print("\n")

    concrete_b = ConcreteClassB()
    concrete_b.template_method()
```

## 场景

### 复用

`unittest`模块中的`TestCase`类使用了模板模式 , `TestCase`类定义了一个测试方法的骨架 , 具体测试逻辑由子类实现

```python
import unittest

class MyTestCase(unittest.TestCase):
    def setUp(self):
        # 在每个测试方法运行前执行
        print("Setting up the test environment")

    def test_example(self):
        # 具体的测试逻辑
        self.assertEqual(1 + 1, 2)

    def tearDown(self):
        # 在每个测试方法运行后执行
        print("Tearing down the test environment")

if __name__ == '__main__':
    unittest.main()
```
