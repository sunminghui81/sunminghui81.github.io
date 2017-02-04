---
layout: post
keywords: parameters, function, method
description: Explanation on parameters of python functions (or class methods) in Python
title: "Parameters of functions in Python"
categories: [python]
tags: [parameters, function, method]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


All variables in Python hold references to objects, and these references are passed to functions. This parameter-passing scheme is called "Call by object reference." An object reference means a name, and the passed reference is an "alias", i.e. a copy of the reference to the same object, just like in C/C++. When you pass a variable to a function, python passes the reference to the object to which the variable refers (the value). Not the variable itself.

If the value passed in a function is immutable, the function does not modify the caller's variable. If the value is mutable, the function may modify the caller's variable in-place:

```python
def try_to_modify(x, y, z):
    x = 23
    y.append(42)
    z = [99]  # new reference
    print x
    print y
    print z
def main():
    a = 77    # immutable variable
    b = [99]  # mutable variable
    c = [28]
    try_to_modify(a, b, c)
    print a
    print b
    print c
if __name__ == "__main__":
    main()
```

In Python, non-innermost-local and not-declared-global accessible names are all aliases.