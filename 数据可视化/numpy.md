# numpy 学习

## python中reshape的用法
```python
1 >>> a = np.array([1, 2, 3, 4]);b = np.array((5, 6, 7, 8));c = np.array([[1, 2, 3, 4],[4, 5, 6, 7], [7, 8, 9, 10]])
2 >>> b
3 array([5, 6, 7, 8])
4 >>> c
5 array([[ 1,  2,  3,  4],
6        [ 4,  5,  6,  7],
7        [ 7,  8,  9, 10]])
8 >>> c.dtype
9 dtype('int32')
10 >>> d = a.reshape((2,2))
11 >>> d
12 array([[1, 2],
13        [3, 4]])
14 >>> d = a.reshape((1,2))
15 Traceback (most recent call last):
16   File "<pyshell#27>", line 1, in <module>
17     d = a.reshape((1,2))
18 ValueError: total size of new array must be unchanged
19 >>> d = a.reshape((1,-1)) # 列为 -1 ，表示列的数不确定
20 >>> d
21 array([[1, 2, 3, 4]])
>>> d = a.reshape((-1,1)) # 行为 -1 ，表示行的数不确定
>>> d
array([[1],       
[2],       
[3],       
[4]])
```