## RabbitMQ基础

### 啥是RabbitMQ

消息中间件

创始人1983 MIT（麻省）

![image-20220705213245693](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705213245693.png)

一个pc发信息到另一个pc，我使用TCP或者http你就必须使用TCP或者http

我使用啥你都用啥，然后这个小伙子想，能不能不这样

集成的多了，一个系统内部实现的通信协议就很多了

`减少复杂度`，`只用一种协议`

就是上图的Bus

就是MQ，集成到一起

各种公司有自己的MQ，这样下来又复杂了



2007RabbitMQ的第一年

Rabbit是兔子，跑的快，繁殖的快

![image-20220705214749898](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220705214749898.png)

（创始人用的ErLang，天生支持高并发）

![image-20220706130346519](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706130346519.png)

### 使用MQ

 ![image-20220706130943869](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706130943869.png)

![image-20220706131009705](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706131009705.png)

1. MQ可以把同步变成异步

2. 系统解耦合

![image-20220706132001482](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706132001482.png)

解耦之后：

![image-20220706132100220](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706132100220.png)

3. 流量削峰（队列的特性）

就相当于火车站售票员，就算火车站人再多，也是一个窗口一个窗口的（一个个队列）

峰值的访问量平摊到后面的时间

![image-20220706132510772](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706132510772.png)

问题：

1. MQ必须不能挂，整个系统的可用性降低
2. 系统的复杂度增加

安装可以看这个

<https://blog.csdn.net/weixin_43779185/article/details/115960054>

结果是：

![image-20220706194418244](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706194418244.png)

> 查看用户

```java
rabbitmqctl.bat list_users
```

![image-20220706194502256](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706194502256.png)

可以访问：

http://localhost:15672

![image-20220706195004303](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706195004303.png)

### 工作模型

![image-20221003143722655](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221003143722655.png)

高可靠

灵活的路由

多客户端（多种语言）

集群与扩展性

高可用队列

权限管理（每种用户有自己的权限）

插件系统

可靠性

与Spring集成

![image-20220706133515180](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706133515180.png)

运行MQ的软件我们叫做Broker，这个单词的意思的“中介，代理”

Producer和Consumer要与Broker建立连接TCP长连接，太消耗资源，所以在TCP的长连接里建立了虚连接channel，我们把这个叫做信道，通信完之后释放一个个channel

发送过来的消息存queue中

Exchange是交换机，本质是绑定列表，实现消息灵活分发

默认端口5672

可以在一台服务器上运行多个Broker，但是没有必要

**可以在一个个Broker里创建多个VHost**

**不同用户用不同的Vhost，他们之间是相互隔离的**

![image-20220706135224867](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706135224867.png)

### Exchange路由消息详解

交换机类型

1、**Direct Exchange直连路由的交换机**

![image-20220706145001422](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706145001422.png)

channel.basicPublish(exc_name,"spring",msg)

（exchange的名字，路由键名字，消息）

只有当router_key和binding_key一样的时候，消息才会送到对应的queue中



2、**TopicExchange主题类型的交换机**

符号*表示一个单词，符号#表示0个或者多个单词

![image-20220706150224149](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706150224149.png)

就比如，第一个binding key匹配的路由键是junior或者junior.a.b或者junior.a.b.c.d.e

比如：

channel.basicPublish("myexc","junior.abc.jvm","msg3");

这条消息能发送给哪个队列？

答案是只有第一个队列



3、**FanoutExchange风扇交换**

![image-20220706151959427](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/tsimage-20220706151959427.png)

没有绑定的

广播类型的交换机

**RabbitMQ的基本使用**

安装ErLang环境

不演示了

注意版本对应

docker安装教程：<https://gper.club/articles/7e7e7f7ff7g5fgc4g6b>

> 流程

1. 引入依赖
2. 消费者
3. 生产者
4. 参数详解

> 实操

1. 引入依赖

```java
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>5.6.0</version>
</dependency>
```

2. 消费者

**谁使用，谁管理，所以在消费者里创建一些**

```java
import com.rabbitmq.client.*;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.util.concurrent.TimeoutException;

public class MyConsumer {
    private final static String EXCHANGE_NAME = "SIMPLE_EXCHANGE";

    private final static String QUEUE_NAME = "SIMPLE_QUEUE";

    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        // 连接IP
        factory.setHost("127.0.0.1");
        // 默认监听端口
        factory.setHost("5672");
        // 虚拟机
        factory.setVirtualHost("/");

        // 设置访问的用户
        factory.setUsername("guest");
        factory.setPassword("guest");
        // 建立连接
        Connection conn = factory.newConnection();
        // 创建消息通道
        Channel channel = conn.createChannel();

        // 声明交换机
        // String exchange, String type, boolean durable, boolean autoDelete, Map<String, Object> args
        // 交换机名字         种类          是否持久化         是否自动删除          其他参数
        channel.exchangeDeclare(EXCHANGE_NAME, "direct", false, false, null);

        // 声明队列
        // String queue, boolean durable, boolean exclusive, boolean autoDelete, Map<String, Object> args
        // 队列名字        是否持久化         是否重复定义         是否自动删除           其他参数
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);
        System.out.println("Waiting for message....");

        // 绑定队列和交换机
        channel.queueBind(QUEUE_NAME, EXCHANGE_NAME, "gupao.best");

        // 创建消费者
        Consumer consumer = new DefaultConsumer(channel){
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope, AMQP.BasicProperties basicProperties, byte[] body ) throws UnsupportedEncodingException {
                String msg = new String(body, "UTF-8");
                System.out.println("Received message:'"+msg+"'");
                System.out.println("consumerTag:"+consumerTag);
                System.out.println("deliveryTag:"+envelope.getDeliveryTag());
            }
        };

        // 开始获取消息
        // String queue, boolean autoAck, Consumer callback
        channel.basicConsume(QUEUE_NAME, true, consumer);
    }
}
```

3. 生产者

```java
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.util.concurrent.TimeoutException;

public class MyProducer {

    private final static String EXCHANGE_NAME = "SIMPLE_EXCHANGE";

    public static void main(String[] args) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        // 连接IP
        factory.setHost("127.0.0.1");
        // 默认监听端口
        factory.setHost("5672");
        // 虚拟机
        factory.setVirtualHost("/");

        // 设置访问的用户
        factory.setUsername("guest");
        factory.setPassword("guest");
        // 建立连接
        Connection conn = factory.newConnection();
        // 创建消息通道
        Channel channel = conn.createChannel();
        // 发送消息
        String msg = "Hello world, Rabbit MQ!";

        // String exchange, String routingKey, BasicProperties props, byte[] body
        channel.basicPublish(EXCHANGE_NAME, "gupao.best", null, msg.getBytes());
        channel.();
        conn.close();
    }
}
```

## RabbitMQ进阶

### 高级特性

#### 死信队列

![image-20221006193247665](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221006193247665.png)

下面给代码，防止晕，我们再来复习一下消息队列的结构

![image-20221006221246234](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221006221246234.png)

![image-20221006221515906](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221006221515906.png)



**死信交换机and死信队列**

1、**过期**

![image-20221006222139886](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221006222139886.png)

![image-20221006222256597](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221006222256597.png)

代码：

**TEST_DLX_QUEUE**队列中死掉的消息都会去**DLX_QUEUE**中

![image-20221006222534496](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221006222534496.png)

![image-20221006222613014](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221006222613014.png)



2、**消费者拒绝**

![image-20221006223041032](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221006223041032.png)

（requeue会再次发送这个消息给对的消费者）（至于怎么找到对的消费者就不知道了）

3、**超内存了，先进先出**

![image-20221006223305651](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221006223305651.png)

![image-20221006223321149](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221006223321149.png)

#### 延迟队列

家里热水器我希望30min之后打开

未支付的订单15min自动关闭



1、可以通过死信队列的机制实现

但是如果第一个消息是30min，第二个消息是10min，先进先出原则的话，就失效了

2、插件：

延迟消息的插件

![image-20221007112643410](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007112643410.png)

![image-20221007113101936](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007113101936.png)

copy到这里

![image-20221007113126395](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007113126395.png)

linux：

![image-20221007113255525](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007113255525.png)

代码：

消费者

![image-20221007113520138](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007113520138.png)

![image-20221007113613734](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007113613734.png)

![image-20221007113626169](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007113626169.png)

![image-20221007113422582](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007113422582.png)

生产者：

![image-20221007113834803](C:/Users/suncong/AppData/Roaming/Typora/typora-user-images/image-20221007113834803.png)

![image-20221007113803688](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007113803688.png)

![](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007113742505.png)

![image-20221007114024152](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007114024152.png)

#### 流量控制

服务端通过内存的大小限制

![image-20221007114830533](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007114830533.png)

消费端通过限制获取消息的条数

prefetch

![image-20221007120208114](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007120208114.png)

#### spring的集成

这里是基于xml文件的，可以细看下面那个springboot的集成

经典 先导依赖

![image-20221007121339862](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007121339862.png)

可以写个xml文件

![image-20221007141519713](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007141519713.png)

![image-20221007142050680](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007142050680.png)

![image-20221007142259967](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007142259967.png)

这里的作用是，比如你要给不同的系统发消息，就发给不同的人

![image-20221007142337708](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007142337708.png)

pro：

![image-20221007121821279](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007121821279.png)

![image-20221007121538967](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007121538967.png)

![image-20221007121943939](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007121943939.png)

![image-20221007142912228](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007142912228.png)

#### springboot的集成

![image-20221007143107122](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007143107122.png)

举例：

我们来实现这个案例

![image-20221007143928874](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221007143928874.png)

消费者一共会收到5条消息

文件结构：

![image-20221011213256566](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221011213256566.png)

config：

![image-20221011213413202](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221011213413202.png)

名字进行了抽取写在配置文件里

![image-20221011213442210](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221011213442210.png)

queue

![image-20221011213517717](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221011213517717.png)

exchange

![image-20221011213632531](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221011213632531.png)

binding

![image-20221011213647803](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221011213647803.png)



注意rabbitmq的服务端（也就是网页打开的管理后台的端口是15672，但是**rabbitmq的端口是5672**）

![image-20221012103750184](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012103750184.png)

当我们创建了一条记录，数据库又增加的时候，mq也会有消息

![image-20221012104731226](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012104731226.png)

![image-20221012104924938](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012104924938.png)

![image-20221012104913895](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012104913895.png)



## RabbitMQ实战

### 消息可靠性投递

![image-20221012110524698](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012110524698.png)



> Broker

第一种：

![image-20221012110616747](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012110616747.png)

```java
/**
 * @Author: qingshan
 * 消息生产者，测试事务模式。发送消息的效率比较低，建议使用Confirm模式
 */
public class TransactionProducer {
    private final static String QUEUE_NAME = "ORIGIN_QUEUE";

    public static void main(String[] args) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setUri(ResourceUtil.getKey("rabbitmq.uri"));

        // 建立连接
        Connection conn = factory.newConnection();
        // 创建消息通道
        Channel channel = conn.createChannel();

        String msg = "Hello world, Rabbit MQ";
        // 声明队列（默认交换机AMQP default，Direct）
        // String queue, boolean durable, boolean exclusive, boolean autoDelete, Map<String, Object> arguments
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);

        try {
            channel.txSelect();
            // 发送消息
            // String exchange, String routingKey, BasicProperties props, byte[] body
            channel.basicPublish("", QUEUE_NAME, null, (msg).getBytes());
            int i =1/0;
            channel.txCommit();
            System.out.println("消息发送成功");
        } catch (Exception e) {
            channel.txRollback();
            System.out.println("消息已经回滚");
        }

        channel.close();
        conn.close();
    }
}
```

阻塞，不建议使用

第二种：

![image-20221012111004121](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012111004121.png)

普通确认模式：

```java
public class NormalConfirmProducer {

    private final static String QUEUE_NAME = "ORIGIN_QUEUE";

    public static void main(String[] args) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setUri(ResourceUtil.getKey("rabbitmq.uri"));

        // 建立连接
        Connection conn = factory.newConnection();
        // 创建消息通道
        Channel channel = conn.createChannel();

        String msg = "Hello world, Rabbit MQ ,Normal Confirm";
        // 声明队列（默认交换机AMQP default，Direct）
        // String queue, boolean durable, boolean exclusive, boolean autoDelete, Map<String, Object> arguments
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);

        // 开启发送方确认模式
        channel.confirmSelect();

        channel.basicPublish("", QUEUE_NAME, null, msg.getBytes());
        // 普通Confirm，发送一条，确认一条
        if (channel.waitForConfirms()) {
            System.out.println("消息发送成功" );
        }

        channel.close();
        conn.close();
    }
}
```

批量确认模式：

```java
public class BatchConfirmProducer {
    private final static String QUEUE_NAME = "ORIGIN_QUEUE";

    public static void main(String[] args) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setUri(ResourceUtil.getKey("rabbitmq.uri"));

        // 建立连接
        Connection conn = factory.newConnection();
        // 创建消息通道
        Channel channel = conn.createChannel();

        String msg = "Hello world, Rabbit MQ ,Batch Confirm";
        // 声明队列（默认交换机AMQP default，Direct）
        // String queue, boolean durable, boolean exclusive, boolean autoDelete, Map<String, Object> arguments
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);

        try {
            channel.confirmSelect();
            for (int i = 0; i < 5000; i++) {
                // 发送消息
                // String exchange, String routingKey, BasicProperties props, byte[] body
                channel.basicPublish("", QUEUE_NAME, null, (msg +"-"+ i).getBytes());
            }
            // 批量确认结果，ACK如果是Multiple=True，代表ACK里面的Delivery-Tag之前的消息都被确认了
            // 比如5条消息可能只收到1个ACK，也可能收到2个（抓包才看得到）
            // 直到所有信息都发布，只要有一个未被Broker确认就会IOException
            channel.waitForConfirmsOrDie();
            System.out.println("消息发送完毕，批量确认成功");
        } catch (Exception e) {
            // 发生异常，可能需要对所有消息进行重发
            e.printStackTrace();
        }

        channel.close();
        conn.close();
    }
}
```

异步确认模式：

```java
public class AsyncConfirmProducer {
    private final static String QUEUE_NAME = "ORIGIN_QUEUE";

    public static void main(String[] args) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setUri(ResourceUtil.getKey("rabbitmq.uri"));

        // 建立连接
        Connection conn = factory.newConnection();
        // 创建消息通道
        Channel channel = conn.createChannel();

        String msg = "Hello world, Rabbit MQ, Async Confirm";
        // 声明队列（默认交换机AMQP default，Direct）
        // String queue, boolean durable, boolean exclusive, boolean autoDelete, Map<String, Object> arguments
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);

        // 用来维护未确认消息的deliveryTag
        final SortedSet<Long> confirmSet = Collections.synchronizedSortedSet(new TreeSet<Long>());

        // 这里不会打印所有响应的ACK；ACK可能有多个，有可能一次确认多条，也有可能一次确认一条
        // 异步监听确认和未确认的消息
        // 如果要重复运行，先停掉之前的生产者，清空队列
        channel.addConfirmListener(new ConfirmListener() {
            public void handleNack(long deliveryTag, boolean multiple) throws IOException {
                System.out.println("Broker未确认消息，标识：" + deliveryTag);
                if (multiple) {
                    // headSet表示后面参数之前的所有元素，全部删除
                    confirmSet.headSet(deliveryTag + 1L).clear();
                } else {
                    confirmSet.remove(deliveryTag);
                }
                // 这里添加重发的方法
            }
            public void handleAck(long deliveryTag, boolean multiple) throws IOException {
                // 如果true表示批量执行了deliveryTag这个值以前（小于deliveryTag的）的所有消息，如果为false的话表示单条确认
                System.out.println(String.format("Broker已确认消息，标识：%d，多个消息：%b", deliveryTag, multiple));
                if (multiple) {
                    // headSet表示后面参数之前的所有元素，全部删除
                    confirmSet.headSet(deliveryTag + 1L).clear();
                } else {
                    // 只移除一个元素
                    confirmSet.remove(deliveryTag);
                }
                System.out.println("未确认的消息:"+confirmSet);
            }
        });

        // 开启发送方确认模式
        channel.confirmSelect();
        for (int i = 0; i < 10; i++) {
            long nextSeqNo = channel.getNextPublishSeqNo();
            // 发送消息
            // String exchange, String routingKey, BasicProperties props, byte[] body
            channel.basicPublish("", QUEUE_NAME, null, (msg +"-"+ i).getBytes());
            confirmSet.add(nextSeqNo);
        }
        System.out.println("所有消息:"+confirmSet);

        // 这里注释掉的原因是如果先关闭了，可能收不到后面的ACK
        //channel.close();
        //conn.close();
    }
}
```

![image-20221012111528920](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012111528920.png)





> **消息从交换机路由到队列**

没有被正确发送的消息，被return回发

第一种方法：

```java
public class ReturnListenerProducer {
    public static void main(String[] args) throws Exception{
        ConnectionFactory factory = new ConnectionFactory();
        factory.setUri(ResourceUtil.getKey("rabbitmq.uri"));

        Connection connection = factory.newConnection();

        Channel channel = connection.createChannel();

        channel.addReturnListener(new ReturnListener() {
            public void handleReturn(int replyCode,
                                     String replyText,
                                     String exchange,
                                     String routingKey,
                                     AMQP.BasicProperties properties,
                                     byte[] body)
                    throws IOException {
                System.out.println("=========监听器收到了无法路由，被返回的消息============");
                System.out.println("replyText:"+replyText);
                System.out.println("exchange:"+exchange);
                System.out.println("routingKey:"+routingKey);
                System.out.println("message:"+new String(body));
            }
        });

        AMQP.BasicProperties properties = new AMQP.BasicProperties.Builder().deliveryMode(2).
                contentEncoding("UTF-8").build();

        // 在声明交换机的时候指定备份交换机
        //Map<String,Object> arguments = new HashMap<String,Object>();
        //arguments.put("alternate-exchange","ALTERNATE_EXCHANGE");
        //channel.exchangeDeclare("TEST_EXCHANGE","topic", false, false, false, arguments);

        // 发送到了默认的交换机上，由于没有任何队列使用这个关键字跟交换机绑定，所以会被退回
        // 第三个参数是设置的mandatory，如果mandatory是false，消息也会被直接丢弃
        channel.basicPublish("","gupaodirect",true, properties,"只为更好的你".getBytes());

        TimeUnit.SECONDS.sleep(10);

        channel.close();
        connection.close();
    }
}
```

第二种方法：备份交换机，这里注意是备份交换机，死信的是队列

![image-20221012133235685](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012133235685.png)



> 消息持久化

方法一：队列和交换机和消息都有对应的durable参数，开启就行

![image-20221012133720666](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012133720666.png)



> 消息传给consumer的途中，寄了

第一种方法：

AutoAck，但是这个ack是一接收到消息就返回的，还是会出现没有处理好消息的情况



第二种方法：

手动ack

![image-20221012134446472](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012134446472.png)

**注意要有try{   } catch{  } finally {ack}         ！！！**

**无论如何都要给他一个应答**



央妈的通用格式

![image-20221012134907037](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012134907037.png)

### 集群和高可用

高可用、负载均衡

自己实现数据同步

Erlang天生支持高并发（原理：/var/lib/rabbitmq/.erlang.cookie一样的话，我们就是一个集群）

25672端口是集群通信端口， **注意打开防火墙**

[docker安装rabbitmq高可用集群](https://gper.club/articles/7e7e7f7ff7g5fgc4g6b)

> 普通集群

![image-20221012144633391](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012144633391.png)

集群一般是奇数个，因为投票的时候防止脑裂

上面的节点1存的是内容，2、3存的是指向1的指针

**万一节点1挂了一样不支持高并发**

> 镜像集群

在上面的基础上

添加：

![image-20221012145015419](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012145015419.png)

上面的数据就同步了，但是会有**性能消耗**

> 负载的组件

看不懂，emo了.....以后用到再研究吧

![image-20221012145642371](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012145642371.png)

### 经验

![image-20221012150005647](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012150005647.png)

**将方法封装**

![image-20221012150439817](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012150439817.png)

![image-20221012150520631](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221012150520631.png)















## RabbitMQ一些问题


