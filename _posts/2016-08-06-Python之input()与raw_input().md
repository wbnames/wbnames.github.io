---
layout: post
title: Python之input()与raw_input()
category: Python
tags: [Python]
---

input()与raw_input()均是 python 的内建函数，通过读取控制台的输入与用户实现交互。但他们的功能不尽相同。举两个小栗子。

## Tips

### Tip1

```python
>>> raw_input_A = raw_input("raw_input: ")
raw_input: asd
>>> input_A = input("Input: ")
Input: asd
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<string>", line 1, in <module>
NameError: name 'asd' is not defined
>>> input_A = input("Input: ")
Input: "asd"
>>> 
```

**Tip：** 这两个函数均能接收 **字符串** ，但 **raw_input()** 直接读取控制台的输入（任何类型的输入它都可以接收）。而对于 **input()** ，它希望能够读取一个合法的 python 表达式，即你输入字符串的时候必须使用引号将它括起来，否则它会引发一个 **Syntax Error** 。

### Tip2
```python
>>> raw_input_B = raw_input("raw_input: ")
raw_input: 123
>>> type(raw_input_B)
<type 'str'>
>>> input_B = input("input: ")
input: 123
>>> type(input_B)
<type 'int'>
>>> 
```

**Tip：raw_input()** 将所有输入作为字符串看待，返回字符串类型。而 **input()** 在对待纯数字输入时具有自己的特性，它返回所输入的数字的类型（ int, float ）；同时在例子 1 知道，**input()** 可接受合法的 python 表达式，举例：**input( 1 + 3 )** 会返回 int 型的 4 。

## Built-in Functions

查看 Built-in Functions ，得知：

```python
input([prompt])
    Equivalent to eval(raw_input(prompt)) 
```
    
* input() 本质上还是使用 raw_input() 来实现的，只是调用完 raw_input() 之后再调用 eval() 函数，所以，你甚至可以将表达式作为 input() 的参数，并且它会计算表达式的值并返回它。  
* 不过在 Built-in Functions 里有一句话是这样写的：Consider using the raw_input() function for general input from users.  
* 除非对 input() 有特别需要，否则一般情况下我们都是推荐使用 raw_input() 来与用户交互。  

