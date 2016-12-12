---
layout: post
keywords: global variables
description: Explanation on global variables in Python
title: "Global variables in Python"
categories: [python]
tags: [variables, global variables]
group: archive
icon: compass
---
{% include mathsyouth/setup %}

Variables declared outside the function can be referenced within the function:

```Python
def addx(y):
    return x + y

def main():
    x = 5
    print addx(10)

if __name__ == "__main__":
    main()
```

But these "global" variables cannot be modified within the function, unless declared global in the function. This doesn't work:

```Python
def setx(y):
    x = y
    print "x is %d " % x

def main():
    x = 5
    print setx(10)
    print x

if __name__ == "__main__":
    main()
```

This works:

```Python
def setx(y):
    global x
    x = y
    print "x is %d " % x

def main():
    x = 5
    print setx(10)
    print x

if __name__ == "__main__":
    main()
```
