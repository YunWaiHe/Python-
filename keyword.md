# Python 关键字

总结python的关键字

## keyword模块

https://docs.python.org/3/library/keyword.html

```python
keyword.iskeyword		# 判断是否是关键字
keyword.issoftkeyword	# 判断是否是软关键字
keyword.kwlist			# 关键字列表
keyword.softkwlist		# 软关键字列表
```

## 关键字

```python
False True None
and or not is
for in while
from import as
async await
class
def return
if elif else break continue
assert
try raise except finally
del
with
global nonlocal
pass
lambda yield
```

## 软关键字

`match`, `case`, `type` , `_` 在某些上下文中可以充当关键字。(3.10+)

>but this distinction is done at the parser level, not when tokenizing.



`match`, `case`,  `_` 用在match语句

```python
flag = False
match (100, 200):
   case (100, 300):  # Mismatch: 200 != 300
       print('Case 1')
   case (100, 200) if flag:  # Successful match, but guard fails
       print('Case 2')
   case (100, y):  # Matches and binds y to 200
       print(f'Case 3, y: {y}')
   case _:  # Pattern not attempted
       print('Case 4, I match anything!')
```



`type`用在type语句(3.12 +)，用于定义类型别名

```python
type Point = tuple[float, float]
```



