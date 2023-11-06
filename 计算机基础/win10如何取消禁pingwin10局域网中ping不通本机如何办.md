# 不能ping通本机win10

> 场景

win10和Ubuntu18出同一个校园网中，win10可以ping通Ubuntu，但是Ubuntu无法ping通win10

win10：ip：172.22.18.8，子网掩码：255.255.0.0，网关：172.22.255.254

Ubuntu：ip：219.216.65.213，子网掩码：255.255.255.0，网关：219.216.65.255

> 分析

由于win10可以ping通Ubuntu，所以两台主机处于同一个网段中。但是由于Ubuntu无法ping通win10，因此使用Ubuntu系统ping了一下win10的网关，发现网关可以ping通，说明问题出在win10上，win10上的设置阻止了别人来ping它。



> 解决1

1).点击控制面板，打开“系统和安全”选项。然后点击“windows防火墙”。

![win10 怎么取消禁ping｜win10系统局域网中ping不通本机怎么办](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202211021708391.jpeg)

![win10 怎么取消禁ping｜win10系统局域网中ping不通本机怎么办](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202211021708422.jpeg)

2).打开进入“高级设置”，选中“入站规则”。

![win10 怎么取消禁ping｜win10系统局域网中ping不通本机怎么办](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202211021708178.jpeg)

![win10 怎么取消禁ping｜win10系统局域网中ping不通本机怎么办](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202211021708342.jpeg)

3).在入门规则中找到“文件和打印机共享（回显请求-ICMPv4-In）”选项

看是有两项，都是未启用。

分别在两项上右键，选中“启用规则”。

![win10 怎么取消禁ping｜win10系统局域网中ping不通本机怎么办](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202211021708866.jpeg)

![win10 怎么取消禁ping｜win10系统局域网中ping不通本机怎么办](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202211021708745.jpeg)

9：重新使用win10去pingUbuntu，如果可以ping通则正常。否则转向`解决2



> 解决2

许多使用win10的朋友应该都会发现其实默认下是允许被ping的，主要原因是被系统自带的防火墙给阻止了，新建一个策略便可处理，下面个跟我们详解一下具体操作办法吧。

1：首先在开始菜单的搜索框中写入“高级安全windows防火墙”，然后点击它，接着鼠标右击入站规则选中“新建规则”项；

![win10 怎么取消禁ping｜win10系统局域网中ping不通本机怎么办](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202211021711062.jpeg)

2：接着选中“自定义”，然后打开下一步按钮；

![win10 怎么取消禁ping｜win10系统局域网中ping不通本机怎么办](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202211021711867.jpeg)

3：然后打开左侧的协议和端口，然后打开协议类型后面的下拉按钮，选中“TCMPv4”项； 

![win10 怎么取消禁ping｜win10系统局域网中ping不通本机怎么办](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202211021711504.jpeg)

4：接着打开左侧的“作用域”，选中为一点ip地址；

![win10 怎么取消禁ping｜win10系统局域网中ping不通本机怎么办](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202211021710590.jpeg)

5：打开左侧的“操作”，然后选中“允许连接”；

![win10 怎么取消禁ping｜win10系统局域网中ping不通本机怎么办](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202211021710886.jpeg)

6：以后打开“名称”，写入以下名称，然后便可完成了。

![win10 怎么取消禁ping｜win10系统局域网中ping不通本机怎么办](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202211021710597.jpeg)