# Design Pattern - 迭代器模式

## 概述

**关键词 : 封装 , 容器 , 遍历**

**迭代器模式 (Iterator Pattern) :** 提供一种方法顺序地访问一组聚合对象 (一个容器) 中的各个元素 , 而又不需要暴露该对象的内部细节

## 实现

```python
# 聚合(容器)对象
class Aggregate:
    def __init__(self):
        self._items = []

    def add_item(self, item):
        self._items.append(item)

    def get_item(self, index):
        return self._items[index]

    def get_count(self):
        return len(self._items)

    def create_iterator(self):
        return Iterator(self)

# 迭代器
class Iterator:
    def __init__(self, aggregate):
        self._aggregate = aggregate
        self._index = 0

    def has_next(self):
        return self._index < self._aggregate.get_count()

    def next(self):
        if self.has_next():
            item = self._aggregate.get_item(self._index)
            self._index += 1
            return item
        else:
            raise StopIteration("No more items in the iterator")

# 使用示例
if __name__ == "__main__":
    # 创建聚合对象
    aggregate = Aggregate()
    aggregate.add_item("Item 1")
    aggregate.add_item("Item 2")
    aggregate.add_item("Item 3")

    # 创建迭代器对象
    iterator = aggregate.create_iterator()

    # 遍历聚合对象
    try:
        while True:
            item = iterator.next()
            print(item)
    except StopIteration as e:
        print(e)
```

## 场景

### 封装

树的多种遍历方式

```python
# 定义树节点类
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None


class Iterator:
    def has_next(self):
        pass

    def next(self):
        pass


# 定义聚合容器
class BinaryTree:
    def __init__(self, root=None):
        self.root = root

    def create_iterator(self, traversal_type):
        if traversal_type == "preorder":
            return PreOrderIterator(self.root)
        elif traversal_type == "inorder":
            return InOrderIterator(self.root)
        elif traversal_type == "postorder":
            return PostOrderIterator(self.root)
        else:
            raise ValueError("Unsupported traversal type")


# 前序遍历迭代器
class PreOrderIterator(Iterator):
    def __init__(self, root):
        self.stack = []
        if root:
            self.stack.append(root)

    def has_next(self):
        return len(self.stack) > 0

    def next(self):
        if not self.has_next():
            raise StopIteration("No more nodes in the tree")
        node = self.stack.pop()
        if node.right:
            self.stack.append(node.right)
        if node.left:
            self.stack.append(node.left)
        return node.value
# 中序遍历迭代器
class InOrderIterator(Iterator):
    def __init__(self, root):
        self.stack = []
        self._leftmost_inorder(root)

    def _leftmost_inorder(self, node):
        while node:
            self.stack.append(node)
            node = node.left

    def has_next(self):
        return len(self.stack) > 0

    def next(self):
        if not self.has_next():
            raise StopIteration("No more nodes in the tree")
        node = self.stack.pop()
        if node.right:
            self._leftmost_inorder(node.right)
        return node.value
# 后续遍历迭代器
class PostOrderIterator(Iterator):
    def __init__(self, root):
        self.stack1 = []
        self.stack2 = []
        if root:
            self.stack1.append(root)

    def has_next(self):
        return len(self.stack2) > 0

    def next(self):
        if not self.has_next():
            raise StopIteration("No more nodes in the tree")
        node = self.stack2.pop()
        return node.value

    def _initialize_stacks(self):
        while self.stack1:
            node = self.stack1.pop()
            self.stack2.append(node)
            if node.left:
                self.stack1.append(node.left)
            if node.right:
                self.stack1.append(node.right)

# 使用示例
if __name__ == "__main__":
    # 创建二叉树
    root = TreeNode(1)
    root.left = TreeNode(2)
    root.right = TreeNode(3)
    root.left.left = TreeNode(4)
    root.left.right = TreeNode(5)
    root.right.left = TreeNode(6)
    root.right.right = TreeNode(7)

    # 创建二叉树对象
    binary_tree = BinaryTree(root)

    # 前序遍历
    print("Pre-order traversal:")
    pre_order_iterator = binary_tree.create_iterator("preorder")
    try:
        while True:
            print(pre_order_iterator.next())
    except StopIteration as e:
        print(e)

    # 中序遍历
    print("\nIn-order traversal:")
    in_order_iterator = binary_tree.create_iterator("inorder")
    try:
        while True:
            print(in_order_iterator.next())
    except StopIteration as e:
        print(e)

    # 后序遍历
    print("\nPost-order traversal:")
    post_order_iterator = binary_tree.create_iterator("postorder")
    post_order_iterator._initialize_stacks()  # 初始化后序遍历的栈
    try:
        while True:
            print(post_order_iterator.next())
    except StopIteration as e:
        print(e)
```

