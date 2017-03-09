---
layout: post
keywords: iterator, generator
description: Python 学习笔记
title: "Understanding iterator and generator"
categories: [python]
tags: [python, iterator, generator]
group: archive
icon: compass
---
{% include mathsyouth/setup %}


### Iterators and Iterables

An iterable is any object in Python which has an `__iter__` or a `__getitem__` method defined which returns an iterator or can take indexes. An iterator is any object in Python which has a `next` in Python 2.X or `__next__` method in Python 3.X defined.

The built-in function `iter` takes an iterable object and returns an iterator.

```python
x = iter([1, 2, 3])
next(x)
# Python 2.X
x.next()
# Python 3.X
x.__next__()
```

Iterators are implemented as classes. Here is an iterator that works like built-in `xrange` function.

```python
class yrange:
    def __init__(self, n):
        self.i = 0
        self.n = n

    def __iter__(self):
        return self

    def next(self):
        if self.i < self.n:
            i = self.i
            self.i += 1
            return i
        else:
            raise StopIteration()
```

The `__iter__` method is what makes an object iterable. Behind the scenes, the `iter` function calls `__iter__` method on the given object. The return value of `__iter__` is an iterator. It should have a `next` method and `raise StopIteration` when there are no more elements. Many built-in functions accept iterators as arguments.

```python
list(yrange(5))
sum(yrange(5))
```

###  Generator Functions

The word “generator” means the genearted object and “generator function” means the function that generates it. When a generator function is called, it returns a generator object without even beginning execution of the function. When `next` method is called for the first time, the function starts executing until it reaches `yield` statement. The yielded value is returned by the `next` call.
A generator is a one-time operation. You can iterate over the generated data once, but if you want to do it again, you have to call the generator function again.

```python
def foo():
    print "begin"
    for i in range(3):
        print "before yield ", i
        yield i
        print "after yield ", i
    print "end"

f = foo()
f.next()
f.next()
```

A Python generator is a function which returns a generator iterator by calling `yield`. `yield` may be called with a value, in which case that value is treated as the "generated" value. The next time `next()` is called on the generator iterator (i.e. in the next step in a `for` loop, for example), the generator resumes execution **from where it called yield**, not from the beginning of the function. All of the state, like the values of local variables, is recovered and the generator continues to execute until the next call to `yield`.

When we call a normal Python function, execution starts at function's first line and continues until a `return` statement, `exception`, or the end of the function (which is seen as an implicit `return None`) is encountered. Once a function returns control to its caller, any work done by the function and stored in local variables is lost. A new call to the function creates everything from scratch.

`return` implies that the function is returning control of execution to the point where the function was called. `yield` however, implies that the transfer of control is temporary and voluntary, and our function expects to regain it in the future. When a generator function calls `yield`, the "state" of the generator function is frozen; the values of all variables are saved and the next line of code to be executed is recorded until `next()` is called again. Once it is, the generator function simply resumes where it left off. If `next()` is never called again, the state recorded during the yield call is (eventually) discarded. `yield` both passes a value to whoever called `next()`, and saves the "state" of the generator function.

Create the infinite sequence:

```python
import math

def primes_sum():
    total = 2
    for next_prime in get_primes(3):
        if next_prime < 200000:
            total += next_prime
        else:
            print(total)
            return

def get_primes(number):
    while True:
        if is_prime(number):
            yield number
        number += 1

def is_prime(number):
    if number > 1:
        if number == 2:
            return True
        if number % 2 == 0:
            return False
        for current in range(3, int(math.sqrt(number) + 1), 2):
            if number % current == 0:
                return False
        return True
    return False
```

#### Coroutines

You can "send" values to a generator using the generator's `send` method. `send` both sends a value to the generator and returns the value yielded by the generator. When you're using send to "start" a generator (that is, execute the code from the first line of the generator function up to the first `yield` statement), you must send `None` (or call `.next()`). This makes sense, since by definition the generator hasn't gotten to the first `yield` statement yet, so if we sent a real value there would be nothing to "receive" it. Once the generator is started, we can send values.

Coroutines are consumers of data. Coroutines are not related to iteration.

**Note: The following code works only in Python 3.X, not Python 2.X.**

```python
def gen_fn():
    result = yield 1
    print('result of yield: {}'.format(result))  # 'joe'
    result2 = yield 2
    print('result of 2nd yield: {}'.format(result2))  # 'bob'
    return 'we are done'

g = gen_fn()
# or x = g.next()
x = g.send(None)
print(x)  # should be 1
y = g.send('joe')
print(y)  # should be 2
try:
    g.send('bob')
except StopIteration as e:
	print(e)  # we are done
```

`StopIteration` raised on generator exit. Return value (if any) passed with exception.

Another example:

```python
import random

def get_data():
    """Return 3 random integers between 0 and 9"""
    return random.sample(range(10), 3)

def consume():
    """Displays a running average across lists of integers sent to it"""
    running_sum = 0
    data_items_seen = 0
    while True:
        data = yield
        data_items_seen += len(data)
        running_sum += sum(data)
        print('The running average is {}'.format(
              running_sum / float(data_items_seen)))

def produce(consumer):
    """Produces a set of values and forwards them to the pre-defined consumer
    function"""
    while True:
        data = get_data()
        print('Produced {}'.format(data))
        consumer.send(data)
        yield

if __name__ == '__main__':
    consumer = consume()
    consumer.send(None)
    producer = produce(consumer)
    for _ in range(10):
        print('Producing...')
        next(producer)
```

For more details on coroutines, please refer to [A Curious Course on Coroutines and Concurrency](http://www.dabeaz.com/coroutines/).

#### Generator Delegation

`yield from` lets one generator delegate to another. To see how, let us return to the simple generator example:

```python
def gen_fn():
    result = yield 1
    print('result of yield: {}'.format(result))  # 'joe'
    result2 = yield 2
    print('result of 2nd yield: {}'.format(result2))  # 'bob'
    return 'we are done'
```

To call this generator from another generator, delegate to it with `yield from`:

```python
# Generator function:
def caller_fn():
    gen = gen_fn()
    rv = yield from gen
    print('return value of yield-from: {}'.format(rv))  # 'we are done'
    return 'caller_fn is done!'

# Make a generator from the generator function
# caller acts as if it were gen, the generator it is delegating to
caller = caller_fn()
x = caller.send(None)
print(x)  # should be 1
y = caller.send('joe')
print(y)  # should be 2
try:
    caller.send('bob')
except StopIteration as e:
    print(e)   #'caller_fn is done!'
```

### Generator Expression

Generator expressions are generator version of list comprehensions. They look like list comprehensions, but returns a generator back instead of a list.

```python
a = (x*x for x in range(10))
print a
print sum(a)
```

When there is only one argument to the calling function, the parenthesis around generator expression can be omitted.

```python
print sum(x*x for x in range(10))
```

Let's say we want to find first 10 (or any n) pythogorian triplets. A triplet `(x, y, z)` is called pythogorian triplet if `x*x + y*y == z*z`.

```python
def integers():
    """Infinite sequence of integers."""
    i = 1
    while True:
        yield i
        i += 1

def take(n, seq):
    """Returns first n values from the given sequence."""
    seq = iter(seq)
    result = []
    try:
        for i in range(n):
            result.append(seq.next())
    except StopIteration:
        pass
    return result

pyt = ((x, y, z) for z in integers() for y in xrange(1, z)
       for x in range(1, y) if x*x + y*y == z*z)
take(10, pyt)
```

### 参考材料

1. [Improve Your Python: 'yield' and Generators Explained](https://jeffknupp.com/blog/2013/04/07/improve-your-python-yield-and-generators-explained/)
1. [Generators](http://book.pythontips.com/en/latest/generators.html)
1. [Iterators & Generators](http://anandology.com/python-practice-book/iterators.html)
1. [Generator Tricks for Systems Programmers](http://www.dabeaz.com/generators/)
1. [Generator Tricks for Systems Programmers - Version 2.0](http://www.dabeaz.com/generators-uk/)
1. [A Curious Course on Coroutines and Concurrency](http://www.dabeaz.com/coroutines/)
1. [Generators: The Final Frontier](http://www.dabeaz.com/finalgenerator/)
1. [PEP 234 -- Iterators](https://www.python.org/dev/peps/pep-0234/)
1. [Understanding Python's "for" statement](http://effbot.org/zone/python-for-statement.htm)