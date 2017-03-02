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


What we commonly refer to as *variables* in Python are more properly called *names*. Likewise, *assignment* is really the binding of a *name* to an *object*.

Let's look at a block of Python code:

```python
some_guy = 'Fred'
some_guy = 'George'
```

### Binding names to objects

On line 1, we create a *binding* between a *name* `some_guy`, and a string *object* containing `'Fred'`. In the context of program execution, the environment is altered; a binding of the name `some_guy` to a string object is created in the `scope of the block` where the statement occurred. When we later say `some_guy = 'George'`, the string object containing `'Fred'` is unaffected. We've just changed the binding of the name `some_guy`. **We haven't, however, changed either the 'Fred' or 'George' string objects.**

It would be incorrect to say that "mutable objects can change and immutable ones can't", however. Consider the following:

```python
first_names = ['Fred', 'George', 'Bill']
last_names = ['Smith', 'Jones', 'Williams']
name_tuple = (first_names, last_names)
print name_tuple

first_names.append('Igor')
print name_tuple
```

Tuples in Python are immutable. We can't change the tuple object `name_tuple` is bound to. But immutable containers may contain references to mutable objects like lists. Therefore, even though the tuple object is immutable, it "changes" when `'Igor'` is appended to the list object `first_names` is bound to.

### Function calls

When you pass a *variable* to a function, python passes the reference to the object to which the variable refers. This parameter-passing scheme is called "call-by-object-reference". For example, when the function `foo(bar)` is called, a binding within the scope of `foo` to the object the argument` bar` is bound to is created. If `bar` refers to a mutable object and `foo` changes its value, then these changes will be visible outside of the scope of the function.

```python
def foo(bar):
    bar.append(42)
    print bar

answer_list = []
foo(answer_list)
print answer_list
```

On the other hand, if `bar` refers to an immutable object, the most that `foo` can do is create a name `bar` in its local namespace and bind it to some other object.

```python
def foo(bar):
    bar = 'new value'
    print bar

answer_list = 'old value'
foo(answer_list)
print answer_list
```

**In Python a variable is not an alias for a location in memory. Rather, it is simply a binding to a Python object.**

Another example:

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

### References

1. [Is Python call-by-value or call-by-reference? Neither.](https://jeffknupp.com/blog/2012/11/13/is-python-callbyvalue-or-callbyreference-neither/)