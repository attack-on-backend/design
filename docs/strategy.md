# Design Pattern - 策略模式

## 概述

**关键词 : 算法封装 , 开闭原则**

**策略模式 (Strategy Pattern) : **定义一族算法类 , 将每个算法分别封装起来 , 让它们可以相互替换 , 策略模式可以使算法的变化独立于使用它们的客户端

## 实现

```python
# 定义策略接口
class Strategy:
    def execute(self, data):
        pass

# 定义具体策略类
class ConcreteStrategyA(Strategy):
    def execute(self, data):
        print(f"Executing strategy A with data: {data}")

class ConcreteStrategyB(Strategy):
    def execute(self, data):
        print(f"Executing strategy B with data: {data}")

class ConcreteStrategyC(Strategy):
    def execute(self, data):
        print(f"Executing strategy C with data: {data}")

# 定义上下文类
class Context:
    def __init__(self, strategy: Strategy):
        self._strategy = strategy

    def set_strategy(self, strategy: Strategy):
        self._strategy = strategy

    def execute_strategy(self, data):
        self._strategy.execute(data)

# 使用示例
if __name__ == "__main__":
    # 创建具体策略实例
    strategy_a = ConcreteStrategyA()
    strategy_b = ConcreteStrategyB()
    strategy_c = ConcreteStrategyC()

    # 创建上下文实例并设置策略
    context = Context(strategy_a)
    context.execute_strategy("Data for Strategy A")

    # 更改策略
    context.set_strategy(strategy_b)
    context.execute_strategy("Data for Strategy B")

    # 更改策略
    context.set_strategy(strategy_c)
    context.execute_strategy("Data for Strategy C")
```

## 场景

### 算法封装

排序策略

```python
# 定义策略接口
class SortStrategy:
    def sort(self, data):
        pass

# 定义具体策略类
class QuickSortStrategy(SortStrategy):
    def sort(self, data):
        print("Using Quick Sort")
        return self._quick_sort(data)

    def _quick_sort(self, data):
        if len(data) <= 1:
            return data
        pivot = data[len(data) // 2]
        left = [x for x in data if x < pivot]
        middle = [x for x in data if x == pivot]
        right = [x for x in data if x > pivot]
        return self._quick_sort(left) + middle + self._quick_sort(right)

class MergeSortStrategy(SortStrategy):
    def sort(self, data):
        print("Using Merge Sort")
        return self._merge_sort(data)

    def _merge_sort(self, data):
        if len(data) <= 1:
            return data
        mid = len(data) // 2
        left = self._merge_sort(data[:mid])
        right = self._merge_sort(data[mid:])
        return self._merge(left, right)

    def _merge(self, left, right):
        result = []
        i = j = 0
        while i < len(left) and j < len(right):
            if left[i] < right[j]:
                result.append(left[i])
                i += 1
            else:
                result.append(right[j])
                j += 1
        result.extend(left[i:])
        result.extend(right[j:])
        return result

class BubbleSortStrategy(SortStrategy):
    def sort(self, data):
        print("Using Bubble Sort")
        n = len(data)
        for i in range(n):
            for j in range(0, n-i-1):
                if data[j] > data[j+1]:
                    data[j], data[j+1] = data[j+1], data[j]
        return data

# 定义上下文类
class SortContext:
    def __init__(self, strategy: SortStrategy):
        self._strategy = strategy

    def set_strategy(self, strategy: SortStrategy):
        self._strategy = strategy

    def sort_data(self, data):
        return self._strategy.sort(data)

# 使用示例
if __name__ == "__main__":
    data = [64, 34, 25, 12, 22, 11, 90]

    # 创建具体策略实例
    quick_sort = QuickSortStrategy()
    merge_sort = MergeSortStrategy()
    bubble_sort = BubbleSortStrategy()

    # 创建上下文实例并设置策略
    context = SortContext(quick_sort)
    sorted_data = context.sort_data(data.copy())
    print(f"Sorted data using Quick Sort: {sorted_data}")

    # 更改策略
    context.set_strategy(merge_sort)
    sorted_data = context.sort_data(data.copy())
    print(f"Sorted data using Merge Sort: {sorted_data}")

    # 更改策略
    context.set_strategy(bubble_sort)
    sorted_data = context.sort_data(data.copy())
    print(f"Sorted data using Bubble Sort: {sorted_data}")
```

