# Design Pattern - 组合模式

## 概述

**关键词 : 树形结构**

**组合模式 (Composite Pattern) :** 将一组对象组织成树形机构 , 以表示一种"部分-整体"的层次结构

层次关系转换为组合关系

## 实现

```python
# 组件（Component）
class Component:
    def operation(self):
        pass

    def add(self, component):
        raise NotImplementedError("Cannot add to a leaf")

    def remove(self, component):
        raise NotImplementedError("Cannot remove from a leaf")

    def get_child(self, index):
        raise NotImplementedError("Cannot get child from a leaf")

# 叶子节点（Leaf）
class Leaf(Component):
    def __init__(self, name):
        self._name = name

    def operation(self):
        return f"Leaf {self._name} operation"

# 组合节点（Composite）
class Composite(Component):
    def __init__(self, name):
        self._name = name
        self._children = []

    def operation(self):
        results = [f"Composite {self._name} operation"]
        for child in self._children:
            results.append(child.operation())
        return "\n".join(results)

    def add(self, component):
        self._children.append(component)

    def remove(self, component):
        self._children.remove(component)

    def get_child(self, index):
        return self._children[index]

# 客户端代码
if __name__ == "__main__":
    # 创建叶子节点
    leaf1 = Leaf("Leaf 1")
    leaf2 = Leaf("Leaf 2")
    leaf3 = Leaf("Leaf 3")

    # 创建组合节点
    composite1 = Composite("Composite 1")
    composite2 = Composite("Composite 2")

    # 构建组合结构
    composite1.add(leaf1)
    composite1.add(composite2)
    composite2.add(leaf2)
    composite2.add(leaf3)

    # 执行操作
    print(composite1.operation())
```

## 场景

### 文件系统

```python
# 组件（Component）
class FileSystemComponent:
    def __init__(self, name):
        self._name = name

    def get_name(self):
        return self._name

    def add(self, component):
        raise NotImplementedError("Cannot add to a leaf")

    def remove(self, component):
        raise NotImplementedError("Cannot remove from a leaf")

    def display(self, depth=0):
        pass
# 叶子节点（Leaf）
class File(FileSystemComponent):
    def display(self, depth=0):
        print("  " * depth + f"File: {self.get_name()}")
        
# 组合节点（Composite）
class Folder(FileSystemComponent):
    def __init__(self, name):
        super().__init__(name)
        self._children = []

    def add(self, component):
        self._children.append(component)

    def remove(self, component):
        self._children.remove(component)

    def display(self, depth=0):
        print("  " * depth + f"Folder: {self.get_name()}")
        for child in self._children:
            child.display(depth + 1)
            
if __name__ == "__main__":
    # 创建文件和文件夹
    file1 = File("file1.txt")
    file2 = File("file2.txt")
    file3 = File("file3.txt")
    file4 = File("file4.txt")

    folder1 = Folder("Folder 1")
    folder2 = Folder("Folder 2")

    # 构建文件系统结构
    folder1.add(file1)
    folder1.add(folder2)
    folder2.add(file2)
    folder2.add(file3)
    folder1.add(file4)

    # 显示文件系统结构
    folder1.display()
```
