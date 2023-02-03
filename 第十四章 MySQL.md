## 第十三章 MySQL

### 基本介绍

#### 1）一些命令

> 查看mysql服务器当前有多少连接 

![image-20220811180701017](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811180701017.png)

> 最大连接数

![image-20220811180721251](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811180721251.png)

> 查询的参数有两个级别，一个session级别，一个global级别

![image-20220811181035494](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220811181035494.png)

> 缓存模块

将数据存储到内存中，但是有时候多个空格就会不一样，不建议使用。

> 可以查看上一条sql语句执行时间是多少

![image-20220812122058379](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812122058379.png)

> 查询类型

模拟客户端的sql执行路径

![image-20220812122347912](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812122347912.png)

#### 2）存储引擎

查询可用引擎

![image-20220812123335561](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812123335561.png)

> Mysql版本

![image-20220812124039831](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812124039831.png)

InnoDB有很多有点

600、700MB内存大都是400MB的索引

#### 3）InnoDB内存架构

![image-20220812141940095](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812141940095.png)

以页为单位，一个单位存储16kb

预读，就是将一整页的存在缓存中，防止后面的IO比较慢

内存中有个模块教Buffer Pool

在磁盘中更新了，但是内存中没有更新的就叫**脏数据**

![image-20220812143305702](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812143305702.png)

##### BufferPool

BufferPool用完了就LRU等等的淘汰机制

上图有个changebuffer，就是存插入的数据的，merge操作会将内存里的数据存到磁盘中

索引在内存中（提高热点数据页的访问速度）

logbuffer的作用就是防止宕机啥的，记录数据内容防止宕机

![image-20220812145636601](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812145636601.png)

redo的存储结构

![image-20220812145914285](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812145914285.png)

#### 4）InnoDB的磁盘结构

![image-20220812151628345](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812151628345.png)

事务日志：

redo log保证持久性

undo log保持原子性（通过回滚等操作逻辑回滚）

![image-20220812152610244](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812152610244.png)

![image-20220812153247428](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812153247428.png)

还有一种更新日志 binlog

![image-20220812152912559](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812152912559.png)

（DDL， define语句，DML，增删改查语句）

实现主从复制的逻辑：

主一般都是写，从是读，那么应该怎么保持一致呢？

![image-20220812153331994](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812153331994.png)

总之就是，凡事防止突然宕机

### 索引深入剖析

#### 1）索引是什么

首先索引有多牛逼？

![image-20220812154811745](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812154811745.png)

可以看到差不多两秒左右

创建索引的结构:

![image-20220812154906021](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812154906021.png)

500w行的数据，创建索引耗时15s

![image-20220812155016532](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812155016532.png)

一样的语句可以看到，只花了0.042s

![image-20220812155107788](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812155107788.png)

**只能说是nb**

![image-20220812155203195](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812155203195.png)

![image-20220812155221624](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812155221624.png)

![image-20220812160537582](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812160537582.png)

还有一个索引，叫**主键索引**，这个在你刚创建主键的时候就有了，在unique的基础上加上not null的要求

 **fulltext索引**用在比如长文本的里面，在查询的时候格式也改变了

```sql
select * from fulltext_test
where match (content) against('value ' IN NATURAL LANGUAGE MODE); 
```

#### 2）索引的存储结构

索引的内容如果用有序数组存储的话，查找没事，但是如果要更新数据的话就需要大规模移动数据

所以就想到**`可以二分查找的链表`**

BST

Binary Search Tree

##### 二叉查找树

![image-20220812173358218](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812173358218.png)

但是

![image-20220812173506074](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812173506074.png)

##### 平衡二叉树

这个树不太平衡，查找的话时间复杂度太高

就有了**平衡二叉树**（AVL TREE-Balanced Binary Search Tree)

左右子树深度差不超过1

![image-20220812173957384](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812173957384.png)

##### B树

更加优化一些：B树

![image-20220812175234224](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812175234224.png)

注意右上方的N和N+1的意思

![image-20220812175418986](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812175418986.png)

##### B+树

B树已经很牛逼了，但是MySQL里用的是另外一种，B+树

![image-20220812175508147](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812175508147.png)

![image-20220812180216383](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220812180216383.png)

注意到叶子节点也是有箭头指向的，对于查找范围数据很友好，不需要从头开始查询。

可以看到索引的方式其实有两种：

![image-20220813135009711](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813135009711.png)

1. hash

![image-20220813135020422](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813135020422.png)

时间复杂度O（1）

重复值很多就会有hash冲突

2. BTREE

但是发现InnoDB里面其实是没法选择Hash的

#### 3）索引在存储引擎中的实现

> 如何查看mysql在服务器的位置？

![image-20220813135951828](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813135951828.png)

MyISAM和InnoDB

相关内容可以查看博客：

https://juejin.cn/post/6844903616902332430

在服务器对应的地址里，cd你的数据库名字可以看到这



![image-20220813142518205](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813142518205.png)



##### MyISAM

分两种，一个是主键索引，一个是辅助索引

检索的时候没有区别，都是先检索myi文件B+树上得到**磁盘地址**

![image-20220813142705860](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813142705860.png)



##### InnoDB

索引即数据

![image-20220813142807328](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813142807328.png)

叶子结点存放数据

**主键值**对数据进行排序（如上图）

> 聚集索引，决定了数据存放顺序
>
> innodb里面主键索引一定是聚集索引
>
> 当主键索引不存在呢？
>
> 会优先选unique not null、等等的字段，实在没有，默认会有ROWID
>
> **所以**一张表不可能没有索引，也不可能没有聚集索引

当有其他的索引（二级索引）（辅助索引）（在innodb里和主键索引有清晰区别）

![image-20220813155157417](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813155157417.png)

（先从辅助索引查到主键索引，然后再去主键索引再去找到）

（这个时候没有主键索引的话，就到上面定义聚集索引的情况了）

#### 4）索引的创建和使用原则

这时候就有聪明的小明问：**那我每个字段都建立索引，查询速率不是嘎嘎快？？**

当然不是

##### 索引创建原则

###### 列的离散度

（distinct count）/count

简而言之就是，**重复字段越少越好**

相信也很容易理解

举例：

500w条数据

gender和name字段

![image-20220813160520889](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813160520889.png)

如果在gender上建立索引

![image-20220813160551929](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813160551929.png)

预估要扫描250w行才能得到数据！

而，在name上建立索引

![image-20220813160643428](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813160643428.png)

预估扫描1行！

在查找的效率差不多和全表扫描差不多时，系统会放弃 索引查找

###### 联合索引最左匹配

第一，联合，就是多个字段联合在一起

```sql
ALTER TABLE user
add INDEX `索引名字` (`字段1`, `字段2`...);
```

第二，最左匹配

举例：

```sql
ALTER TABLE user
add INDEX `index1` (`a`, `b`, `c`);
```

1) SELECT * FROM user WHERE a = 1 AND b = 2 AND c = 3;

2. SELECT * FROM user WHERE b = 1 AND c = 2 AND a = 3;

3. SELECT * FROM user WHERE a = 1;

4. SELECT * FROM user WHERE b = 2 AND c = 3;

   1,2,3可以，4用不到（具体可以在语句前面加上explain）

   1符合条件

   2会有个OPTIMIZER优化器，自动调节语句顺序

   3这个就是最左匹配的意思

![image-20220813162442998](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813162442998.png)

​	4就是没有用到这个索引的案例（中间也不能中断）

所以，当查询条件是name、name和phone的时间比较长的时候，只要建立后面的联合最左匹配索引就行

###### 覆盖索引

先介绍回表

> 像上面我们InnoDB的情况，当查询了辅助索引之后回再拿着主键去查询主键索引，这就是回表，增加了性能消耗

避免回表，比如

```sql
EXPLAIN SELECT name, phone FROM user WHERE name = 'a' AND phone = '133333';
```

![image-20220813195215052](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813195215052.png)

Using index说明覆盖了索引

如果改成*，当然不能覆盖索引，没有索引的就必须到主键索引里去找了

**（这就是为什么阿里员工手册里写的，sql语句不用*，要写全）**

![image-20220813195938168](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813195938168.png)

7前缀

8无序，插入比较乱

![image-20220813200253542](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813200253542.png)

3：![image-20220813200338573](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813200338573.png)

（从左往右找的，前面给个%不知道查哪个字符串）

4：不确定，是由Optimizer优化器决定的

Cost Based Optimizer(CBO)

根据I/O、CPU成本

### MySQL事务与锁机制

#### 1）什么是事务

事务是DBMS执行过程中的一个**逻辑单位**，有一个有限的数据库操作序列构成。

事务的四大特性：(ACID)

1. 原子性（Atomicity）undo log 失败了要回滚
2. 一致性（Consistent) 事务前后都是合法的
3. 隔离性(Isolation) 语句之间不相互影响
4. 持久性(Durable) 崩溃恢复后还在 redo log

![image-20220813202809101](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220813202809101.png)

#### 2）事务并发带来的问题

不可重复读：第一个事务查询，第二个a了。第一个事务再查询发现结果不一样（a=更新、删除）

幻读：同上（a=插入）

#### 3）MVCC

第一种：在读取数据前，进行加锁，阻止其他事务对其修改，Lock Based Concurrency Control（LBCC）

​			  缺点：太暴力，读就不允许写，降低并发性能

第二种：生成一个数据请求时间点的一致性数据**快照**，并且用这个快照来提供一定级别（语句级别或事务级）的一			 致性读取（MVCC）在<span style="color:red">Multi Version Concurrency Control</span>	

MVCC：

![image-20220814192930992](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220814192930992.png)

**只要记住是当前的版本就行，过去的版本查询到的东西不一定会改变！**

#### 4）锁的基本类型

![image-20220814193923308](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220814193923308.png)

##### 行锁

![image-20220814194142825](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220814194142825.png)

![image-20220815153308642](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220815153308642.png)

![image-20220815153248504](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220815153248504.png)

tip：

![image-20220815153123311](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220815153123311.png)

这里可以通过查找变量，看看排它锁的最长时间

##### 表锁

![image-20220815155236268](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220815155236268.png)

意向锁的意思就是告诉其他事物，这张表有事务在用（并不是真的锁定这张表，只是个正在使用的标志）

#### 5）锁的原理

![image-20220818152956138](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818152956138.png)

![image-20220818153026170](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818153026170.png)

原理 就和之前那个图一样

![image-20220818153041369](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818153041369.png)

**先锁name，再锁id**

#### 6）行锁算法

![image-20220818194445175](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818194445175.png)

![image-20220818195222951](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818195222951.png)

![image-20220818195139392](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818195139392.png)

比如这里查询6之后，（4,7）之间就被锁定了，再插入5,6就会失败

比如查询>20的，锁定的就是最右边的区间（10，正无穷），再插入11也不行

临建锁是前面两个的加起来

![image-20220818200128300](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818200128300.png)

#### 7）事务隔离级别的总结

2022/8/18 没看懂

![image-20220818201308066](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818201308066.png)

![image-20220818201323676](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818201323676.png)

![image-20220818201800120](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818201800120.png)

总结：

![image-20220818201915066](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818201915066.png)

### MySQL的优化思路和工具

![image-20220818202049996](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818202049996.png)

#### 1）优化配置

![image-20220818202117869](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818202117869.png)

当面试被问到MySQL应该怎么优化？

不只是加索引

1、服务端

客户端连接数

可以调大

![image-20220818202351858](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818202351858.png)

或者有一些字段会一直占用连接

可以设置最大超时时间

28800s是8个小时，可以适当调小

![image-20220818202423457](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818202423457.png)

2、客户端

在ORM的层面，Mybatis等等支持连接池或者自带**连接池**

还有一些连接池工具，比如阿里的Durid

连接池不能设置太大，Druid是8

#### 2）MySQL架构优化

1，缓存，redis等等

![image-20220818205555353](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818205555353.png)

数据一致性 bin log

基于主从复制的读写分离

![image-20220818205757500](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818205757500.png)

2，分库分表

并发访问压力

![image-20220818205906869](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818205906869.png)

一张表数据量过大

![image-20220818205944705](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818205944705.png)

#### 3）MySQL慢查询日志

DBA有时候会发现有些语句比较慢，他们怎么发现的呢？![image-20220818210526129](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818210526129.png)

![image-20220818210102144](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818210102144.png)

![image-20220818210149521](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818210149521.png)

![image-20220818210229076](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818210229076.png)

然后重启mysql

```sql
service mysqld restart
```

比如这里的设置的超时时间是2s

![image-20220818210438392](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818210438392.png)

我们可以在上面指定的日志里面看到这个语句的相关信息

![image-20220818210714458](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818210714458.png)

我们可以使用一个工具，在mysql安装包的bin目录下，mysqldumpslow

![image-20220818211011087](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818211011087.png)

还有一个工具，是谷歌程序猿集成到mysql中的

![image-20220818211155936](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818211155936.png)

**注意，可以看到这些默认的性能分析工具都是关闭的，因为会消耗性能，所以一般测试完都要及时关闭**

![image-20220818211407965](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818211407965.png)

然后根据语句id查看具体语句的情况

![image-20220818211530957](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818211530957.png)

也可以：

![image-20220818211443743](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818211443743.png)

> 一些有用语句

客户端的一个一个的连接对于服务端来说是一个一个的线程

![image-20220818212531465](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818212531465.png)

#### 4）MySQL执行语句

explain的各个字段解释：

![image-20220818213315112](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818213315112.png)

（当然id也会有一样的，一样的话就从上到下，不一样就从下到上）

![image-20220818213608998](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818213608998.png)

![image-20220818213902769](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818213902769.png)

![image-20220818215023777](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818215023777.png)

#### 5）MySQL引擎和表结构的优化

> 存储引擎

查询和加入比较多的，MyIsam

数据一致性，并发，InnoDB

> 表结构

![image-20220818215630999](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818215630999.png)

![image-20220818220103532](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818220103532.png)

#### 6）MySQL优化总结

优化方面：

![image-20220818220213301](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818220213301.png)

**从上往下，成本越大，效益越低**

![image-20220818220521083](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20220818220521083.png)







# 黑马Mysql

