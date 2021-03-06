---
title: lua语法
date: 2019-04-14 11:10:38
tags:
categories:
    - lua
---
### lua特性
开源，轻量级，c语言编写实现，实现面向对象有点绕，执行效率高

### lua开发环境
1. linux
  ```
    -- 下载lua包、解压
    wget -c http://www.lua.org/ftp/lua-5.3.0.tar.gz
    tar zxvf lua-5.3.0.tar.gz

    -- 下载libreadline相关支持
    sudo apt-get install libreadline5
    sudo apt-get install libreadline-gplv2-dev

    -- 编译安装
    cd lua-5.3.0
    make linux
    sudo make install

    -- 测试命令
    lua
  ```
2. windows
  1. [运行环境 LuaForWindows --> https://github.com/rjpcomputing/luaforwindows/releases](https://github.com/rjpcomputing/luaforwindows/releases)
  2. [IDE pycharm --> https://blog.csdn.net/u012911347/article/details/82191541](https://blog.csdn.net/u012911347/article/details/82191541)

### 基本语法
1. 注释

```lua
-- 这是一条注释

--[[
这是多行注释
--]]
```

2. 在lua中用end给代码分段(function、if、while、for都是以end结束)，局部变量的作用域是从定义到对应的end结束
3. 局部变量要用local修饰，没有local修饰的都是全局变量(o my god！！)
4. 类型有nil、bool、number、string、function、userdata(任意的c数据结构)、thread、table
5. lua中只有false和nil是假的，如果写if 0 then 这样后面的语句是会被执行的(是不是感觉很反人类)

```lua
if 0 then
  print('0 is true')
end
```

6. type函数的返回类型是string

```lua
if type(x) == 'nil' then
  print('x type is nil')
end
```

7. table是lua中最主要的数据结构, table是kv的模式，如果没有写key值lua会默认给一个从1开始的key值

```lua
local tab01 = {a='a',b='b',c='c',1,2,d='d',3}
--print使用..拼接打印
print('a:'..tab01.a)     -- a:a
print('d:'..tab01['d'])  -- d:d
print('1:'..tab01[1])    -- 1:1
print('3:'..tab01[3])    -- 3:3

-- insert(table, [pos,], val)
-- pos不填为尾插。pos这个值最好是正常使用不要越界，因为在排序的时候会出问题
tab02 = {4,3,2,1}
table.insert(tab02, 4, 5)  -- 在这里pos的值确保在[1, 5]
print(table.concat(tab02)) --43251

-- 自定义table打印函数
tab_print = function(tab)
  for k, v in pairs(tab) do
    io.write(v)
  end
  io.write('\n')
end

-- sort排序 排序的时候是从1开始到‘第一个为nil’的index，在这个index之后的数据是不会参与排序
-- 上面的引号解释，如果赋值table内的元素为nil，排序是会出问题的
tab02[100] = 0
table.sort(tab02)
print(table.concat(tab02))  --12345
tab_print(tab02)            -- 123450

-- remove(table, [pos,]) 默认是尾插
table.remove(tab02, 5)
print(table.concat(tab02))  --1234
tab_print(tab02)            -- 12340

-- getn(table)
print(table.getn(tab02))   --4
print(#tab02)              --4
```

8. 迭代器

```lua
--pairs遍历table中的所kv
--ipairs遍历table从1开始到‘第一个为nil’的index
local tab01 = {a='a',b='b',c='c',1,2,d='d',3}
tab01[100] = 100

--pairs
for k, v in pairs(tab01) do
  io.write(k..':'..v..',')
end
io.write('\n')

--ipairs
for k, v in ipairs(tab01) do
  io.write(k..':'..v..',')
end
io.write('\n')
```

9. 自己创建的带状态的迭代器

```lua
-- 使用闭包函数在1，2之间循环，直到条件不满足的时候跳出闭包函数
function iterator(tab)
  local index = 0
  local count = #tab
  return function()         -- 闭包函数 1
    index = index + 1
    if index <= count then
      return tab[index]   -- 返回迭代器的当前元素 2
    end
  end
end

tab = {1,2,3,4,5,6}
for v in iterator(tab) do
  io.write(v..',')
end
io.write('\n')
```
10. lua的垃圾内存回收机制，当一块内存没有被引用后就会被回收，去除引用的方式是直接给变量赋值为nil
11. 运算符
    * ~= 是不等于  
    * ..链接两个字符串
    * \# 返回字符或者表的长度
12. string的三种表示 '' "" [[]]，string的拼接也可以用format
```lua
x='1'
y="2"
z=[[3]]
print(string.format("%s+%s=%s",x,y,z))
```
13. 可变参
```lua
function average(...)
   sum = 0
   local args={...}    
   for i,v in ipairs(args) do
      sum = sum + v
   end
   return sum/#args
end
```

### table实现面向对象
1. lua实现面向对象的机制
  1. 函数也是一个类型，table中可以包含函数
  2. 使用：的函数，会自带一个self参数，用来指明是哪个table调用的
  3. 设置原表setmetatable(table, meta_table)，在table中找不到的元素，lua会去meta_table.\__index表中去找， 但是元方法就不会去meta_table.\__index中去找
  4. 下面是实现了一个相对目前认为是比较简单的类继承
```lua
local Father = {}
local Son = {}

Father.__index = Father  -- 这样写可以少一个中间table
Son.__index = Son

function Father.new()
  local o = {}
  setmetatable(o, Father)
  return o
end

function Son.new()
  local o = {}             -- 1
  setmetatable(o, Son)     -- 2
  return o
end

setmetatable(Son, Father)  -- 3

function Father:printf()
  print("Father printf")
end

--[[                      -- 注释1
function Son:printf()
  print("Son printf")
end
--]]

local x = Son.new()
x:printf()
-- 解释下这个x:printf()的调用过程
-- 1. 首先x是个table
-- 2. table中没有printf函数就会去Son中找 如果注释1打开就会调用Son:printf()
-- 3. Son中也没有printf函数就要去Father中找
```

### end
ok今天就学到这里了，老夫要去健身房lu铁了，感觉lua可能会用的不是很爽
