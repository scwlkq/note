# Kafka

## Kafka基本介绍

LInkedin 18年就有5.6亿用户

![image-20230128173509675](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128173509675.png)

![image-20230128173517226](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128173517226.png)

一开始用的是RabbitMQ，但是流量太大了就研究出Kafka

Kafka是个作家 

![image-20230128173921666](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128173921666.png)

![image-20230128173953105](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128173953105.png)

每秒可收几十万条

内部设置了partition，可以直接扩容

数据存在磁盘持久化，有副本

> 作用
>
> 消息传递  异步通信，削峰，业务解耦
>
> 网站活动跟踪
>
> 指标监控
>
> 日志聚合

## Kafka安装

### 单机

#### 1、下载解压kafka

获取下载地址（点开具体版本）：
http://kafka.apache.org/downloads

下载Binary 二进制版本而不是源码

复制链接：
![20200709_142405.png](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefined759489235af54da5848bed8d35665d73.png)

```
cd /usr/local/soft
wget https://mirror.bit.edu.cn/apache/kafka/2.5.0/kafka_2.13-2.5.0.tgz
tar -xzvf kafka_2.13-2.5.0.tgz
cd kafka_2.13-2.5.0
```

#### 2、启动zookeeper

kafka需要依赖ZK，安装包中已经自带了一个ZK，也可以改成指定已运行的ZK。
如果改成指定的ZK需要修改修改 kafka 安装目录下的 config/server.properties 文件中的 zookeeper.connect 。这里使用自带的ZK。

后台启动ZK：

```
nohup ./bin/zookeeper-server-start.sh config/zookeeper.properties >> zookeeper.nohup &
```

检查zookeeper是否启动成功：

```
ps -ef|grep zookeeper
```

#### 3、启动kafka

修改相关配置

```
vim config/server.properties
```

有几个配置可以根据需求改

broker.id=0

![image-20230128233631577](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128233631577.png)

默认端口

![image-20230128234230004](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128234230004.png)

**对Kafka来说，日志就是数据，数据就是日志**

![image-20230128234326504](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128234326504.png)

partition

![image-20230128234414284](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128234414284.png)

消息留存时间

![image-20230128234448523](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128234448523.png)

消息保留大小

![image-20230128234511096](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128234511096.png)

![image-20230128234549245](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128234549245.png)

Broker ID启动以后就不能改了

```
broker.id=1
```

取消注释，改成本机IP：

```
listeners=PLAINTEXT://192.168.44.160:9092
```

num.partitions后面增加2行。
发送到不存在topic自动创建。允许永久删除topic。

```
num.partitions=1
auto.create.topics.enable=true
delete.topic.enable=true
```

后台启动kafka（kafka安装目录下）：

```
nohup ./bin/kafka-server-start.sh ./config/server.properties & 
```

日志在logs目录下

#### 4、创建Topic

创建一个名为gptest的topic，只有一个副本，一个分区：

```
sh bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic gptest
```

查看已经创建的 topic：

```
sh bin/kafka-topics.sh -list -zookeeper localhost:2181
```

#### 5、启动Producer

打开一个窗口，在kafka解压目录下：

```
sh bin/kafka-console-producer.sh --broker-list localhost:9092 --topic gptest
```

#### 6、启动Consumer

在一个新的远程窗口中：

```
sh bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic gptest --from-beginning
```

#### 7、Producer窗口发送消息

输入hello world 回车
![20200709_144635.png](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefined189abc49401042f4b25f4c5da0aefc06.png)

消费者收到了消息：
![20200709_144648.png](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedb052bfea9eec4cc79af2f6418880aa85.png)

------

删除kafka全部数据步骤：
1、停止每台机器上的kafka；
2、删除kafka存储目录（server.properties文件log.dirs配置，默认为“/tmp/kafka-logs”）全部topic的数据目录；
3、删除zookeeper上与kafka相关的znode节点；除了/zookeeper
4、重启kafka。

### 集群

注意：
单机的kafka和集群的kafka不要混用一个zk，否则会出现数据混乱的问题。

集群肯定要用到zk

#### 0、安装zookeeper

##### 1、下载ZK

https://zookeeper.apache.org/releases.html
这里下载二进制版本，不需要编译
以安装路径 /usr/local/soft为例

```bash
cd /usr/local/soft
wget https://mirror.bit.edu.cn/apache/zookeeper/zookeeper-3.6.1/apache-zookeeper-3.6.1-bin.tar.gz
```

##### 2、解压

```bash
tar -xzvf apache-zookeeper-3.6.1-bin.tar.gz
```

##### 3、修改配置文件

```bash
cd apache-zookeeper-3.6.1-bin/conf
cp zoo_sample.cfg zoo.cfg
```

端口号默认2181。
配置文件zoo.cfg里面的dataDir要修改，如果不改，需要创建这个目录

```bash
mkdir -p /tmp/zookeeper
```

##### 4、启动ZK

```bash
cd ../bin
./zkServer.sh start
```

zookeeper默认端口 2181

输出日志：

```bash
ZooKeeper JMX enabled by default
Using config: /usr/local/soft/apache-zookeeper-3.6.1-bin/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
```

查看是否启动成功

```bash
ps -ef | grep zookeeper
```

其他命令：
查看zk的运行状态

```bash
./zkServer.sh status
```

客户端链接zk

```bash
./zkCli.sh 
```

ls 查看节点

```bash
ls
```

get 获取节点数据和更新信息

```bash
get /节点名字
```

create 创建节点 e 临时节点 s 顺序节点

```bash
create [-s] [-e] path data acl 
```

删除节点

```bash
delete path [version] 
```



#### 1、下载解压kafka

```bash
cd /usr/local/soft
wget https://mirror.bit.edu.cn/apache/kafka/2.5.0/kafka_2.13-2.5.0.tgz
tar -xzvf kafka_2.13-2.5.0.tgz
cd kafka_2.13-2.5.0
```

#### 2、修改配置文件

复制3个配置文件

```
cd config
cp server.properties server1.properties 
cp server.properties server2.properties 
cp server.properties server3.properties 
```

修改配置文件中的broker.id分别为1、2、3
listeners这一行取消注释，端口号分别为9093、9094、9095
log.dirs分别设置为kafka-logs1、kafka-logs2、kafka-logs3（先创建）

```
mkdir -p /tmp/kafka-logs1 /tmp/kafka-logs2 /tmp/kafka-logs3
```

server1.properties 的配置：

```
broker.id=1
listeners=PLAINTEXT://192.168.44.161:9093
log.dirs=/tmp/kafka-logs1
```

server2.properties 的配置：

```
broker.id=2
listeners=PLAINTEXT://192.168.44.161:9094
log.dirs=/tmp/kafka-logs2
```

server3.properties 的配置：

```
broker.id=3
listeners=PLAINTEXT://192.168.44.161:9095
log.dirs=/tmp/kafka-logs3
```

#### 3、启动3个服务

第一步：
启动ZK。

可以使用alias

![image-20230128235919060](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230128235919060.png)

再启动kafka。

```
cd ../bin
./kafka-server-start.sh -daemon ../config/server1.properties
./kafka-server-start.sh -daemon ../config/server2.properties
./kafka-server-start.sh -daemon ../config/server3.properties
```

PS：如果遇到zk node exists的问题，先把brokers节点删掉（临时解决方案）。

#### 4、集群下创建Topic

在bin目录下，
创建一个名为gptest的topic，只有一个副本，一个分区：

```
sh kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic gptest
```

查看已经创建的 topic：

```
sh kafka-topics.sh -list -zookeeper localhost:2181
```

#### 5、集群下启动Consumer

在一个新的远程窗口中：

```
sh kafka-console-consumer.sh --bootstrap-server 192.168.44.161:9093,192.168.44.161:9094,192.168.44.161:9095 --topic gptest --from-beginning
```

#### 6、集群下启动Producer

打开一个新的窗口，在kafka解压目录下：

```
sh kafka-console-producer.sh --broker-list 192.168.44.161:9093,192.168.44.161:9094,192.168.44.161:9095 --topic gptest
```

#### 7、集群下Producer窗口发送消息

在生产者窗口输入hello world 回车

> 大致代码

依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.kafka</groupId>
        <artifactId>kafka-clients</artifactId>
        <version>2.3.0</version>
    </dependency>

</dependencies>
```

生产者：

```java
public class ProducerTest {

    public static void main(String[] args) {
        Properties pros=new Properties();
        pros.put("bootstrap.servers","192.168.44.161:9093,192.168.44.161:9094,192.168.44.161:9095");
        pros.put("group.id","gp-test-group");
        pros.put("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        pros.put("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
        // 0 发出去就确认 | 1 leader 落盘就确认| all 所有Follower同步完才确认
        pros.put("acks","-1");
        // 异常自动重试次数
        pros.put("retries",3);
        // 多少条数据发送一次，默认16K
        pros.put("batch.size",16384);
        // 批量发送的等待时间
        pros.put("linger.ms",5);
        // 客户端缓冲区大小，默认32M，满了也会触发消息发送
        pros.put("buffer.memory",33554432);
        // 获取元数据时生产者的阻塞时间，超时后抛出异常
        pros.put("max.block.ms",3000);
        Producer<String,String> producer = new KafkaProducer<String,String>(pros);

        for (int i =0 ;i<1000000;i++) {
            producer.send(new ProducerRecord<String,String>("mytopic",Integer.toString(i),Integer.toString(i)));
            // System.out.println("发送:"+i);
        }
        producer.close();
    }

}
```

消费者：

```java
public class ConsumerTest {
    public static void main(String[] args) {
        Properties props= new Properties();
        props.put("bootstrap.servers","192.168.44.161:9093,192.168.44.161:9094,192.168.44.161:9095");
        props.put("group.id","gp-test-group");
        // 是否自动提交偏移量，只有commit之后才更新消费组的 offset
        props.put("enable.auto.commit","true");
        // 消费者自动提交的间隔
        props.put("auto.commit.interval.ms","1000");
        // 从最早的数据开始消费 earliest | latest | none
        props.put("auto.offset.reset","earliest");
        props.put("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");

        KafkaConsumer<String,String> consumer=new KafkaConsumer<String, String>(props);
        consumer.subscribe(Arrays.asList("mytopic"));
        try {
            while (true){
                ConsumerRecords<String,String> records=consumer.poll(1000);
                for (ConsumerRecord<String,String> record:records){
                    System.out.printf("offset = %d ,key =%s, value= %s, partition= %s%n" ,record.offset(),record.key(),record.value(),record.partition());
                }
            }
        }finally {
            consumer.close();
        }
    }

}
```

## Kafka架构

整体架构图

![image-20230129000804469](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129000804469.png)

1.Broker

中介、或者代理

![image-20230129140703691](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129140703691.png)

2.Producer、Consumer、Message

Message和Record一个意思

消息传输需要序列化

上面的测试代码里面也可以看到对应的序列化

> 消息是consumer根据自己的处理速度从Broker拿的
>
> 有两种方式
>
> 第一种是pull，相当于你到快递站拿快递  异步（Kafka两种都支持）
>
> 第二种是push，相当于送货上门  阻塞（rabbitmq主要是这个）

3.Topic

一类消息的分类

发布订阅模式

![image-20230129141857496](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129141857496.png)

4.Partition

如果是3的模型的话，不好做横向拓展，就有了Partition



topic是逻辑的，Partition是物理的，进文件可以看到关于内存的信息

配置文件里有个segment，内存块，当一个partition内存满了之后，会自动扩，下面第一张图是配置文件（改16M是为了测试扩容），第二张图是扩容的两个segment（那一串数字是基于上一个segment的内存块的偏移量（offset）得出的）

![image-20230129142618395](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129142618395.png)

![image-20230129142430271](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129142430271.png)

下面的配置文件的意思是，创建一个Topic默认会有一个partition

![image-20230129142030564](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129142030564.png)

![image-20230129143031612](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129143031612.png)

5.Replica

**数据的备份**

每个partition只会有一个Leader（标红的），其他的Follower有机制去同步数据

![image-20230129143201741](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129143201741.png)

（不一定一个机器一个Leader）

比如：

![image-20230129143945645](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129143945645.png)

会有一个assignReplicasToBrokers的方法分配



**这么多partition，选哪个收消息呢？**

可以在代码中自定义实现算法

producer.send(new ProducerRecord<String, String>("mytopic",string1,string2))

这个ProducerRecord是可以重载的：

![image-20230129145837656](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129145837656.png)

不指定就发送到默认的partition

比如这样：先获取size，再负载均衡

![image-20230129145909587](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129145909587.png)



**选举的流程**

先选个Controller，通过rpc通知各个partition



6.Consumer Group

![image-20230129150155785](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129150155785.png)

![image-20230129150209476](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129150209476.png)

![image-20230129150535772](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129150535772.png)

7.Consumer offset

别的消息队列都是消费完消息就删除，所以下次来直接读取就可以了，但是Kafka 的消费者是怎么知道下次来读取哪条消息的呢

offset记录了消息的序号

![image-20230129150719079](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129150719079.png)

对应关系存在broker中

![image-20230129150919096](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129150919096.png)

通过hash取模算出下标

![image-20230129150852183](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129150852183.png)

8.Kafka与Zookeeper

zk作用：

![image-20230129152613716](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129152613716.png)

最后，整体架构图：

![image-20230129152827599](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129152827599.png)

## Java结合Kafka

### Java中使用

![image-20230129152901700](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129152901700.png)

consumer：

```java
public class ConsumerTest {
    public static void main(String[] args) {
        Properties props= new Properties();
        props.put("bootstrap.servers","192.168.44.161:9093,192.168.44.161:9094,192.168.44.161:9095");
        props.put("group.id","gp-test-group");
        // 是否自动提交偏移量，只有commit之后才更新消费组的 offset
        props.put("enable.auto.commit","true");
        // 消费者自动提交的间隔
        props.put("auto.commit.interval.ms","1000");
        // 从最早的数据开始消费 earliest | latest | none
        props.put("auto.offset.reset","earliest");
        props.put("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");

        KafkaConsumer<String,String> consumer=new KafkaConsumer<String, String>(props);
        consumer.subscribe(Arrays.asList("mytopic"));
        try {
            while (true){
                ConsumerRecords<String,String> records=consumer.poll(1000);
                for (ConsumerRecord<String,String> record:records){
                    System.out.printf("offset = %d ,key =%s, value= %s, partition= %s%n" ,record.offset(),record.key(),record.value(),record.partition());
                }
            }
        }finally {
            consumer.close();
        }
    }

}
```



26行可以订阅多个topic

producer：

```java
public class ProducerTest {

    public static void main(String[] args) {
        Properties pros=new Properties();
        pros.put("bootstrap.servers","192.168.44.161:9093,192.168.44.161:9094,192.168.44.161:9095");
        pros.put("group.id","gp-test-group");
        pros.put("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        pros.put("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
        // 0 发出去就确认 | 1 leader 落盘就确认| all 所有Follower同步完才确认
        pros.put("acks","-1");
        // 异常自动重试次数
        pros.put("retries",3);
        // 多少条数据发送一次，默认16K
        pros.put("batch.size",16384);
        // 批量发送的等待时间
        pros.put("linger.ms",5);
        // 客户端缓冲区大小，默认32M，满了也会触发消息发送
        pros.put("buffer.memory",33554432);
        // 获取元数据时生产者的阻塞时间，超时后抛出异常
        pros.put("max.block.ms",3000);
        Producer<String,String> producer = new KafkaProducer<String,String>(pros);

        for (int i =0 ;i<1000000;i++) {
            producer.send(new ProducerRecord<String,String>("mytopic",Integer.toString(i),Integer.toString(i)));
            // System.out.println("发送:"+i);
        }
        producer.close();
    }

}
```

### SpringBoot

![image-20230129164303324](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129164303324.png)

依赖：

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

配置类更加优雅：

```properties
server.port=7271
#============== kafka ===================
# Ö¸¶¨kafka serverµÄµØÖ·£¬¼¯ÈºÅä¶à¸ö£¬ÖÐ¼ä£¬¶ººÅ¸ô¿ª
spring.kafka.bootstrap-servers=192.168.44.161:9093,192.168.44.161:9094,192.168.44.161:9095

#=============== provider  =======================
spring.kafka.producer.retries=0
spring.kafka.producer.batch-size=16384
spring.kafka.producer.buffer-memory=33554432
spring.kafka.producer.acks=1
spring.kafka.producer.properties.linger.ms=0
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer

#=============== consumer  =======================
spring.kafka.consumer.group-id=springboottopic-group
spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.consumer.enable-auto-commit=true
spring.kafka.consumer.auto-commit-interval=1000
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer
```

consumer：

```java
@Component
public class ConsumerListener {

    @KafkaListener(topics = "springboottopic")
    public void onMessage(String msg){
        System.out.println("----收到消息："+msg+"----");
    }

}
```

producer：

```java
@Component
public class KafkaProducer {

    @Autowired
    private KafkaTemplate<String,Object> kafkaTemplate;

    public String send(@RequestParam String msg){
        kafkaTemplate.send("springboottopic", msg);
        return "ok";
    }

}
```

当然send也会有很多重载：

```java
public ListenableFuture<SendResult<K, V>> send(String topic, @Nullable V data) {
    ProducerRecord<K, V> producerRecord = new ProducerRecord(topic, data);
    return this.doSend(producerRecord);
}

public ListenableFuture<SendResult<K, V>> send(String topic, K key, @Nullable V data) {
    ProducerRecord<K, V> producerRecord = new ProducerRecord(topic, key, data);
    return this.doSend(producerRecord);
}

public ListenableFuture<SendResult<K, V>> send(String topic, Integer partition, K key, @Nullable V data) {
    ProducerRecord<K, V> producerRecord = new ProducerRecord(topic, partition, key, data);
    return this.doSend(producerRecord);
}

public ListenableFuture<SendResult<K, V>> send(String topic, Integer partition, Long timestamp, K key, @Nullable V data) {
    ProducerRecord<K, V> producerRecord = new ProducerRecord(topic, partition, timestamp, key, data);
    return this.doSend(producerRecord);
}

public ListenableFuture<SendResult<K, V>> send(ProducerRecord<K, V> record) {
    return this.doSend(record);
}

public ListenableFuture<SendResult<K, V>> send(Message<?> message) {
    ProducerRecord<?, ?> producerRecord = this.messageConverter.fromMessage(message, this.defaultTopic);
    if (!producerRecord.headers().iterator().hasNext()) {
        byte[] correlationId = (byte[])message.getHeaders().get("kafka_correlationId", byte[].class);
        if (correlationId != null) {
            producerRecord.headers().add("kafka_correlationId", correlationId);
        }
    }

    return this.doSend(producerRecord);
}
```

启动类没啥要加的：

```java
@SpringBootApplication
public class KafkaApp {
    public static void main(String[] args) {
        SpringApplication.run(KafkaApp.class, args);
    }
}
```

## Kafka和RabbitMQ对比

![image-20230129160556325](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129160556325.png)

![image-20230129160904340](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/undefinedimage-20230129160904340.png)

rabbitmq：

消息灵活分发（Exchange绑定key）、功能（死信队列、延迟队列）



Kafka：

消息保存在服务器上、更高的吞吐量、消息的顺序



## Q&A

Kafka收发消息不用ack速度比较快，顺序文件，0拷贝

















































