...menustart


...menuend


```python
>>> a = [2,1]
>>> b = [1,2]
>>> np.cross(a,b)
array(3)
>>> a = [2,1,0]
>>> b = [1,2,0]
>>> np.cross(a,b)
array([0, 0, 3])
>>>
```

AI

BFS 

bᵐ, 没有权重


+ 权重 UCS

A*  UCS +  Heuristics (Greedy)


8 Puzzle



CSP

N-Queens


Adv

mini-max , act as a ghost? 

Expectimax Search -> MDP


==========

Dot Product

In mathematics, the dot product or scalar product[note 1] is an algebraic operation that takes two equal-length sequences of numbers (usually coordinate vectors), and returns a single number.

Algebraically, the dot product is the sum of the products of the corresponding entries of the two sequences of numbers. Geometrically, it is the product of the Euclidean magnitudes of the two vectors and the cosine of the angle between them.


在游戏开发中, 点积是一个非常有用的工具。

Two vectors of the same dimension 


代数上说, 对 两个维度相同的变量, 或着说 对 两组维度相同的数字序列 做点积, 就是 把它们的各自对应的条目一一配对做乘法, 然后把结果相加，最后得到一个数字。

所以，向量 [xxx] , 和 向量 [yyy] 做点积， 就等于 xxxx .


从几何上说，两个向量 v,w 做点积, 





- Measuring direction

cosθ = x·y/‖x‖‖y‖

负点积 意味着向量指向不同的方向。
如果点积为零，则两个向量正交（垂直）。

can I see it？


- Projecting Vectors

当至少一个向量是单位长度时，点积是 非单位长度向量在归一化向量轴上的投影长度
（当它们都是单位长度时，它们都在方向上移动相同的量 彼此的）。

点到直线的距离问题


- Planes (aka walls/slopes/triangles)

v<sub>out</sub> = v<sub>in</sub> - 2( v<sub>in</sub> · n ) n




向量运算在游戏开发中的应用和思考

https://wuzhiwei.net/vector_in_games/?hmsr=toutiao.io&utm_campaign=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io

Applications of the Vector Dot Product for Game Programming

https://hackernoon.com/applications-of-the-vector-dot-product-for-game-programming-12443ac91f16




[Cross Product Application](https://amirazmi.net/cross-products-in-game-development-and-their-use-cases/)





