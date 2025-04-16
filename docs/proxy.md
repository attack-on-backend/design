# Design Pattern - 代理模式

## 概念

**关键词 : 访问控制 , 业务解耦 , 附加功能**

**代理模式 (Proxy Pattern) :** 为其他对象提供一种代理以控制对这个对象的访问

## 实现

```python
class Object:
    def __init__(self):
        self.a = 1
        self.b = 2
        self.c = 3

class ProxyObject:
    def __init__(self, real_object):
        self._real = real_object
        # 只提供了用于
        self.a = self._real.a
```

## 场景

### 访问控制

```python
class ProtectedSubject:
    def request(self):
        print("ProtectedSubject: Handling request.")

class ProtectedProxy:
    def __init__(self, protected_subject):
        self._protected_subject = protected_subject

    def request(self, user):
        if self.check_access(user):
            self._protected_subject.request()
        else:
            print("ProtectedProxy: Access denied.")

    def check_access(self, user):
        print(f"ProtectedProxy: Checking access for {user}.")
        return user == "admin"

# 客户端代码
protected_subject = ProtectedSubject()
protected_proxy = ProtectedProxy(protected_subject)
# 用户 'admin' 请求
protected_proxy.request("admin")

# 用户 'guest' 请求
protected_proxy.request("guest")
```

### 特殊代理

特殊代理有很多种 : 远程代理 , 虚拟代理 , `Copy-on-Write`代理 , `Cache`代理 , 防火墙代理 , 同步化代理 , 智能引用代理

以远程代理为例 , 我们所使用的RPC框架就是远程代理的一种表现

`server.py` 

```python
from xmlrpc.server import SimpleXMLRPCServer
from xmlrpc.server import SimpleXMLRPCRequestHandler

# 定义远程方法
class MyRPCServer:
    def add(self, x, y):
        print(f"Server: Adding {x} and {y}")
        return x + y

    def multiply(self, x, y):
        print(f"Server: Multiplying {x} and {y}")
        return x * y

# 创建服务器
server = SimpleXMLRPCServer(("localhost", 8000), requestHandler=SimpleXMLRPCRequestHandler)
server.register_introspection_functions()

# 注册实例方法
server.register_instance(MyRPCServer())

print("Server: Listening on port 8000...")
server.serve_forever()
```

`client.py` 

```python
import xmlrpc.client

# 连接到服务器
server = xmlrpc.client.ServerProxy("http://localhost:8000/")

# 调用远程方法
result_add = server.add(5, 3)
print(f"Client: Result of add(5, 3) is {result_add}")

result_multiply = server.multiply(5, 3)
print(f"Client: Result of multiply(5, 3) is {result_multiply}")
```



