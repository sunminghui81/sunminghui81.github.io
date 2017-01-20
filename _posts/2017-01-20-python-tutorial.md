---
layout: post
keywords: python
description: Python 读书笔记
title: "The Python Tutorial 实战笔记"
categories: [python]
tags: [python]
group: archive
icon: compass
---
{% include mathsyouth/setup %}

### Python 解释器

#### 调用 python 解释器

`python -m module [arg] ...` 运行　`module` 的源文件， `[arg]` 为调用 `module` 所需要的参数。

#### 源文件编码

在 Python 源文件中可以使用不同于 ASCII 的编码，最好的方法是在 `#!` 行后面加上一个更特殊的注释行来定义源文件编码：

```Python
# -*- coding: ascii -*-
# -*- coding: utf_8 -*-
# -*- coding: utf_16 -*-
```

### Python 语法

1. 如果不希望前缀为 `\` 的字符被解释为特殊字符，则可以通过在第一个引号前添加 `r` 来使用原始字符串：<br>

   ```
   print 'C:\some\name'  # here \n means newline!
   print r'C:\some\name' # note the r before the quote
   ```
1. 字符串文字可以跨越多行, 一种方法是使用三重引号："""...""" 或 '''...'''，行的结尾会自动包含在字符串中，但可以通过在行末添加 `\` 来防止这种情况发生。下面的例子：<br>

   ```
   print """\
   Usage: thingy [OPTIONS]
        -h                        Display this usage message
        -H hostname               Hostname to connect to
   """
   print """
   Usage: thingy [OPTIONS]
        -h                        Display this usage message
        -H hostname               Hostname to connect to
   """
   text = ('Put several strings within parentheses '
           'to have them joined together.')
   print text
   ```
1. Lists 的所有切片操作返回包含所请求元素的新列表。这意味着以下切片将返回该列表的新（浅）副本：<br>

   ```
   squares = [1, 4, 9, 16, 25]
   squares[:]
   ```
1. A trailing comma avoids the newline after the output: <br>

   ```
   a, b = 0, 1
   while b < 1000:
       print b,
       a, b = b, a+b
   ```
1. Loop statements may have an `else` clause; it is executed when the loop terminates through exhaustion of the list (with `for`) or when the condition becomes false (with `while`), but not when the loop is terminated by a `break` statement. This is exemplified by the following loop, which searches for prime numbers: <br>

   ```
   for n in range(2, 10):
       for x in range(2, n):
           if n % x == 0:
               print n, 'equals', x, '*', n/x
               break
       else:
           print n, 'is a prime number'
   ```
1. Even functions without a `return` statement do return a value, called `None` (it’s a built-in name). Falling off the end of a function also returns `None`. Writing the value `None` is normally suppressed by the interpreter if it would be the only value written.
1. The default values are evaluated at the point of function definition in the *defining* scope, so that <br>

   ```
   i = 5

   def f(arg=i):
       print arg

   i = 6
   f()
   ```
   will print 5.
1. **Important warning:** The default value is evaluated only once. This makes a difference when the default is a mutable object such as a list, dictionary, or instances of most classes. For example, the following function accumulates the arguments passed to it on subsequent calls: <br>

   ```
   def f(a, L=[]):
       L.append(a)
       return L

   print f(1)
   print f(2)
   print f(3)
   ```
   This will print<br>

   ```
   [1]
   [1, 2]
   [1, 2, 3]
   ```
   If you don’t want the default to be shared between subsequent calls, you can write the function like this instead:<br>

   ```
   def f(a, L=None):
       if L is None:
           L = []
       L.append(a)
       return L

   print f(1)
   print f(2)
   print f(3)
   ```
1. Keyword arguments <br>

   ```
   def parrot(voltage, state='a stiff', action='voom', type='Norwegian Blue'):
       print "-- This parrot wouldn't", action,
       print "if you put", voltage, "volts through it."
       print "-- Lovely plumage, the", type
       print "-- It's", state, "!"
   ```
   This function can be called in any of the following ways:<br>

   ```
   parrot(1000)                                       # 1 positional argument
   parrot(voltage=1000)                               # 1 keyword argument
   parrot(voltage=1000000, action='VOOOOOM')          # 2 keyword arguments
   parrot(action='VOOOOOM', voltage=1000000)          # 2 keyword arguments
   parrot('a million', 'bereft of life', 'jump')      # 3 positional arguments
   parrot('a thousand', state='pushing up daisies')   # 1 positional, 1 keyword
   ```
   but all the following calls would be invalid:<br>

   ```
   parrot()                     # required argument missing
   parrot(voltage=5.0, 'dead')  # non-keyword argument after a keyword argument
   parrot(110, voltage=220)     # duplicate value for the same argument
   parrot(vol=5.0)              # unknown keyword argument
   ```
   In a function call, keyword arguments must follow positional arguments. All the keyword arguments passed must match one of the arguments accepted by the function, and their order is not important. This also includes non-optional arguments.
1. When a final formal parameter of the form `**name` is present, it receives a dictionary containing all keyword arguments except for those corresponding to a formal parameter. This may be combined with a formal parameter of the form `*name` which receives a tuple containing the positional arguments beyond the formal parameter list. (`*name` must occur before `**name`.) <br>

   ```
   def cheeseshop(kind, *arguments, **keywords):
       print "-- Do you have any", kind, "?"
       print "-- I'm sorry, we're all out of", kind
       for arg in arguments:
           print arg
       print "-" * 40
       keys = sorted(keywords.keys())
       for kw in keys:
           print kw, ":", keywords[kw]
    ```
    It could be called like this:<br>

    ```
    cheeseshop("Limburger", "It's very runny, sir.",
           "It's really very, VERY runny, sir.",
           shopkeeper='Michael Palin',
           client="John Cleese",
           sketch="Cheese Shop Sketch")
    ```
    Note that the list of keyword argument names is created by sorting the result of the keywords dictionary’s `keys()` method before printing its contents; if this is not done, the order in which the arguments are printed is undefined.
1. The reverse situation occurs when the arguments are already in a list or tuple but need to be unpacked for a function call requiring separate positional arguments. Write the function call with the `*`-operator to unpack the arguments out of a list or tuple: <br>

   ```
   args = [2, 5]
   range(*args)
   ```
   In the same fashion, dictionaries can deliver keyword arguments with the `**`-operator.
1. Coding style:
   * Use 4-space indentation, and no tabs.
   * Wrap lines so that they don’t exceed 79 characters.
   * Use docstrings.
   * Use spaces around operators and after commas, but not directly inside bracketing constructs: `a = f(1, 2) + g(3, 4)`.
   * Name your classes and functions consistently; the convention is to use CamelCase for classes and lower_case_with_underscores for functions and methods.
1. The list methods make it very easy to use a list as a stack, where the last element added is the first element retrieved (“last-in, first-out”). To add an item to the top of the stack, use `append()`. To retrieve an item from the top of the stack, use `pop()` without an explicit index. For example: <br>

   ```
   stack = [3, 4, 5]
   stack.append(6)
   stack.append(7)
   stack
   stack.pop()
   stack
   stack.pop()
   stack
   ```
 1. While appends and pops from the end of list are fast, doing inserts or pops from the beginning of a list is slow (because all of the other elements have to be shifted by one). To implement a queue, use `collections.deque` which was designed to have fast appends and pops from both ends (“first-in, first-out”). For example: <br>

    ```
    from collections import deque
    queue = deque(["Eric", "John", "Michael"])
    queue.append("Terry")
    queue.append("Graham")
    queue
    queue.popleft()
    queue
    queue.popleft()
    queue
    ```
1. List comprehensions <br>

   ```
   squares = [x**2 for x in range(10)]
   ```
   This is also equivalent to `squares = map(lambda x: x**2, range(10))`, but it’s more concise and readable.<br>

   ```
   [(x, y) for x in [1,2,3] for y in [3,1,4] if x != y]
   # create a list of 2-tuples like (number, square)
   [(x, x**2) for x in range(6)]
   matrix = [
       [1, 2, 3, 4],
       [5, 6, 7, 8],
       [9, 10, 11, 12],
   ]
   # The following list comprehension will transpose rows and columns
   [[row[i] for row in matrix] for i in range(4)]
   transposed = []
   for i in range(4):
       transposed.append([row[i] for row in matrix])
   # In the real world, you should prefer built-in functions to complex flow
   # statements. The zip() function would do a great job for this use case
   zip(*matrix)
   ```
1. The `del` statement <br>

   ```
   a = [1, 2, 3]
   b = a
   c = a[:]
   del a[:]
   del a
   ```
1. Tuples are immutable, and usually contain a heterogeneous sequence of elements that are accessed via unpacking or indexing. Lists are mutable, and their elements are usually homogeneous and are accessed by iterating over the list.
1. A tuple with one item is constructed by following a value with a comma (it is not sufficient to enclose a single value in parentheses). Ugly, but effective. For example: <br>

   ```
   singleton = 'hello',
   singleton = ('hello')
   singleton = ('hello',)
   ```
1. The statement `t = 12345, 54321, 'hello!'` is an example of *tuple packing*: the values `12345`, `54321` and `'hello!'` are packed together in a tuple. The reverse operation is also possible: `x, y, z = t` This is called, appropriately enough, *sequence unpacking* and works for any sequence on the right-hand side. Note that multiple assignment is really just a combination of tuple packing and sequence unpacking.
1. Unlike sequences, which are indexed by a range of numbers, dictionaries are indexed by keys, which can be any immutable type; strings and numbers can always be keys.
1. When looping through a sequence, the position index and corresponding value can be retrieved at the same time using the `enumerate()` function. <br>

   ```
   for i, v in enumerate(['tic', 'tac', 'toe']):
       print i, v
   ```
   To loop over two or more sequences at the same time, the entries can be paired with the zip() function. <br>

   ```
   questions = ['name', 'quest', 'favorite color']
   answers = ['lancelot', 'the holy grail', 'blue']
   for q, a in zip(questions, answers):
       print 'What is your {0}?  It is {1}.'.format(q, a)
   ```
   To loop over a sequence in reverse, first specify the sequence in a forward direction and then call the `reversed()` function. <br>

   ```
   for i in reversed(xrange(1,10,2)):
       print i,
   ```
   To loop over a sequence in sorted order, use the `sorted()` function which returns a new sorted list while leaving the source unaltered. <br>

   ```
   basket = ['apple', 'orange', 'apple', 'pear', 'orange', 'banana']
   for f in sorted(set(basket)):
       print f,
   ```
   When looping through dictionaries, the key and corresponding value can be retrieved at the same time using the `iteritems()` method. <br>

   ```
   knights = {'gallahad': 'the pure', 'robin': 'the brave'}
   for k, v in knights.iteritems():
       print k, v
   ```


### 参考材料

1. [The Python Tutorial](https://docs.python.org/2/tutorial/)
1. [Python syntax and semantics](https://en.wikipedia.org/wiki/Python_syntax_and_semantics)
