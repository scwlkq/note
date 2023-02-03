## 第十五章 Nginx

### 初步认识Nginx

![image-20220703163038876](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tstsimage-20220703163038876.png)

最原始的架构图——单体架构

![image-20220707145651062](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707145651062.png)

优点：简单

缺点：不能高并发，宕机了就寄了

公司的架构——常用

![image-20220707145750339](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707145750339.png)

一般的技术选型

![image-20220707150126261](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707150126261.png)

nginx可以做静态资源和负载均衡选择器

![image-20220707150540061](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707150540061.png)

Apache太老了，有一点点跟不上时代，重量级，比较繁琐，不支持高并发

IIS是微软的，不跨平台，没得说

Tomcat、Jetty、JBOSS是动态引擎，中间需要转化，有点费时

Lighttpd国外的nginx

![image-20220707151001686](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707151001686.png)

#### 1）nginx优点

响应、高峰时期响应更快

高扩展

高可靠

低内存消耗（花很少的钱，做很好的事）

支持高并发

支持热部署

更自由的协议

#### 2）相关概念介绍

##### 反向代理

先讲正向代理：

相当于平时上谷歌，必须要挂梯子才能上

![image-20220707152102599](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707152102599.png)

用户是知道这个代理服务的存在的，也知道目标服务器，需要用户配置

**正向代理**是一个位于客户端和原始服务器之间的服务器，为了从原始服务器取得内容，客户端向代理发送 一个请求并指定目标（原始服务器），然后代理向原始服务器转交请求并将获得的内容的返回给客户端。

![image-20220707152602076](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707152602076.png)

直接暴露8081不安全

用户是不知道有8081服务存在的，用户访问不需要配置

举个例子：

你（8081服务）想做好事，想把物资送出去但是不想让别人知道是谁送的，你可以捐口罩给慈善机构（代理服务）定向告诉慈善机构我这个东西要给谁，医院（用户）得到了物资，解决了当前的困扰，但是不知道是谁送的。

**反向代理**位于用户和目标服务器之间，但是对于用户而言，反向代理服务器就相当于目标服务器，即用户直接访问反向代理服务器就可以获得目标服务器的资源。同时，用户不需要知道目标服务器的地址，也无须在用户端作任何设定。

![image-20220707153439234](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707153439234.png)

##### 负载均衡

**负载均衡**称为Load Balance，其含义就是指将负载（工作任务）进行平摊、分摊到多个操作单元上进行运行，其含义就是指将负载（工作任务）进行平衡、分摊到多个操作单元上进行运行

![image-20220707153921846](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707153921846.png)

单体服务器是有上限的，一个人能力再强你也不能一天耕十亩地

![image-20220707154033027](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707154033027.png)

肯定不能让用户自己选，就相当于一个公司没有前台

![image-20220707154139018](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707154139018.png)

实现逻辑下面会讲

##### 动静分离

"动"指动态页面，不同请求结果不同 

"静"指静态页面，不会经常变化

![image-20220707154446371](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707154446371.png)

![image-20220707155003166](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707155003166.png)

![image-20220707155057243](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707155057243.png)

比如我想访问logo.png的静态资源，他就会去访问静态资源服务器9091，我想访问动态页面，就会将login.jsp请求发送给动态资源服务器

**动静分离**是指在web服务器架构中，将静态页面与动态页面或者静态内容接口分开不同系统访问的架构设计方法，进而提升整个服务访问性能和可维护性

#### 3）安装Nginx

linux环境安装

Debian

<https://nginx.org/en/download.html>

页面最下方

![image-20220707160224538](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707160224538.png)

![image-20220707160249545](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707160249545.png)我这里就选择Debian

![image-20220707160956029](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707160956029.png)

![image-20220707164329506](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707164329506.png)

运行nginx不报错算成功

![image-20220707164557798](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707164557798.png)

访问对应的IPV4地址就会看到nginx（默认80端口）

![image-20220707164653959](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707164653959.png)

 ![image-20220707165340578](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707165340578.png)

#### 4）常用命令

![image-20220707165815770](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707165815770.png)

##### **启动**

可以直接这么启动

![image-20220707165942255](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707165942255.png)

默认启动方式，会加载/usr/local/nginx/conf/nginx.conf

![image-20220707170140479](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707170140479.png)

-h可以查看帮助

可以看到如果我们需要指定启动的配置文件，我们可以-c

![image-20220707170501006](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707170501006.png)

比如：

```java
./sbin/nginx -c conf/nginx-new.conf
```

可以改一些端口号啥的

上面那个pid的文件也可以改

```java
./sbin/nginx -g "pid logs/nginx-new.pid;"
```

**注意这个分号**

可以查看进行编号的命令

```java
ps -ef|grep nginx 
```

grep是过滤的意思

##### **停止**

在sbin目录下

```java
nginx -S stop
```

![image-20220707172810070](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707172810070.png)

我们-h之后可以看到-s后面可以跟很多命令

如stop、quit、reopen、reload

下面是一种场景，我启动的时候-g了，指定pid文件，所以quit的时候也要指定pid文件要不然找不到

![image-20220707173200475](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707173200475.png)

**以下三种是信号量停止**

1）quit

当我们

```java\
ps -ef|grep nginx 
```

后，会发现

![image-20220707171535536](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707171535536.png)

有个master，有个worker

并且可以看到10714(worker)后面跟着个10713(master)

we can执行

```java
kill -quit 10713
```

然后master和worker就都寄了

![image-20220707172148100](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707172148100.png)

**（稍微等一下，我处理完这个需求就停）**

2）term

```java
kill -term 10713
```

![image-20220707172355935](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707172355935.png)

3）-9

```java
kill -9 10713
```

kill的是谁就只干掉它

![image-20220707172433061](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707172433061.png)

一般不用，因为要一个一个杀掉，太麻烦了

##### 重启

we can

（注意要在/sbin目录下）

```java
nginx -t
```

可以检查当前的配置文件有没有问题

![image-20220707173614547](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707173614547.png)

当然也可以-c指定filename

重启命令：（上面说过了）

```java
nginx -s reload
```

还可以（信号量）

```java
kill -hup 进程编号
```

#### 5）核心配置文件

##### 配置文件介绍

（了解即可）

![image-20220707174510212](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707174510212.png)

conf文件夹里就是配置文件

![image-20220707174601157](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707174601157.png)

进入这个nginx.conf

最核心的三块：

![image-20220707174727230](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707174727230.png)

![image-20220707174736346](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707174736346.png)

首先#是注释符号，语句以；结尾

![image-20220707175047571](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707175047571.png)

第一部分猜一猜也知道是啥了

第二部分是单个worker能处理的连接数

第三部分有web服务，有端口等

![image-20220707175820211](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707175820211.png)

index和error页面存放在下面的文件里

![image-20220707175628102](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707175628102.png)

##### 虚拟主机配置

因为主要是给后端看的，运维这里就跳跳 

![image-20220707194126309](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707194126309.png)

先创建三个文件夹

![image-20220707194305609](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707194305609.png)

在bbs、www、edu下面就可以vim一个index.html

![image-20220910143557991](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220910143557991.png)

然后在conf文件里，再增加了两个server就行

比如bbs的

 

（root那一行要以；结尾）

刚才设置了域名，有一个比较骚的办法不需要服务器配置域名，但是只能你电脑访问

![image-20220707195411177](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707195411177.png)

#### 6） **location的匹配规则**

![image-20220707195613628](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707195613628.png)

![image-20220707195834036](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707195834036.png)

（注意有个空格）

=进行普通字符串精准匹配

~代表执行一个正则表达式，区分大小写

~*代表执行一个正则表达式，不区分大小写

![image-20220910144300586](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220910144300586.png)

普通的location：

![image-20220707200439303](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707200439303.png)

比如我们现在整一个demo路径

![image-20220707201550823](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707201550823.png)

![image-20220707201740170](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707201740170.png)

![image-20220707201912128](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707201912128.png)

这个都是等价搜索条件的

这样也可以

![image-20220707202055361](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707202055361.png)

精准匹配优先级高于普通匹配

正则匹配：

![image-20220707204057552](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220707204057552.png)

![image-20220910150100794](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220910150100794.png)

### 实战

#### 1）实现反向代理

这里的写法是用的虚拟机，服务器的可以看这个<https://gitdoc.maiquer.tech/article/307331691409925>

首先没有反向代理

写一个简单controller类

```java
@RestController
public class UserController {

    @RequestMapping("/user/query")
    public String query(){
        return "success";
    }
}
```

![image-20220708132011457](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708132011457.png)

现在开始反向代理配置

```bash
cp nginx.conf nginx1.conf
```

先复制一个nginx1.conf到目录

为了方便

centos的小伙伴：

在server上面加一行

```bash
include extra/*.conf;
```

然后将server节点的内容全部删掉

（可以在非插入状态下使用num+dd快速删除几行）

如：

```bash
# 删除9行
9dd
```

debian的小伙伴发现已经写好了

![image-20220708135804173](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708135804173.png)

nginx.conf里面有一行

![image-20220708135831763](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708135831763.png)

意思是包含conf.d文件夹下面的所有.conf结尾的文件

![image-20220708144938698](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708144938698.png)

![image-20220708150251737](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708150251737.png)

![image-20220708150455907](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708150455907.png)

![image-20220708150648253](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708150648253.png)

另外：如果想要知道是谁发的（通过nginx获取到访问者的ip）

可以这么写：

![image-20220708151442454](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708151442454.png)

![image-20220708152023346](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708152023346.png)

上面不是header里面取出来的信息是nginx（代理服务）的地址

下面header里面是请方的地址

#### 2）实现负载均衡

![image-20220708154041330](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708154041330.png)

![image-20220708154438132](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708154438132.png)

或者想要取消掉这台机器，可以在端口号后面加 down

轮询的时候就不会访问到了

还有backup等等，weight表示权重

负载均衡的策略：

1. 轮询

![image-20220708160019077](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708160019077.png)

问题是session这种的话，轮询到下一个服务器上就会说是未登录状态，这样就有问题了

2. ip_hash

将这个ip的请求一直发给一个服务器

![image-20220708160223603](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708160223603.png)

3. weight

在对应服务器后面写weight=...

#### 3）实现动静分离

![image-20220708160855119](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708160855119.png)

可以建一个static目录，下面存放静态资源

然后：

![image-20220708161428567](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708161428567.png)

![image-20220708161546817](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708161546817.png)

就可以访问的到了

![image-20220708161945758](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708161945758.png)

还可以设置过期时间

![image-20220708162056640](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708162056640.png)

1d一天

也可以打开压缩

![image-20220910154552471](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220910154552471.png)

![image-20220910154656884](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220910154656884.png)

#### 4）Nginx原理介绍

![image-20220910191938090](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220910191938090.png)

> ![image-20220708163140888](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708163140888.png)
>
> ![image-20220911152337204](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911152337204.png)
>
> ![image-20220911152346257](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911152346257.png)
>
> ![image-20220911152401000](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911152401000.png)

![image-20220708163525960](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220708163525960.png)

![image-20220911153017177](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911153017177.png)



nginx是**热部署**的

![image-20220911153103182](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911153103182.png)

![image-20220911153227523](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911153227523.png)

请求来了，两种方案：

1.互抢

访问量较小的话，性能不高，访问量较大的时候ok

2.互斥锁

![image-20220911154041470](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911154041470.png)

抢到这把锁就去处理，没抢到的就去休息（防止一直抢到，配置锁的时候会有阀值）

适合访问量较小的

#### 5）Nginx参数化配置

原来的：

![image-20220911154504841](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911154504841.png)

![image-20220911154937599](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911154937599.png)

![image-20220911155114570](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911155114570.png)

![image-20220911155339248](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911155339248.png)

server块会单独配置到conf里面

#### 6）跨域访问

![image-20220911155708981](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911155708981.png)

![image-20220911160007338](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911160007338.png)

![image-20220911160016926](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911160016926.png)

![image-20220911160140263](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911160140263.png)

举例子：

跨端口访问会出错：

![image-20220911160652549](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911160652549.png)

加上上面的：

![image-20220911160712513](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911160712513.png)

![image-20220911161434453](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911161434453.png)

就比如，你要追个女生，你问这个女生有没有对象（发option）

这个女生说没对象（可以跨域），你疯狂追

说有对象（不可以跨域），你就不能追了

#### 7）防盗链配置

![image-20220911162200798](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911162200798.png)

![image-20220911162316923](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911162316923.png)

#### 8）搭载nginx高可用集群

![image-20220911162354083](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911162354083.png)

nginx作为门户，挂了就相当于被抹脖子了

1.准备两个虚拟机的环境

![image-20220911162648956](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911162648956.png)

![image-20220911162704357](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911162704357.png)

在/etc目录下有个keepalive的配置文件

![image-20220911162858014](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911162858014.png)

注意从节点的权重要比主节点低

启动服务：

![image-20220911163054333](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911163054333.png)

访问上面的VRRP虚拟地址可以访问

![image-20220911163258689](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911163258689.png)

![image-20220911163405541](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220911163405541.png)

撒花~

**nginx还是要注重多实战！！！**







































































































