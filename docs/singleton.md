# Design Pattern - 单例模式

## 概述

**关键词 : 资源竞争 , 全局唯一**

**单例模式 (Singleton Pattern) :** 一个类值允许创建一个对象 (或者实例) , 那这个类就是单例类 , 这种设计模式就叫单例模式

## 实现

以下几种方式主要针对Python语言特性的实现 , 在不同的语言中实现会存在差异

### Module

Python 中的 module 天生就是单例 , 至于为什么 , 见官方文档 [compiled-python-file](https://docs.python.org/3.6/tutorial/modules.html#compiled-python-files)

**singleton.py**

```python
class Singleton(object):
    pass

singleton = Singleton()
```

使用

```python
from singleton import singleton
```

### \_\_new__

Python 中的对象将有 `__new__` 来开辟空间创建实例 , 这是比较经典的一种做法

```python
import threading

class Singleton(object):
    _threading_lock = threading.RLock()
    

    def __new__(cls, *args, **kwargs):
        if not hasattr(cls, "_instance"):
            with cls._threading_lock:
                if not hasattr(cls, "_instance"):
                    cls._instance = object.__new__(cls)
        return cls._instance  
```

Python 是支持多线程的 , 所以为了线程安全 , 加上锁

### Metaclass

使用元类来实现单例模式 , 实际上就是控制 `class()` 的行为 , 也就是 `__call__` 魔术方法

如果对于 `metaclass` 不懂 , 你可以看我的另一篇博客 [《Python之路 - 元类》](<https://lyonyang.github.io/blogs/01-Python/09-In-Depth/09-Python%E4%B9%8B%E8%B7%AF%20-%20%E5%85%83%E7%B1%BB.html>)

```python
class SingletonMeta(type):
    
    _instances = {}

    def __call__(self, *args, **kwargs):
        if self not in self._instances:
            self._instances[self] = super(SingletonMeta, self).__call__(*args, **kwargs)
        return self._instances[self]

class Singleton(metaclass=SingletonMeta):
    pass

singleton = Singleton()
```

元类创建类本身就是线程安全的 , 所以你并不需要担心抢占资源的问题

### 类装饰器

```python
def singleton(cls):
    _instance = {}

    def _singleton(*args, **kargs):
        if cls not in _instance:
            _instance[cls] = cls(*args, **kargs)
        return _instance[cls]

    return _singleton

@singleton
class Singleton:
    pass
```

使用类装饰器 , 实际上就是把类转换成一个函数对象 , 因此跟实例的创建关系不大 , 因为从始至终也就实例化了一次 , 而且它是线程安全的

### 类方法

通过我们自定义的方法来获取对象 , 而不通过实例化的途径

```python
class Singleton(object):

    @classmethod
    def instance(cls, *args, **kwargs):
        if not hasattr(cls, "_instance"):
            cls._instance = cls(*args, **kwargs)
        return cls._instance

singleton = Singleton.instance()
```

虽然这种方式也能实现单例模式 , 但是它不是线程安全的 , 就算在方法里加了锁 , 也不是线程安全的 , 这里可能跟 Python 的类的加载机制有关 , 不深究了

**注意 : 在测试过程中 , 千万要把 Python 的垃圾回收这一问题隔离 , 也就是说实例不要一实例化之后就丢弃 , 否则可能会出现无效的结果**

## 场景

###  资源竞争

以日志打印为例 , 我们会构建一个Logger和多个Controller

```python
class Logger:
    def __init__(self):
        self.writer = open("log.txt", 'a')

    def log(self, message):
        with self.writer as f:
            f.write(message)
            
class UserController:
    def __init__(self):
        self.logger = Logger()

    def login(self, username, password):
        # ...省略业务逻辑...
        self.logger.log(username + " logined!")


class OrderController:
    def __init__(self):
        self.logger = Logger()

    def create(self, order):
        # ...省略业务逻辑...
        self.logger.log("Created an order: " + str(order))
```

这个例子中 , 多个`Controller`每次都会实例化一个`Logger` , 并获取`log.txt`的文件句柄进行操作

然而在多线程环境下 , 同时分别执行`create()`和`order()` , 同时往`log.txt`中写日志就会出现日志信息相互覆盖的情况

不同的Logger实例不共享`fp`对象 , 为了解决这个问题我们会考虑在`log()`方法进行加锁 , 且是对象级别的锁无效 , 因为有多个Logger对象同时写入 , 此时应该使用类级别的锁或者使用到单例模式

Logger设计成一个单例类 , 程序中只会出现一个Logger实例

```python
class Logger(object):
    _threading_lock = threading.RLock()

    def __new__(cls, *args, **kwargs):
        if not hasattr(cls, "_instance"):
            with cls._threading_lock:
                if not hasattr(cls, "_instance"):
                  	ins = object.__new__(cls)
                    ins.writer = open("log.txt", 'a')
                    cls._instance = ins
        return cls._instance
      
    def log(self, message):
        self.writer.write(message + '\n')
```

### 全局唯一

在业务上 , 全局唯一的场景有非常多 ; 比如项目配置文件的配置类 , Web的路由等等

这里我们以ID生成器为例 , 在业务中有时候数据库的主键ID分配往往都是在数据插入时生成 , 为了避免提高插入前数据的使用性我们通常会先主动获取ID出来

```python
class IdGenerator:
    def __new__(cls, *args, **kwargs):
        if not hasattr(cls, "_instance"):
            with cls._threading_lock:
                if not hasattr(cls, "_instance"):
                    ins = object.__new__(cls)
                    ins.current_id = 1  # ...省略从数据库获取ID...
                    cls._instance = ins
        return cls._instance

    def get_id(self):
        self.current_id += 1
        return self.current_id
```

ID生成器只能存在一个 , 否则就存在生成重复ID的情况

关于ID生成器其实还有非常多的优化方案 , 比如使用Redis来存储ID , 这样可以在分布式系统中使用 (其中包括可以使用Redis实现分布式锁)













