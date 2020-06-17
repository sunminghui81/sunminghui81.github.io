---
layout: post
keywords: parameters, function, method
description: Explanation on parameters of python functions (or class methods) in Python
title: "Python 函数如何传递参数"
categories: [python]
tags: [parameters, function, method]
group: archive
icon: compass
---
{% include mathsyouth/setup %}

Python 中通常说到的变量实际上指的是一个名字，所谓的赋值实际上是将 Python 中的对象和该名字绑定在一起，可以理解为该名字的值实际上是对象的地址。以下面的代码为例：

```python
some_guy = 'Fred'
some_guy = 'George'
```

### 将名字和对象绑定

第一行代码将名字 `some_guy` 和字符串对象 `'Fred'`绑定在一起。第二行代码，将`some_guy` 和新的字符串对象 `'George'` 重新绑定在一起，但是并没有改变字符串对象 `'Fred'` 或者 `'George'`本身。我们经常听到的说法“mutable 对象可以改变对象的值但是 immutable 却不可以“也是不对的。以下面的代码为例：

```python
first_names = ['Fred', 'George', 'Bill']
last_names = ['Smith', 'Jones', 'Williams']
name_tuple = (first_names, last_names)
print(name_tuple)

first_names.append('Igor')
print(name_tuple)
```

在 Python 中，`tuple` 是不可变的对象，也就是说，对象的值不能修改。但是，不可变的容器中可以包含可变对象（比如：`list`)的引用。因此，即使 `tuple` 对象不可变，如果我们改变了它指向对象的值，`tuple` 本身也会发现变化。


### Python 中一切皆为对象

在 Python 中，所有可以放在等号右边的值都是对象，比如：`10` 和 `"Hello World"` 都是对象。通过 `dir` 函数可以察看所有对象的属性，比如：

```python
print(dir(10))
foo = 10
help(foo.__add__)
foo = "Hello World"
print(dir(foo))
```

因为所有的名字都是和对象绑定在一起，所以我们可以更改很多系统自带变量的值，比如：
```python
import datetime
import imp

print(datetime.datetime.now())

class PartyTime():

    def __call__(self, *arg):
        imp.reload(datetime)
        value = datetime.datetime(*arg)
        datetime.datetime = self
        return value

    def __getattr__(self, value):
        if value == "now":
            return lambda: print("Party Time!")
        else:
            imp.reload(datetime)
            value = getattr(datetime.datetime, value)
            datetime.datetime = self
            return value

datetime.datetime = PartyTime()
datetime.datetime.now()
today = datetime.datetime(2017, 3, 7)
print(today)
print(today.timestamp())
```


#### 不可变和可变对象

不可变对象的意思是说，对象一旦在内存空间中分配了就不可以更改，如果要更改只能给更改后的对象重新分配内存空间。但是，对于可变对象来说，可以直接将内存空间中分配好的值覆盖掉，不需要分配新的内存空间。由于 `tuple` 是不可变的对象，那么一旦一个 `tuple` 分配好了，该 `tuple` 的值便不可以更改，如果 `tuple` 的值为对象引用的话，更改指向的对象的值是合法的，比如：

```python
class Foo(object):
    def __init__(self):
        self.value = 0
    def __str__(self):
        return str(self.value)
    def __repr__(self):
        return str(self.value)

f = Foo()
print(f)
foo_tuple = (f, f)
print(foo_tuple)
foo_tuple[0] = 1 # 该赋值回报错
f.value = 1
print foo_tuple
```

### 函数调用

当我们将一个变量传递给一个函数时，传递的值实际上是对象的引用。比如说，当函数 `foo(bar)` 调用时，在 `foo` 的范围内，将会将一个名字和 `bar` 所包含的对象引用值绑定在一起。如果 `bar` 绑定的是可变对象且 `foo` 函数改变了它的值，那么在该函数的范围外我们也将看到 `bar` 所指向的对象也发生了变化。比如：


```python
def foo(bar):
    bar.append(42)
    print(bar)

answer_list = []
foo(answer_list)
print(answer_list)
```

如果 `bar` 绑定的是不可变对象，那么 `foo` 函数在它的命名空间范围内至多可以复制 `bar` 所绑定的不可变对象，并将其与一个局部名字进行绑定在一起。比如：

```python
def foo(bar):
    bar = 'new value'
    print(bar)

answer_list = 'old value'
foo(answer_list)
print(answer_list)
```

再看一个例子：

```python
def try_to_modify(x, y, z):
    x = 23
    y.append(42)
    z = [99]  # new reference
    print(x)
    print(y)
    print(z)


def main():
    a = 77    # immutable variable
    b = [99]  # mutable variable
    c = [28]
    try_to_modify(a, b, c)
    print(a)
    print(b)
    print(c)
    

if __name__ == "__main__":
    main()
```


### 参考资料

1. [Understanding Python variables and Memory Management](http://foobarnbaz.com/2012/07/08/understanding-python-variables/)
1. [Is Python pass-by-reference or pass-by-value?](https://robertheaton.com/2014/02/09/pythons-pass-by-object-reference-as-explained-by-philip-k-dick/)
1. [Is Python call-by-value or call-by-reference? Neither.](https://jeffknupp.com/blog/2012/11/13/is-python-callbyvalue-or-callbyreference-neither/)
1. [Drastically Improve Your Python: Understanding Python's Execution Model](https://jeffknupp.com/blog/2013/02/14/drastically-improve-your-python-understanding-pythons-execution-model/)

