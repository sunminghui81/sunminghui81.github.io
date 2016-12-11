---
layout: post
keywords: k-means, clustering
description: 理解k-means算法的优缺点
title: "k-means的使用场景"
categories: [scikit-learn]
tags: [kmeans, clustering]
group: archive
icon: code-fork
---
{% include mathsyouth/setup %}

通过scikit-learn代码库中k-means算法的实现`sklearn.cluster.KMeans`，学习k-means算法的优缺点。首先，随机生成`isotropic Gaussian blobs`:

```Python
import matplotlib.pyplot as plt
from sklearn.datasets import make_blobs
n_samples = 1500
random_state = 170
X, y = make_blobs(n_samples=n_samples, random_state=random_state)
plt.scatter(X[:, 0], X[:, 1], c=y)
plt.title("Generate isotropic Gaussian blobs for clustering")
plt.show()
```

生成右图：<img src="/image/kmeans/isotropic_gaussian_blobs.png" alt="isotropic" width="50%" height="50%"/>

从上图可以看出，我们随机产生了3个cluster。但当我们事先并不知道有多少cluster时，为了通过k-means算法来估算cluster，需要设置多个cluster初始值，运行多次k-means算法：
```Python
from sklearn.cluster import KMeans
plt.figure(figsize=(8, 8))
for n_clusters in range(2, 6):
    y_pred = KMeans(n_clusters=n_clusters, random_state=random_state).fit_predict(X)
    plt.subplot(219+n_clusters)
    plt.scatter(X[:, 0], X[:, 1], c=y_pred)
    plt.title("Guess %d clusters" % n_clusters)

plt.show()
```

生成下图：<img src="/image/kmeans/guess_number_clusters.png" alt="guess number" width="100%" height="100%"/>

从上图可以看出，当猜测的cluster数目不对时，我们的预测与实际情况相差较大；为了保证预测尽量准确，我们需要设置不同的初始值来分别运行k-means算法，然后从中取最小的`inertia`或`within-cluster sum of squared criterion`。

现假设生成的数据是`anisotropicly distributed`，我们来观察一下k-means算法的效果：

```Python
import numpy as np
transformation = [[ 0.60834549, -0.63667341], [-0.40887718, 0.85253229]]
X_aniso = np.dot(X, transformation)
y_pred = KMeans(n_clusters=3, random_state=random_state).fit_predict(X_aniso)
plt.figure(figsize=(8, 8))
plt.subplot(211)
plt.scatter(X_aniso[:, 0], X_aniso[:, 1], c=y)
plt.title("Generate anisotropicly distributed data")
plt.subplot(212)
plt.scatter(X_aniso[:, 0], X_aniso[:, 1], c=y_pred)
plt.title("K-means for clustering anisotropicly distributed blobs")
plt.show()
```

生成下图：<img src="/image/kmeans/anisotropicly_distributed_data.png" alt="anisotropicly" width="70%" height="70%"/>

从上图我们可以看出，对于`anisotropicly distributed`的数据，k-means算法效果较差。

现假设不同的cluster有不同的方差（`different variance`），我们来观察一下k-means算法的效果：

```Python
X_varied, y_varied = make_blobs(n_samples=n_samples,
                                cluster_std=[1.0, 2.5,  0.5],
                                random_state=random_state)
y_pred = KMeans(n_clusters=3, random_state=random_state).fit_predict(X_varied)
plt.figure(figsize=(8,8))
plt.subplot(211)
plt.scatter(X_varied[:, 0], X_varied[:, 1], c=y_varied)
plt.title("Generate clusters with different variance")
plt.subplot(212)
plt.scatter(X_varied[:, 0], X_varied[:, 1], c=y_pred)
plt.title("K-means for clustering data with different variance")
plt.show()
```

生成下图：<img src="/image/kmeans/clusters_different_variance.png" alt="different variance" width="70%" height="70%"/>

从上图我们可以看出，对于不同的cluster有不同的方差的数据，k-means算法效果较差。

现假设不同的cluster数据规模不一致，我们来观察一下k-means算法的效果：

```Python
X_filtered = np.vstack((X[y == 0][:500], X[y == 1][:100], X[y == 2][:10]))
y_filtered = np.hstack((y[y == 0][:500], y[y == 1][:100], y[y == 2][:10]))
y_pred = KMeans(n_clusters=3, random_state=random_state).fit_predict(X_filtered)
plt.figure(figsize=(8,8))
plt.subplot(211)
plt.scatter(X_filtered[:, 0], X_filtered[:, 1], c=y_filtered)
plt.title("Generate unevenly sized blobs")
plt.subplot(212)
plt.scatter(X_filtered[:, 0], X_filtered[:, 1], c=y_pred)
plt.title("K-means for clustering unevenly sized blobs")
plt.show()
```

生成下图：<img src="/image/kmeans/clusters_unevenly_sized.png" alt="unevenly siezed" width="70%" height="70%"/>

从上图我们可以看出，对于不同的cluster有不同的数据规模时，k-means算法效果依然很好。
