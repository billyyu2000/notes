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




[Cross Product Application](https://amirazmi.net/cross-products-in-game-development-and-their-use-cases/)

[向量外積與四元數 李白飛](http://episte.math.ntu.edu.tw/articles/mm/mm_15_1_12/index.html)

vxw = -wxv 
行列式 area of the 平行四边形
how to compute ？ 

    1. numerical formula
    2. add column [i,j,k]  , det(||)

几何意义

    |a||b|sinθ

行列式 就是 用来衡量 某个线性变化 对空间的拉伸作用。

2D空间的叉乘可以简化为两个Z轴为0的3D向量的叉乘。

a·b = det( | b v w | )
a = vxw



1. turn left, or turn right
2. find the normal vector
3. 


```python
>>> np.linalg.det( [ [1,6,0], [-2,7,0], [1,-4, 1] ]  )
19.000000000000004
>>> np.linalg.det( [ [1,6,0], [-2,7,0], [1,3, 1] ]  )
19.000000000000004
>>> np.linalg.det( [ [1,6,0], [-2,7,0], [0,3, 1] ]  )
19.000000000000004
>>> np.linalg.det( [ [1,6,0], [-2,7,0], [-1,3, 1] ]  )
19.000000000000004
```




The way the default code editor support works, is specific for each platform. On OSX the arguments works by using
NSRunningApplication.
The result you should see is similar to the one that you would get from writing open -n Path/to/Macvim.app --args $(File) +$(Line). I can see that this is not supported by macvim, so you would have to ask them to support this for you.
Another approach is to create a small package, implement https://docs.unity3d.com/2020.1/Documentation/ScriptReference/Unity.CodeEditor.IExternalCodeEditor.html and register it using CodeEditor.Register. This will override the default support, and you can implement your own way of opening files etc.


