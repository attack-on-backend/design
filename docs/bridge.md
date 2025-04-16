# Design Pattern - 桥接模式

## 概述

**关键词 : 抽象类库 , 抽象与实现分离 , 独立组合**

**桥接模式 (Bridge Pattern) :** 将抽象和实现解耦 , 使得它们可以独立地变化

抽象和实现指的不是类的抽象和实现 , 而是一套类库 (一组功能) 的抽象以及实现

## 实现

```python
# 实现部分（Implementor）
class Implementor:
    def operation_implementation(self):
        pass

# 具体实现部分（Concrete Implementor）
class ConcreteImplementorA(Implementor):
    def operation_implementation(self):
        return "ConcreteImplementorA Operation"

class ConcreteImplementorB(Implementor):
    def operation_implementation(self):
        return "ConcreteImplementorB Operation"

# 抽象部分（Abstraction）
class Abstraction:
    def __init__(self, implementor: Implementor):
        self._implementor = implementor

    def operation(self):
        return f"Abstraction: {self._implementor.operation_implementation()}"

# 扩展抽象部分（Refined Abstraction）
class RefinedAbstraction(Abstraction):
    def operation(self):
        return f"RefinedAbstraction: {self._implementor.operation_implementation()}"

# 客户端代码
if __name__ == "__main__":
    # 创建具体实现部分
    implementor_a = ConcreteImplementorA()
    implementor_b = ConcreteImplementorB()

    # 创建抽象部分并关联具体实现部分
    abstraction_a = Abstraction(implementor_a)
    abstraction_b = Abstraction(implementor_b)

    # 创建扩展抽象部分并关联具体实现部分
    refined_abstraction_a = RefinedAbstraction(implementor_a)
    refined_abstraction_b = RefinedAbstraction(implementor_b)

    # 执行操作
    print(abstraction_a.operation())  # 输出: Abstraction: ConcreteImplementorA Operation
    print(abstraction_b.operation())  # 输出: Abstraction: ConcreteImplementorB Operation
    print(refined_abstraction_a.operation())  # 输出: RefinedAbstraction: ConcreteImplementorA Operation
    print(refined_abstraction_b.operation())  # 输出: RefinedAbstraction: ConcreteImplementorB Operation

```

## 场景

### 抽象类库

以Python的`SQLAlchemy`模块为例

`SQLAlchemy`定义了一套类库 , 其中组件包括 : 

- 引擎 (Engine) : 负责与数据库进行连接和通信
- 方言 (Dialect) : 实现与特定数据库的通信细节
- 连接池 (Connection Pool) : 管理数据库连接的生命周期
- 会话 (Session) : 管理ORM对象的生命周期和持久化操作
- 映射器 (Mapper) : 将Python类与数据库表进行映射

实现就是对各个数据库的支持 , 如`MySQL` , `Sqlite` ....

### 独立组合

以Python的`logging`模块为例 , 记录器 , 处理器 , 格式化器之间独立组合

```python
import logging

# 创建一个日志记录器
logger = logging.getLogger('example_logger')
logger.setLevel(logging.DEBUG)  # 设置日志级别

# 创建一个处理器，将日志消息输出到控制台
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.DEBUG)

# 创建一个处理器，将日志消息写入文件
file_handler = logging.FileHandler('example.log')
file_handler.setLevel(logging.ERROR)

# 创建一个格式化器
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')

# 将格式化器设置到处理器中
console_handler.setFormatter(formatter)
file_handler.setFormatter(formatter)

# 将处理器添加到日志记录器中
logger.addHandler(console_handler)
logger.addHandler(file_handler)

# 记录日志消息
logger.debug('This is a debug message')
logger.info('This is an info message')
logger.warning('This is a warning message')
logger.error('This is an error message')
logger.critical('This is a critical message')
```



