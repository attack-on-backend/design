# Design Pattern - 中介模式

## 概述

**关键词 : 中间层 , 星型结构**

**中介模式 (Mediator Pattern) :** 用一个中介对象来封装一系列的对象交互 , 中介者使各对象不需要显式地相互引用 , 从而使其耦合松散 , 而且可以独立地改变它们之间的交互

## 实现

```python
# 定义中介接口
class Mediator:
    def send(self, message, colleague):
        pass

# 定义同事类
class Colleague:
    def __init__(self, mediator):
        self.mediator = mediator

    def send(self, message):
        self.mediator.send(message, self)

    def receive(self, message):
        pass

# 定义具体同事类
class ConcreteColleagueA(Colleague):
    def receive(self, message):
        print(f"ConcreteColleagueA received: {message}")

class ConcreteColleagueB(Colleague):
    def receive(self, message):
        print(f"ConcreteColleagueB received: {message}")

# 定义具体中介类
class ConcreteMediator(Mediator):
    def __init__(self):
        self.colleague_a = None
        self.colleague_b = None

    def set_colleague_a(self, colleague_a):
        self.colleague_a = colleague_a

    def set_colleague_b(self, colleague_b):
        self.colleague_b = colleague_b

    def send(self, message, colleague):
        if colleague == self.colleague_a:
            self.colleague_b.receive(message)
        elif colleague == self.colleague_b:
            self.colleague_a.receive(message)

if __name__ == "__main__":
    # 创建中介
    mediator = ConcreteMediator()

    # 创建同事
    colleague_a = ConcreteColleagueA(mediator)
    colleague_b = ConcreteColleagueB(mediator)

    # 设置中介中的同事
    mediator.set_colleague_a(colleague_a)
    mediator.set_colleague_b(colleague_b)

    # 发送消息
    colleague_a.send("Hello from A")
    colleague_b.send("Hello from B")
```

## 场景

### 中间层

聊天室

```python
# 定义中介接口
class Mediator:
    def send(self, message, colleague):
        pass

# 定义同事类
class Colleague:
    def __init__(self, mediator):
        self.mediator = mediator

    def send(self, message):
        pass

    def receive(self, message):
        pass

# 定义具体同事类
class User(Colleague):
    def __init__(self, name, mediator):
        super().__init__(mediator)
        self.name = name

    def send(self, message):
        print(f"{self.name} sends: {message}")
        self.mediator.send(message, self)

    def receive(self, message):
        print(f"{self.name} receives: {message}")

# 定义具体中介类
class ChatRoom(Mediator):
    def __init__(self):
        self.users = {}

    def add_user(self, user):
        self.users[user.name] = user

    def send(self, message, colleague):
        for name, user in self.users.items():
            if user != colleague:
                user.receive(message)

if __name__ == "__main__":
    # 创建聊天室（中介）
    chat_room = ChatRoom()

    # 创建用户（同事）
    user1 = User("Alice", chat_room)
    user2 = User("Bob", chat_room)
    user3 = User("Charlie", chat_room)

    # 将用户添加到聊天室
    chat_room.add_user(user1)
    chat_room.add_user(user2)
    chat_room.add_user(user3)

    # 用户发送消息
    user1.send("Hello, everyone!")
    user2.send("Hi Alice!")
    user3.send("Hey Bob!")
```



