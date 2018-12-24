---
title: mkdown
date: 2018-12-24 16:09:57
tags:
---
---

### 1、标题

#### 这是四级标题
##### 这是五级标题
###### 这是六级标题

---

### 2、字体
**这是加粗的文字**
*这是倾斜的文字*`
***这是斜体加粗的文字***
~~这是加删除线的文字~~

---

### 3、引用
>这是引用的内容
>>这是引用的内容

---
### 4、分割线
---

---

### 5、图片
#### 5.1 网络
![blockchain](https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/
u=702257389,1274025419&fm=27&gp=0.jpg "区块链")
#### 5.2 本地
![totoro](mkdown/totoro.jpg)

---

### 6、超链接
[简书](http://jianshu.com)
[百度](http://baidu.com)

---
### 7、

#### 7.1 列表
- 列表内容
+ 列表内容
* 列表内容

#### 7.2 有序列表
1. 列表内容
2. 列表内容
3. 列表内容
  1. 子列表内容
  2. 子列表内容
  3. 子列表内容

---

### 8、表格
表头|表头|表头
:--|:--:|--:
左对齐|居中|右对齐
左对齐|居中|右对齐

---

### 9、代码
#### 9.1 代码
```
  代码...
  代码...
  代码...
```
#### 9.2 python代码
```python
@requires_authorization
def somefunc(param1='', param2=0):
    '''A docstring'''
    if param1 > param2:
        print 'Greater'
    return (param2 - param1 + 1) or None

class SomeClass:
    pass
```
#### 9.2 javascript代码
``` javascript
/**
* nth element in the fibonacci series.
* @param n >= 0
* @return the nth element, >= 0.
*/
function fib(n) {
  var a = 1, b = 1;
  var tmp;
  while (--n >= 0) {
    tmp = a;
    a += b;
    b = tmp;
  }
  return a;
}

document.write(fib(10));
```

---

### 10、流程图
```flow
st=>start: Start:>https://www.zybuluo.com
io=>inputoutput: verification
op=>operation: Your Operation
cond=>condition: Yes or No?
sub=>subroutine: Your Subroutine
e=>end

st->io->op->cond
cond(yes)->e
cond(no)->sub->io
```
