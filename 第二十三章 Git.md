**这里非常建议看闫学长这个教程，非常人性化，我这里就简单记记**

![image-20220705202551021](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705202551021.png)

![image-20220705202709321](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705202709321.png)



Git简介
Git (全局信息追踪器)。
Git是一个分布式版本控制工具, Git的使用中央仓库不是必须的,用户本地就是- -个完整的版本仓库,代码的
前进、回退、删除等等操作都可以直接在本地进行,不需要中央仓库。但是,在实际操作中,为了能够和其他同事
快速沟通以及合并代码, -般还是会搭建一个中央仓库。 Git对分支的管理非常友好,可以快速创建或者合并分
Svn集中式的版本控制工具, Svn中,必须要有中央仓库,所有的版本信息都保存在中央仓库中,代码的前
进、回退、删除等等操作都需要在中央仓库中进行,用户本地保存的只是版本仓库的一个副本, Svn中的分支非常
臃肿。|

![image-20220705202907530](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705202907530.png)

SVN一个服务挂掉了，就完蛋了。

但是git是分布式的，他会每个节点都存储值

## status和diff命令

![image-20220705203459620](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705203459620.png)

![image-20220705203538930](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705203538930.png)

![image-20220705203556654](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705203556654.png)

![image-20220705203615925](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705203615925.png)

diff就是查看未上传的文件和本地修改的文件有哪些区别

## log

![image-20220705203914591](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705203914591.png)、

![image-20220705204008635](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705204008635.png)

## 回退

![image-20220705204102556](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705204102556.png)

![image-20220705204230075](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705204230075.png)

## 工作区和暂存区

![image-20220705204507894](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705204507894.png)

![image-20220705204526042](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705204526042.png)

![image-20220705204552722](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705204552722.png)

## 分支

> git branch 查看分支

![image-20220705204744080](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705204744080.png)

原来叫master的来着，都一样

> git branch dev1

![image-20220705204913683](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705204913683.png)

> git checkout dev1

![image-20220705205001707](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705205001707.png)

> git checkout -b dev2

创建分支并且切换到dev2中

> 合并分支

切换到master分支，然后merge

![image-20220705205355275](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705205355275.png)

> git branch -d dev1

删除分支

## 冲突

![image-20220705210011265](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705210011265.png)

总结：两个分支不一样的交给我的话，决定权在我

因为分支在交了之后就没有意义了，就可以删除了

![image-20220705210236662](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705210236662.png)

## Bug分支

![image-20220705210558936](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705210558936.png)

![image-20220705210713881](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705210713881.png)

![image-20220705210742928](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705210742928.png)

![image-20220705210844007](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705210844007.png)

![image-20220705211229685](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705211229685.png)







































