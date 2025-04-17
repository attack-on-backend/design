# Attack on Design - 解释器模式 🪼

## 概述

**关键词 : 表达式 , 编译解释**

**解释器模式 (Interpreter Pattern) :** 为某个语言定义它的语法表示 , 并定义一个解释器用来处理这个语法

## 实现

```python
# 定义抽象表达式接口
class Expression:
    def interpret(self, context):
        pass

# 定义终端表达式类
class TerminalExpression(Expression):
    def __init__(self, value):
        self.value = value

    def interpret(self, context):
        # 返回终端表达式的值
        return self.value

# 定义非终端表达式类（加法）
class AddExpression(Expression):
    def __init__(self, left, right):
        self.left = left
        self.right = right

    def interpret(self, context):
        # 解释左表达式和右表达式，并返回它们的和
        left_result = self.left.interpret(context)
        right_result = self.right.interpret(context)
        return left_result + right_result

# 定义非终端表达式类（减法）
class SubtractExpression(Expression):
    def __init__(self, left, right):
        self.left = left
        self.right = right

    def interpret(self, context):
        # 解释左表达式和右表达式，并返回它们的差
        left_result = self.left.interpret(context)
        right_result = self.right.interpret(context)
        return left_result - right_result

# 定义上下文类
class Context:
    def __init__(self):
        self.variables = {}

    def set_variable(self, name, value):
        self.variables[name] = value

    def get_variable(self, name):
        return self.variables.get(name, 0)


if __name__ == "__main__":
    # 创建上下文
    context = Context()

    # 创建表达式
    num1 = TerminalExpression(10)
    num2 = TerminalExpression(5)
    add_expr = AddExpression(num1, num2)
    subtract_expr = SubtractExpression(add_expr, TerminalExpression(3))

    # 解释表达式
    result_add = add_expr.interpret(context)
    result_subtract = subtract_expr.interpret(context)

    print(f"加法结果: {result_add}")  # 输出: 加法结果: 15
    print(f"减法结果: {result_subtract}")  # 输出: 减法结果: 12
```

## 场景

### 表达式

构建ORM表达式

```python
# 定义抽象表达式接口
class Expression:
    def interpret(self, context):
        pass

# 定义具体表达式类
class SelectExpression(Expression):
    def __init__(self, table, columns, condition=None):
        self.table = table
        self.columns = columns
        self.condition = condition

    def interpret(self, context):
        query = f"SELECT {', '.join(self.columns)} FROM {self.table}"
        if self.condition:
            query += f" WHERE {self.condition.interpret(context)}"
        return query

class WhereExpression(Expression):
    def __init__(self, condition):
        self.condition = condition

    def interpret(self, context):
        return self.condition.interpret(context)

class AndExpression(Expression):
    def __init__(self, left, right):
        self.left = left
        self.right = right

    def interpret(self, context):
        return f"({self.left.interpret(context)} AND {self.right.interpret(context)})"

class OrExpression(Expression):
    def __init__(self, left, right):
        self.left = left
        self.right = right

    def interpret(self, context):
        return f"({self.left.interpret(context)} OR {self.right.interpret(context)})"


class TerminalExpression(Expression):
    def __init__(self, column, value):
        self.column = column
        self.value = value

    def interpret(self, context):
        return f"{self.column} = '{self.value}'"

# 定义上下文类
class Context:
    def __init__(self):
        self.variables = {}

    def set_variable(self, name, value):
        self.variables[name] = value

    def get_variable(self, name):
        return self.variables.get(name, 0)

if __name__ == "__main__":
    # 创建上下文
    context = Context()

    # 创建终端表达式
    condition1 = TerminalExpression("name", "Alice")
    condition2 = TerminalExpression("age", "30")

    # 创建 AND 和 OR 表达式
    and_condition = AndExpression(condition1, condition2)
    or_condition = OrExpression(condition1, condition2)

    # 创建 SELECT 表达式
    select_expr = SelectExpression("users", ["id", "name", "age"], WhereExpression(and_condition))
    select_expr_or = SelectExpression("users", ["id", "name", "age"], WhereExpression(or_condition))

    # 解释表达式
    query_and = select_expr.interpret(context)
    query_or = select_expr_or.interpret(context)

    print(f"AND 查询: {query_and}")  # 输出: AND 查询: SELECT id, name, age FROM users WHERE (name = 'Alice' AND age = '30')
    print(f"OR 查询: {query_or}")  # 输出: OR 查询: SELECT id, name, age FROM users WHERE (name = 'Alice' OR age = '30')
```



