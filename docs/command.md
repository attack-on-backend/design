# Attack on Design - 命令模式 🪼

## 概述

**关键词 : 命令 , 函数封装对象**

**命令模式 (Command Pattern) :** 将请求 (命令) 封装成一个对象 , 这样可以使用不同的请求参数化其他对象 (将不同请求依赖注入到其他对象) , 并且能够支持请求 (命令) 的排队执行、记录日志、撤销等 (附加控制) 功能

## 实现

```python
# 命令接口
class Command:
    def execute(self):
        pass

# 具体命令
class ConcreteCommand(Command):
    def __init__(self, receiver):
        self.receiver = receiver

    def execute(self):
        self.receiver.action()

# 接收者
class Receiver:
    def action(self):
        print("执行操作")

# 调用者
class Invoker:
    def __init__(self):
        self._commands = []

    def add_command(self, command):
        self._commands.append(command)

    def execute_commands(self):
        for command in self._commands:
            command.execute()

# 客户端代码
if __name__ == "__main__":
    receiver = Receiver()
    command = ConcreteCommand(receiver)
    invoker = Invoker()
    invoker.add_command(command)
    invoker.execute_commands()
```

## 场景

### 命令

订单创建确认取消

```python
class Command:
    def execute(self):
        pass

# receiver.py
class OrderProcessor:
    def create_order(self, order_id, details):
        print(f"订单 {order_id} 已创建，详情: {details}")

    def cancel_order(self, order_id):
        print(f"订单 {order_id} 已取消")

    def confirm_order(self, order_id):
        print(f"订单 {order_id} 已确认")

class CreateOrderCommand(Command):
    def __init__(self, processor, order_id, details):
        self.processor = processor
        self.order_id = order_id
        self.details = details

    def execute(self):
        self.processor.create_order(self.order_id, self.details)

class CancelOrderCommand(Command):
    def __init__(self, processor, order_id):
        self.processor = processor
        self.order_id = order_id

    def execute(self):
        self.processor.cancel_order(self.order_id)

class ConfirmOrderCommand(Command):
    def __init__(self, processor, order_id):
        self.processor = processor
        self.order_id = order_id

    def execute(self):
        self.processor.confirm_order(self.order_id)

class Invoker:
    def __init__(self):
        self._commands = []

    def add_command(self, command):
        self._commands.append(command)

    def execute_commands(self):
        for command in self._commands:
            command.execute()

if __name__ == "__main__":
    processor = OrderProcessor()
    invoker = Invoker()

    # 创建命令
    create_command = CreateOrderCommand(processor, "12345", "笔记本电脑 x2")
    confirm_command = ConfirmOrderCommand(processor, "12345")
    cancel_command = CancelOrderCommand(processor, "12345")

    # 添加命令到调用者
    invoker.add_command(create_command)
    invoker.add_command(confirm_command)
    invoker.add_command(cancel_command)

    # 执行命令
    invoker.execute_commands()
```

