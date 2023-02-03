# 第九章 Spring5高级应用及核心原理

**<u>有一些xml配置文件相关的基础太过冗长，可以跳过</u>**

## Spring框架初探

![image-20220706195558587](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706195558587.png)

![image-20220706195629223](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706195629223.png)

![image-20220706200018162](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706200018162.png)

针对组件进行具体分析

![image-20220706200204227](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706200204227.png)

![image-20220706200557824](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706200557824.png)

## Spring框架高级应用

**升职加薪重要点**

![image-20220822215609036]()

![image-20220709150956208](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220709150956208.png)

![image-20220709151113839](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220709151113839.png)

Spring官网

<https://spring.io/>

SpringFramework的核心

IOC和AOP



### IOC控制反转

Inversion of controller

本质上是种概念，是一种思想

比如你想吃包子，你不会自己去做而是去包子铺买

在程序中，包子就是对象

控制反转就是对对象控制权的转移，SpringIOC容器创建对象，然后将对象的使用权交出去

用对象的时候不用一直new，而是直接IOC造好了，直接拿

#### 1）基于XML配置文件方式实现 

##### 基本使用

代码的形式来演示：

新建一个Maven项目

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.1.17.RELEASE</version>
    </dependency>
</dependencies>
```

我们可以看到左边除了JDK1.8之外，还多了几个包

![image-20220709162752135](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220709162752135.png)

![image-20220709163837484](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220709163837484.png)

##### IOC获取Bean的方式有多种

> 基于xml

![image-20220709164126191](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220709164126191.png)

将需要被IOC容器管理的类型通过<bean>标签来注册

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 添加需要被容器管理的内容-->
    <bean class="com.ioc.pojo.UserBean" id="userBean"/>
</beans>
```

这里可以自行在UserBean类中的无参构造器里写一句话，标记一下，会发现运行`ApplicationContext`的时候会调用里面有的类的无参构造函数

没有无参构造的话，会报错但是

```java
/**
     * IOC的方式获取Bean
     */
    @Test
    public void fun2(){
        // 1.IOC的初始化操作
        ApplicationContext ac =
                new ClassPathXmlApplicationContext("applicationContext.xml");
        // 2.从容器中获取UserBean对象
        UserBean userBean = (UserBean) ac.getBean("userBean");
        userBean.say();
    }
```

问题来了，new的话一行解决了，你这里还特别麻烦？

上面的new就像是你自己，今天早上做包子，好吃，但是一直做就麻烦了，虽然商家（下面IOC）启动有点麻烦，但是人家是专业做包子的，做起来不会烦

**当new很多个对象的时候，效率会降低，这时候就要交给`ApplicationContext`来做**

> name形式

![image-20220709170151162](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220709170151162.png)

一个人有身份证号码也有姓名

一个Bean有id也有name

写法和上面一样

```java
/**
 * IOC的方式获取Bean
 */
@Test
public void fun2(){
    // 1.IOC的初始化操作
    ApplicationContext ac =
            new ClassPathXmlApplicationContext("applicationContext.xml");
    // 2.从容器中获取UserBean对象
    UserBean userBean = (UserBean) ac.getBean("userBean2");
    userBean.say();
}
```

> id和name的区别

好的

一个人有身份证号码也有姓名

一个Bean可以有多个id也有多个name

![image-20220709170635264](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220709170635264.png)

但是getBean的时候，id要都写进去，name只要写一个就行（奇奇怪怪）

**id只可以声明一个，但是name的话可以声明多个**

相当于上面的id即使用，隔开了仍然是一个名字

------------------

除此之外，还有一些方法可以找到重复的Bean里的唯一的那一个：

![image-20220709171152212](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220709171152212.png)

或者设置![image-20220709172138406](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220709172138406.png)

##### BeanFactory

![image-20220712134501305](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712134501305.png)

![image-20220713143923909](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713143923909.png)

##### 其他注入方式

此外还有静态工厂、动态工厂、构造注入和设值注入等等

#### 2）基于Java配置的实现方式（注解）

![image-20220713144828351](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713144828351.png)

![image-20220713144909293](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713144909293.png)

![image-20220713145134843](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713145134843.png)

![image-20220713152137488](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713152137488.png)

> Service也是可以改名称的

![image-20220713151144368](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713151144368.png)



> @Resource和@Autowired的区别

`@Resource`是jdk里的，可以根据名称和类型找

![image-20220713151643708](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713151643708.png)

![image-20220713151653948](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713151653948.png)

`@Autowired`是Spring里的，默认只能根据类型，可以加上@Qualifier根据名称

![image-20220713151051803](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tstsimage-20220713151051803.png)



> @ComponentScan

![image-20220713152015663](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713152015663.png)



> @Value

![image-20220713152411143](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713152411143.png)

![image-20220713152451166](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713152451166.png)

![image-20220713152531440](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713152531440.png)

T是泛型

![image-20220713152621528](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713152621528.png)

![image-20220713152643776](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713152643776.png)

![image-20220713152658633](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713152658633.png)

获取配置文件中的信息：

![image-20220713152750384](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713152750384.png)

![image-20220713153720546](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220713153720546.png)

123







































## Spring核心之AOP讲解

AOP面向切面编程

Spring有很好的实现

### 1）AOP前置知识之代理模式

![image-20220712205632752](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712205632752.png)

比如小张请周杰伦来开演唱会，周杰伦只负责唱歌，其他的事情交给代理来做

（proxy代理）

![image-20220712210227469](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712210227469.png)

#### 静态代理

直接将代理对象声明出来

![image-20220712211324778](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712211324778.png)

测试：

![image-20220712211457721](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712211457721.png)

![image-20220712211510027](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712211510027.png)

![image-20220712211636186](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712211636186.png)

#### 动态代理

静态代理灵活性不是很高，当有很多个接口的时候就不需要一个一个的写了

> JDK动态代理

如果目标的对象实现的有接口，就可以jdk动态代理

还是用上面的例子：

就是实现invoke方法

![image-20220712212545658](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712212545658.png)

里面就和静态类差不多

![image-20220712212816073](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712212816073.png)

> CGLIB动态代理

没有接口的

**我既然成不了你的女朋友，我就当你妈！**

继承了目标对象，重写部分方法

![image-20220712213031564](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712213031564.png)

![image-20220712213304624](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712213304624.png)

![image-20220712213427104](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712213427104.png)

然后就是实现方法

![image-20220712213657860](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712213657860.png)

测试：

![image-20220712213725990](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712213725990.png)

### 2）AspectJ实现

**AOP只是一种思想**

Spring的本身的AOP比较繁琐，但是其他人设计的AspectJ好用，Spring就拿过来了。

依赖：

![image-20220712214616350](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712214616350.png)

先写三个简单的方法

![image-20220712214858200](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712214858200.png)

![image-20220712215013068](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712215013068.png)

这个注解相当于放开AspectJ的使用

然后创建切面类：

> 切入点表达式

很多，挑几个常用的记

（真的真的好多好多，略看就行）

![image-20220712215525696](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712215525696.png)

然后建一个aspect类，下面写aspect类

![image-20220712215742003](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712215742003.png)

这个意思就是在这个对象的方法执行之前会执行什么方法

下面是实例：

![image-20220712220127827](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712220127827.png)

`within`是指定切入的对象的类型：

![image-20220712220237285](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712220237285.png)

（其他的大同小异，有兴趣可以去研究一下）

> 下面是通知类型的介绍：

@Before是前置通知前面已经讲过了

@AfterReturning：

![image-20220712220821308](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712220821308.png)

可以获取返回的对象

@Around

![image-20220712220844997](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220712220844997.png)

### 3）AOP源码浅析

 





























































































## Spring核心之事务管理

## SpringMVC之高级进阶

## Spring核心原理

## Spring集成篇

###  SpringMVC



### MyBatis



### SpringSecurity







### Shiro

![image-20220822103024557](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220822103024557.png)

> 第一个案例

![image-20220822103725335](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220822103725335.png)

456

















































### satoken

<https://sa-token.dev33.cn/doc>





































































## Spring高级应用及核心原理

