# Python中的下划线

总结Python中下划线符号的含义

https://peps.python.org/pep-0008/

## 单下划线

表示临时变量或一次性变量。

```python
a = [ _ for _ in zoo ]
```

## 单前导下划线

弱'internal use'指示器。弱指的是不严格限制外部使用。

```python
a = class_A()
a._var

from module import _var
# from module import * 则不会导入下划线开头符号
```

## 单末尾下划线

约定俗成：避免与Python关键字冲突。

```python
tkinter.Toplevel(master, class_='ClassName')
# 参数名称class_, 避免与class冲突
```

## 双前导下划线

用于命名类属性时，调用名称修饰name mangling

inside class FooBar, `__boo` becomes `_FooBar__boo`

## 双前导 双末尾下划线

魔法属性，魔法方法。

在开发Python代码时，**仅使用文档中明确说明的魔法方法和属性**。

不要自行发明或滥用双下划线前后缀的名称。