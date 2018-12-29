---
title: python语法
date: 2018-12-29 14:52:49
tags:
categories:
    - python
---
### python中一切皆对象

### map
map()是 Python 内置的高阶函数，它接收一个函数 f 和一个 list，并通过把函数 f 依次作用在 list 的每个元素上，得到一个新的 list 并返回。

### print不换行
```
print '--------',
```

### 指定解码用utf-8（以下代码添加到文件头部）
```
# -*- coding: utf-8 -*-    
```

### 运算
```
x = 2**10  # 幂运算2^10
```

### pycharm高亮
```
# todo:
```

### 匿名函数
```
sum = lambda arg1, arg2: arg1 + arg2    # 函数也是一个对象
print sum(10, 20)                       # 重载括号 输出30
```

### 变量
1. strings, tuples, 和 numbers 是不可更改的对象，而 list,dict 等则是可以修改的对象。
2. 不可变类型：变量赋值 x=5 后再赋值 x=10，这种情况相当于生成了新的x 原来的5被丢弃了
3. 可变类型：变量赋值list_x = [1,2,3] 后再赋值list_x = [1,2]，这种list_x没有动，只是内部的一部分被修改了

### 函数的参数传递
1. 不可变类型：类似c++的值传递，如整数、字符串、元组。如fun(x)，传递的只是x的值，没有影响x对象本身。比如在 fun(x)内部修改x的值，只是修改另一个复制的对象，不会影响x本身。
2. 可变类型：类似c++的引用传递，如列表、字典  。如fun(list_x)，则是将list_x真正的传过去，修改后fun外部的list_x也会受影响
3. 严格意义在python中我们不能说值传递还是引用传递，我们应该说传不可变对象和传可变对象。

### 作用域
1. 全局变量想作用于函数内，需加 global
2. python中的命名空间只有函数，不存在{}的命名空间

### 函数名
1. 以单下划线开头_foo的代表不能直接访问的类属性
2. 以双下划线开头的__foo代表类的私有成员
3. 以双下划线开头和结尾的__foo__代表 Python 里特殊方法专用的标识，如__init__()代表类的构造函数。

### 生成器
1. 斐波那契数列不获取fib函数的返回值
```python
def fib(max):         # 生成器函数
    n,a,b =0,0,1      # 初始值
    while n < max:    # 循环
        yield b       # 函数执行到这里的时候会被记录下来，在调用x.next()的时候会继续执行
        a,b =b,a+b    # 斐波那契向后移动
        n += 1        # 跳出循环的增量
x = fib(6)            # 这就是一个生成器 每次只生成一个数值(不占用内存)
for i in fib(6):      # 变量生成器
    print(i)          # 打印生成器生产的值
```

### 装饰器
1. 装饰器装饰函数，一般用于变量的校验&给传入对象添加属性
2. @functools.wraps(func) 可以保证装饰器不会对被装饰函数造成影响
3. 不带参数的装饰器，数字是函数执行的顺序
```python
def debug(func):                      # 1装饰器的名字 传入函数
    @functools.wraps(func)            # 2可以保证装饰器不会对被装饰函数造成影响
    def wrapper(*args, **kwargs):     # 4获取被装饰函数的变量
        pass                          # 5你已经拿到了变量，这里就可以处理对应相关的业务逻辑
        return func(*args, **kwargs)  # 6运行并返回被装饰的函数
    return wrapper                    # 3执行装饰函数
@debug                    # 调用装饰器
def say(something):       # 被装饰函数
    pass                  # 被装饰函数业务逻辑
```
4. 带参数的装饰器，数字是函数执行的顺序
```python
def logging(level):                           # 1装饰器的名字 传入装饰器的参数
    @functools.wraps(func)                    # 2可以保证装饰器不会对被装饰函数造成影响
    def wrapper(func):                        # 4传入被装饰函数
        def inner_wrapper(*args, **kwargs):   # 6获取被装饰函数的变量
            pass                              # 7你已经拿到了所有变量，这里就可以处理对应相关的业务逻辑
            return func(*args, **kwargs)      # 8运行并返回被装饰的函数
        return inner_wrapper                  # 5执行内部装饰函数
    return wrapper                            # 3执行装饰函数
@logging(level='INFO')     # 调用装饰器
def say(something):        # 被装饰函数
    pass                   # 被装饰函数业务逻辑
@logging(level='DEBUG')    # 调用装饰器
def do(something):         # 被装饰函数
    pass                   # 被装饰函数业务逻辑
```
