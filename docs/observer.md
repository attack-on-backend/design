# Attack on Design - 观察者模式 🪼

## 概述

**关键词 : 多播通信 , 发布订阅**

**观察者模式 (Observer Pattern) :** 在对象间定义一种一对多的依赖关系 , 当这个对象状态发生改变时 , 所有依赖它的对象都会被通知并自动更新

## 实现

```python
# 定义观察者接口
class Observer:
    def update(self, message):
        pass

# 定义具体观察者类
class ConcreteObserverA(Observer):
    def update(self, message):
        print(f"ConcreteObserverA received message: {message}")

class ConcreteObserverB(Observer):
    def update(self, message):
        print(f"ConcreteObserverB received message: {message}")

# 定义主题接口
class Subject:
    def __init__(self):
        self._observers = []

    def attach(self, observer):
        if observer not in self._observers:
            self._observers.append(observer)

    def detach(self, observer):
        try:
            self._observers.remove(observer)
        except ValueError:
            pass

    def notify(self, message):
        for observer in self._observers:
            observer.update(message)

# 定义具体主题类
class ConcreteSubject(Subject):
    def some_business_logic(self):
        print("ConcreteSubject: I'm doing something important.")
        self.notify("Something important happened!")

# 使用示例
if __name__ == "__main__":
    subject = ConcreteSubject()

    observer_a = ConcreteObserverA()
    observer_b = ConcreteObserverB()

    subject.attach(observer_a)
    subject.attach(observer_b)

    subject.some_business_logic()

    subject.detach(observer_a)

    subject.some_business_logic()
```

## 场景

### 多播通信

登录异常状态通知

```python
import time

class Observer:
    def update(self, observable, object):
        pass

class Observable:

    def __init__(self):
        self._observers = []

    def add_observer(self, observer):
        self._observers.append(observer)

    def remove_observer(self, observer):
        self._observers.remove(observer)

    def notify_observers(self, object):
        for o in self._observers:
            o.update(self, object)


class Account(Observable):

    def __init__(self):
        super().__init__()
        self._latest_ip = {}
        self._latest_region = {}

    def login(self, name, ip, time):
        region = self.get_region(ip)
        if self.is_long_distance(name, region):
            self.notify_observers({
                "name": name,
                "ip": ip,
                "region": region,
                "time": time
            })
        self._latest_region[name] = region
        self._latest_ip[name] = ip

    def get_region(self, ip):
        # 由IP地址获取地区信息, 这里只是模拟
        ip_regions = {
            "101.47.18.9": "浙江省杭州市",
            "67.218.147.69": "美国洛杉矶"
        }
        region = ip_regions.get(ip)
        return "" if region is None else region

    def is_long_distance(self, name, region):
        # 计算本次登录与最近几次登录的地区差异
        latest_region = self._latest_region.get(name)
        return latest_region is not None and latest_region != region
# 观察者
class SmsSender(Observer):
    def update(self, observable, object):
        print("SmsSender: 发送短信通知给用户")
# 观察者
class MailSender(Observer):
    def update(self, observable, object):
        print("MailSender: 发送邮件通知给用户")

if __name__ == '__main__':
    account = Account()
    account.add_observer(SmsSender())
    account.add_observer(MailSender())
    account.login("Tony", "101.47.18.9", time.time())
    account.login("Tony", "67.218.147.69", time.time())
```

