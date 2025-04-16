# Design Pattern - 建造者模式

## 概念

**关键词 : 复杂对象构建 , 分步构建 , 有效状态**

**建造者模式 (Builder Pattern) :** 将一复杂对象的构建过程和它的表现分离 , 使得同样的构建过程可以获取 (创建) 不同的表现

## 实现

```python
class Builder:
    def build_part_a(self):
        pass

    def build_part_b(self):
        pass
		# 可以抽出来构建一个Director类, 用来指挥建造过程
    def build(self):
        a = self.build_part_a()
        b = self.build_part_b()
        return Object(a, b)
```

## 场景

###  分步构建

Python标准库中命令行工具的构建方式就是使用的建造者模式

```python
import argparse

parser = argparse.ArgumentParser(description="Process some integers.")  # 创建解析器
parser.add_argument("integers", metavar="N", type=int, nargs="+", help="an integer for the accumulator")  # 添加参数
parser.add_argument("--sum", dest="accumulate", action="store_const", const=sum, default=max, help="sum the integers (default: find the max)")

args = parser.parse_args()  # 构建完成并解析命令行参数
print(args.accumulate(args.integers))
```

### 有效状态

在业务上很多时候对象构建需要保证其有效性 , 比如多个属性缺一会导致无效 , 此时就可以用建造者模式来一步到位

```python
# 长方体
cuboid = Cuboid()
cuboid.length = 10 # 无效
cuboid.width = 10 # 无效
cuboid.height = 10 # 有效
```

为了避免这种无效状态 , 我们需要使用构造函数一次性初始化好所有的成员变量

如果构造函数参数过多 , 我们就需要考虑使用建造者模式 , 先设置建造者的变量 , 然后再一次性地创建对象 , 让对象一直处于有效状态
