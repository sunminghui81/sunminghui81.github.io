---
layout: post
keywords: Python
description: Python lint 错误信息
title: Python Lint 错误
categories: [Python]
tags: [安全编码]
group: archive
icon: google
---

### E1138 unsupported-delete-operation
对于一个类如果其具有object.delitem(self, key)实现，那么在进行类对象操作时，就可以使用del关键字执行调用__delitem__实现的操作。
例如：
```python

class Foo:
    def __init__(self, numbers):
        self.numbers = numbers
    
    def __delitem__(self, index):
        self.numbers.pop(index)

foo = Foo([1,2,3])
del foo[0]
print(foo.numbers)
------------------
[2,3]
````

### E1139 invalid-metaclass
对于一个类在其创建时，指定metaclass时，指定的元类必须是有效的。  
元类有效的意思是：元类本身是type的子类，其作用是跟type一样用于创建class。  
在解析器内部，一般的通过class关键字定义的类，是会转化为type进行创建的。  
type创建类的方法：
```python
def fn(self, name='world'): 
    print('Hello, %s.' % name)

Hello = type('Hello', (object,), dict(hello=fn))
Hello().hello("sun")
-----------
Hello, sun
```  

type()的三个参数分别是：类的名称，父类的集合tuple，这个类的方法名和对应的方法实现绑定操作。

那么类在定义时如果指定metaclass，意思就是这个类的创建是使用这个指定的metaclass（type子类）来进行创建。  
> 1. 类的多重继承会继承这个metaclass，即认为这些类都是指定了metaclass的
> 2. 所有指定metaclass的类，都是由这个metaclass的__new__()来创建
> 3. 一般元类的命名最后都是以\*Metaclass结尾的

例如下面这个例子，给User这个类指定元类，并且在元类中为User这个类在创建时，增加一个add方法：
```python
class UserMetaclass(type):
    def __new__(cls, name, bases, attrs):
        attrs['add'] = lambda self, value: self.append(value)
        return type.__new__(cls, name, bases, attrs)

class User(list, metaclass=UserMetaclass):
    def __init__(self, users):
        for u in users:
            self.append(u)

u = User(["sun", "ming", "hui"])
u.add("Julien")
print(u)
----------------
['sun', 'ming', 'hui', 'Julien']

```

### E1140 ubhashable dict key
dict类型中元素的key值必须是hashable的，具体hashable的含义是：值对象在生命周期内是不变的，并且具备相等比较能力。
> 1. 值对象具有 __hash__()方法
> 2. 值对象具有 __eq__()方法 或者 __cmp__()方法
> 3. 基础值类型中，list、dict、set都是不可hash的
> 4. 没有实现hashable对象方法的类对象，也是unhashable的



### 参考
1. [使用元类](https://www.liaoxuefeng.com/wiki/1016959663602400/1017592449371072) --- 廖雪峰的官方网站



