# Attack on Design - 工厂模式 🪼

## 概念

**关键词 :  动态创建 , 复杂转移**

工厂模式分为三种 : **简单工厂 , 工厂方法 , 抽象工厂**

**简单工厂 (Simple Factory) :** 定义一个创建对象 (实例化对象) 的接口 , 通过参数来决定创建哪个类的实例 , 被创建的实例通常具有共同的父类

**工厂方法 (Factory Method) :** 定义一个创建对象 (实例化对象) 的接口 , 让子类来决定创建哪个类的实例 , 工厂方法使一个类的实例化延迟到其子类

**抽象工厂 (Abstract Factory) :** 提供一个创建一系列相关或互相依赖的对象的接口 , 而无须指定它们的具体类

## 实现

### 简单工厂

```python
class SimpleFactory:
    # 不符合开闭原则
    def create_object(self, name):
        if name == "A":
            return A()
        elif name == "B":
            return B()
        elif name == "C":
            return C()
```

### 工厂方法

```python
class Factory:
    # 将创建的实现延迟到子类
    def create(self):
        pass

class AFactory(Factory):
    def create(self):
        return A()

class BFactory(Factory):
    def create(self):
        return B()
      
# 使用, 可以定义简单工厂来使用
class SimpleFactory:
    FACTORY_MAP = {
        'A': AFactory(),
        'B': BFactory(),
        'C': CFactory(),
    }

    def create_factory(self, name):
        return self.FACTORY_MAP.get(name)
```

### 抽象工厂

```python
# 抽象工厂, 适用多级分类
class AbstractFactory:
    def create_a(self, name):
        pass
    def create_b(self, name):
        pass

class AFactory(AbstractFactory):
    def create_a(self, name):
        return AA()
    def create_b(self, name):
        return AB()

class BFactory(AbstractFactory):
    def create_a(self, name):
        return BA()
    def create_b(self, name):
        return BB()
```

## 场景

### 动态创建

在业务上通常要支持多种配置文件格式的解析 , 比如`json` , `xml` , `yaml` 等等 , 且以后可能还要进行扩展

如果通过简单的`if...else` 来处理 , 创建的复杂度会越来越高 , 且不易扩展

```python
# 解析器
class Parser:
    def parse(self):
        pass

class JSONPaser(Parser):
    def parse(self):
        # ...省略解析逻辑...

class XMLParser(Parser):
    def parse(self):
        # ...省略解析逻辑...

class YAMLParser(Parser):
    def parse(self):
        # ...省略解析逻辑...

# 配置对象
class Config:
    def __init__(self, options):
        for attr, option in options.items():
            setattr(self, attr, option)

class ConfigFactory:
    PARSER_MAP = {
        "json": JSONPaser(),
        "xml": XMLParser(),
        "yaml": YAMLParser()
    }

    def create(self, config_type):
        parser = self.PARSER_MAP.get(config_type)
        options = parser.parse()
        return Config(options)
```
