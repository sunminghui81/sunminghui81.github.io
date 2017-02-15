---
layout: post
keywords: SciPy, NumPy
description: NumPy 和 SciPy 学习总结
title: "NumPy 和 SciPy 学习笔记"
categories: [机器学习]
tags: [机器学习, NumPy, SciPy]
group: archive
icon: github
---
{% include mathsyouth/setup %}


### NumPy 关键知识点

1. Arithmetic operators on arrays apply elementwise. <br>

   ```python
   import numpy as np
   a = np.array( [20,30,40,50] )
   b = np.arange( 4 )
   c = a - b
   b**2
   ```
1. The matrix product can be performed using the dot function or method: <br>

   ```python
   import numpy as np
   A = np.array( [[1,1], [0,1]] )
   B = np.array( [[2,0], [3,4]] )
   A*B  # elementwise product
   A.dot(B)  # matrix product
   np.dot(A, B)
   ```
1. By specifying the `axis` parameter you can apply an operation along the specified axis of an array: <br>

   ```python
   import numpy as np
   b = np.arange(12).reshape(3,4)
   b.sum(axis=0)  # sum of each column
   b.min(axis=1)  # min of each row
   b.cumsum(axis=1)  # cumulative sum along each row
   ```
1. Multidimensional arrays can have one index per axis. When fewer indices are provided than the number of axes, the missing indices are considered complete slices: <br>

   ```python
   def f(x, y):
       return 10*x + y
   b = np.fromfunction(f, (5,4), dtype=int)
   b[-1]  # the last row. Equivalent to b[-1,:]
   ```
   The expression within brackets in `b[i]` is treated as an `i` followed by as many instances of `:` as needed to represent the remaining axes. NumPy also allows you to write this using dots as `b[i,...]`.
1. The dots (`...`) represent as many colons as needed to produce a complete indexing tuple. For example, if `x` is a rank 5 array (i.e., it has 5 axes), then
   * `x[1,2,...]` is equivalent to `x[1,2,:,:,:]`,
   * `x[...,3]` to `x[:,:,:,:,3]`,
   * `x[4,...,5,:]` to `x[4,:,:,5,:]`.


### 参考文献

1. [NumPy Quickstart](https://docs.scipy.org/doc/numpy-dev/user/quickstart.html)
