# Python 导入

总结Python导入机制

https://docs.python.org/zh-cn/3/tutorial/modules.htm

https://docs.python.org/zh-cn/3/reference/import.html#importsystem

## 1. module

> A module is a file containing Python definitions and statements.
> 模块是包含Python定义和语句的文件。

### 模块文件

- .py 脚本文件（版本平台无关）
- .pyc 编译后的字节码文件（版本平台相关）

https://stackoverflow.com/questions/514371/whats-the-bad-magic-number-error

py 手动生成 pyc

```shell
python -m py_compile module1.py
# 或者
python -m compileall module1.py

# 生成 .\__pycache__\module1.cpython-312.pyc
# 模块名为 module1
```

### 命名要求

**[PEP 8 中对包名和模块名的要求](https://peps.python.org/pep-0008/#package-and-module-names)：**

> Modules should have short, all-lowercase names. Underscores can be used in the module name if it improves readability. Python packages should also have short, all-lowercase names, although the use of underscores is discouraged.

短、全小写（可以在名称中间使用下划线以增强可读性，最好没有`.`）

### 命名空间

> Each module has its own private namespace, which is used as the global namespace by all functions defined in the module. 

每个模块有自己的命名空间。

惯例：`_`开头表示私有，非下划线开头表示公有





## 2. package

https://docs.python.org/zh-cn/3/glossary.html#term-package

> 一种可包含子模块或递归地包含子包的 Python [module](https://docs.python.org/zh-cn/3/glossary.html#term-module)。 从技术上说，包是具有 `__path__` 属性的 Python 模块。

https://docs.python.org/zh-cn/3/tutorial/modules.html#packages

> Packages are a way of structuring Python’s module namespace by using “dotted module names”.
> 包是通过使用“带点号模块名”来构造 Python 模块命名空间的一种方式。

### 1) regular package

https://docs.python.org/zh-cn/3/glossary.html#term-regular-package

常规包，含有`__init__.py`文件的目录。

```python
pkgname				# 顶层包 pkgname
├── __init__.py		# 顶层包 pkgname 初始化
├── pkg0			# 子包 pkg0
│   ├── __init__.py # 子包 pkg0 初始化
│   └── code1.py	# 模块 code1
├── pkg1
│   ├── __init__.py
│   └── code1.py
└── pkg2
	...
```

### 2) namespace package

https://docs.python.org/zh-cn/3/glossary.html#term-namespace-package

> [**PEP 420**](https://peps.python.org/pep-0420/) 所引入的一种仅被用作子包的容器的 [package](https://docs.python.org/zh-cn/3/glossary.html#term-package)，命名空间包可以没有实体表示物，其描述方式与 [regular package](https://docs.python.org/zh-cn/3/glossary.html#term-regular-package) 不同，因为它们没有 `__init__.py` 文件。

命名空间包，不包含`__init__.py` 文件的目录。



### 模块与包

|                   | `__file__`                      | `spec.has_location` | `__path__`                    | `spec.submodule_search_locations` |
| ----------------- | :------------------------------ | ------------------- | ----------------------------- | --------------------------------- |
| module            | `'xxx/modulename.py'`           | `True`              | has no attribute `'__path__'` | `None`                            |
| regular package   | `'xxx/packagename/__init__.py'` | `True`              | `['xxx/packagename']`         | `['xxx/packagename']`             |
| namespace package | `None`                          | `False`             | `_NamespacePath([])`          | `_NamespacePath([])`              |



## 3. 搜索路径

1. 内置（sys.builtin_module_names）
2. 变量（sys.path）

**sys.path初始化**：https://docs.python.org/zh-cn/3/library/sys_path_init.html#sys-path-init

- 脚本目录
- 环境变量 PYTHONPATH
- site-packages



## 4. 基础导入

> importing -- 导入[¶](https://docs.python.org/zh-cn/3/glossary.html#term-importing)
>
> 令一个模块中的 Python 代码能为另一个模块中的 Python 代码所使用的过程。

`import`是调用导入机制的常用方式。

https://docs.python.org/zh-cn/3/reference/simple_stmts.html#import

### 0) 约定

导入语法使用EBNF形式描述

```python
mod  ::=  (package ".")* (package | module)
```

`package`表示包名，`module`表示狭义模块名（模块文件的文件名）

### 1) 整体导入

**作用**：导入包/模块，至当前命名空间。

**形式**：```"import" module ["as" identifier] ("," module ["as" identifier])*```

**例子**：

```python
import mod as m0, m1, m2
# mod  ::=  (package ".")* (package | module)

m0.func() 	# 调用函数
m0.classA() # 实例化类
m0.var1 	# 访问变量

import package
# package.xxx形式支持取决于__init__.py

import requests
r = requests.get('https://www.python.org')
# requests 包的 __init__.py中
# from .api import delete, get, head, options, patch, post, put, request
```


在文件或者模块中``import foo.bar.baz as z`，最高层级，全局命名空间新增`'z':<module 'foo.bar.baz' from ''>`，使用`globals()`查看

在类中import，类的局部命名空间新增，使用``classname.__dict__`查看

在函数中import，函数的局部命名空间新增，使用`locals()`查看



### 2) 部分导入

**作用：**导入包/模块的指定符号（函数名、变量名、类名、模块名、包名等），至当前命名空间。

**形式：**

```"from" relative_module "import" identifier ["as" identifier] ("," identifier ["as" identifier])*```
```"from" relative_module "import" "(" identifier ["as" identifier] ("," identifier ["as" identifier])* [","] ")"```

**例子：**

```python
# 绝对导入

# 导入模块中的函数、变量、类等
from mod import func1 as f1, func2 as f2, class1 as c1, var1
from mod import (func1 as f1, func2 as f2, class1 as c1) # 括号便于换行
# mod  ::=  (package ".")* module

f1()	# 调用函数
c1()	# 实例化类
var1	# 访问变量

# 导入包中的模块或者子包
from package import module		# 导入模块
from package import subpackage	# 导入子包
```



### 3) 通配符形式导入

**作用：**导入指定模块的**所有公共符号**，至全局命名空间。

**形式：**```"from" relative_module "import" "*"```

**例子：**

```python
from mod import *
# mod  ::=  (package ".")* module

f1()	# 调用函数
c1()	# 实例化类
var1	# 访问变量

from package import *
```

仅在模块层级（最高层级）上被允许（在函数、类等内部（非最高层级）不允许import *）

**需要注意的是：**

- `import *`不会导入下划线开头（按照惯例，此为私有）的变量、函数、类`_var`、`__var`、`__var__`
- `import *`可能会造成名称混淆，命名冲突等问题，使用需谨慎。



### 4) 相对导入

相对导入适用于同一个包内模块之间的相互引用。

```python
package/
    __init__.py
    subpackage1/
        __init__.py
        moduleX.py
        moduleY.py
    subpackage2/
        __init__.py
        moduleZ.py
    moduleA.py
```

不论是在 `subpackage1/moduleX.py` 还是 `subpackage1/__init__.py` 中，以下导入都是有效的:

```python
from .moduleY import spam
from .moduleY import spam as ham
from . import moduleY
from ..subpackage1 import moduleY
from ..subpackage2.moduleZ import eggs
from ..moduleA import foo
```

需要注意的是，使用相对导入的模块不能直接作为脚本运行。https://www.cnblogs.com/hi3254014978/p/15317976.html

主模块（用于运行的py）内的导入语句必须使用绝对导入。

`requests`库，库内的代码使用相对导入，对非包内模块的引用采用绝对导入。





## 5. 高级导入

### 1) *`__import__`*

主要供python解释器使用，日常 Python 编程中**不建议直接使用**。

应当使用`importlib. import_module()`

### 2）importlib模块

与导入系统进行交互的模块。

调用导入机制的高级方式。可实现动态导入，延迟导入等等。

https://docs.python.org/zh-cn/3/library/importlib.html#

常用方法

`importlib.import_module(name, package=None)`

```python
requests = import_module('requests')
pkg_subpkg = import_module('..mod', 'pkg.subpkg')  # 导入 pkg.mod
```



`importlib.reload(module)` 

重新加载已经导入的模块。
适用于：频繁修改与测试模块代码的场景，动态更新配置的场景。（避免频繁重启解释器）



`importlib.invalidate_caches()` 

使`finder`在`sys.meta_path`中的缓存无效。
适用于需要让所有的查找器知道新模块的存在的场景，例如导入动态创建的模块。

```python
动态创建新模块文件
module_dir, module_path, module_name

sys.path.append(module_dir)		# 更新搜索路径
importlib.invalidate_caches() 	# 使缓存失效
dynamic_module = importlib.import_module(module_name)	# 动态导入新模块
dynamic_module.func()	# 调用新模块中的函数
```



`importlib.util`

https://docs.python.org/zh-cn/3/library/importlib.html#module-importlib.util

```python
# 判断模块是否可以导入
if name in sys.modules:
    # 已经导入
elif (spec := importlib.util.find_spec(name)) is not None:
    # 可以导入
else:
    # 找不到
```

模块分类（模块，常规包，命名空间包）

```python
from types import ModuleType
from inspect import ismodule
from importlib.machinery import ModuleSpec
from importlib.util import find_spec, module_from_spec
from typing import Union


def inspect_module(module: Union[str, ModuleType]) -> Union[ModuleSpec, None]:
    """
    :param module: 模块名称 或 模块实例
    :return: 模块的 spec

    返回指定模块的 spec，如果模块不存在，则返回 None。

    如果模块在运行时发生动态修改，那么返回的 spec 可能不是最新的。

    https://docs.python.org/zh-cn/3/library/importlib.html#importlib.machinery.ModuleSpec
    """
    alias = None
    if isinstance(module, ModuleType):
        # module 实例
        spec = module.__spec__
    elif isinstance(module, str):
        # module 名称
        name = module
        if module in globals():
            if ismodule(globals()[name]):
                module = globals()[name]
                spec = module.__spec__
            else:
                # 非模块名称
                print(f"'{name}' is not a module name, but a {type(globals()[name])}\n")
                return None
        else:
            # identifier 不存在
            spec = find_spec(name=name)
            if spec is None:
                print(f"module '{name}' is not found\n")
                return None
            module = module_from_spec(spec)

        alias = module
    else:
        # 非法输入格式
        print(f"module should be a string or a module, not {type(module)}\n")
        return None

    # 检查
    fullname = spec.name
    has_location = spec.has_location
    submodule_search_locations = spec.submodule_search_locations
    origin = spec.origin
    if origin is None and not has_location:
        type_ = 'namespace package'
    elif submodule_search_locations is None:
        type_ = 'module'
    else:
        type_ = 'regular package'

    # 输出信息
    print(f'fullname : {fullname}')
    if alias is not None and alias != fullname:
        print(f'alias    : {alias}')
    print(f'type     : {type_}')
    if type_ != 'namespace package' and origin == 'frozen':
        print(f'__file__ : {module.__file__}')
    print(f'origin   : {origin}')
    print(f'sub_path : {submodule_search_locations}')
    print()
    # 返回 spec
    return spec


if __name__ == '__main__':
    inspect_module("pprint")
```





## 6. 相关属性与函数

```python
globals() 	# 返回当前模块命名空间字典
locals()	# 更新并返回代表当前本地符号表的字典

classname.__dict__

dir() 			# 返回当前local作用域的名称列表
dir(classname)  # 返回object的有效属性列表
dir(funcname)
dir(varname)
dir(modulename)

sys.stdlib_module_names		# 包含标准库模组名称字符串的冻结集合，在所有平台所有解释器保持一致
sys.builtin_module_names	# 解释器builtin模块名称集合
sys.meta_path	# meta_path finder组成的列表
sys.modules		# 已加载模块字典
sys.path		# 模块搜索路径列表
```

https://docs.python.org/zh-cn/3/library/functions.html#globals

https://docs.python.org/zh-cn/3/library/functions.html#locals

https://docs.python.org/zh-cn/3/library/functions.html#dir
