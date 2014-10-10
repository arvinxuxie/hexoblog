---
layout: post
title: Scientific Computing
category: python
tags: python
---
## 科学Python构件
* **Python**: 
* **IPython**: 一个高级的[Python shell](http://ipython.scipy.org/moin/)
* **Numpy**: 提供强大的`numerical arrays`对象，以及操纵它们的例程。[http://www.numpy.org/](http://www.numpy.org/)
* **Scipy**: 高级的数据处理例程`processing routines`.最优化`Optimization`,回归`regression`,插值`interpolation`,etc[http://www.numpy.org/](http://www.numpy.org/)
* **Matplotlib**: 2-D可视化，`publication-ready` plots[http://www.numpy.org/](http://www.numpy.org/)
* **Mayavi**: 3-D可视化[http://www.numpy.org/](http://www.numpy.org/)
## Python语言
遍历一个目录：`os.path.walk`产生一个文件名的列表在目录树中。
```
In [32]: for dirpath, dirnames, filenames in os.walk(os.curdir):
   ....:     for fp in filenames:
   ....:         print os.path.abspath(fp)
   ....:         
/home/xjh021/dailytest/python/cardrun.py
/home/xjh021/dailytest/python/easyMath.py
/home/xjh021/dailytest/python/typechk.py
/home/xjh021/dailytest/python/closureVars.py
/home/xjh021/dailytest/python/write.py
/home/xjh021/dailytest/python/userpw.py
/home/xjh021/dailytest/python/whodata.txt
/home/xjh021/dailytest/python/pact.py
```
环境变量：
```
In [38]: os.environ
Out[38]: {'LESS': '-R', 'GNOME_DESKTOP_SESSION_ID': 'this-is-deprecated', 'GJS_DEBUG_OUTPUT': 'stderr', 'LC_CTYPE': 'en_US.UTF-8', 'WINDOWPATH': '7', 'NVM_NODEJS_ORG_MIRROR': 'http://nodejs.org/dist'}
In [40]: os.getenv('LESS')
Out[40]: '-R'
```

`shutil`:高级文件操作
* `shutil.rmtree`:递归的删除目录树(Recursively delete a directory tree)
* `shutil.move`: 递归的移动一个文件或目录到另一个地方
* `shutil.copy`: 复制文件或目录

`glob`:文件模型匹配
```
In [51]: glob.glob('*.py')
Out[51]: 
['cardrun.py',
 'easyMath.py',
 'typechk.py',
 'closureVars.py',
 'idcheck.py']
```

`pickle`:方便的永久性存储，对于存储任意对象到文件中很有用，但不安全快捷。
```
In [64]: import pickle
In [65]: l = [1, None, 'Stan']
In [66]: pickle.dump(l, file('test.pk1', 'w'))
In [67]: pickle.load(file('test.pk1'))
Out[67]: [1, None, 'Stan']
```

## Numpy:创建和操纵数值型数据
### Numpy数列对象
Numpy arrays:
```
In [96]: import numpy as np
In [97]: a = np.array([0, 1, 2, 3])
In [98]: a
Out[98]: array([0, 1, 2, 3])

In [99]: L = range(1000)
In [100]: %timeit [i**2 for i in L]
10000 loops, best of 3: 145 us per loop
In [101]: a = np.arange(1000)
In [102]: %timeit a**2
100000 loops, best of 3: 3.86 us per loop
```
创建数列
```
# 1-D
In [111]: a = np.array([0, 1, 2, 3])
In [112]: a
Out[112]: array([0, 1, 2, 3])
In [113]: a.ndim
Out[113]: 1
In [114]: a.shape
Out[114]: (4,)
In [115]: len(a)
Out[115]: 4

#2-D, 3-D
In [116]: b = np.array([[0, 1, 2], [3, 4, 5]])
In [117]: b
Out[117]: 
array([[0, 1, 2],
       [3, 4, 5]])
In [118]: b.ndim
Out[118]: 2
In [119]: b.shape
Out[119]: (2, 3)
In [120]: len(b)
Out[120]: 2
In [121]: c = np.array([[[1], [2]], [[3], [4]]])
In [122]: c
Out[122]: 
array([[[1],
        [2]],

       [[3],
        [4]]])
In [123]: c.ndim
Out[123]: 3
In [124]: c.shape
Out[124]: (2, 2, 1)
```
生成数列的函数
```
In [130]: a = np.arange(10)    #0 .. n-1 (!)
In [131]: a
Out[131]: array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])

In [132]: b = np.arange(1, 9, 2)   #start, end (exclusive), step
In [133]: b
Out[133]: array([1, 3, 5, 7])

In [134]: c = np.linspace(0, 1, 6)   #start, end, num-points
In [135]: c
Out[135]: array([ 0. ,  0.2,  0.4,  0.6,  0.8,  1. ])

In [136]: d = np.linspace(0, 1, 5, endpoint=False)
In [137]: d
Out[137]: array([ 0. ,  0.2,  0.4,  0.6,  0.8])
```
常见数列`Common arrays`:
```
In [138]: a = np.ones((3, 3))    #reminder: (3, 3) is a tuple
In [139]: a
Out[139]: 
array([[ 1.,  1.,  1.],
       [ 1.,  1.,  1.],
       [ 1.,  1.,  1.]])

In [140]: b = np.zeros((2, 2))
In [141]: b
Out[141]: 
array([[ 0.,  0.],
       [ 0.,  0.]])

In [142]: c = np.eye(3)
In [143]: c
Out[143]: 
array([[ 1.,  0.,  0.],
       [ 0.,  1.,  0.],

In [144]: d = np.diag(np.array([1, 2, 3, 4]))
In [145]: d
Out[145]: 
array([[1, 0, 0, 0],
       [0, 2, 0, 0],
       [0, 0, 3, 0],
       [0, 0, 0, 4]])
       [ 0.,  0.,  1.]])

```
`np.random`:随机数(Mersenne Twister PRNG)
```
In [146]: a = np.random.rand(4)    #uniform in [0, 1]
In [147]: a
Out[147]: array([ 0.11869568,  0.6682459 ,  0.98293953,  0.83949676])
In [148]: b = np.random.randn(4)    #Gaussian
In [149]: b
Out[149]: array([-0.2438436 , -1.24960437,  0.13631717, -0.76721059])
In [150]: np.random.seed(1234)    #Setting the random seed
```
基本数据类型
```
In [156]: a = np.array([1, 2, 3])
In [157]: a.dtype
Out[157]: dtype('int64')

In [158]: b = np.array([1., 2., 3.])
In [160]: b.dtype
Out[160]: dtype('float64')
```
提示：不同的数据类型允许我们存储数据在内存中更紧凑。更多的时候我们工作的是浮点数据类型，NumPy会自动探测输入的数据类型。
```
In [163]: c = np.array([1, 2, 3], dtype=float)
In [164]: c
Out[164]: array([ 1.,  2.,  3.])
In [165]: c.dtype
Out[165]: dtype('float64')

In [166]: a = np.ones((3, 3))
In [167]: a.dtype
Out[167]: dtype('float64')

# Complex
In [168]: d = np.array([1+2j, 3+4j, 5+6*1j])
In [169]: d.dtype
Out[169]: dtype('complex128')

#Bool
In [170]: e = np.array([True, False, False, True])
In [171]: e.dtype
Out[171]: dtype('bool')

#Strings
In [172]: f = np.array(['Bonjour', 'Hello',])
In [173]: f.dtype    #strings containing max. 7 letters
Out[173]: dtype('|S7')
```
**基本的可视化**`Basic visualization`
开始启动IPython的*pylab*模式
```
ipython --pylab
```
在`notebook`中
```
ipython notebook --paylab=inline
```
如果IPython已经启动则
```
%pylab
# 在notebook
%pylab inline
```
**1D plotting**
```
In [9]: import matplotlib.pyplot as plt

In [10]: x = np.linspace(0, 3, 20)

In [11]: y = np.linspace(0, 9, 20)

In [12]: plt.plot(x, y)
Out[12]: [<matplotlib.lines.Line2D at 0x7f2e382d5d50>]

In [13]: plt.plot(x, y, 'o')
Out[13]: [<matplotlib.lines.Line2D at 0x7f2e383f25d0>]
```
![图1]({{BASE_PATH}}/image/PythonScientific/imag1.png)
```
In [19]: image = np.random.rand(30, 30)
In [20]: plt.imshow(image, cmap=plt.cm.hot)
Out[20]: <matplotlib.image.AxesImage at 0x38cdcd0>
In [21]: plt.colorbar()
Out[21]: <matplotlib.colorbar.Colorbar instance at 0x38d2ea8>
```
![图2]({{BASE_PATH}}/image/PythonScientific/imag2.png)

`Numpy`数列对象
```
In [33]: a = np.arange(10)
In [34]: a
Out[34]: array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
In [35]: a[0], a[2], a[-1]
Out[35]: (0, 2, 9)
In [36]: a[::-1]
Out[36]: array([9, 8, 7, 6, 5, 4, 3, 2, 1, 0])
In [37]: a = np.diag(np.arange(3))
In [38]: a
Out[38]: 
array([[0, 0, 0],
       [0, 1, 0],
       [0, 0, 2]])
In [39]: a[1, 1]
Out[39]: 1
In [40]: a[2, 1] = 10
In [41]: a
Out[41]: 
array([[ 0,  0,  0],
       [ 0,  1,  0],
       [ 0, 10,  2]])
In [42]: a[1]
Out[42]: array([0, 1, 0])
```
你可以结合赋值和分片
```
In [47]: a = np.arange(10)
In [48]: a[5:] = 10
In [49]: a
Out[49]: array([ 0,  1,  2,  3,  4, 10, 10, 10, 10, 10])
In [50]: b = np.arange(5)
In [51]: a[5:] = b[::-1]
In [52]: a
Out[52]: array([0, 1, 2, 3, 4, 4, 3, 2, 1, 0])

In [58]: np.arange(6) + np.arange(0, 51, 10)[:, np.newaxis]
Out[58]: 
array([[ 0,  1,  2,  3,  4,  5],
       [10, 11, 12, 13, 14, 15],
       [20, 21, 22, 23, 24, 25],
       [30, 31, 32, 33, 34, 35],
       [40, 41, 42, 43, 44, 45],
       [50, 51, 52, 53, 54, 55]])

In [103]: a = np.array([[4,3], [2,1]])
In [104]: np.tile(a, (2, 3))
Out[104]: 
array([[4, 3, 4, 3, 4, 3],
       [2, 1, 2, 1, 2, 1],
       [4, 3, 4, 3, 4, 3],
       [2, 1, 2, 1, 2, 1]])
```
**拷贝和视图**`Copies and views`
分片操作可以创造视图`view`在原始数列的基础上，这只是一种访问数列数据的方法。因此原始数列并没有在内存中拷贝，你可以使用`np.may_share_memory()`检测如果两个数列共享一个内存单元。
```
In [84]: a = np.arange(10)
In [85]: a
Out[85]: array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
In [86]: b = a[::2]
In [87]: b
Out[87]: array([0, 2, 4, 6, 8])
In [88]: np.may_share_memory(a, b)
Out[88]: True

In [111]: b[0] = 12
In [112]: b
Out[112]: array([12,  2,  4,  6,  8])
In [113]: a
Out[113]: array([12,  1,  2,  3,  4,  5,  6,  7,  8,  9]) #(!)

In [115]: a = np.arange(10)
In [116]: c = a[::2].copy()
In [117]: np.may_share_memory(a, c)
Out[117]: False
```
质数筛选`Prime number sieve`
```
# Construct a shape(100,) boolean array is_prime, filled with True in the beginning:
In [136]: is_prime = np.ones((100,), dtype=bool)
# Cross out 0 and 1 which are not primes:
In [137]: is_prime[:2]=0
# For each integer j starting from 2, cross out its higher multiples:
In [139]: for j in range(2, N_max):
   .....:     is_prime[2*j::j] = False
```

**酷眩的索引**`Fancy indexing`
Tip:Numpy 数列能够被索引分片，也能用于布尔和整型数列(`mask`)。这种方法被称为`fancy indexing`,它们被生成为拷贝而不是视图`copies not views`
```
In [172]: np.random.seed(3)
In [174]: a = np.random.random_integers(0, 20, 15)
In [175]: a
Out[175]: array([10,  3,  8,  0, 19, 10, 11,  9, 10,  6,  0, 20, 12,  7, 14])

In [176]: (a % 3 == 0)
Out[176]: 
array([False,  True, False,  True, False, False, False,  True, False,
        True,  True, False,  True, False, False], dtype=bool)
In [177]: mask = (a % 3 == 0)
In [178]: extract_from_a = a[mask]   #or, a[a%3 == 0]
In [179]: extract_from_a       #extract a sub-array with the mask
Out[179]: array([ 3,  0,  9,  6,  0, 12])
```
索引用于`mask`是非常有用的对于设置一个新值为它的子数列：
```
In [180]: a[a % 3 == 0] = -1
In [181]: a
Out[181]: array([10, -1,  8, -1, 19, 10, 11, -1, 10, -1, -1, 20, -1,  7, 14])
```
索引对于整型数列`Indexing with an array of integers`
```
In [182]: a = np.arange(0, 100, 10)
# 索引可以完成一些相同下标重复多次
In [183]: a[[2, 3, 2, 4, 2]]    #注意[2,3,2,4,2]是python类型的一个列表
Out[183]: array([20, 30, 20, 40, 20])

In [184]: a[[9, 7]] = -100
In [185]: a
Out[185]: array([   0,   10,   20,   30,   40,   50,   60, -100,   80, -100])
```
Tip: 当一个新数列被索引创建，这个新数列有相同的`shape`
```
In [186]: a = np.arange(10)

In [187]: idx = np.array([[3,4],[9,7]])

In [188]: idx.shape
Out[188]: (2, 2)

In [189]: a[idx]
Out[189]: 
array([[3, 4],
       [9, 7]])
```
### 数值型数列的操作
**元素智能的操作**`Elementwise operations`
```
In [191]: a = np.array([1, 2, 3, 4])
In [192]: a + 1
Out[192]: array([2, 3, 4, 5])
In [193]: 2**a
Out[193]: array([ 2,  4,  8, 16])

In [194]: b = np.ones(4) + 1
In [195]: a - b
Out[195]: array([-1.,  0.,  1.,  2.])
In [196]: a * b
Out[196]: array([ 2.,  4.,  6.,  8.])
```
注意数列的相乘
```
In [197]: c = np.ones((3, 3))
In [198]: c.dot(c)
Out[198]: 
array([[ 3.,  3.,  3.],
       [ 3.,  3.,  3.],
       [ 3.,  3.,  3.]])
In [199]: c * c    # not matrix multiplication
Out[199]: 
array([[ 1.,  1.,  1.],
       [ 1.,  1.,  1.],
       [ 1.,  1.,  1.]])
In [206]: (c+2)*(c+3)
Out[206]: 
array([[ 12.,  12.,  12.],
       [ 12.,  12.,  12.],
       [ 12.,  12.,  12.]])
```



