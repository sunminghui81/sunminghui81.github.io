---
layout: post
keywords: TensorFlow, deep learning
description: TensorFlow 学习总结
title: "TensorFlow 基本概念"
categories: [machine-learning]
tags: [machine learning, TensorFlow, deep learning]
group: archive
icon: github
---
{% include mathsyouth/setup %}


### TensorFlow Core 关键知识点

#### Constant 和 Variables 的区别

1. A constant is constant. A variable can be assigned to, its value can be changed.
2. A constant's value is stored in the graph and its value is replicated wherever the graph is loaded. Sessions allocate memory to store variable values
3. `tf.constant` is an op, while `tf.Variable` is a class. `tf.Variable` holds several ops:

   ```Python
   x = tf.Variable(...)
   x.initializer # init
   x.eval() # read op
   x.assign(...) # write op
   ```
   You have to initialize variables before using them. The easiest way is initializing all variables at once using: `tf.global_variables_initializer()`.

#### Placeholders 和 Variables 的区别

1. [What's the difference between tf.placeholder and tf.Variable](http://stackoverflow.com/questions/36693740/whats-the-difference-between-tf-placeholder-and-tf-variable)
1. [TensorFlow: tf.placeholder and tf.Variable - why is the dimension not required?](http://stackoverflow.com/questions/41352745/tensorflow-tf-placeholder-and-tf-variable-why-is-the-dimension-not-required)
1. [Variables: Creation, Initialization, Saving, and Loading](https://www.tensorflow.org/programmers_guide/variables)

#### Op needs to be run to take effect

```Python
W = tf.Variable(10)
W.assign(100)
with tf.Session() as sess:
    sess.run(W.initializer)
    print W.eval()   # 10

###
W = tf.Variable(10)
assign_op = W.assign(100)
with tf.Session() as sess:
    # You don’t need to initialize variable because assign_op does it for you
    # sess.run(W.initializer)
    sess.run(assign_op)
    print W.eval()
```

#### Each session maintains its own copy of variable

```Python
W = tf.Variable(10)
sess1 = tf.Session()
sess2 = tf.Session()
sess1.run(W.initializer)
sess2.run(W.initializer)

print(sess1.run(W.assign_add(10)))
print(sess2.run(W.assign_sub(2)))

print(sess1.run(W.assign_add(100)))
print(sess2.run(W.assign_sub(50)))

sess1.close()
sess2.close()
```

#### Lazy loading

```Python
# Normal Loading
x = tf.Variable(10, name='x')
y = tf.Variable(20, name='y')
z = tf.add(x, y) # you create the node for add node before executing the graph
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    writer = tf.summary.FileWriter('./my_graph', sess.graph)
    for _ in range(10):
       sess.run(z)
    writer.close()

# Lazy Loading
x = tf.Variable(10, name='x')
y = tf.Variable(20, name='y')
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    writer = tf.summary.FileWriter('./my_graph', sess.graph)
    for _ in range(10):
        sess.run(tf.add(x, y)) # someone decides to be clever to save one line of code
    writer.close()
```

#### `tf.nn.softmax_cross_entropy_with_logits` 和 `tf.nn.sparse_softmax_cross_entropy_with_logits` 的区别

1. http://stackoverflow.com/questions/37312421/tensorflow-whats-the-difference-between-sparse-softmax-cross-entropy-with-logi

#### Numerical problem in `tf.nn.softmax_cross_entropy_with_logits`

1. https://github.com/tensorflow/tensorflow/issues/2327
1. http://stackoverflow.com/questions/35976954/calculating-cross-entropy-manually-vs-using-softmax-cross-entropy-with-logits-in

#### TensorFlow sequences are not iterable

Note that unlike NumPy or Python sequences, TensorFlow sequences are not iterable:

```Python
for _ in np.linspace(0, 10, 4):  # OK
for _ in tf.linspace(0, 10, 4):  # TypeError("'Tensor' object is not iterable.")
for _ in range(4):  # OK
for _ in tf.range(4):  # TypeError("'Tensor' object is not iterable.")
```

### 参考文献

1. [Getting Started With TensorFlow](https://www.tensorflow.org/get_started/get_started)
1. [MNIST For ML Beginners](https://www.tensorflow.org/get_started/mnist/beginners)
1. [CS224d: TensorFlow Tutorial](https://cs224d.stanford.edu/lectures/CS224d-Lecture7.pdf)
1. [CS 20SI: TensorFlow for Deep Learning Research](http://web.stanford.edu/class/cs20si/syllabus.html)
1. [Lecture notes for CS 20SI](http://web.stanford.edu/class/cs20si/lectures/)
1. [What is a TensorFlow Session?](http://danijar.com/what-is-a-tensorflow-session/)
