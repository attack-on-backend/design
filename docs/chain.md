# Design Pattern - 职责链模式

## 概述

**关键词 : 化繁为简 , 链式调用 , 开闭原则**

**职责链模式 (Chain Of Responsibility Pattern) :** 为避免请求发送者与接收者耦合在一起 , 让多个对象都有可能接收请求 , 将这些接收对象连接成一条链 , 并且沿着这条链传递请求 , 直到有对象处理它为止

## 实现

```python
# 这是一种链式实现, 还可以通过数组来实现
# 定义处理者接口
class Handler:
    def __init__(self):
        self._next_handler = None

    def set_next(self, handler):
        self._next_handler = handler
        return handler

    def handle(self, request):
        if self._next_handler:
            return self._next_handler.handle(request)
        return None

# 定义具体处理者类
class ConcreteHandlerA(Handler):
    def handle(self, request):
        if request == "A":
            return f"ConcreteHandlerA handled the request: {request}"
        else:
            return super().handle(request)

class ConcreteHandlerB(Handler):
    def handle(self, request):
        if request == "B":
            return f"ConcreteHandlerB handled the request: {request}"
        else:
            return super().handle(request)

class ConcreteHandlerC(Handler):
    def handle(self, request):
        if request == "C":
            return f"ConcreteHandlerC handled the request: {request}"
        else:
            return super().handle(request)

# 使用示例
if __name__ == "__main__":
    # 创建处理者实例
    handler_a = ConcreteHandlerA()
    handler_b = ConcreteHandlerB()
    handler_c = ConcreteHandlerC()

    # 设置处理者链
    handler_a.set_next(handler_b).set_next(handler_c)

    # 处理请求
    requests = ["A", "B", "C", "D"]

    for request in requests:
        result = handler_a.handle(request)
        if result:
            print(result)
        else:
            print(f"No handler found for request: {request}")
```

## 场景

###  化繁为简

敏感词过滤 , 每一种过滤器都需要调用 , 责任链模式将每个过滤算法独立

```python
# 定义处理者接口
class Filter:
    def __init__(self):
        self._next_filter = None

    def set_next(self, filter_):
        self._next_filter = filter_
        return filter_

    def filter(self, text):
        if self._next_filter:
            return self._next_filter.filter(text)
        return text

# 定义具体处理者类
class PoliticalFilter(Filter):
    def filter(self, text):
        sensitive_words = ["politics", "government", "election"]
        for word in sensitive_words:
            text = text.replace(word, "[REDACTED]")
        return super().filter(text)

class PornographyFilter(Filter):
    def filter(self, text):
        sensitive_words = ["sex", "porn", "adult"]
        for word in sensitive_words:
            text = text.replace(word, "[REDACTED]")
        return super().filter(text)

class AdvertisingFilter(Filter):
    def filter(self, text):
        sensitive_words = ["buy", "sale", "discount"]
        for word in sensitive_words:
            text = text.replace(word, "[REDACTED]")
        return super().filter(text)

# 使用示例
if __name__ == "__main__":
    # 创建过滤器实例
    political_filter = PoliticalFilter()
    pornography_filter = PornographyFilter()
    advertising_filter = AdvertisingFilter()

    # 设置过滤器链
    political_filter.set_next(pornography_filter).set_next(advertising_filter)

    # 创建文本请求
    texts = [
        "This is a political discussion about government and election.",
        "This is a pornographic video with sex and adult content.",
        "This is a great buy with a huge discount.",
        "This is a normal text with no sensitive words."
    ]

    # 处理文本请求
    for text in texts:
        filtered_text = political_filter.filter(text)
        print(f"Original: {text}")
        print(f"Filtered: {filtered_text}\n")
```

