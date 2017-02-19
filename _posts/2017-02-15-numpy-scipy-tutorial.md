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

1. For `array`, all arithmetic operations (`*`, `/`,` +`, `-` etc) are elementwise. <br>

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
1. The following three commands all return a modified array, but do not change the original array: <br>

   ```python
   a = np.floor(10*np.random.random((3,4)))
   a.ravel()  # returns the array, flattened
   a.reshape(6, 2)  # returns the array with a modified shape
   a.resize(6, 2)
   print a
   a.T  # returns the array, transposed
   a.transpose()
   a.T.shape
   ```
1. Several arrays can be stacked together along different axes: <br>

   ```python
   a = np.floor(10*np.random.random((2,2)))
   b = np.floor(10*np.random.random((2,2)))
   print a
   print b
   np.vstack((a, b))
   np.hstack((a, b))
   from numpy import newaxis
   a = np.array([4.,2.])
   b = np.array([2.,8.])
   a[:, newaxis]
   np.column_stack((a[:,newaxis], b[:,newaxis]))
   ```
1. Using `hsplit`, you can split an array along its horizontal axis, either by specifying the number of equally shaped arrays to return, or by specifying the columns after which the division should occur: <br>

   ```python
   a = np.floor(10*np.random.random((2,12)))
   np.hsplit(a, 3)  # Split a into 3
   np.hsplit(a,(3,4))   # Split a after the third and the fourth column
   ```
   `vsplit` splits along the vertical axis, and `array_split` allows one to specify along which axis to split.
1. The `view` method creates a new array object that looks at the same data. <br>

   ```python
   a = np.arange(12)
   b = a
   b is a
   c = a.view()
   c is a
   c.base is a
   c.shape = 2, 6  # a's shape doesn't change
   c[0, 4] = 555  # a's data changes
   print a
   a.shape = 3, 4
   s = a[:, 1:3]
   s[:] = 10  # s[:] is a view of s.
   print a
   ```
1. The `copy` method makes a complete copy of the array and its data. <br>

   ```python
   d = a.copy()   # a new array object with new data is created
   d is a
   d.base is a   # d doesn't share anything with a
   d[0,0] = 9999
   print a
   ```
1. Linear algebra. <br>

   ```python
   A = np.array([[1.0, 2.0], [3.0, 4.0]])
   B = np.linalg.inv(A)
   B.dot(A)
   U = np.eye(2) # unit 2x2 matrix; "eye" represents "I"
   J = np.array([[0.0, -1.0], [1.0, 0.0]])
   J.dot(J)
   np.dot(J, J)
   b = np.array([[5.], [7.]])
   np.linalg.solve(A, b)
   np.linalg.eig(A)
   ```
1. To change the dimensions of an array, you can omit one of the sizes which will then be deduced automatically: <br>

   ```python
   a = np.arange(30)
   a.shape = 2,-1,3  # -1 means "whatever is needed"
   print a.shape
   print a
   ```
1. You can treat one-dimensional arrays as either row or column vectors. `dot(A,v)` treats `v` as a column vector, while `dot(v,A)` treats `v` as a row vector. This can save you having to type a lot of transposes.
1. For `matrix`, maximum of two-dimensional. To hold three-dimensional data you need `array` or perhaps a Python list of `matrix`. Minimum of two-dimensional. You cannot have vectors. They must be cast as single-column or single-row matrices. The `array` is thus much more advisable to use.
1. Since `array` may be multidimensional, you must specify a slice for each dimension of the array: <br>

   ```python
   import numpy as np
   a = np.arange(1, 13).reshape(3, 4)
   # Use slicing to pull out the subarray consisting of the first 2 rows
   # and columns 1 and 2
   b = a[:2, 1:3]
   # A slice of an array is a view into the same data, so modifying it
   # will modify the original array.
   print a[0, 1]
   print b[0, 0]
   b[0, 0] = 77
   print a[0, 1]
   ```
1. You can also mix integer indexing with slice indexing. However, doing so will yield an array of lower rank than the original array. <br>

   ```python
   import numpy as np
   a = np.arange(1, 13).reshape(3, 4)
   # Mixing integer indexing with slices yields an array of lower rank,
   # while using only slices yields an array of the same rank as the
   # original array:
   row_r1 = a[1, :]    # Rank 1 view of the second row of a  
   row_r2 = a[1:2, :]  # Rank 2 view of the second row of a
   print row_r1, row_r1.shape  # Prints "[5 6 7 8] (4,)"
   print row_r2, row_r2.shape  # Prints "[[5 6 7 8]] (1, 4)"

   # We can make the same distinction when accessing columns of an array.
   ```
1. Integer array indexing allows you to construct arbitrary arrays using the data from another array. One useful trick with integer array indexing is selecting or mutating one element from each row of a matrix. Here is an example: <br>

   ```python
   import numpy as np
   a = np.arange(1, 13).reshape(4, 3)
   print a[[0, 1, 2], [0, 1, 0]]  # Prints "[1 5 7]"
   # The above example of integer array indexing is equivalent to this:
   print np.array([a[0, 0], a[1, 1], a[2, 0]])

   # Create an array of indices
   b = np.array([0, 2, 0, 1])
   # Select one element from each row of a using the indices in b
   print a[np.arange(4), b]  # Prints "[ 1  6  7 11]"
   # Mutate one element from each row of a using the indices in b
   a[np.arange(4), b] += 10
   print a
   ```
1. Broadcasting involves a few steps:
   1. The last dimension of each array is compared.
      * If the dimension lengths are equal, or one of the dimensions is of length 1, then we keep going.
      * If the dimension lengths aren’t equal, and none of the dimensions have length 1, then there’s an error.
   1. Continue checking dimensions until the shortest array is out of dimensions.
1. Broadcasting two arrays together follows these rules:
   * If the arrays do not have the same rank, prepend the shape of the lower rank array with 1s until both shapes have the same length.
   * The two arrays are said to be compatible in a dimension if they have the same size in the dimension, or if one of the arrays has size 1 in that dimension.
   * The arrays can be broadcast together if they are compatible in all dimensions.
   * After broadcasting, each array behaves as if it had shape equal to the elementwise maximum of shapes of the two input arrays.
   * In any dimension where one array had size 1 and the other array had size greater than 1, the first array behaves as if it were copied along that dimension
1. A broadcasting example. <br>

   ```python
   import numpy as np
   a = np.arange(5)
   b = a[:, np.newaxis]
   # The sum is
   # [[0, 1, 2, 3, 4],
   #   [1, 2, 3, 4, 5],
   #   [2, 3, 4, 5, 6],
   #   [3, 4, 5, 6, 7],
   #   [4, 5, 6, 7, 8]]
   print a + b
   c = a[np.newaxis, :]
   # The sum is array([[0, 2, 4, 6, 8]])
   print a + c
   d = np.random.rand(5, 3)
   # ValueError: operands could not be broadcast together with
   # shapes (5,3) (5,)/(1, 5)
   print a + d
   print c + d
   # The result is ok
   print a + d.T
   print b + d
   ```


### SciPy 关键知识点

When SciPy is built using the optimized ATLAS LAPACK and BLAS libraries, it has very fast linear algebra capabilities. If you dig deep enough, all of the raw lapack and blas libraries are available for your use for even more speed. SciPy contains more fully-featured versions of the linear algebra modules, as well as many other numerical algorithms. If you are doing scientific computing with python, you should probably install both NumPy and SciPy.



### 参考文献

1. [NumPy Quickstart Tutorial](https://docs.scipy.org/doc/numpy-dev/user/quickstart.html) has good code examples and covers most basic NumPy functionality.
1. [NumPy for Matlab users](https://docs.scipy.org/doc/numpy-dev/user/numpy-for-matlab-users.html)
1. [Python NumPy Tutorial](http://cs231n.github.io/python-numpy-tutorial/#numpy) – a great tutorial on NumPy and other Python libraries.
1. [Frequently Asked Questions](http://www.scipy.org/scipylib/faq.html)
1. [100 numpy exercises](http://www.labri.fr/perso/nrougier/teaching/numpy.100/index.html)
1. [Linear Algebra (scipy.linalg)](https://docs.scipy.org/doc/scipy/reference/tutorial/linalg.html)
1. [NumPy Tutorial: Data analysis with Python](http://www.dataquest.io/blog/numpy-tutorial-python/?utm_source=mybridge&utm_medium=blog&utm_campaign=read_more) - Python Top 10 Articles for the Past Year (v.2017)
1. [Scipy Lecture Notes](http://www.scipy-lectures.org/index.html) - Python Top 10 Articles for the Past Year (v.2017)
