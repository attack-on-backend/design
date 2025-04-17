# Attack on Design - 适配器模式 🪼

## 概述

**关键词 : 接口适配 , 缺陷补偿 , 依赖替换**

**适配器模式 (Adapter Pattern) :** 将一个类的接口变成客户端所期望的另一种接口 , 从而使原本因接口不匹配而无法一起工作的两个类能够在一起工作

## 实现

```python
# 目标接口（Target）
class Target:
    def request(self):
        pass

# 适配者（Adaptee）
class Adaptee:
    def specific_request(self):
        return "Adaptee's specific request"

# 适配器（Adapter）
class Adapter(Target):
    def __init__(self, adaptee: Adaptee):
        self._adaptee = adaptee

    def request(self):
        # 将目标接口的请求转换为适配者的请求
        return f"Adapter: (TRANSLATED) {self._adaptee.specific_request()}"

# 客户端代码
if __name__ == "__main__":
    # 创建适配者对象
    adaptee = Adaptee()
    print("Adaptee interface is incompatible with the client")
    print(f"Adaptee: {adaptee.specific_request()}")

    # 创建适配器对象
    adapter = Adapter(adaptee)
    print("Client can work with the Adapter:")
    print(f"Adapter: {adapter.request()}")
```

## 场景

### 接口适配

`pyodbc`和`psycopg2`两个库进行适配

```python
# 适配者（Adaptee）: pyodbc
import pyodbc

class PyODBCAdapter:
    def __init__(self, connection_string):
        self.connection = pyodbc.connect(connection_string)
        self.cursor = self.connection.cursor()

    def execute(self, query):
        self.cursor.execute(query)
        return self.cursor.fetchall()

    def close(self):
        self.cursor.close()
        self.connection.close()

# 适配者（Adaptee）: psycopg2
import psycopg2

class Psycopg2Adapter:
    def __init__(self, connection_string):
        self.connection = psycopg2.connect(connection_string)
        self.cursor = self.connection.cursor()

    def execute(self, query):
        self.cursor.execute(query)
        return self.cursor.fetchall()

    def close(self):
        self.cursor.close()
        self.connection.close()

# 目标接口（Target）
class DatabaseAdapter:
    def execute(self, query):
        pass

    def close(self):
        pass

# 适配器（Adapter）: PyODBCAdapter 适配器
class PyODBCAdapterAdapter(DatabaseAdapter):
    def __init__(self, connection_string):
        self.adaptee = PyODBCAdapter(connection_string)

    def execute(self, query):
        return self.adaptee.execute(query)

    def close(self):
        self.adaptee.close()

# 适配器（Adapter）: Psycopg2Adapter 适配器
class Psycopg2AdapterAdapter(DatabaseAdapter):
    def __init__(self, connection_string):
        self.adaptee = Psycopg2Adapter(connection_string)

    def execute(self, query):
        return self.adaptee.execute(query)

    def close(self):
        self.adaptee.close()

# 客户端代码
if __name__ == "__main__":
    # 使用 PyODBCAdapterAdapter
    pyodbc_adapter = PyODBCAdapterAdapter('DRIVER={ODBC Driver 17 for SQL Server};SERVER=localhost;DATABASE=testdb;UID=user;PWD=password')
    result = pyodbc_adapter.execute('SELECT * FROM users')
    print(result)
    pyodbc_adapter.close()

    # 使用 Psycopg2AdapterAdapter
    psycopg2_adapter = Psycopg2AdapterAdapter('dbname=testdb user=user password=password host=localhost')
    result = psycopg2_adapter.execute('SELECT * FROM users')
    print(result)
    psycopg2_adapter.close()
```

### 依赖替换

在业务中通常会使用到三方厂商的其他能力 , 当要更换厂商时通常API需要重新对接

此时就可以使用适配器模式来降低更换成本 , 只需要定义一个适配器就可以完美连接系统

```python
# 原厂商
class OriginVendor:
    def read_temperature(self):
        return 25.0  # 返回温度，单位为摄氏度
# 新厂商
class NewVendor:
    def get_temp(self):
        return 77.0  # 返回温度，单位为华氏度

# 适配器
class Adapter(NewVendor):
    def __init__(self, vendor):
        self._vendor = vendor

    def read_temperature(self):
        # 将华氏度转换为摄氏度
        fahrenheit = self.vendor.get_temp()
        celsius = (fahrenheit - 32) * 5.0 / 9.0
        return celsius
```
