## 第二十二章 Maven

### 22.1 Maven 基础

#### 基础介绍

自动化构建工具Maven

读：咩温，不读：妈温

​		以前我们在eclipse等编译器编写java程序的时候都需要在lib目录下手动添加jar。

![image-20220705170514363](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705170514363.png)

a project object model(POM)

![image-20220705170612089](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705170612089.png)

**Maven两大核心**：

> 1. 依赖管理：就是对jar管理
>
> 2. 项目构建：项目编码完成之后，对项目相应的操作
>
>    如编译、测试、打包、部署等等

#### Maven的安装配置

https://maven.apache.org/download.cgi

![image-20220705171041285](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705171041285.png)

安装需要java环境

配置可以看闫哥的圈套手把手配置教程

https://juejin.cn/user/915274236957640/posts

![image-20220705183651973](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705183651973.png)

![image-20220705183703397](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705183703397.png)

#### Maven的核心配置

![image-20220705184023671](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705184023671.png)

![image-20220705184744734](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705184744734.png)

<scope></scope>(作用域)

指定改jar包的作用范围

![image-20220705185056256](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705185056256.png)

比如你在A地有个女朋友，然后你去了B地，又交了一个女朋友，过年的时候你回家，你把B第女朋友带到A地，两个女朋友就会打架（冲突）

在项目中有servlet，部署到tomcat上也有servlet的话就会冲突

换句话说项目maven里的servlet作用域只有在clean、compile、package等等的地方。

scope中可以填写的一些值

![image-20220705185733078](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705185733078.png)

> 依赖冲突：

![image-20220705190056456](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705190056456.png)

比如下面的情况，两个包的bean的版本号不对

![image-20220705190814186](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705190814186.png)

1) 第一优先声明原则

​		谁先定义的用谁的（pom文件中的顺序）

​		![image-20220705191244799](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705191244799.png)

2. 直接声明使用

​	在pom文件中声明bean相关的依赖，然后自己选择版本，Maven中的bean就是自己定义的版本

3. 排除依赖

​	显示的声明不依赖哪个

![image-20220705191620429](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705191620429.png)

4. 版本依赖

![image-20220705191951605](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705191951605.png)

> IDEA里的应用

![image-20220705192226425](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705192226425.png)

了解：

![image-20220705192518855](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705192518855.png)

### 22.2 Maven 高级

#### Nexus介绍

![image-20220705193434966](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705193434966.png)

有些公司会有自己的Maven仓库并且不希望开源的，这个以后工作可能会用到

![image-20220705193805933](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705193805933.png)

![image-20220705193814681](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705193814681.png)

![image-20220705193939820](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705193939820.png)

![image-20220705194056121](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705194056121.png)

代理仓库没有看，如果需要了解上传jar包到maven仓库的可以查看闫学长的这篇博客：

https://juejin.cn/post/6989210238834442276

由此联想到打包的时候需要注意的问题：
https://juejin.cn/post/6989212502982656031

（先人指路）

git上传也需要隐藏一些比如数据库账号密码之类的东西（如果把自己的数据库地址开源了那问题就大了）

后面git教程里会有

#### 私服搭建应用

### 22.3 Maven 应用

#### 聚合工程介绍

所谓聚合项目，实际上就是对项目分模块，互联网的项目一般来说按业务分（订单模块、VIP模块、支付模块、CMS模块...），传统的软件项目，大都采用分层的方式（Dao、Service、Controller):

![image-20220705195435282](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705195435282.png)

![image-20220705195941032](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705195941032.png)

![image-20220705200129938](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705200129938.png)

![image-20220705200219841](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705200219841.png)![image-20220705200334118](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705200334118.png)

![image-20220705200648344](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705200648344.png)

创建子模块：

![image-20220705201017774](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705201017774.png)

![image-20220705201045683](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705201045683.png)

![image-20220705201122095](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705201122095.png)

![image-20220705201338068](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705201338068.png)















































#### 聚合功能实战