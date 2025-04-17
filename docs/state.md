# Attack on Design - 状态模式 🪼

## 概述

**关键词 : 状态切换 , 上下文管理**

**状态模式 (State Pattern) :** 允许一个对象在其内部状态发生改变时改变其行为 , 使这个对象看上去就像改变了它的类型一样

## 实现

```python
# 定义状态接口
class State:
    def handle(self, context):
        pass

# 定义具体状态类
class ConcreteStateA(State):
    def handle(self, context):
        print("Handling request in state A")
        print("Transitioning to state B")
        context.transition_to(ConcreteStateB())

class ConcreteStateB(State):
    def handle(self, context):
        print("Handling request in state B")
        print("Transitioning to state A")
        context.transition_to(ConcreteStateA())

# 定义上下文类
class Context:
    def __init__(self, state: State):
        self._state = state

    def transition_to(self, state: State):
        print(f"Context: Transitioning to {state.__class__.__name__}")
        self._state = state

    def request(self):
        self._state.handle(self)

# 使用示例
if __name__ == "__main__":
    # 创建初始状态
    initial_state = ConcreteStateA()

    # 创建上下文实例并设置初始状态
    context = Context(initial_state)

    # 处理请求
    context.request()
    context.request()
    context.request()
```

## 场景

### 状态切换

假设我们有一个电梯系统，电梯有三种状态：

- 停止状态：电梯停止时的行为
- 上升状态：电梯上升时的行为
- 下降状态：电梯下降时的行为。

```python
class ElevatorState:
    def handle(self, context):
        pass

# 定义具体状态类
class StoppedState(ElevatorState):
    def handle(self, context):
        print("Elevator is stopped.")
        print("Available commands: start_up, start_down")
        command = input("Enter command: ").strip().lower()
        if command == "start_up":
            context.transition_to(UpState())
        elif command == "start_down":
            context.transition_to(DownState())
        else:
            print("Invalid command. Please try again.")

class UpState(ElevatorState):
    def handle(self, context):
        print("Elevator is moving up.")
        print("Available commands: stop, continue_up")
        command = input("Enter command: ").strip().lower()
        if command == "stop":
            context.transition_to(StoppedState())
        elif command == "continue_up":
            print("Elevator continues moving up.")
        else:
            print("Invalid command. Please try again.")

class DownState(ElevatorState):
    def handle(self, context):
        print("Elevator is moving down.")
        print("Available commands: stop, continue_down")
        command = input("Enter command: ").strip().lower()
        if command == "stop":
            context.transition_to(StoppedState())
        elif command == "continue_down":
            print("Elevator continues moving down.")
        else:
            print("Invalid command. Please try again.")

# 定义上下文类
class ElevatorContext:
    def __init__(self, state: ElevatorState):
        self._state = state

    def transition_to(self, state: ElevatorState):
        print(f"Elevator: Transitioning to {state.__class__.__name__}")
        self._state = state

    def request(self):
        self._state.handle(self)

# 使用示例
if __name__ == "__main__":
    # 创建初始状态
    initial_state = StoppedState()

    # 创建上下文实例并设置初始状态
    elevator = ElevatorContext(initial_state)

    # 处理请求
    while True:
        elevator.request()
```



