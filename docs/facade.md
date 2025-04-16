# Design Pattern - 门面模式

## 概述

**关键词 : 子系统 , 易用性**

**门面模式 (Facade Pattern) :** 为子系统提供一组统一的接口 , 定义一组高层接口让子系统更易用

## 实现

```python
# 子系统类（Subsystem Classes）
class SubsystemA:
    def operation_a(self):
        return "SubsystemA: Operation A"

class SubsystemB:
    def operation_b(self):
        return "SubsystemB: Operation B"

class SubsystemC:
    def operation_c(self):
        return "SubsystemC: Operation C"

# 外观类（Facade）
class Facade:
    def __init__(self):
        self._subsystem_a = SubsystemA()
        self._subsystem_b = SubsystemB()
        self._subsystem_c = SubsystemC()

    def operation(self):
        results = []
        results.append(self._subsystem_a.operation_a())
        results.append(self._subsystem_b.operation_b())
        results.append(self._subsystem_c.operation_c())
        return "\n".join(results)

# 客户端代码
if __name__ == "__main__":
    facade = Facade()
    result = facade.operation()
    print(result)
```

## 场景

### 子系统

家电一键开关

```python
# 子系统类（Subsystem Classes）
class Lights:
    def turn_on(self):
        return "Lights: Turned on"

    def turn_off(self):
        return "Lights: Turned off"

class TV:
    def turn_on(self):
        return "TV: Turned on"

    def turn_off(self):
        return "TV: Turned off"

class AirConditioner:
    def turn_on(self):
        return "AirConditioner: Turned on"

    def turn_off(self):
        return "AirConditioner: Turned off"

# 外观类（Facade）
class HomeAutomationFacade:
    def __init__(self):
        self.lights = Lights()
        self.tv = TV()
        self.air_conditioner = AirConditioner()

    def turn_on_all(self):
        results = []
        results.append(self.lights.turn_on())
        results.append(self.tv.turn_on())
        results.append(self.air_conditioner.turn_on())
        return "\n".join(results)

    def turn_off_all(self):
        results = []
        results.append(self.lights.turn_off())
        results.append(self.tv.turn_off())
        results.append(self.air_conditioner.turn_off())
        return "\n".join(results)

if __name__ == "__main__":
    facade = HomeAutomationFacade()
    print("Turning on all devices:")
    print(facade.turn_on_all())
    print("\nTurning off all devices:")
    print(facade.turn_off_all())
```
