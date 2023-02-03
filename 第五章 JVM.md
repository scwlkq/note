## 第五章 JVM

![image-20220705160828259](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tstsimage-20220705160828259.png)

### 5.1 初识JVM

#### 5.1.1 JDK、JRE和JVM

![image-20220705161616068](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705161616068.png)

工具集（上图最上面一行）：

java能运行.class文件，javac能够编译我们的源码文件，jar打包......

Java有句话：

Write once， run anywhere！

JVM可以屏蔽一些操作系统的区别

#### 5.1.2 源码到类文件

比如我们先准备一个Person.java

执行

```java
javac Person.java
```

![image-20220907134824141](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907134824141.png)

会在目录下面得到一个class文件

JVM只认class文件

JVM是java的一个进程

但是打开会发现全部是乱码

![image-20220907134612850](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907134612850.png)

可以借助sublime（因为class文件是一个二进制的流文件）

![image-20220907134718571](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907134718571.png)

16进制

![image-20220907135101982](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907135101982.png)

#### 5.1.3 机制

类加载机制：类文件怎么交给jvm去运行

![image-20220907135214465](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907135214465.png)

![image-20220907135701017](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907135701017.png)

![image-20220907135806787](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907135806787.png)

![image-20220907135850908](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907135850908.png)

#### 5.1.4 类加载器

![image-20220907140406178](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907140406178.png)

![image-20220907140555944](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907140555944.png)

### 5.2 运行时数据区

#### 5.2.1结构

有些数据是和java虚拟机进程一起的，创建而创建，销毁而销毁

有一些是随着线程在一起的

![image-20220907142921505](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907142921505.png)

#### 5.2.2 方法区

![image-20220907143429626](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907143429626.png)

由于多个线程都是从方法区获取数据，方法区是**线程非安全**的

#### 5.2.3 堆

一切皆对象，所以堆的内存占用最大

![image-20220907143636120](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907143636120.png)

#### 5.2.4 Java虚拟机栈

上面两个都是进程的，接下去的是线程的

（补充：比如int a = 1；是和对线一起存储在堆里的，因为并不属于常量所以不存储在方法区）

**如何描述许多线程跑的状态呢？**

![image-20220907145100673](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907145100673.png)

![image-20220907145129330](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907145129330.png)

![image-20220907145148119](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907145148119.png)

![image-20220907145257609](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907145257609.png)

![image-20220907145426150](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907145426150.png)

溢出会报错：**非常经典的StackOverFlow**

![image-20220907145535692](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907145535692.png)

> 栈帧

![image-20220907145832302](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907145832302.png)

有点抽象，举个例子：

在刚才的目录下执行：

```java
javap -c Person,class > Person.txt
```

（jdk自带的反编译工具）

比如这个两数相加的cal方法：

Person文件代表的字节码指令：

![image-20220907150840794](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907150840794.png)

原来的代码：

![image-20220907150948957](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907150948957.png)

![image-20220907151019498](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907151019498.png)

![image-20220907151030417](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907151030417.png)

**仔细看这张图！看完你就懂了**

![image-20220907151105236](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907151105236.png)

两个加的数字先从操作数栈弹出，再相加放回操作数栈，然后再弹出，赋值给局部变量表

#### 5.2.5 程序计数器

线程暂时失去cpu执行权， 下次还需要从这个地方开始执行

![image-20220907151535085](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907151535085.png)

![image-20220907153124958](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907153124958.png)

![image-20220907153219806](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907153219806.png)

#### 5.2.6 本地技术栈

![image-20220907153416443](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907153416443.png)

![image-20220907153512123](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907153512123.png)

### 5.3 JVM内存模型

#### 5.3.1 模型

![image-20220907170323970](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220907170323970.png)

 内存分布：

堆：3000MB

Old：2000MB

Eden：800MB

S0：100MB

S1：100MB

策略：

1、Old区存GC>15的（一般是15）

2、Young区对象一般朝生夕死

3、对象先进来到Eden区，到Eden区不够放就会触发GC，回收掉一些没有用的对象，这时候会有个问题，就是内存空间不连续，这时候会将GC后剩下的对象存放到S0，然后等下一波GC，Eden区的第二轮没被回收的和S0的一起被放到S1。S0和S1反复进行......

4、新进来的对象太大，“担保机制”，Eden区向Old区借地方

#### 5.3.2 Young区

![image-20220909152141869](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909152141869.png)

![image-20220909152225321](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909152225321.png)

换取10%的空间是为了解决空间碎片的问题

#### 5.3.3 Eden区

![image-20220909152350352](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909152350352.png)

不够分配新的对象，就会频繁触发GC，占用线程

#### 5.3.4 Survivor区

![image-20220909152455473](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909152455473.png)

#### 5.3.5 结合工具体验和验证

![image-20220909154024897](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909154024897.png)

![image-20220909160424253](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909160424253.png)

java自带的工具+插件

![image-20220909160450964](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909160450964.png)

![image-20220909160516898](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909160516898.png)

![image-20220909160621766](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909160621766.png)

设置一个一直重复造对象的函数

![动画](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/动画.gif)

也可以通过一些命令，下面会有，控制每个区域的大小

超过内存就报错

### 5.4 垃圾回收

#### 5.4.1 如何判断一个对象是垃圾

![image-20220909161444779](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909161444779.png)

#### 5.4.2 分代收集算法

![image-20220909161736166](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909161736166.png)

会有空间碎片的问题

![image-20220909161807389](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909161807389.png)

![image-20220909161818890](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909161818890.png)

会有一半的空间不能用

![image-20220909161842443](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909161842443.png)

![image-20220909162055534](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909162055534.png)

#### 5.4.3 垃圾收集器

![image-20220909162134065](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909162134065.png)

![image-20220909162243375](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909162243375.png)

![image-20220909162440836](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909162440836.png)

![image-20220909162505374](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909162505374.png)

（号称＜10ms，了解即可）

#### 5.4.4 有关知识

![image-20220909162552151](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909162552151.png)

![image-20220909162617655](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909162617655.png)

![image-20220909162629490](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909162629490.png)

![image-20220909162642359](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909162642359.png)

### 5.5 工欲善其事必先利其器

#### 5.5.1 JVM参数

![image-20220909162708284](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909162708284.png)

比如java -version不随版本变化而变化的，就是标准参数

-x参数：

![image-20220909162916136](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909162916136.png)

-xx参数：

![image-20220909162943341](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909162943341.png)

其他：

![image-20220909163007858](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163007858.png)

![image-20220909163023918](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163023918.png)

或者java -jar 的时候跟在后面

#### 5.5.2 JVM命令

![image-20220909163346324](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163346324.png)

jsp

![image-20220909163138232](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163138232.png)

![image-20220909163220591](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163220591.png)

jinfo

![image-20220909163239004](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163239004.png)

jstat

![image-20220909163311484](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163311484.png)

![image-20220909163321601](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163321601.png)

jmap

![image-20220909163401708](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163401708.png)

#### 5.5.3 常用工具

![image-20220909163459995](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163459995.png)

jconsole

![image-20220909163531073](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163531073.png)

jvisualvm上面说了

arthas：差不多，命令行交互式界面

mat/perfma(per er ma)

### 5.6性能优化

#### 5.6.1 OOM

![image-20220909163806037](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163806037.png)

可以用mat定位oom

![image-20220909163913982](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163913982.png)

#### 5.6.2 GC优化

打开GC日志

![image-20220909163955391](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909163955391.png)

看吞吐量和停顿时间

用gceasy看

![image-20220909164105120](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909164105120.png)

#### 5.6.3 性能优化

![image-20220909164229338](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220909164229338.png)

### 5.7 一些问题

