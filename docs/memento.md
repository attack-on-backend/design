# Attack on Design - 备忘录模式 🪼

## 概述

**关键词 : 快照 , 状态恢复**

**备忘录模式 (Memento Pattern) :** 在不破坏内部结构的前提下捕获一个对象的内部状态 , 这样便可以在以后将该对象恢复到原先保存的状态

备忘录模式也叫快照模式

## 实现

```python
# 定义备忘录类
class Memento:
    def __init__(self, state):
        self._state = state

    def get_state(self):
        return self._state

# 定义发起人类
class Originator:
    def __init__(self, state):
        self._state = state

    def set_state(self, state):
        print(f"Originator: Setting state to {state}")
        self._state = state

    def get_state(self):
        return self._state

    def save_state_to_memento(self):
        print(f"Originator: Saving to Memento.")
        return Memento(self._state)

    def restore_state_from_memento(self, memento):
        self._state = memento.get_state()
        print(f"Originator: State after restoring from Memento: {self._state}")

# 定义管理者类
class Caretaker:
    def __init__(self):
        self._mementos = []

    def add_memento(self, memento):
        self._mementos.append(memento)

    def get_memento(self, index):
        return self._mementos[index]

# 使用示例
if __name__ == "__main__":
    # 创建发起人对象
    originator = Originator("State1")

    # 创建管理者对象
    caretaker = Caretaker()

    # 保存状态
    caretaker.add_memento(originator.save_state_to_memento())

    # 改变状态
    originator.set_state("State2")
    caretaker.add_memento(originator.save_state_to_memento())

    # 改变状态
    originator.set_state("State3")

    # 恢复状态
    print("\nRestoring to State2:")
    originator.restore_state_from_memento(caretaker.get_memento(1))

    # 恢复状态
    print("\nRestoring to State1:")
    originator.restore_state_from_memento(caretaker.get_memento(0))
```

## 场景

### 状态恢复

文本编辑器 , 支持用户可以撤销到之前保存的状态

```python
# 定义备忘录类
class Memento:
    def __init__(self, state):
        self._state = state

    def get_state(self):
        return self._state

# 定义发起人类
class TextEditor:
    def __init__(self, text=""):
        self._text = text

    def set_text(self, text):
        print(f"TextEditor: Setting text to '{text}'")
        self._text = text

    def get_text(self):
        return self._text

    def save_state_to_memento(self):
        print("TextEditor: Saving to Memento.")
        return Memento(self._text)

    def restore_state_from_memento(self, memento):
        self._text = memento.get_state()
        print(f"TextEditor: State after restoring from Memento: '{self._text}'")

# 定义管理者类
class Caretaker:
    def __init__(self):
        self._mementos = []

    def add_memento(self, memento):
        self._mementos.append(memento)

    def get_memento(self, index):
        return self._mementos[index]

    def get_all_mementos(self):
        return self._mementos

# 使用示例
if __name__ == "__main__":
    # 创建文本编辑器对象
    editor = TextEditor()

    # 创建管理者对象
    caretaker = Caretaker()

    # 初始文本
    editor.set_text("Hello, World!")
    caretaker.add_memento(editor.save_state_to_memento())

    # 修改文本
    editor.set_text("Hello, Python!")
    caretaker.add_memento(editor.save_state_to_memento())

    # 修改文本
    editor.set_text("Hello, Design Patterns!")
    caretaker.add_memento(editor.save_state_to_memento())

    # 显示所有保存的状态
    print("\nSaved states:")
    for i, memento in enumerate(caretaker.get_all_mementos()):
        print(f"State {i}: {memento.get_state()}")

    # 恢复到第二个状态
    print("\nRestoring to State 1:")
    editor.restore_state_from_memento(caretaker.get_memento(1))

    # 恢复到第一个状态
    print("\nRestoring to State 0:")
    editor.restore_state_from_memento(caretaker.get_memento(0))
```

