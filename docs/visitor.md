# Design Pattern - 访问者模式

## 概述

**关键词 : 数据操作解耦 , 开闭原则**

**访问者模式 (Visitor Pattern) :** 封装一些作用于某种数据结构中各元素的操作 , 它可以在不改变数据结构的前提下定义作用于这些元素新的操作

访问模式可以在不改变数据结构的前提下定义作用于这些元素的新操作 , 将数据结构和操作解耦 , 同时方便扩展新操作

## 实现

```python
# 定义访问者接口
class Visitor:
    def visit_element_a(self, element_a):
        pass

    def visit_element_b(self, element_b):
        pass

# 定义元素接口
class Element:
    def accept(self, visitor):
        pass

# 定义具体元素类
class ConcreteElementA(Element):
    def accept(self, visitor):
        visitor.visit_element_a(self)

    def operation_a(self):
        return "ConcreteElementA operation"

class ConcreteElementB(Element):
    def accept(self, visitor):
        visitor.visit_element_b(self)

    def operation_b(self):
        return "ConcreteElementB operation"

# 定义具体访问者类
class ConcreteVisitorA(Visitor):
    def visit_element_a(self, element_a):
        print(f"ConcreteVisitorA visited {element_a.operation_a()}")

    def visit_element_b(self, element_b):
        print(f"ConcreteVisitorA visited {element_b.operation_b()}")

class ConcreteVisitorB(Visitor):
    def visit_element_a(self, element_a):
        print(f"ConcreteVisitorB visited {element_a.operation_a()}")

    def visit_element_b(self, element_b):
        print(f"ConcreteVisitorB visited {element_b.operation_b()}")

# 定义对象结构类
class ObjectStructure:
    def __init__(self):
        self._elements = []

    def add_element(self, element):
        self._elements.append(element)

    def remove_element(self, element):
        self._elements.remove(element)

    def accept(self, visitor):
        for element in self._elements:
            element.accept(visitor)

# 使用示例
if __name__ == "__main__":
    # 创建对象结构
    object_structure = ObjectStructure()

    # 创建具体元素
    element_a = ConcreteElementA()
    element_b = ConcreteElementB()

    # 将元素添加到对象结构中
    object_structure.add_element(element_a)
    object_structure.add_element(element_b)

    # 创建具体访问者
    visitor_a = ConcreteVisitorA()
    visitor_b = ConcreteVisitorB()

    # 访问对象结构中的元素
    print("Using ConcreteVisitorA:")
    object_structure.accept(visitor_a)

    print("\nUsing ConcreteVisitorB:")
    object_structure.accept(visitor_b)
```

## 场景

### 数据操作解耦

文档处理系统 , 数据包括段落、标题、列表 , 不同的访问者渲染文档成不同的格式

```python
# 定义访问者接口
class Visitor:
    def visit_paragraph(self, paragraph):
        pass

    def visit_heading(self, heading):
        pass

    def visit_list(self, lst):
        pass

# 定义元素接口
class Element:
    def accept(self, visitor):
        pass

# 定义具体元素类
class Paragraph(Element):
    def __init__(self, text):
        self.text = text

    def accept(self, visitor):
        visitor.visit_paragraph(self)

class Heading(Element):
    def __init__(self, level, text):
        self.level = level
        self.text = text

    def accept(self, visitor):
        visitor.visit_heading(self)

class List(Element):
    def __init__(self, items):
        self.items = items

    def accept(self, visitor):
        visitor.visit_list(self)

# 定义具体访问者类
class HTMLVisitor(Visitor):
    def visit_paragraph(self, paragraph):
        print(f"<p>{paragraph.text}</p>")

    def visit_heading(self, heading):
        print(f"<h{heading.level}>{heading.text}</h{heading.level}>")

    def visit_list(self, lst):
        print("<ul>")
        for item in lst.items:
            print(f"  <li>{item}</li>")
        print("</ul>")

class TextVisitor(Visitor):
    def visit_paragraph(self, paragraph):
        print(paragraph.text)

    def visit_heading(self, heading):
        print(f"{'#' * heading.level} {heading.text}")

    def visit_list(self, lst):
        for item in lst.items:
            print(f"- {item}")

# 定义对象结构类
class Document:
    def __init__(self):
        self.elements = []

    def add_element(self, element):
        self.elements.append(element)

    def accept(self, visitor):
        for element in self.elements:
            element.accept(visitor)

# 使用示例
if __name__ == "__main__":
    # 创建文档对象
    document = Document()

    # 创建文档元素
    paragraph1 = Paragraph("This is a paragraph.")
    heading1 = Heading(1, "Main Title")
    list1 = List(["Item 1", "Item 2", "Item 3"])
    paragraph2 = Paragraph("This is another paragraph.")
    heading2 = Heading(2, "Sub Title")

    # 将元素添加到文档中
    document.add_element(paragraph1)
    document.add_element(heading1)
    document.add_element(list1)
    document.add_element(paragraph2)
    document.add_element(heading2)

    # 创建具体访问者
    html_visitor = HTMLVisitor()
    text_visitor = TextVisitor()

    # 使用 HTML 访问者访问文档
    print("Rendering document as HTML:")
    document.accept(html_visitor)

    print("\nRendering document as plain text:")
    document.accept(text_visitor)
```

