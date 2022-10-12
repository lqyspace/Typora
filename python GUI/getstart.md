[TOC]



# GUI编程

## 什么是GUI

Python的图形界面



### 关于PyQt5

PyQt5是Digia的一套应用框架与python的结合，同时支持2.x和3.x。Qt库由Riverbank Computing开发，是最强大的Gui库之一，官网：[www.riverbankcomputing.co.uk/news](www.riverbankcomputing.co.uk/news)。

PyQt5是由一系列Python模块组成。超过620个类，6000函数和方法。能在诸如Unix、Windows和Mac OS等主流操作系统上运行。PyQt5有两种证书，GPL和商业证书。



参考教程：[PyQt中文教程](https://maicss.gitbook.io/pyqt-chinese-tutoral/pyqt5/index)

## 在Python中哪些库支持GUI编程

PyQt6，PyQt5，PyQt3，PyQt2

## 开发环境安装

Python：3.8

python安装完成后，安装PyQt5。最简单的使用pip install在线安装，但由于网络问题在国内下载国外的python模块比较慢，所以使用国内的镜像下载python模块，使用以下命令安装PyQt5：

```
pip install PyQt5 -i https://pypi.douban.com/simple
```

PyQt5.9不再提供常用的Qt工具，比如图形界面开发工具Qt Designer、国际化翻译工具Liguist，所以还需要使用如下命令安装常用的Qt工具：

```
pip install PyQt5-tools -i http://pypi.douban.com/simple
或者
pip install PyQt5-tools
```



## GUI编程你应该学什么

- 大致了解你所选的GUI库
- 基本的程序结构：跑通GUI库，能够看到一个界面
-  事件和信号
- 动画效果
- 界面跳转
- 设计工具使用
- 额外
  - 网络
  - 多线程
  - 数据库
  - 文件操作
  - 绘图
  - 多媒体
  - 定时器
  - 定位
  - 国际化



## 了解PyQt5库结构

1、官方文档：[https://www.riverbankcomputing.com/static/Docs/PyQt5/module_index.html](https://www.riverbankcomputing.com/static/Docs/PyQt5/module_index.html)

说明：主要运用QtWidgets这个模块。

2、常用模块：

- QtWidgets：包含一整套UI元素控件，用于建立符合系统风格的界面；包含了一系列创建桌面应用的UI元素。
- QtGui：涵盖了窗口系统，事件处理，2D图像，基本绘画，字体和文字等等
- QtCore：涵盖了包的核心的非GUI功能的组件：时间，文件，目录，数据类型，文本流，链接。线程进程等等
- QtWebKit：包含了一个基于WebKit2的web浏览器
- QtSql：处理数据库的类
- QtTest：提供了测试PyQt5应用的工具。
- QtMultimedia：包含了处理多媒体内容和调用摄像头API的类
- QtMultimediaWidgets
- QtBluetooth：包含查找和连接蓝牙的类
- QtNetwork：包含了网络编程的类，这些工具能让TCP/IP和UDP开发变得更加方便和可靠。
- ...
- Qt：将基本全部模块中的类综合到一个单一模块中；好处：不用关心模块包含哪些类；坏处：占用内存



# Hello World

## 例1 简单的窗口

```python
__author__ = "lqy"

from PyQt5.QtWidgets import QApplication, QWidget, QLabel
import sys

# qApp 是一个全局的 QApplication(), 前提是导入 PyQt5.Qt 中的所有

# 创建一个应用对象
# sys.argv 是一组命令行参数的列表，这个参数提供对脚本的控制功能
app = QApplication(sys.argv)

# 返回 sys.argv 的命令行参数列表
print(app.arguments())
print(qApp.arguments())

# ############### 控件操作
# QWidget控件是一个用户界面的基本控件，它提供了基本的应用构造器。
# 默认情况下，构造器是没有父级的，没有父级的构造器被称为窗口（window）。
window = QWidget()

# 给窗口添加一个标题，标题在标题栏展示
window.setWindowTitle("ConceptNet5")

# 重置图形界面的大小
window.resize(500, 500)

# 修改控件位置的方法，注意屏幕坐标系的原点是左上角
window.move(400, 300) # 移动图形界面的位置

label = QLabel(window)
label.setText("hello world!")
label.move(200, 200)

# show()能让控件在桌面上显示出来。控件在内存里创建，之后才能在显示器上显示出来
window.show()

# ################ 开始进入应用程序，并进入消息循环
# 进入应用的主循环中，事件处理器这个时候开始工作。
# 主循环从窗口上接收事件，并把事件派发到应用控件里。
# 当调用exit()方法或直接销毁主控件时，主循环就会结束。
# sys.exit()方法能确保主循环安全退出。外部环境会收到主控件如何结束的信息。
# exec_()之所以有个下划线，是因为exec是一个Python的关键字。
sys.exit(app.exec_())
```

运行上面的代码，能展示出一个小窗口。

```python
import sys
from PyQt5.QtWidgets import QApplication, QWidget
```

这里引入了PyQt5.QtWidgets模块，这个模块包含了基本的组件。

```python
app = QApplication(sys.argv)
```

每个PyQt5应用都必须创建一个应用对象。sys.argv是一组命令行参数的列表。Python可以在shell里运行，这个参数提供对脚本控制的功能。

```python
w = QWidget()
```

QWidge控件是一个用户界面的基本控件，它提供了基本的应用构造器。默认情况下，构造器是没有父级的，没有父级的构造器被称为窗口（window）。

```python
w.resize(250, 150)
```

resize()方法能改变控件的大小，这里的意思是窗口宽250px，高150px。

```python
w.move(300, 300)
```

`move()`是修改控件位置的的方法。它把控件放置到屏幕坐标的(300, 300)的位置。注：屏幕坐标系的原点是屏幕的左上角。

```python
w.setWindowTitle('Simple')
```

我们给这个窗口添加了一个标题，标题在标题栏展示（虽然这看起来是一句废话，但是后面还有各种栏，还是要注意一下，多了就蒙了）。

```python
w.show()
```

`show()`能让控件在桌面上显示出来。控件在内存里创建，之后才能在显示器上显示出来。

```python
sys.exit(app.exec_())
```

最后，我们进入了应用的主循环中，事件处理器这个时候开始工作。主循环从窗口上接收事件，并把事件派发到应用控件里。当调用`exit()`方法或直接销毁主控件时，主循环就会结束。`sys.exit()`方法能确保主循环安全退出。外部环境会收到主控件如何结束的信息。

`exec_()`之所以有个下划线，是因为`exec`是一个Python的关键字。



> PyQt5程序基本结构分析

```python
from PyQt5.Qt import * # 主要包含一些常用的类，汇总到一块儿
import sys

# 1、创建应用
app = QApplication(sys.argv)
# print(app.arguments())
# 全局 qApp = QApplication(),提前导入 from PyQt5.Qt import *
# print(qApp.arguments())

# 2、控件的操作
# 创建控件，设置控件大小，位置，样式...，事件，信号处理
# 2.1 创建控件
# 当我们创建一个控件之后，如果说，这个控件没有父控件，则把他当做顶层控件（即窗口）
# 系统会自动的给窗口添加一些装饰(如标题栏)窗口控件具备一些特性（设置标题，图标）
window = QWidget()
# window = QPushButton()
# window = QLabel()


# 2.2 设置控件
# window.setText("hello sz")
window.setWindowTitle("我的尝试")
window.resize(400, 400)


# 控件也可以做为一个容器（承载其他的控件）
# label = QLabel()
# label.setText("你好晕")
# label.setWindowTitle("这是另外一个")
# label.show()

# 这说明 window 这个控件是父控件，label作为子控件，所以可不用 show()
label = QLabel(window)
label.setText("我是大哥")
label.move(150, 150)


# 2.3 展示控件
# 刚创建好一个控件之后（前提：这个控件没有什么父控件），默认情况下不会被展示，
# 只有手动调用show()才可以展示
window.show()

# 应用程序的执行，进入到消息循环
# 让整个程序开始执行，并且进入到消息循环（无限循环）
# 检测整个程序接收到用户的交互信息
# app.exec_() 让程序进入主循环，不要停止
# app.exec_() 执行的状态信息给sys.exit(),好让系统知道我什么时候停止
sys.exit(app.exec_())
```



### 如何设置活动模板

1、打开pycharm的 File---->setting。

2、然后搜索live Templates

3、然后选择 python，点击右上角的 `+`

![image-20220810210522566](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208102105712.png)

4、添加快捷键的缩写

5、进行简单介绍

6、输入模板内容：同时也可以在相应的位置 输入 `$TITLE$` 或 `$CIDE$` 等进行光标定位

![image-20220810210943633](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208102109675.png)

7、选择你的模板应用的场合

8、Apply

9、OK



### 面向对象版的基本结构

```python
from PyQt5.Qt import * # 主要包含一些常用的类，汇总到一块儿


class Window(QWidget):
    def __init__(self):
        QWidget.__init__(self) # 继承父类的 __init__()
        self.setWindowTitle("QLabel的学习")
        self.resize(500, 500)
        self.set_ui()

    def set_ui(self):
        label = QLabel(self)
        label.setText("这是面向对象版本！")


if __name__ == '__main__':
    import sys

    app = QApplication(sys.argv)

    window = Window()
    window.show()

    sys.exit(app.exec_())
```



## 什么是控件

### 1、什么是控件

**1、一个程序界面上的各个独立的元素**

- 一块矩形区域

**2、具备不同的功能**

- 用户点击
- 接受用户输入
- 展示内容
- 存放其他的控件

**3、初试常用的控件：基本的控件**

- 按钮
  - QPushButton
    - 可以双击，可以单击，也可以右击
  - QCommanLinkButton
    - 效果跟QPushButton差不多
  - QRadioButton
    - 单选的按钮，有单选框
  - QCheckBox
    - 复选的按钮，有复选的按钮
- 输入控件
  - 纯键盘输入
    - QLineEdit：单行的文本输入框
    - QTextEdit：多行的输入框架：可以输入富文本
    - QPlainTextEdit：普通的多行文本，只能输入普通文本，不能输入富文本
    - QKeySequenceEdit：输入快捷键的文本框，直接按快捷键就可以输入
  - 步长调节(QAbstractSpinBox)(键盘+鼠标)
    - QDateTimeEdit：
      - QDateEdit：上下按钮调节日期
      - QTimeEdit：上下按钮调节时间
    - QSpinBox：上下按钮以整型数字加减调节
    - QDoubleSpinBox：上下按钮以浮点型数字加减调节
  - 组合框
    - QComboBox(下拉选择输入)：比如 选择不同的城市会显示不同的地级市
      - QFontComboBox：选择不同的字体
  - 滑块（QAbstractSlider）(鼠标)
    - QDial：比如动态调节字体大小所用到的滑块，是圆形的
    - QSlider：比如电脑的音量调节所用到的滑块，是长条形
    - QScrollBar：滚动条，比如网页侧边的滚动条，是滑块形的
  - 橡皮筋选中
    - QRubberBand：比如在桌面上使用电脑鼠标按住左键画出的框
  - 对话框(QDialog)
    - QColorDialog：颜色对话框：选择颜色
    - QFileDialog：选择文件
    - QFontDialog：选择字体，设置样式等
    - QInputDialog：接收用户输入，弹出一个对话框
  - 日期：QCalendarWidget：选择日期，功能齐全
- 展示控件
  - QLabel：效果图
    - 普通文本：展示文字，一般不可编辑
    - 数字
    - 富文本：图片，文本，超链接等
    - 图片
    - QLabel-动画
  - QLCDNumber：有点像LCD灯的效果
  - QProgressBar：进度条，用在下载进度，加载进度
  - 对话框(QDialog)
    - QMessageBox：各种消息的弹出框，比如内容弹出框，警告框，提示框等等
    - QErrorMessage：错误对话框，提示错误信息
    - QProgressDialog：进度对话框，显示进度
- 容器控件
  - QToolBox：承载其他的控件，装其他的控件
  - QDialogButtonBox：承载对话框里面的按钮
  - QGroupBox：将控件进行一组组的收纳，一组里面有多个控件
  - QMdiSubWindow：QMdiArea和QMdiSubWindow
    - 创建子控件，子窗口
- 结构控件
  - QMainWindow
    - QMenuBar：菜单栏
      - QMenu：菜单
    - QToolBar：工具栏
      - QToolButton：工具按钮
    - QStatusBar：状态栏
      - 窗口底部提示一些信息
  - QTabwidget：标签控件
    - QTabBar：做标签的切换
  - QStackedWidget：栈结构窗口界面
  - QSplitter：分隔控件，把一个窗口分隔成几部分
  - QDockWidget：可以让一部分控件悬浮起来，停靠在上面，下面，左面，右面
- 滚动控件：QAbstractScrollArea
  - QTextBrowser：文本浏览器的滚动
  - QScrollArea：滚动的区域，可以展示比较大的图片
  - QAbstractItemView：
    - QColumnView：列视图
    - QHeaderView：头部视图
    - QListView
      - QListWidget：展示列表里面的元素
      - QUndoView：恢复视图，比如撤销和重做

    - QTableView：表格视图
      - QTableWidget

    - QTreeView：QTreeWidget：树形视图

  - QMdiarea：在父窗口中悬浮展示子窗口，可最大化，最小化，关闭
  - QGraphicsView：画图控件

- 辅助控件
  - QFocusFrame：获取焦点
  - QSizeGrip：窗口右下角的鼠标拖动的提示
  - QDesktopWidget：桌面控件，可以快速访问电脑桌面的宽高信息等

- 其他
  - 向导/打印(QDialog)
    - QWizard：向导页，比如安装过程，第几步第几步，最后完成
      - QWizardPage：单独的一个向导页

    - QAbstractPrintDialog：QPrintDialog：打印界面
    - QPrintPreviewDialog：QPrintPreviewWidget：打印预览
    - QPageSetupDialog：打印的A4纸张的页面设置

  - 欢迎界面QSplashScreen：QSplashScreen，欢迎页面
  - 功能性控件
    - QVideoWidget：展示视频，播放视频
      - QCameraViewfinder：相机，摄像头控件

    - QWebEngineView：浏览器插件，引擎


**4、不同的控件有**

- 相同的共性
  - 名字
  - 矩形区域
  - 位置
  - 大小
  - 可以设置样式
  - ...
- 不同的特性
  - 展示内容
  - 接收输入
  - 用户交互
  - 容器
  - 框架
  - ...



### 2、控件的基类QObject

#### 2.1 QWidget

- QObject
  - QWidget
    - QAbstractButton
      - QPushButton
    - QFrame
      - QAbstractScrollArea
        - QAbstractItemView
        - QTextEdit
    - QAbstractSlider
    - QAbstractSpinBox
    - QComboBox
    - QDialog

```python
from PyQt5.Qt import *

# print(QObject.__subclasses__())
# print(QWidget.__subclasses__())
# print(QAbstractButton.__subclasses__())

def getSubClasses(cls):
    for subcls in cls.__subclasses__():
        print(subcls)
        # if len(subcls.__subclasses__())>0:
        #     getSubClasses(subcls)


if __name__ == '__main__':
    getSubClasses(QWidget)
```

> QObject

描述：所有Qt对象的基类

功能作用：

#### 2.2 对象名称，属性

- API
  - setObjectName("唯一名称") 给一个Qt对象设置一个名称，一般这个名称是唯一的，当作对象deID来使用
  - objectName() 获取一个Qt对象的名称
  - setProperty("属性名称", 值) 给一个Qt对象动态添加一个属性与值
  - property("属性名称") 获取一个对象的属性值
  - dynamicPropertyNames() 获取一个对象中所有通过setProperty()设置的属性名称
- 应用场景
  - 用于qss的ID选择器，属性选择器，方便统一设置样式
  - 用于装饰的信号与槽
- 案例：创建多个用于信息提示的QLabel
  - 要求
    - 1、凡是提示的QLabel控件，都要求设置：字体大小25px，字体颜色灰色，边框圆角8px
    - 2、信息提示分多个级别
      - 正常（normal）：绿色边框，绿色字体
      - 警告（warning）：黄色边框，黄色字体
      - 错误（error）：红色边框，红色字体
  - 涉及知识点
    - qss样式表
    - 文件读取
    - 对象\属性名称设置
  - 辅助资料：QObject.qss
  - 掌握级别：体验对象\属性名称的设置，结合样式表所起到的效果

**案例：**完整代码如下：

```python
from PyQt5.Qt import *  # 主要包含一些常用的类，汇总到一块儿


class Window(QWidget):
    def __init__(self):
        QWidget.__init__(self)
        self.setWindowTitle("QObject的学习")
        self.resize(500, 500)
        self.set_ui()

    def set_ui(self):
        # self.QObjectExtendTest()
        self.QObjectNameAndPropertyOperate()

    def QObjectExtendTest(self):
        # print(QObject.__subclasses__())
        mros = QObject.mro()  # 输出父类
        for mro in mros:
            print(mro)

    def QObjectNameAndPropertyOperate(self):
        # 测试API
        # ****************测试API***************开始
        # obj = QObject()
        # # 为对象设置一个名称，唯一的
        # obj.setObjectName("Notice")
        # print(obj.objectName())
        # 
        # # 给一个Qt对象动态添加一个属性与值
        # obj.setProperty('notice_level', 'error')
        # obj.setProperty('notice_level2', 'warning')
        # print(obj.property('notice_level2'))
        # 
        # # 动态获取所有通过setProperty()设置的属性名称
        # print(obj.dynamicPropertyNames())
        # ****************测试API***************结束

        # ****************案例演示***************开始
        # 加载qss样式
        with open('QObject.qss', 'r') as f:
            qApp.setStyleSheet(f.read())  # 加载整个应用程序上

        label = QLabel(self)
        label.setText('我是你大哥！')
        label.setObjectName('notice') # 给对象添加一个ID名称
        label.setProperty("notice_level", 'normal')

        label2 = QLabel(self)
        label2.setText('人狠话不多！')
        label2.move(100, 100)
        label2.setObjectName("notice") # 给对象添加一个ID名称
        label2.setProperty("notice_level", 'warning')

        label3 = QLabel(self)
        label3.setText('标签3')
        label3.move(150, 150)
        label3.setProperty("notice_level", 'error')

        label4 = QLabel(self)
        label4.setText('标签4')
        label4.move(160, 160)
        label4.setObjectName('notice')

        # btn = QPushButton(self)
        # btn.setText('btn')
        # btn.move(50, 50)

        # label.setStyleSheet("font-size:20px; color:red;")

        # ****************案例演示***************结束


if __name__ == '__main__':
    import sys

    app = QApplication(sys.argv)

    window = Window()
    window.show()

    sys.exit(app.exec_())

```

说明：

1、qss样式类似于css的样式，里面同样有ID选择器，属性选择器

2、本案例中，notice相当于ID名称，notice_level的值是属性值

3、`QLabel#notice` 的 `#` 号两边不能有空格

4、多样性的写法：`QLabel#notice`，`QLabel#notice[notice_level='normal']`，`QLabel#[notice_level='warning']`

5、对象的 ID名称 与 属性名称 不必非得同时出现

6、qss文件模板：

```python
QLabel#notice {
    font-size: 20px;
    color: gray;
    border: 1px solid gray;
    border-radius: 8px;
}

QLabel#notice[notice_level='normal'] {
    color: green;
    border-color: green;
}

QLabel#notice[notice_level='warning'] {
    color: yellow;
    border-color: yellow;
}

QLabel[notice_level='error'] {
    color: red;
    border-color: red;
}
```



#### 2.3 父子对象的操作

- API
  - setParent(parent)
    - 设置父对象
    - 父对象只能设置一个
    - 尝试构造右侧父子关系图
      - ![image-20220811160215651](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208111602747.png)
  - parent() ：获取父对象
  - children()：获取所有**直系**子对象
  - findChild(参数1，参数2，参数3)
    - 获取**某一个**指定名称和类型的子对象
    - 参数1：类型
      - 类型：QObject
      - 类型元组：QPushButton，QLabel
    - 参数2：名称
      - 名称：notice（可以省略）
    - 参数3：查找选项
      - Qt.FindChildrenRecursively：递归查找（默认选项）
      - Qt.FindDirectChildrenOnly：只查找直接子对象
  - findChildren(参数1，参数2，参数3)
    - 查找所有子对象，包括递归查找
- 应用场景
  - **涉及到Qt对象内存管理机制**
    - QObject继承树
      - 所有的对象都是直接或者间接的继承自QObject
      - QObject在一个对象数中组织他们自己
        - 当创建了一个QObject时，如果使用了其他对象作为其父对象，那么，他就会被添加到父对象的children()列表中
      - 当父对象被销毁时，这个QObject也会被销毁
    - QWidget
      - 扩展了父子关系
      - 当一个控件设置了父控件
        - 会包含在父控件内部
        - 受父控件区域裁剪
        - 父控件被删减时，子控件会自动删除
      - 场景案例
        - 一个对话框，上面有很多操作按钮（取消，OK），按钮和对话框本身是父子控件关系
        - 我们操作的时候，是操作的对话框控件本身，而不是其内部的子控件（按钮）
        - 当对话框被删除时，内部的子控件也会自动的删除
  - **如果一个控件没有任何父控件，那么就会被当作顶层控件（窗口）**
    - 多个顶层窗口相互独立
  - **如果想要一个控件被包含在另外一个控件内部，就需要设置父子关系**
    - 显示位置受父控件约束
    - 生命周期也被父控件接管
- 案例
  - 1、创建两个独立的窗口
    - 要求：设置不同的标题（社会我顺哥、人狠话不多）
    - 涉及的知识点：创建窗口、设置窗口标题
    - 掌握级别：了解如果创建控件没有设置任何父控件，会有什么效果
  - 2、创建一个窗口，包含另外两个子控件QWidget
  - 3、创建一个窗口，包含多个子控件QWidget和QLabel

**案例：**完整代码如下：

```python
from PyQt5.Qt import *  # 主要包含一些常用的类，汇总到一块儿


class Window(QWidget):
    def __init__(self):
        QWidget.__init__(self)
        self.setWindowTitle("QObject的学习")
        self.resize(500, 500)
        self.set_ui()

    def set_ui(self):
        # self.QObjectExtendTest()
        # self.QObjectNameAndPropertyOperate()
        self.QObjectParentAndChildrenOperate()

    def QObjectExtendTest(self):
        # print(QObject.__subclasses__())
        mros = QObject.mro()  # 输出父类
        for mro in mros:
            print(mro)

    def QObjectNameAndPropertyOperate(self):
        # 测试API
        # ****************测试API***************开始
        # obj = QObject()
        # # 为对象设置一个名称，唯一的
        # obj.setObjectName("Notice")
        # print(obj.objectName())
        # 
        # # 给一个Qt对象动态添加一个属性与值
        # obj.setProperty('notice_level', 'error')
        # obj.setProperty('notice_level2', 'warning')
        # print(obj.property('notice_level2'))
        # 
        # # 动态获取所有通过setProperty()设置的属性名称
        # print(obj.dynamicPropertyNames())
        # ****************测试API***************结束

        # ****************案例演示***************开始
        # 加载qss样式
        with open('QObject.qss', 'r') as f:
            qApp.setStyleSheet(f.read())  # 加载整个应用程序上

        label = QLabel(self)
        label.setText('我是你大哥！')
        label.setObjectName('notice') # 给对象添加一个ID名称
        label.setProperty("notice_level", 'normal')

        label2 = QLabel(self)
        label2.setText('人狠话不多！')
        label2.move(100, 100)
        label2.setObjectName("notice") # 给对象添加一个ID名称
        label2.setProperty("notice_level", 'warning')

        label3 = QLabel(self)
        label3.setText('标签3')
        label3.move(150, 150)
        label3.setProperty("notice_level", 'error')

        label4 = QLabel(self)
        label4.setText('标签4')
        label4.move(160, 160)
        label4.setObjectName('notice')

        # btn = QPushButton(self)
        # btn.setText('btn')
        # btn.move(50, 50)

        # label.setStyleSheet("font-size:20px; color:red;")

        # ****************案例演示***************结束

    def QObjectParentAndChildrenOperate(self):
        # ****************测试API***************开始
        obj0 = QObject()
        obj1 = QObject()
        obj2 = QObject()
        obj2.setObjectName('obj2')
        obj3 = QObject()
        obj4 = QObject()
        obj5 = QObject()
        print('obj0', obj0)
        print('obj1', obj1)
        print('obj2', obj2)
        print('obj3', obj3)
        print('obj4', obj4)
        print('obj5', obj5)
        obj1.setParent(obj0)
        obj2.setParent(obj0)
        # label = QLabel()
        # label.setParent(obj0)
        obj3.setParent(obj1)
        obj3.setObjectName('obj3')
        obj4.setParent(obj2)
        obj5.setParent(obj2)
        # print(obj4.parent())
        # print(obj0.children())

        print(obj0.findChild(QObject))
        # 第二个参数：name
        print(obj0.findChild(QObject, 'obj2'))

        # 默认是递归查找，所以能找到obj3
        print(obj0.findChild(QObject, 'obj3'))

        # 只查找直接子对象：由于没有递归查找，所以没有找到
        print(obj0.findChild(QObject, 'obj3', Qt.FindDirectChildrenOnly))


        # 使用findChildren()
        # 把obj0的所有子对象都找到了，包括递归查找
        print(obj0.findChildren(QObject))
        # ****************测试API***************结束
        
        # ****************内存管理机制***************开始
        obj1 = QObject()
        # self.obj1 = obj1
        obj2 = QObject()
        obj2.setParent(obj1)

        # 监听obj2对象被释放
        obj2.destroyed.connect(lambda : print("obj2对象被释放了")) # 输出：obj2对象被释放了
        
        """
        由于函数被执行完，所以obj1对象被释放，导致obj2对象也被释放
        """

        """
        加上一个self.obj1 = obj1,导致obj1不会自动释放
        """

        """
        del self.obj1 # 手动释放obj1，导致obj2也被释放
        """
        
        # ****************内存管理机制***************结束



if __name__ == '__main__':
    import sys

    app = QApplication(sys.argv)

    window = Window()
    window.show()

    sys.exit(app.exec_())

```

**案例：**完整代码：

```python
from PyQt5.Qt import *  # 主要包含一些常用的类，汇总到一块儿


class Window(QWidget):
    def __init__(self):
        QWidget.__init__(self)
        self.setWindowTitle("QObject的学习")
        self.resize(500, 500)
        self.set_ui()

    def set_ui(self):
        # self.QObjectExtendTest()
        # self.QObjectNameAndPropertyOperate()
        self.QObjectParentAndChildrenOperate()

    def QObjectExtendTest(self):
        # print(QObject.__subclasses__())
        mros = QObject.mro()  # 输出父类
        for mro in mros:
            print(mro)

    def QObjectNameAndPropertyOperate(self):
        # 测试API
        # ****************测试API***************开始
        # obj = QObject()
        # # 为对象设置一个名称，唯一的
        # obj.setObjectName("Notice")
        # print(obj.objectName())
        # 
        # # 给一个Qt对象动态添加一个属性与值
        # obj.setProperty('notice_level', 'error')
        # obj.setProperty('notice_level2', 'warning')
        # print(obj.property('notice_level2'))
        # 
        # # 动态获取所有通过setProperty()设置的属性名称
        # print(obj.dynamicPropertyNames())
        # ****************测试API***************结束

        # ****************案例演示***************开始
        # 加载qss样式
        with open('QObject.qss', 'r') as f:
            qApp.setStyleSheet(f.read())  # 加载整个应用程序上

        label = QLabel(self)
        label.setText('我是你大哥！')
        label.setObjectName('notice') # 给对象添加一个ID名称
        label.setProperty("notice_level", 'normal')

        label2 = QLabel(self)
        label2.setText('人狠话不多！')
        label2.move(100, 100)
        label2.setObjectName("notice") # 给对象添加一个ID名称
        label2.setProperty("notice_level", 'warning')

        label3 = QLabel(self)
        label3.setText('标签3') 
        label3.move(150, 150)
        label3.setProperty("notice_level", 'error')

        label4 = QLabel(self)
        label4.setText('标签4')
        label4.move(160, 160)
        label4.setObjectName('notice')

        # btn = QPushButton(self)
        # btn.setText('btn')
        # btn.move(50, 50)

        # label.setStyleSheet("font-size:20px; color:red;")

        # ****************案例演示***************结束

    def QObjectParentAndChildrenOperate(self):
        # ****************测试API***************开始
        # obj0 = QObject()
        # obj1 = QObject()
        # obj2 = QObject()
        # obj2.setObjectName('obj2')
        # obj3 = QObject()
        # obj4 = QObject()
        # obj5 = QObject()
        # print('obj0', obj0)
        # print('obj1', obj1)
        # print('obj2', obj2)
        # print('obj3', obj3)
        # print('obj4', obj4)
        # print('obj5', obj5)
        # obj1.setParent(obj0)
        # obj2.setParent(obj0)
        # # label = QLabel()
        # # label.setParent(obj0)
        # obj3.setParent(obj1)
        # obj3.setObjectName('obj3')
        # obj4.setParent(obj2)
        # obj5.setParent(obj2)
        # # print(obj4.parent())
        # # print(obj0.children())
        #
        # print(obj0.findChild(QObject))
        # # 第二个参数：name
        # print(obj0.findChild(QObject, 'obj2'))
        #
        # # 默认是递归查找，所以能找到obj3
        # print(obj0.findChild(QObject, 'obj3'))
        #
        # # 只查找直接子对象：由于没有递归查找，所以没有找到
        # print(obj0.findChild(QObject, 'obj3', Qt.FindDirectChildrenOnly))
        #
        #
        # # 使用findChildren()
        # # 把obj0的所有子对象都找到了，包括递归查找
        # print(obj0.findChildren(QObject))
        # ****************测试API***************结束
        
        # ****************内存管理机制***************开始
        obj1 = QObject()
        self.obj1 = obj1
        obj2 = QObject()
        obj2.setParent(obj1)

        # 监听obj2对象被释放
        obj2.destroyed.connect(lambda : print("obj2对象被释放了")) # 输出：obj2对象被释放了

        """
        由于函数被执行完，所以obj1对象自动被释放，导致obj2对象也被释放
        """

        """
        加上一个self.obj1 = obj1,导致obj1不会自动释放
        """

        """
        del self.obj1 # 手动释放obj1，导致obj2也被释放
        """
        # ****************内存管理机制***************结束


if __name__ == '__main__':
    import sys
    app = QApplication(sys.argv)

    # window = Window()
    # window.show()

    # 案例1
    win1 = QWidget()
    win1.setWindowTitle('社会我顺哥')
    win1.setStyleSheet("background-color: red;")
    win1.resize(500, 500)

    win2 = QWidget()
    win2.setWindowTitle('人狠话不多')
    win2.setStyleSheet("background-color: green;")
    win2.resize(1000, 100)

    # 案例2
    win_root = QWidget()
    label = QLabel()
    label.setParent(win_root)
    label.setText('label1')

    label2 = QLabel()
    label2.setParent(win_root)
    label2.setText('label2')
    label2.move(50, 50)

    label3 = QLabel()
    label3.setParent(win_root)
    label3.setText('label2')
    label3.move(80, 60)

    btn = QPushButton(win_root)
    btn.move(100, 100)
    btn.setText('btn')


    # 把win2放在win1的里面
    win2.setParent(win1)

    # win1.show()
    # win2.show()
    win_root.show()

    for sub_widget in win_root.findChildren(QLabel):
        print(sub_widget)
        sub_widget.setStyleSheet("background-color: green;")

    sys.exit(app.exec_())
```



#### 2.4 信号处理

- 信号与槽机制

  - 基本概念：信号（Signal）与槽（Slot）是Qt的核心机制，主要作用在于对象之间进行通讯
    - 信号：当一个控件状态发生改变时，向外界发出的信息
    - 槽：一个执行某些操作的函数/方法
    - 所有继承自QWidget的控件都支持“信号与槽”的机制
  - 机制描述
    - 手动操作：信号——>槽
    - 自动操作：当信号发出时，连接的槽函数会自动执行
  - 基本使用介绍
    - 信号
      - 控件内置的一些：QPushButton().pressed、QPushButton().clicked
      - 也可以自定义
    - 槽
      - 不同控件内置的槽函数
      - 自定义的函数/方法
    - 连接方式
      - object.信号.connect(槽函数)
    - 特性
      - 一个信号可以连接多个槽函数
      - 一个信号也可以连接另外一个信号
      - 信号的参数可以是任何python类型
      - 一个槽可以监听多个信号

  - 高级
- API
  - widget.信号.connect(槽)
  - obj.disconnect()
    - 取消连接信号与槽
    - obj
      - 控件
      - 信号
  - widget.blockSignals(bool)：临时（取消）阻止指定控件所有的信号与槽的链接
  - widget.signalsBlocked()：获取信号是否被阻止
  - widget.receivers(信号)：返回连接到信号的接收器数量

- 应用场景
  - 1、监听信号，响应用户行为
  - 2、信号与槽机制
- 案例
  - 1、当用户点击按钮的时候打印“点我干啥”
  - 2、在所有修改的窗口标题前，添加前缀“撩课——”

#### 2.5 信号

- objectNameChanged(objectName)：对象名称发生改变时发射此信号
- destroyed(obj)：对象被销毁时，发射此信号

##### **案例：**完整代码

```python
    def QObjectSignalsOperates(self):
        self.obj = QObject()

        # obj.destroyed() # 对象被销毁时，触发槽
        # obj.objectNameChanged() # 对象改名时，触发槽

        # 自定义槽函数
        def destroy_cao(obj): # obj 参数可要可不要，如果不接受被释放的对象，那就可以不用要
            print("对象被释放了", obj)

        # self 代表窗口，只要窗口不消失，就不会自动被释放
        # destroyed 信号被触发时，向外界发射时，会自动携带一个被释放的对象
        # 此时，只要给槽函数一个参数，即可让该参数进行接收
        self.obj.destroyed.connect(destroy_cao)

        del self.obj
```

```python
    def QObjectSignalsOperates(self):
        self.obj = QObject()

        # obj.destroyed() # 对象被销毁时，触发槽
        # obj.objectNameChanged() # 对象改名时，触发槽

        # 自定义槽函数
        # def destroy_cao(obj): # obj参数可要可不要，如果不接受被释放的参数，那就可以不要
        #     print("对象被释放了", obj)

        # self 代表窗口，只要窗口不消失，就不会自动被释放
        # destroyed 信号被触发时，向外界发射时，会自动携带一个被释放的对象
        # 此时，只要给槽函数一个参数，即可让该参数进行接收
        # self.obj.destroyed.connect(destroy_cao)
        #
        # del self.obj

        def obj_name_cao(name): # name可要可不要，如果不用来接收被改变的名字，那就可以不用要
            print("对象名称发生改变！", name)

       	self.obj.objectNameChanged.connect(obj_name_cao)

        self.obj.setObjectName('xxxxx')
```

##### **取消连接如下**：

```python
    def QObjectSignalsOperates(self):
        self.obj = QObject()

        # obj.destroyed() # 对象被销毁时，触发槽
        # obj.objectNameChanged() # 对象改名时，触发槽

        # 自定义槽函数
        # def destroy_cao(obj): # obj参数可要可不要，如果不接受被释放的参数，那就可以不要
        #     print("对象被释放了", obj)

        # self 代表窗口，只要窗口不消失，就不会自动被释放
        # destroyed 信号被触发时，向外界发射时，会自动携带一个被释放的对象
        # 此时，只要给槽函数一个参数，即可让该参数进行接收
        # self.obj.destroyed.connect(destroy_cao)
        #
        # del self.obj

        def obj_name_cao(name): # name可要可不要，如果不用来接收被改变的名字，那就可以不用要
            print("对象名称发生改变！", name)

        self.obj.objectNameChanged.connect(obj_name_cao)

        self.obj.setObjectName('xxxxx')
        self.obj.objectNameChanged.disconnect(obj_name_cao)
        self.obj.setObjectName('oooooo')
```

##### **重新连接：**

```python
    def QObjectSignalsOperates(self):
        self.obj = QObject()

        # obj.destroyed() # 对象被销毁时，触发槽
        # obj.objectNameChanged() # 对象改名时，触发槽

        # 自定义槽函数
        # def destroy_cao(obj): # obj参数可要可不要，如果不接受被释放的参数，那就可以不要
        #     print("对象被释放了", obj)

        # self 代表窗口，只要窗口不消失，就不会自动被释放
        # destroyed 信号被触发时，向外界发射时，会自动携带一个被释放的对象
        # 此时，只要给槽函数一个参数，即可让该参数进行接收
        # self.obj.destroyed.connect(destroy_cao)
        #
        # del self.obj

        def obj_name_cao(name): # name可要可不要，如果不用来接收被改变的名字，那就可以不用要
            print("对象名称发生改变！", name)

        self.obj.objectNameChanged.connect(obj_name_cao)

        self.obj.setObjectName('xxxxx')
        self.obj.objectNameChanged.disconnect(obj_name_cao)
        self.obj.setObjectName('oooooo')

        self.obj.objectNameChanged.connect(obj_name_cao)
        self.obj.setObjectName('xxxoooo')
```

##### **临时阻塞信号：**

```python
    def QObjectSignalsOperates(self):
        self.obj = QObject()

        # obj.destroyed() # 对象被销毁时，触发槽
        # obj.objectNameChanged() # 对象改名时，触发槽

        # 自定义槽函数
        # def destroy_cao(obj): # obj参数可要可不要，如果不接受被释放的参数，那就可以不要
        #     print("对象被释放了", obj)

        # self 代表窗口，只要窗口不消失，就不会自动被释放
        # destroyed 信号被触发时，向外界发射时，会自动携带一个被释放的对象
        # 此时，只要给槽函数一个参数，即可让该参数进行接收
        # self.obj.destroyed.connect(destroy_cao)
        #
        # del self.obj

        def obj_name_cao(name): # name可要可不要，如果不用来接收被改变的名字，那就可以不用要
            print("对象名称发生改变！", name)
            
        def obj_name_cao2(name): # name可要可不要，如果不用来接收被改变的名字，那就可以不用要
            print("对象名称发生改变！", name)

        self.obj.objectNameChanged.connect(obj_name_cao)
        self.obj.objectNameChanged.connect(obj_name_cao2)

        print(self.obj.receivers(self.obj.objectNameChanged)) #返回链接到此信号的接收器的数量，输出结果为2
        self.obj.setObjectName('xxxxx')
        # self.obj.objectNameChanged.disconnect(obj_name_cao)
        
        print(self.obj.signalsBlocked(), '1')
        self.obj.blockSignals(True) # 临时阻塞信号
        
        print(self.obj.signalsBlocked(), '2')
        self.obj.setObjectName('oooooo')
        
        self.obj.blockSignals(False)  # 恢复信号
        print(self.obj.signalsBlocked(), '3')
        
        self.obj.objectNameChanged.connect(obj_name_cao)
        self.obj.setObjectName('xxxoooo')
```



##### 信号处理案例

- 1、当用户点击按钮的时候打印“点我干啥”

```python
btn = QPushButton(self)
btn.setText("点击我")
def cao():
print("点我干啥")

btn.clicked.connect(cao)
```

- 2、在所有修改的窗口标题前，添加前缀“撩课——”
  - 要求：比如，后续我们修改标题为 ”你好“；最终变为 ”撩课-你好“；支持多次修改
  - 设计知识点：设置窗口标题，监听窗口标题改变信号，临时取消/恢复信号与曹的链接

```python
window = QWidget()

def cao(title):
    print('窗口标题改变了：', title)
    # window.windowTitleChanged.disconnect(cao)
    window.blockSignals(True)
    window.setWindowTitle('撩课-' + title)
    # window.windowTitleChanged.connect(cao)
    window.blockSignals(False)

window.windowTitleChanged.connect(cao)


window.setWindowTitle('Hello!')
window.setWindowTitle('Hello12!')
window.show()
```



#### 2.6 类型判定

##### API

- isWidgetType()
- inherits(父类)：一个对象是否直接或者间接继承某个类

##### 应用场景

- 过滤筛选控件

##### 案例

1、创建一个窗口，包含多个QLabel或其他的组件

- 要求：将包含在窗口内的所有QLabel控件，设置背景色cyan
- 涉及知识点：子控件获取；控件类型判定；样式设置
- 掌握级别：掌握控件判定部分

```python
def QObjectType(self):
	obj = QObject()
	w = QWidget()
	btn = QPushButton()
	label = QLabel()

	objs = [obj, w, btn, label]

	for i in objs:
		# print(i.isWidgetType()) # False True True True
        # print(i.inherits("QWidget")) # False True True True
        print(i.inherits('QPushButton')) # False False True False
```





#### 2.7 对象删除

##### API

- obj.deleteLater()：**删除一个对象时，也会解除它与父对象之间的关系；**deleteLater()并没有将对象立即销毁，而是向主消息循环发送了一个event，下一次主消息循环收到这个event之后才会销毁对象；这样做的好处是可以在延迟删除的时间内完成一些操作，坏处就是内存释放会不及时。

##### 应用场景

- 想要移除某一个应用时使用

##### 案例

- 测试以上的API

```python
    def QObjectDelete(self):
        obj1 = QObject()
        self.obj1 = obj1 # 加一个self.obj1，会导致window运行期间，不会被打印
        obj2 = QObject()
        obj3 = QObject()

        obj3.setParent(obj2)
        obj2.setParent(obj1)

        obj1.destroyed.connect(lambda : print('obj1被释放！'))
        obj2.destroyed.connect(lambda: print('obj2被释放！'))
        obj3.destroyed.connect(lambda: print('obj3被释放！'))

        # del obj2
        obj2.deleteLater() # 稍后删除
        print(obj1.children())
```

输出：

```python
[<PyQt5.QtCore.QObject object at 0x000001E2EB275670>]
obj2被释放！
obj3被释放！
obj1被释放
```

说明：

- obj2由于稍后删除，所以 obj1.children() 先执行
- obj2稍后删除已被删除，打印 “obj2，obj3被删除”
- 程序执行完毕以后，obj1被释放

```python
# ****************案例***************开始
label1 = QLabel(self)
label1.setText('社会你阳哥！')
label1.move(100, 100)

# del label1 # 即使在这个地方删除label1，可是在窗口中仍然会显示label1的内容
# label1.deleteLater() # 使用延迟删除怎会彻底删除label1的控件

label2 = QLabel(self)
label2.setText("任何话不多")
label2.move(200, 200)

btn = QPushButton(self)
btn.setText('点我')
btn.move(300, 300)

# for widget in self.findChildren(QLabel):
#     print(widget)

for widget in self.children():
    if widget.inherits("QLabel"):
        widget.setStyleSheet("background-color: red;")
# ****************案例***************结束
```



#### 2.8 事件处理

##### API

- childEvent()
- customEvent()
- eventFilter()
- installEventFilter()
- removeEventFilter()
- event()

##### 应用场景

- 事件机制
  - 相比较于信号与槽机制
    - 信号与槽机制是对事件机制高级封装
    - 事件机制更偏底层（远离用户）
- 拦截事件、监听特定行为

![image-20220816213207954](https://raw.githubusercontent.com/lqyspace/mypic/master/PicBed/202208162132170.png)

假设操作系统上运行一个应用软件（QtGui程序）：

1、一个应用程序（Qt）对应有一个QApplication对象

2、一个应用程序中会有几个消息队列

3、应用程序中的消息循环就是在处理这些消息队列

用户的操作会产生各种事件消息。

第一个接收到消息的就是操作系统，操作系统会将消息分发到对应的应用程序“消息对列”中。

系统产生的消息组成的队列：鼠标事件，键盘事件，拖放事件

程序内部产生的消息组成的队列：定时器事件，绘屏事件

消息循环：1、不断地按顺序检测消息队列中是否有消息；2、如果发现“事件消息”，则会包装成“QEvent对象”进行分发处理

将事件接受者（receiver）和事件对象（evt）传递给QApplication对象的notify（receiver，evt）方法。（notify此方法负责事件的分发，发送给对应的对象进行处理）

分发给receiver对象的event(evt)方法，此方法会根据evt的事件类型分发给receiver具体的事件函数。

##### 案例

测试以上API。

```python
import sys

from PyQt5.QtWidgets import QWidget, QApplication, QPushButton
from PyQt5 import QtCore, QtGui
from PyQt5.QtCore import QEvent


class App(QApplication):
    def notify(self, a0: QtCore.QObject, a1: QtCore.QEvent) -> bool:
        """
        函数参数中的冒号是参数的类型建议符，告诉程序员希望传入的实参类型（这个类型也可以是自己定义的类 ）
        -> bool 是函数返回值的类型建议值，用来说明该函数返回的是什么类型
        类型建议符并非强制规定和检查，也就是说即使传入的实际参数与建议符不符，也不会报错
        :param a0:
        :param a1:
        :return:
        """
        if a0.inherits("QPushButton") and a1.type() == QEvent.MouseButtonPress:
            print(a0, a1)
        return super().notify(a0, a1)


class Btn(QPushButton):
    def event(self, e: QtCore.QEvent) -> bool:
        if e.type() == QEvent.MouseButtonPress:
            print(e)
        return super(Btn, self).event(e)

    def mousePressEvent(self, e: QtGui.QMouseEvent) -> None:
        print("鼠标被按下了......")
        super(Btn, self).mousePressEvent(e)


if __name__ == '__main__':
    app = App(sys.argv)
    window = QWidget()

    btn = Btn(window)
    btn.move(100, 100)
    btn.setText('按钮')


    def cao():
        print("按钮被激活了")


    btn.pressed.connect(cao)

    window.show()
    sys.exit(app.exec_())

```



#### 2.9 定时器

##### API

- startTime(ms, Qt.TimerType)-> timer_id
  - 开启一个定时器
  - Qt.TimerType
    - Qt.PreciseTimer：精确定时器，尽可能保持毫秒准确
    - Qt.CoarseTimer：粗定时器，5%的误差间隔
    - Qt.VeryCoarseTimer：很粗的定时器，只能到毫秒级
  - timer_id：定时器的唯一标识
- killTime(timer_id)：根据定时器ID，杀死定时器
- timerEvent()：定时器执行事件



##### 应用场景

- 轮询
- 倒计时

##### 案例

1、创建一个窗口，并设置一个子控件QLabel

要求：展示10秒的倒计时，倒计时结束，就停止计时

2、创建一个窗口，通过定时器不断增加该窗口的尺寸大小
