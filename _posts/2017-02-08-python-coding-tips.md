---
layout: post
keywords: coding tips
description: 记录 Python coding tips
title: "Python coding tips"
categories: [python]
tags: [python coding tips]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### Python coding tips

#### Using Enumerate() Function

The `enumerate()` function adds a counter to an iterable object. An iterable is an object that has a `__iter__` method which returns an iterator.

A typical example of the `enumerate()` function is to loop over a list and keep track of the index. For this, we could use a count variable. But Python gives us a nicer syntax for this using the enumerate() function.

```python
subjects = ('Python', 'Coding', 'Tips')

for i, subject in enumerate(subjects):
    print(i, subject)
```

#### Conditional Expressions

```python
# make number always be odd
number = count if count % 2 else count - 1
# Call a function if the object is not None.
data = data.load() if data is not None else 'Dummy'
print("Data collected is ", data)

def small(a, b, c):
	return a if a <= b and a <= c else (b if b <= a and b <= c else c)

print(small(1, 0, 1))
print(small(1, 2, 2))
print(small(2, 2, 3))
print(small(5, 4, 3))
```

#### In-Place Swapping Of Two Numbers

The assignment on the right seeds a new tuple. While the left one instantly unpacks that (unreferenced) tuple to the names.

```python
x, y = 10, 20
print(x, y)

x, y = y, x
print(x, y)
```

#### Work With Multi-Line Strings

```python
multiStr= ("select * from multi_row "
           "where row_id < 5 "
           "order by age")

print(multiStr)
```

#### Print The File Path Of Imported Modules

If you want to know the absolute location of modules imported in your code, then use the below trick.

```python
import threading
import socket

print(threading)
print(socket)
```

#### Use Interactive `_` Operator

In the Python console, whenever we test an expression or call a function, the result dispatches to a temporary name `_`.

```python
>>> 2 + 1
3
>>> _
3
>>> print _
3
```

#### Debugging Scripts

We can set breakpoints in our Python script with the help of the `pdb` module.

```python
import pdb

pdb.set_trace()
```

We can specify `pdb.set_trace()` anywhere in the script and set a breakpoint there. It’s extremely convenient.

#### File Sharing

In Python 2.X

```shell
python -m SimpleHTTPServer
```

In Python 3.X

```shell
python3 -m http.server
```

Above commands would start a server on the default port i.e. `8000`. You can also use a custom port by passing it as the last argument to the above commands.

#### Simplify If Statement

```python
if m in [1,3,5,7]:

if m==1 or m==3 or m==5 or m==7:
```

#### Detect Python Version At Runtime

Sometimes we may not want to execute our program if the Python engine currently running is less than the supported version.

```python
import sys

# Detect the Python version currently in use.
if not hasattr(sys, "hexversion") or sys.hexversion != 50660080:
    print("Sorry, you aren't running on Python 3.5\n")
    print("Please upgrade to 3.5.\n")
    sys.exit(1)

# Print Python version in a readable format.
print("Current Python version: ", sys.version)
```

#### Unpack Function Arguments Using Splat Operator

```python
def test(x, y, z):
	print x, y, z

testDict = {'x': 1, 'y': 2, 'z': 3}
testList = [10, 20, 30]

test(*testDict)
test(**testDict)
test(*testList)
```

#### Use A Dictionary To Store A Switch

```python
stdcalc = {
	'sum': lambda x, y: x + y,
	'subtract': lambda x, y: x - y
}

print(stdcalc['sum'](9,3))
print(stdcalc['subtract'](9,3))
```

#### Calculate The Factorial Of Any Number In One Line

In Python 2.X

```python
result = (lambda k: reduce(int.__mul__, range(1,k+1),1))(3)
print(result)
```

In Python 3.X

```python
import functools
result = (lambda k: functools.reduce(int.__mul__, range(1,k+1),1))(3)
print(result)
```

#### Find The Most Frequent Value In A List

```python
test = [1,2,3,4,2,2,3,1,4,4,4]
print(max(set(test), key=test.count))
```

#### Reset Recursion Limit

Python restricts recursion limit to 1000. We can though reset its value.

```python
import sys

x=1001
print(sys.getrecursionlimit())

sys.setrecursionlimit(x)
print(sys.getrecursionlimit())
```

#### Check The Memory Usage Of An Object

In Python 2.7, a 32-bit integer consumes 24-bytes whereas it utilizes 28-bytes in Python 3.5. To verify the memory usage, we can call the `getsizeof` method.

```python
import sys
x=1
print(sys.getsizeof(x))
```

#### Create A Dictionary From Two Related Sequences

```python
t1 = (1, 2, 3)
t2 = (10, 20, 30)

print(dict (zip(t1,t2)))
```

#### In Line Search For Multiple Prefixes In A String

```python
print("http://www.google.com".startswith(("http://", "https://")))
print("http://www.google.co.uk".endswith((".com", ".co.uk")))
```

#### Form A Unified List Without Using Any Loops

```python
import itertools

test = [[-1, -2], [30, 40], [25, 35]]
print(list(itertools.chain.from_iterable(test)))
```

#### Implement A True Switch-Case Statement In Python

```python
def xswitch(x):
	return xswitch._system_dict.get(x, None)

xswitch._system_dict = {'files': 10, 'folders': 5, 'devices': 2}

print(xswitch('default'))
print(xswitch('devices'))
```


### 参考材料

1. [Ten Essential Python Coding Tips For Beginners](http://www.techbeamers.com/top-10-python-coding-tips-for-beginners/)
1. [30 Essential Python Tips And Tricks For Programmers](http://www.techbeamers.com/essential-python-tips-tricks-programmers/)