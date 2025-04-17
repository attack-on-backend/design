# Attack on Design - 装饰器模式 🪼

## 概述

**关键词 : 功能增强 , 动态添加**

**装饰器模式 (Decorator Pattern) :** 动态地给一个对象增加一些额外的职责 , 就扩展对象功能来说 , 装饰器模式比生成子类的方式更灵活

## 实现

装饰器模式是功能增强 , 所以通常装饰器和组件都继承相同接口或抽象

```python
# 抽象组件（Component）
class Component:
    def operation(self):
        pass

# 具体组件（Concrete Component）
class ConcreteComponent(Component):
    def operation(self):
        return "ConcreteComponent Operation"

# 装饰器（Decorator）
class Decorator(Component):
    def __init__(self, component: Component):
        self._component = component

    @property
    def component(self):
        return self._component

    def operation(self):
        return self._component.operation()

# 具体装饰器（Concrete Decorator）
class ConcreteDecoratorA(Decorator):
    def operation(self):
        return f"ConcreteDecoratorA({self.component.operation()})"

class ConcreteDecoratorB(Decorator):
    def operation(self):
        return f"ConcreteDecoratorB({self.component.operation()})"

# 客户端代码
if __name__ == "__main__":
    # 创建具体组件
    concrete_component = ConcreteComponent()
    print(concrete_component.operation())  # 输出: ConcreteComponent Operation

    # 使用具体装饰器A装饰具体组件
    decorator_a = ConcreteDecoratorA(concrete_component)
    print(decorator_a.operation())  # 输出: ConcreteDecoratorA(ConcreteComponent Operation)

    # 使用具体装饰器B装饰具体组件
    decorator_b = ConcreteDecoratorB(concrete_component)
    print(decorator_b.operation())  # 输出: ConcreteDecoratorB(ConcreteComponent Operation)

    # 使用具体装饰器A和具体装饰器B装饰具体组件
    decorator_a_b = ConcreteDecoratorA(ConcreteDecoratorB(concrete_component))
    print(decorator_a_b.operation())  # 输出: ConcreteDecoratorA(ConcreteDecoratorB(ConcreteComponent Operation))
```

### Python装饰器与装饰器模式的区别

| 区别点     | Python装饰器                                                 | 装饰器模式                 |
| ---------- | ------------------------------------------------------------ | -------------------------- |
| 设计思想   | 函数式编程思想                                               | 面向对象思想               |
| 修饰的对象 | 可以修饰一个函数, 也可以修饰一个类                           | 修饰的是某个类中的指定方法 |
| 影响的范围 | 修饰一个函数时, 对这个函数的所有调用都起效; 修饰一个类时, 对这个类的所有实例都起效 | 只对修饰的这一个对象起效   |

在Python中装饰器使用比装饰器模式要更广泛 , 因为官方的语法糖支持比较好 

Python装饰器是利用闭包来实现的

## 场景

### 功能增强

文件读取增强

```python
import logging
# 配置日志记录
logging.basicConfig(level=logging.INFO)

class FileReader:
    def __init__(self, filename):
        self.filename = filename

    def read(self):
        with open(self.filename, 'r') as file:
            return file.read()
          
class FileDecorator(FileReader):
    def __init__(self, file_reader: FileReader):
        self._file_reader = file_reader

    def read(self):
        return self._file_reader.read()

class LoggingDecorator(FileDecorator):
    def read(self):
        logging.info(f"Reading from {self._file_reader.filename}")
        content = self._file_reader.read()
        logging.info(f"Read {len(content)} characters")
        return content

class BufferingDecorator(FileDecorator):
    def __init__(self, file_reader: FileReader, buffer_size=1024):
        super().__init__(file_reader)
        self.buffer_size = buffer_size
        self.buffer = ""

    def read(self):
        if not self.buffer:
            self.buffer = self._file_reader.read(self.buffer_size)
        content = self.buffer
        self.buffer = ""
        return content
      
if __name__ == "__main__":
    # 创建具体组件
    file_reader = FileReader('example.txt')
    print(file_reader.read())

    # 使用日志装饰器
    logging_decorator = LoggingDecorator(file_reader)
    print(logging_decorator.read())

    # 使用缓冲装饰器
    buffering_decorator = BufferingDecorator(file_reader)
    print(buffering_decorator.read())

    # 使用日志装饰器和缓冲装饰器
    logging_buffering_decorator = LoggingDecorator(BufferingDecorator(file_reader))
    print(logging_buffering_decorator.read())
```



