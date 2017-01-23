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

1. `python -m module [arg] ...` 运行　`module` 源文件， `[arg]` 为调用 `module` 所需要的参数。确保 `module` 中存在判断其是否是入口函数的逻辑： <br>

   ```python
   if __name__ == "__main__":
       import sys
       main(int(sys.argv[1]))
   ```
   If the module is imported, the code is not run: `import module`。
1. `python -c command [arg] ...` 运行 `command` 中的执行语句。Since Python statements often contain spaces or other characters that are special to the shell, it is usually advised to *quote* `command` in its entirety with single quotes. <br>

   ```
   python -c 'import sys; print sys.argv; print "Hello world"'
   ```


#### 源文件编码

在 Python 源文件中可以使用不同于 ASCII 的编码，最好的方法是在 `#!` 行后面加上一个更特殊的注释行来定义源文件编码：

```python
# -*- coding: ascii -*-
# -*- coding: utf_8 -*-
# -*- coding: utf_16 -*-
```

### Python 语法

1. 如果不希望前缀为 `\` 的字符被解释为特殊字符，则可以通过在第一个引号前添加 `r` 来使用原始字符串：<br>

   ```python
   print 'C:\some\name'  # here \n means newline!
   print r'C:\some\name' # note the r before the quote
   ```
1. 字符串文字可以跨越多行, 一种方法是使用三重引号："""...""" 或 '''...'''，行的结尾会自动包含在字符串中，但可以通过在行末添加 `\` 来防止这种情况发生。下面的例子：<br>

   ```python
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

   ```python
   squares = [1, 4, 9, 16, 25]
   squares[:]
   ```
1. A trailing comma avoids the newline after the output: <br>

   ```python
   a, b = 0, 1
   while b < 1000:
       print b,
       a, b = b, a+b
   ```
1. Loop statements may have an `else` clause; it is executed when the loop terminates through exhaustion of the list (with `for`) or when the condition becomes false (with `while`), but not when the loop is terminated by a `break` statement. This is exemplified by the following loop, which searches for prime numbers: <br>

   ```python
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

   ```python
   i = 5

   def f(arg=i):
       print arg

   i = 6
   f()
   ```
   will print 5.
1. **Important warning:** The default value is evaluated only once. This makes a difference when the default is a mutable object such as a list, dictionary, or instances of most classes. For example, the following function accumulates the arguments passed to it on subsequent calls: <br>

   ```python
   def f(a, L=[]):
       L.append(a)
       return L

   print f(1)
   print f(2)
   print f(3)
   ```
   This will print <br>

   ```python
   [1]
   [1, 2]
   [1, 2, 3]
   ```
   If you don’t want the default to be shared between subsequent calls, you can write the function like this instead: <br>

   ```python
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

   ```python
   def parrot(voltage, state='a stiff', action='voom', type='Norwegian Blue'):
       print "-- This parrot wouldn't", action,
       print "if you put", voltage, "volts through it."
       print "-- Lovely plumage, the", type
       print "-- It's", state, "!"
   ```
   This function can be called in any of the following ways:<br>

   ```python
   parrot(1000)                                       # 1 positional argument
   parrot(voltage=1000)                               # 1 keyword argument
   parrot(voltage=1000000, action='VOOOOOM')          # 2 keyword arguments
   parrot(action='VOOOOOM', voltage=1000000)          # 2 keyword arguments
   parrot('a million', 'bereft of life', 'jump')      # 3 positional arguments
   parrot('a thousand', state='pushing up daisies')   # 1 positional, 1 keyword
   ```
   but all the following calls would be invalid:<br>

   ```python
   parrot()                     # required argument missing
   parrot(voltage=5.0, 'dead')  # non-keyword argument after a keyword argument
   parrot(110, voltage=220)     # duplicate value for the same argument
   parrot(vol=5.0)              # unknown keyword argument
   ```
   In a function call, keyword arguments must follow positional arguments. All the keyword arguments passed must match one of the arguments accepted by the function, and their order is not important. This also includes non-optional arguments.
1. When a final formal parameter of the form `**name` is present, it receives a dictionary containing all keyword arguments except for those corresponding to a formal parameter. This may be combined with a formal parameter of the form `*name` which receives a tuple containing the positional arguments beyond the formal parameter list. `*name` must occur before `**name`. <br>

   ```python
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

   ```python
   cheeseshop("Limburger", "It's very runny, sir.",
              "It's really very, VERY runny, sir.",
              shopkeeper='Michael Palin',
              client="John Cleese",
              sketch="Cheese Shop Sketch")
   ```
   Note that the list of keyword argument names is created by sorting the result of the keywords dictionary’s `keys()` method before printing its contents; if this is not done, the order in which the arguments are printed is undefined.
1. The reverse situation occurs when the arguments are already in a list or tuple but need to be unpacked for a function call requiring separate positional arguments. Write the function call with the `*`-operator to unpack the arguments out of a list or tuple: <br>

   ```python
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

   ```python
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

    ```python
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

   ```python
   squares = [x**2 for x in range(10)]
   ```
   This is also equivalent to `squares = map(lambda x: x**2, range(10))`, but it’s more concise and readable.<br>

   ```python
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

   ```python
   a = [1, 2, 3]
   b = a
   c = a[:]
   del a[:]
   del a
   ```
1. Tuples are immutable, and usually contain a heterogeneous sequence of elements that are accessed via unpacking or indexing. Lists are mutable, and their elements are usually homogeneous and are accessed by iterating over the list.
1. A tuple with one item is constructed by following a value with a comma (it is not sufficient to enclose a single value in parentheses). Ugly, but effective. For example: <br>

   ```python
   singleton = 'hello',
   singleton = ('hello')
   singleton = ('hello',)
   ```
1. The statement `t = 12345, 54321, 'hello!'` is an example of *tuple packing*: the values `12345`, `54321` and `'hello!'` are packed together in a tuple. The reverse operation is also possible: `x, y, z = t` This is called, appropriately enough, *sequence unpacking* and works for any sequence on the right-hand side. Note that multiple assignment is really just a combination of tuple packing and sequence unpacking.
1. Unlike sequences, which are indexed by a range of numbers, dictionaries are indexed by keys, which can be any immutable type; strings and numbers can always be keys.
1. When looping through a sequence, the position index and corresponding value can be retrieved at the same time using the `enumerate()` function. <br>

   ```python
   for i, v in enumerate(['tic', 'tac', 'toe']):
       print i, v
   ```
   To loop over two or more sequences at the same time, the entries can be paired with the zip() function. <br>

   ```python
   questions = ['name', 'quest', 'favorite color']
   answers = ['lancelot', 'the holy grail', 'blue']
   for q, a in zip(questions, answers):
       print 'What is your {0}?  It is {1}.'.format(q, a)
   ```
   To loop over a sequence in reverse, first specify the sequence in a forward direction and then call the `reversed()` function. <br>

   ```python
   for i in reversed(xrange(1,10,2)):
       print i,
   ```
   To loop over a sequence in sorted order, use the `sorted()` function which returns a new sorted list while leaving the source unaltered. <br>

   ```python
   basket = ['apple', 'orange', 'apple', 'pear', 'orange', 'banana']
   for f in sorted(set(basket)):
       print f,
   ```
   When looping through dictionaries, the key and corresponding value can be retrieved at the same time using the `iteritems()` method. <br>

   ```python
   knights = {'gallahad': 'the pure', 'robin': 'the brave'}
   for k, v in knights.iteritems():
       print k, v
   ```
1. The built-in function `dir()` is used to find out which names a module defines. It returns a sorted list of strings: <br>

   ```python
   import sys
   dir(sys)
   ```
   Without arguments, `dir()` lists all types of names: variables, modules, functions, etc you have defined currently. `dir()` does not list the names of built-in functions and variables. If you want a list of those, they are defined in the standard module `__builtin__`: <br>

   ```python
   import __builtin__
   dir(__builtin__)
   ```
1. When importing the package, Python searches through the directories on `sys.path` looking for the package subdirectory. The `__init__.py` files are required to make Python treat the directories as containing packages; this is done to prevent directories with a common name, such as `string`, from unintentionally hiding valid modules that occur later on the module search path. In the simplest case, `__init__.py` can just be an empty file, but it can also execute initialization code for the package or set the `__all__` variable.
1. The `str()` function is meant to return representations of values which are fairly human-readable, while `repr()` is meant to generate representations which can be read by the interpreter. <br>

   ```python
   s = 'Hello world'
   str(s)
   repr(s)
   hello = 'Hello world \n'
   print str(s)
   # The repr() of a string adds string quotes and backslashes
   print repr(s)
   ```
1. A number in the brackets refers to the position of the object passed into the str.format() method: <br>

   ```python
   print '{0} and {1}'.format('spam', 'eggs')
   print '{1} and {0}'.format('spam', 'eggs')
   ```
   If keyword arguments are used in the `str.format()` method, their values are referred to by using the name of the argument.<br>

   ```python
   print 'This {food} is {adjective}.'.format(
         food='spam', adjective='absolutely horrible')
   ```
   Positional and keyword arguments can be arbitrarily combined: <br>

   ```python
   print 'The story of {0}, {1}, and {other}.'.format('Bill', 'Manfred',
                                                      other='Georg')
   ```
   Passing an integer after the `:` will cause that field to be a minimum number of characters wide. This is useful for making tables pretty. <br>

   ```python
   table = {'Sjoerd': 4127, 'Jack': 4098, 'Dcab': 7678}
   for name, phone in table.items():
       print '{0:10} ==> {1:10d}'.format(name, phone)
   ```
1. `f = open('workfile', 'w'); f.write(string)` writes the contents of *string* to the file, returning None. To write something other than a string, it needs to be converted to a string first: <br>

   ```python
   value = ('the answer', 42)
   s = str(value)
   f.write(s)
   ```
   `f.tell()` returns an integer giving the file object’s current position in the file, measured in bytes from the beginning of the file. To change the file object’s position, use `f.seek(offset, from_what)`. The position is computed from adding `offset` to a reference point selected by the `from_what` argument. A `from_what` value of 0 measures from the beginning of the file, 1 uses the current file position, and 2 uses the end of the file as the reference point. <br>

   ```python
   f = open('workfile', 'r+')
   f.write('0123456789abcdef')
   f.seek(5)      # Go to the 6th byte in the file
   f.read(1)
   f.seek(-3, 2)  # Go to the 3rd byte before the end
   f.read(1)
   ```
   When you’re done with a file, call `f.close()` to close it and free up any system resources taken up by the open file.
1. If you have an object `x`, you can view its JSON string representation with a simple line of code: <br>

   ```python
   import json
   json.dumps([1, 'simple', 'list'])
   ```
   Another variant of the `dumps()` function, called `dump()`, simply serializes the object to a file. So if `f` is a file object opened for writing, we can do this: <br>

   ```python
   json.dump(x, f)
   ```
   To decode the object again, if `f` is a file object which has been opened for reading: <br>

   ```python
   x = json.load(f)
   ```
1. An except clause may name multiple exceptions as a parenthesized tuple, for example: <br>

   ```python
   except (RuntimeError, TypeError, NameError):
       pass
   ```
   Note that the parentheses around this tuple are required, because `except ValueError, e:` was the syntax used for what is normally written as `except ValueError as e:` in modern Python. The old syntax is still supported for backwards compatibility. This means `except RuntimeError, TypeError` is not equivalent to `except (RuntimeError, TypeError):` but to `except RuntimeError as TypeError:` which is not what you want. The `try ... except` statement has an optional `else` clause, which, when present, must follow all `except` clauses. It is useful for code that must be executed if the `try` clause does not raise an exception. For example: <br>

   ```python
   for arg in sys.argv[1:]:
       try:
           f = open(arg, 'r')
       except IOError:
           print 'cannot open', arg
       else:
           print arg, 'has', len(f.readlines()), 'lines'
           f.close()
   ```
   One may instantiate an exception first before raising it and add any attributes to it as desired. <br>

   ```python
   try:
       raise Exception('spam', 'eggs')
   except Exception as inst:
       print type(inst)     # the exception instance
       print inst.args      # arguments stored in .args
       print inst           # __str__ allows args to be printed directly
       x, y = inst.args
       print 'x =', x
       print 'y =', y
   ```
   A `finally` clause is always executed before leaving the `try` statement, whether an exception has occurred or not. When an exception has occurred in the `try` clause and has not been handled by an `except` clause (or it has occurred in an `except` or `else` clause), it is re-raised after the `finally` clause has been executed. <br>

   ```python
   def divide(x, y):
       try:
           result = x / y
       except ZeroDivisionError:
           print "division by zero!"
       else:
           print "result is", result
       finally:
           print "executing finally clause"
   divide(1, 2)
   divide(1, 0)
   divide("1", "0")
   ```
   In real world applications, the `finally` clause is useful for releasing external resources (such as files or network connections), regardless of whether the use of the resource was successful. The `with` statement allows objects like files to be used in a way that ensures they are always cleaned up promptly and correctly. <br>

   ```python
   with open("myfile.txt") as f:
       for line in f:
           print line,
   ```
   After the statement is executed, the file `f` is always closed, even if a problem was encountered while processing the lines. Other objects which provide predefined clean-up actions will indicate this in their documentation.
1. The `heapq` module provides functions for implementing heaps based on regular lists. The lowest valued entry is always kept at position zero. This is useful for applications which repeatedly access the smallest element but do not want to run a full list sort: <br>

   ```python
   from heapq import heapify, heappop, heappush
   data = [1, 3, 5, 7, 9, 2, 4, 6, 8, 0]
   heapify(data)                      # rearrange the list into heap order
   heappush(data, -5)                 # add a new entry
   [heappop(data) for i in range(3)]  # fetch the three smallest entries
   ```


### 参考材料

1. [The Python Tutorial](https://docs.python.org/2/tutorial/)
1. [Python syntax and semantics](https://en.wikipedia.org/wiki/Python_syntax_and_semantics)
