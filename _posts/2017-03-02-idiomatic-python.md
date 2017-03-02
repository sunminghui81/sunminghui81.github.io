---
layout: post
keywords: idomatic Python
description: 记录 Python coding tips
title: "Idiomatic Python"
categories: [python]
tags: [idiomatic python]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### Working With Data

#### Use the 'default' parameter of dict.get() to provide default values

Often overlooked in the `get()` definition is the default parameter. Without using default (or the `collections.defaultdict` class), your code will be littered with confusing if statements. Remember, strive for clarity.

```python
log_severity = configuration.get('severity', log.Info)
```

#### Use Context Managers to ensure resources are properly managed

Context managers (objects meant to be used with the `with` statement) can make resource management both safer and more explicit. The canonical example is file IO.

```python
with open(path_to_file, 'r') as file_handle:
    for line in file_handle:
        if some_function_that_throws_exceptions(line):
            # do something
# No need to explicitly call 'close'. Handled by the File context manager
```

#### Learn the contents of the itertools module

The documentation for `itertools` has a [Recipes section](https://docs.python.org/3/library/itertools.html#itertools-recipes) that provides idiomatic implementations of common functional programming constructs, all created using the `itertools` module. For some reason, a vanishingly small number of Python developers seem to be aware of the 'Recipes' section and, indeed, the itertools module in general (hidden gems in the Python documentation is actually a recurring theme).

### Control Structures

#### Avoid placing conditional branch on the same line as the colon

Using indentation to indicate scope (like you already do everywhere else in Python) makes it easy to determine what will be executed as part of a conditional statement.

#### Avoid repeating variable name in compound if Statement

When one wants to check against a number of values, using a temporary collection makes the intention clear.

*Bad*

```python
if name == 'Tom' or name == 'Dick' or name == 'Harry':
    is_generic_name = True
```

*Idiomatic*

```python
if name in ('Tom', 'Dick', 'Harry'):
    is_generic_name = True
```

#### Use list comprehensions to create lists that are subsets of existing data

List comprehensions, when used judiciously, increase clarity in code that builds a list from existing data. Especially when data is both checked for some condition and transformed in some way, list comprehensions make it clear what's happening. There are also (usually) performance benefits to using list comprehensions (or alternately, set comprehensions) due to optimizations in the CPython interpreter.

### 参考材料

1. [Writing Idiomatic Python](https://jeffknupp.com/blog/2012/10/04/writing-idiomatic-python/)
