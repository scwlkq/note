## Netty

课程来自黑马程序猿

![image-20221013130349304](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221013130349304.png)

### 一、NIO基础

non-blocking io 非阻塞IO

#### 1）Channel

一个IO流传输的双向通道

![image-20221013131825396](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221013131825396.png)、

##### FileChannel

![image-20221015161449782](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015161449782.png)

![image-20221015161521647](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015161521647.png)

![image-20221015161700290](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015161700290.png)

数据太大：

![image-20221015161829572](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015161829572.png)

Paths：

![image-20221015162020769](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015162020769.png)

遍历目录：

![image-20221015162500650](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015162500650.png)

删除目录：

![image-20221015162606987](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015162606987.png)

拷贝目录：

![image-20221015162627785](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015162627785.png)

#### 2）Buffer

写入或者读IO流的缓冲区，暂存channel中的数据，应用程序和网络之间的桥梁

![image-20221013131905827](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221013131905827.png)

##### ByteBuffer的使用

![image-20221013140648012](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221013140648012.png)

```java
public class TestByteBuffer {
    public static void main(String[] args) {
        // 获得FileChannel
        try (FileChannel channel = new FileInputStream("stu.txt").getChannel()) {
            // 获得缓冲区
            ByteBuffer buffer = ByteBuffer.allocate(10);
            int hasNext = 0;
            StringBuilder builder = new StringBuilder();
            while((hasNext = channel.read(buffer)) > 0) {
                // 切换模式 limit=position, position=0
                buffer.flip();
                // 当buffer中还有数据时，获取其中的数据
                while(buffer.hasRemaining()) {
                    builder.append((char)buffer.get());
                }
                // 切换模式 position=0, limit=capacity
                buffer.clear();
            }
            System.out.println(builder.toString());
        } catch (IOException e) {
        }
    }
}
```



##### ByteBuffer的结构

![image-20221013203423408](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221013203423408.png)

![image-20221013203755026](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221013203755026.png)

![image-20221013203857759](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221013203857759.png)

![image-20221013203908731](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221013203908731.png)

![image-20221013204415355](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221013204415355.png)

![image-20221013204444858](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221013204444858.png)



##### 一些api

netty_demo里有一些工具类

![image-20221015155411767](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015155411767.png)

![image-20221015155649724](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015155649724.png)

mark和reset

![image-20221015160051376](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015160051376.png)

![image-20221015160247913](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015160247913.png)

分散读集中写

分散读：

![image-20221015160742142](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015160742142.png)

集中写：

![image-20221015160913268](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015160913268.png)

黏包半包分析：

![image-20221015161048284](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015161048284.png)

![image-20221015161327770](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015161327770.png)





#### 3）Selector

![image-20221013132840511](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221013132840511.png)

相当于服务员，thread只能服务一个人

![image-20221013133136329](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221013133136329.png)

短连接，连接完就跑

服务员必须忙完一个人才能接待另一个人

![image-20221013133417454](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221013133417454.png)

**非阻塞**

哪个顾客有需求就服务哪个，**适用于流量比较低的场景**









#### 4）网络编程

##### bio的实现

Server：

![image-20221015163259149](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015163259149.png)

Clinent：

![image-20221015163330540](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015163330540.png)

![image-20221015163816953](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015163816953.png)

##### nio的实现

![image-20221015164210156](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015164210156.png)

![image-20221015164231963](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015164231963.png)

accept方法在非阻塞的情况下得不到数据就会返回null

![image-20221015164309548](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015164309548.png)

##### selector对nio的优化

上面我们会看到，cpu会一直空转

![image-20221015170031558](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015170031558.png)

cancel：

![image-20221015170540942](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015170540942.png)

  处理read

![image-20221015171003944](https://typora-38282696.oss-cn-shanghai.aliyuncs.com/image-20221015171003944.png)





























































### 二、Netty入门



### 三、Netty进阶



### 四、Netty优化



### 五、Netty源码





































