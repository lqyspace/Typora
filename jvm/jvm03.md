[TOC]

## 3.4 什么情况下对象会进入老年代

![image-20230326180345083](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261803179.png)

![image-20230326181101446](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202303261811671.png)

**如果必须使用此参数进行调优，可以考虑新生代使用 ParNew 垃圾回收器，老年代使用 CMS 垃圾回收器。**

 