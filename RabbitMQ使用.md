# RabbitMQ使用教程

## 1.MQ简介

消息队列（Message Queue，简称MQ），从字面意思上看，本质是个队列，FIFO先入先出，只不过队列中存放的内容是message而已。
其主要用途：不同进程Process/线程Thread之间通信。

> 产生原因：

- 不同进程（process）之间传递消息时，两个进程之间耦合程度过高，改动一个进程，引发必须修改另一个进程，为了隔离这两个进程，在两进程间抽离出一层（一个模块），所有两进程之间传递的消息，都必须通过消息队列来传递，单独修改某一个进程，不会影响另一个；
- 不同进程（process）之间传递消息时，为了实现标准化，将消息的格式规范化了，并且，某一个进程接受的消息太多，一下子无法处理完，并且也有先后顺序，必须对收到的消息进行排队，因此诞生了事实上的消息队列；

MQ框架非常之多，比较流行的有RabbitMq、ActiveMq、ZeroMq、kafka，以及阿里开源的RocketMQ。本文主要介绍RabbitMq。

## 2.RabbitMQ

### 2.1.RabbitMQ的简介

- MQ--message queue，消息队列是应用程序和应用程序之间的通信方法

- RabbitMQ -- 开源，在AMQP基础上完整的可复用的企业消息信息系统

- 支持主流操作系统

- 支持多种开发语言

- 开发语言：Erlang – 面向并发的编程语言。

  #### 2.1.1.AMQP

  AMQP是消息队列的一个协议。

### 2.2.官网

https://www.rabbitmq.com/

![image-20200601172038925](https://gitee.com/ubfirst/Typora/raw/master/img/20200601172046.png)

### 2.3.五种消息队列

![image-20200601172308827](https://gitee.com/ubfirst/Typora/raw/master/img/20200601172308.png)

## 3.搭建RabbitMQ环境

### 3.1 下载RabbitMQ

https://www.rabbitmq.com/download.html

![image-20200601172652217](https://gitee.com/ubfirst/Typora/raw/master/img/20200601172652.png)

![image-20200601173212208](https://gitee.com/ubfirst/Typora/raw/master/img/20200601173212.png)

### 3.2安装Erlang

#### 3.2.1 windows下安装

http://www.erlang.org/download/

![image-20200601173338564](https://gitee.com/ubfirst/Typora/raw/master/img/20200601173338.png)

#### 3.2.2 配置Erlang环境变量

>  指向安装文件夹  变量名：ERLANG_HOME

![image-20200601175016994](https://gitee.com/ubfirst/Typora/raw/master/img/20200601175017.png)

> path变量 新建 %ERLANG_HOME%\bin

![image-20200601175041610](https://gitee.com/ubfirst/Typora/raw/master/img/20200601175041.png)

> 验证是否配置成功  cmd -- erl

![image-20200601175256231](https://gitee.com/ubfirst/Typora/raw/master/img/20200601175256.png)

#### 3.2.3 启动/停止 rabbitmq

> 配置启用管理插件

- 文件安装位置

D:\SoftWare\RabbitMQ Server\rabbitmq_server-3.8.4\sbin

```xml
cmd -- rabbitmq-plugins enable rabbitmq_management
```

![image-20200601175949467](https://gitee.com/ubfirst/Typora/raw/master/img/20200601175949.png)

> 找到启动文件

![image-20200601173820666](https://gitee.com/ubfirst/Typora/raw/master/img/20200601173820.png)

- 文件位置

```xml
C:\Users\11658\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\RabbitMQ Server
```

> 启动 net start RabbitMQ

![image-20200601174140488](https://gitee.com/ubfirst/Typora/raw/master/img/20200601174140.png)

> 停止 net stop RabbitMQ

![image-20200601174241399](https://gitee.com/ubfirst/Typora/raw/master/img/20200601174241.png)

> 查看 http://127.0.0.1:15672/    

账号/密码：guest/guest

![image-20200601180034926](https://gitee.com/ubfirst/Typora/raw/master/img/20200601180035.png)

> 主页

![image-20200601181039029](https://gitee.com/ubfirst/Typora/raw/master/img/20200601181039.png)

## 4.页面操作

### 4.1.添加用户

![image-20200601180658194](https://gitee.com/ubfirst/Typora/raw/master/img/20200601180658.png)

### 4.2.用户角色

1、超级管理员(administrator)
可登陆管理控制台，可查看所有的信息，并且可以对用户，策略(policy)进行操作。
2、监控者(monitoring)
可登陆管理控制台，同时可以查看rabbitmq节点的相关信息(进程数，内存使用情况，磁盘使用情况等)
3、策略制定者(policymaker)
可登陆管理控制台, 同时可以对policy进行管理。但无法查看节点的相关信息(上图红框标识的部分)。
4、普通管理者(management)
仅可登陆管理控制台，无法看到节点信息，也无法对策略进行管理。
5、其他
无法登陆管理控制台，通常就是普通的生产者和消费者。

### 4.3. 创建Virtual Hosts

![image-20200601181424321](https://gitee.com/ubfirst/Typora/raw/master/img/20200601181424.png)

> 给用户添加host

![image-20200601181729874](https://gitee.com/ubfirst/Typora/raw/master/img/20200601181730.png)

> 查看是否设置权限成功

![image-20200601181809663](https://gitee.com/ubfirst/Typora/raw/master/img/20200601181809.png)



## 5.实例

### 5.1 "Hello World!"

![image-20200601214844941](https://gitee.com/ubfirst/Typora/raw/master/img/20200601214845.png)

- 简单模式

#### 5.1.1 创建工具类

```java
package com.zhang;

import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;

/**
 * 通用获取MQ连接
 */
public class ConnectionUtil {
    public static Connection getConnection() throws IOException {
        //创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        //设置ip
        factory.setHost("localhost");
        //设置端口
        factory.setPort(5672);
        //设置账号密码
        factory.setUsername("admin");
        factory.setPassword("admin");
        //设置vhost,和客户端设置的保持一致
        factory.setVirtualHost("testHost");
        //获取连接
        Connection connection = factory.newConnection();
        return connection;
    }
}
```

#### 5.1.2. 生产者

```java
package com.zhang.direct;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.zhang.ConnectionUtil;

import java.io.IOException;

public class Provider1 {
    private static final String QUEUE_NAME = "test01Queue";

    public static void main(String[] args) throws IOException {
        //1.获取连接
        Connection connection = ConnectionUtil.getConnection();
        //2.通过连接创建通道
        Channel channel = connection.createChannel();
        //3.通道创建队列
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        //4.创建消息内容绑定到通道
        String msg = "MQ简单模式";
        channel.basicPublish("",QUEUE_NAME,null,msg.getBytes());
        System.out.println("==》发送消息"+msg);
        //5.关闭通道和连接
        channel.close();
        connection.close();
    }
}
```

- 启动生产者查看客户端信息

![image-20200601213407394](https://gitee.com/ubfirst/Typora/raw/master/img/20200601213407.png)

- 点击name可获得更多信息

![image-20200601213515623](https://gitee.com/ubfirst/Typora/raw/master/img/20200601213515.png)



#### 5.1.3. 消费者

```java
package com.zhang.direct;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.QueueingConsumer;
import com.zhang.ConnectionUtil;

import java.io.IOException;

/**
 * 简单模式的消费者
 */
public class Consumer01 {
    private static final String QUEUE_NAME = "test01Queue";

    public static void main(String[] args) throws IOException, InterruptedException {
        //消费者也需要获取连接
        Connection connection = ConnectionUtil.getConnection();
        //创建MQ通道
        Channel channel = connection.createChannel();
        //通过通道声明队列
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        //定义队列的消费者
        QueueingConsumer consumer = new QueueingConsumer(channel);
        //监听队列
        channel.basicConsume(QUEUE_NAME,true,consumer);
        //获取消息
        while (true){
            QueueingConsumer.Delivery delivery = consumer.nextDelivery();
            String msg = new String(delivery.getBody());
            System.out.println("===>consumer:"+msg);
        }


    }
}
```

- 消费者消费信息之后就不存在了

![image-20200601214723909](https://gitee.com/ubfirst/Typora/raw/master/img/20200601214724.png)

### 5.2.Work Queues

![image-20200601215008176](https://gitee.com/ubfirst/Typora/raw/master/img/20200601215008.png)

- 一个生产者，两个消费者
- 一个消息只能被一个消费者消费

#### 5.2.1 生产者

生产几十条消息

```java
package com.zhang.workqueues;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.zhang.ConnectionUtil;

import java.io.IOException;

/**
 * 生产者生产消息
 */
public class Provider {
    private static final String QUEUE_NAME = "test_queue_work";

    public static void main(String[] args) throws IOException, InterruptedException {
        //获取连接
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        //声明队列
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        for (int i = 0; i < 50; i++) {
            //消息内容
            String msg = ""+i;
            channel.basicPublish("",QUEUE_NAME,null,msg.getBytes());
            System.out.println("生产消息===》"+i);
            Thread.sleep(i * 10);
        }
        channel.close();
        connection.close();
    }
}

```

#### 5.2.2 消费者

消费者1

```java
package com.zhang.workqueues;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.QueueingConsumer;
import com.zhang.ConnectionUtil;

import java.io.IOException;

/**
 * 消费者1,测试手动确认
 */
public class Consumer01 {

    private static final String QUEUE_NAME = "test_queue_work";

    public static void main(String[] args) throws IOException, InterruptedException {
        //获取连接
        Connection connection = ConnectionUtil.getConnection();
        //创建通道
        Channel channel = connection.createChannel();
        //通道连接队列
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        //同一时刻服务器只发送一条消息给消费者
        //prefetchCount：会告诉RabbitMQ不要同时给一个消费者推送多于N个消息，即一旦有N个消息还没有ack，则该consumer将block掉，直到有消息ack
        //global：true\false 是否将上面设置应用于channel，简单点说，就是上面限制是channel级别的还是consumer级别
        //channel.basicQos(1);

        //定义队列的消费者
        QueueingConsumer consumer = new QueueingConsumer(channel);
        //通道监听,false表示手动返回完成状态，true表示自动
        channel.basicConsume(QUEUE_NAME,false,consumer);
        //获取消息
        while (true){
            QueueingConsumer.Delivery delivery = consumer.nextDelivery();
            String msg = new String(delivery.getBody());
            System.out.println("消费者1获取消息===》"+msg);
            Thread.sleep(100);
            //返回确认状态，即手动确认需要上述监听开启手动确认
            //channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);
        }


    }
}

```

消费者2

```java
package com.zhang.workqueues;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.QueueingConsumer;
import com.zhang.ConnectionUtil;

import java.io.IOException;

/**
 * 消费者2
 */
public class Consumer02 {
    private static final String QUEUE_NAME = "test_queue_work";

    public static void main(String[] args) throws IOException, InterruptedException {
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);

        //prefetchCount：会告诉RabbitMQ不要同时给一个消费者推送多于N个消息，即一旦有N个消息还没有ack，则该consumer将block掉，直到有消息ack
        //global：true\false 是否将上面设置应用于channel，简单点说，就是上面限制是channel级别的还是consumer级别
        //channel.basicQos(1);

        //定义队列的消费者
        QueueingConsumer consumer = new QueueingConsumer(channel);
        //监听队列，false表示手动返回完成状态，true表示自动
        channel.basicConsume(QUEUE_NAME,true,consumer);
        while (true){
            QueueingConsumer.Delivery delivery = consumer.nextDelivery();
            String s = new String(delivery.getBody());
            System.out.println("消费者2获取消息===》"+s);
            Thread.sleep(1000);
            //下面这行注释掉表示使用自动确认模式
            //channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);
        }
    }
}

```

#### 5.2.3.测试

测试结果：
1、消费者1和消费者2获取到的消息内容是不同的，同一个消息只能被一个消费者获取。
2、消费者1和消费者2获取到的消息的数量是相同的，一个是消费奇数号消息，一个是偶数。

- 其实，这样是不合理的，因为消费者1线程停顿的时间短。应该是消费者1要比消费者2获取到的消息多才对。
  RabbitMQ 默认将消息顺序发送给下一个消费者，这样，每个消费者会得到相同数量的消息。即轮询（round-robin）分发消息。
- 怎样才能做到按照每个消费者的能力分配消息呢？联合使用 Qos 和 Acknowledge 就可以做到。
  basicQos 方法设置了当前信道最大预获取（prefetch）消息数量为1。消息从队列异步推送给消费者，消费者的 ack 也是异步发送给队列，从队列的视角去看，总是会有一批消息已推送但尚未获得 ack 确认，Qos 的 prefetchCount 参数就是用来限制这批未确认消息数量的。设为1时，队列只有在收到消费者发回的上一条消息 ack 确认后，才会向该消费者发送下一条消息。prefetchCount 的默认值为0，即没有限制，队列会将所有消息尽快发给消费者。
- 2个概念
- 轮询分发 ：使用任务队列的优点之一就是可以轻易的并行工作。如果我们积压了好多工作，我们可以通过增加工作者（消费者）来解决这一问题，使得系统的伸缩性更加容易。在默认情况下，RabbitMQ将逐个发送消息到在序列中的下一个消费者(而不考虑每个任务的时长等等，且是提前一次性分配，并非一个一个分配)。平均每个消费者获得相同数量的消息。这种方式分发消息机制称为Round-Robin（轮询）。
- 公平分发 ：虽然上面的分配法方式也还行，但是有个问题就是：比如：现在有2个消费者，所有的奇数的消息都是繁忙的，而偶数则是轻松的。按照轮询的方式，奇数的任务交给了第一个消费者，所以一直在忙个不停。偶数的任务交给另一个消费者，则立即完成任务，然后闲得不行。而RabbitMQ则是不了解这些的。这是因为当消息进入队列，RabbitMQ就会分派消息。它不看消费者为应答的数目，只是盲目的将消息发给轮询指定的消费者。

为了解决这个问题，我们使用basicQos( prefetchCount = 1)方法，来限制RabbitMQ只发不超过1条的消息给同一个消费者。当消息处理完毕后，有了反馈，才会进行第二次发送。
还有一点需要注意，使用公平分发，必须关闭自动应答，改为手动应答。

#### 5.2.4.Work模式的“能者多劳”

打开注释

```java
// 同一时刻服务器只会发一条消息给消费者
channel.basicQos(1);
```

```java
//开启这行 表示使用手动确认模式
channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
```

同时修改为手动确认模式

```java
// 监听队列，false表示手动返回完成状态，true表示自动
channel.basicConsume(QUEUE_NAME, false, consumer);
```

先启动消费者，再启动生产者

### 5.3.发布/订阅模式

![image-20200601225127335](https://gitee.com/ubfirst/Typora/raw/master/img/20200601225127.png)

- 一个生产者，多个消费者
- 每个消费者各自消费队列
- 生产者没有直接发消息到队列，通过交换机发送
- 每个队列都要绑定到交换机
- 生产者发送的消息，经过交换机，到达队列，实现，一个消息被多个消费者获取的目的

注意：一个消费者队列可以有多个消费者实例，只有其中一个消费者实例会消费

#### 5.3.1.生产者/消费者  

> 生产者  -- 向交换机发送消息

```java
package com.zhang.publishsubscribe;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.zhang.ConnectionUtil;

import java.io.IOException;

/**
 * 发布订阅模式生产者
 */
public class Provider {
    private static final String EXCHAGE_NAME = "test_exchange_fanout";

    public static void main(String[] args) throws IOException {
        //获取连接
        Connection connection = ConnectionUtil.getConnection();
        //创建通道
        Channel channel = connection.createChannel();
        //声明交换机
        channel.exchangeDeclare(EXCHAGE_NAME,"fanout");
        //消息内容
        String msg = "exchage msg";
        channel.basicPublish(EXCHAGE_NAME,"",null,msg.getBytes());
        System.out.println("发布订阅模式，生产者启动===》"+msg);
        channel.close();
        connection.close();
    }
}
```

> 消费者01

```java
package com.zhang.publishsubscribe;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.QueueingConsumer;
import com.zhang.ConnectionUtil;

import java.io.IOException;

/**
 * 发布订阅模式，消费者1 看做是前台系统
 */
public class Consumer01 {

    private final static String QUEUE_NAME = "test_queue_work1";
    private final static String EXCHANGE_NAME = "test_exchange_fanout";
    public static void main(String[] args) throws IOException, InterruptedException {
        //获取链接/通道
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        //通道创建队列
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        //队列绑定到交换机
        channel.queueBind(QUEUE_NAME,EXCHANGE_NAME,"");
        //同一时刻服务器只会发送一条消息给消费者
        channel.basicQos(1);
        //定义消费者队列
        QueueingConsumer consumer = new QueueingConsumer(channel);
        //监听队列，手动返回确认信息
        channel.basicConsume(QUEUE_NAME,false,consumer);

        //获取消息
        while (true){
            QueueingConsumer.Delivery delivery = consumer.nextDelivery();
            String s = new String(delivery.getBody());
            System.out.println("消费者1获取消息===>" + s);
            Thread.sleep(10);
            channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);
        }
    }
}
```

> 消费者2

```java
package com.zhang.publishsubscribe;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.QueueingConsumer;
import com.zhang.ConnectionUtil;

import java.io.IOException;

/**
 * 发布订阅模式，消费者2 看做是搜索系统
 */
public class Consumer02 {

    private final static String QUEUE_NAME = "test_queue_work2";
    private final static String EXCHANGE_NAME = "test_exchange_fanout";
    public static void main(String[] args) throws IOException, InterruptedException {
        //获取链接/通道
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        //通道创建队列
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        //队列绑定到交换机
        channel.queueBind(QUEUE_NAME,EXCHANGE_NAME,"");
        //同一时刻服务器只会发送一条消息给消费者
        channel.basicQos(1);
        //定义消费者队列
        QueueingConsumer consumer = new QueueingConsumer(channel);
        //监听队列，手动返回确认信息
        channel.basicConsume(QUEUE_NAME,false,consumer);

        //获取消息
        while (true){
            QueueingConsumer.Delivery delivery = consumer.nextDelivery();
            String s = new String(delivery.getBody());
            System.out.println("消费者2获取消息===>" + s);
            Thread.sleep(100);
            channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);

        }
    }
}
```

#### 5.3.2.测试

测试结果：
同一个消息被多个消费者获取。一个消费者队列可以有多个消费者实例，只有其中一个消费者实例会消费到消息。

在管理工具中查看队列和交换机的绑定关系：

![image-20200601232602904](https://gitee.com/ubfirst/Typora/raw/master/img/20200601232603.png)

### 5.4.Routing - 路由模式

![image-20200601232756207](https://gitee.com/ubfirst/Typora/raw/master/img/20200601232756.png)

- 一个队列可以绑定多个路由

#### 5.4.1生产者

```java
package com.zhang.routing;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.zhang.ConnectionUtil;

import java.io.IOException;

/**
 * 路由模式  生产者
 */
public class Provider {
    private static final String EXCHAGE_NAME = "test_exchange_direct";

    public static void main(String[] args) throws IOException {
        //获取连接
        Connection connection = ConnectionUtil.getConnection();
        //创建通道
        Channel channel = connection.createChannel();
        //声明交换机
        channel.exchangeDeclare(EXCHAGE_NAME,"direct");
        //消息内容
        String msg = "delete msg";
        channel.basicPublish(EXCHAGE_NAME,"delete",null,msg.getBytes());
        System.out.println("路由模式，生产者启动===》"+msg);
        channel.close();
        connection.close();
    }
}
```

#### 5.4.2 消费者

```java
package com.zhang.routing;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.QueueingConsumer;
import com.zhang.ConnectionUtil;

import java.io.IOException;

/**
 * 路由模式，消费者1 看做是前台系统
 */
public class Consumer01 {

    private final static String QUEUE_NAME = "test_routing1";
    private final static String EXCHANGE_NAME = "test_exchange_direct";
    public static void main(String[] args) throws IOException, InterruptedException {
        //获取链接/通道
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        //通道创建队列
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        //队列绑定到交换机
        channel.queueBind(QUEUE_NAME,EXCHANGE_NAME,"update");
        channel.queueBind(QUEUE_NAME,EXCHANGE_NAME,"delete");
        //同一时刻服务器只会发送一条消息给消费者
        channel.basicQos(1);
        //定义消费者队列
        QueueingConsumer consumer = new QueueingConsumer(channel);
        //监听队列，手动返回确认信息
        channel.basicConsume(QUEUE_NAME,false,consumer);
        //获取消息
        while (true){
            QueueingConsumer.Delivery delivery = consumer.nextDelivery();
            String s = new String(delivery.getBody());
            System.out.println("路由模式，消费者1获取消息===>" + s);
            Thread.sleep(10);
            channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);
        }
    }
}
```

```java
package com.zhang.routing;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.QueueingConsumer;
import com.zhang.ConnectionUtil;

import java.io.IOException;

/**
 * 路由模式，消费者2 看做是搜索系统
 */
public class Consumer02 {

    private final static String QUEUE_NAME = "test_routing2";
    private final static String EXCHANGE_NAME = "test_exchange_direct";
    public static void main(String[] args) throws IOException, InterruptedException {
        //获取链接/通道
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        //通道创建队列
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        //队列绑定到交换机
        channel.queueBind(QUEUE_NAME,EXCHANGE_NAME,"insert");
        channel.queueBind(QUEUE_NAME,EXCHANGE_NAME,"update");
        channel.queueBind(QUEUE_NAME,EXCHANGE_NAME,"delete");
        //同一时刻服务器只会发送一条消息给消费者
        channel.basicQos(1);
        //定义消费者队列
        QueueingConsumer consumer = new QueueingConsumer(channel);
        //监听队列，手动返回确认信息
        channel.basicConsume(QUEUE_NAME,false,consumer);
        //获取消息
        while (true){
            QueueingConsumer.Delivery delivery = consumer.nextDelivery();
            String s = new String(delivery.getBody());
            System.out.println("路由模式，消费者2获取消息===>" + s);
            Thread.sleep(100);
            channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);
        }
    }
}
```

### 5.5.topic -- 主题模式（通配符模式）

![image-20200601233952922](https://gitee.com/ubfirst/Typora/raw/master/img/20200601233953.png)

- 将路由键和某模式进行匹配,符号“#”匹配一个或多个词，“*”匹配一个词
- "topic.#" 能够匹配“topic.dd.aaa”,"topic.*"只能匹配格式“topic.xx”
- 同一个消息被多个消费者获取。一个消费者队列可以有多个消费者实例，只有其中一个消费者实例会消费到消息。

5.5.1.生产者

```java
package com.zhang.topic;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.zhang.ConnectionUtil;

import java.io.IOException;

/**
 * topic模式  生产者
 */
public class Provider {
    private static final String EXCHAGE_NAME = "test_exchange_topic";

    public static void main(String[] args) throws IOException {
        //获取连接
        Connection connection = ConnectionUtil.getConnection();
        //创建通道
        Channel channel = connection.createChannel();
        //声明交换机
        channel.exchangeDeclare(EXCHAGE_NAME,"topic");
        //消息内容
        String msg = "hello topic";
        channel.basicPublish(EXCHAGE_NAME,"routekey.1.1",null,msg.getBytes());
        System.out.println("topic模式，生产者启动===》"+msg);
        channel.close();
        connection.close();
    }

}

```

5.5.2.消费者

```java
package com.zhang.topic;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.QueueingConsumer;
import com.zhang.ConnectionUtil;

import java.io.IOException;

/**
 * topic模式，消费者1 看做是前台系统
 */
public class Consumer01 {

    private final static String QUEUE_NAME = "test_topic_work1";
    private final static String EXCHANGE_NAME = "test_exchange_topic";
    public static void main(String[] args) throws IOException, InterruptedException {
        //获取链接/通道
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        //通道创建队列
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        //队列绑定到交换机
        channel.queueBind(QUEUE_NAME,EXCHANGE_NAME,"routekey.#");
        //同一时刻服务器只会发送一条消息给消费者
        channel.basicQos(1);
        //定义消费者队列
        QueueingConsumer consumer = new QueueingConsumer(channel);
        //监听队列，手动返回确认信息
        channel.basicConsume(QUEUE_NAME,false,consumer);

        //获取消息
        while (true){
            QueueingConsumer.Delivery delivery = consumer.nextDelivery();
            String s = new String(delivery.getBody());
            System.out.println("topic模式，消费者1获取消息===>" + s);
            Thread.sleep(10);
            channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);

        }
    }
}

```

```java
package com.zhang.topic;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.QueueingConsumer;
import com.zhang.ConnectionUtil;

import java.io.IOException;

/**
 * 路由模式，消费者2 看做是搜索系统
 */
public class Consumer02 {

    private final static String QUEUE_NAME = "test_topic_work2";
    private final static String EXCHANGE_NAME = "test_exchange_topic";
    public static void main(String[] args) throws IOException, InterruptedException {
        //获取链接/通道
        Connection connection = ConnectionUtil.getConnection();
        Channel channel = connection.createChannel();
        //通道创建队列
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        //队列绑定到交换机
        channel.queueBind(QUEUE_NAME,EXCHANGE_NAME,"*.*");

        //同一时刻服务器只会发送一条消息给消费者
        channel.basicQos(1);
        //定义消费者队列
        QueueingConsumer consumer = new QueueingConsumer(channel);
        //监听队列，手动返回确认信息
        channel.basicConsume(QUEUE_NAME,false,consumer);

        //获取消息
        while (true){
            QueueingConsumer.Delivery delivery = consumer.nextDelivery();
            String s = new String(delivery.getBody());
            System.out.println("topic模式，消费者2获取消息===>" + s);
            Thread.sleep(100);
            channel.basicAck(delivery.getEnvelope().getDeliveryTag(),false);
        }
    }
}
```



































文章引自：https://blog.csdn.net/lzx1991610/article/details/102970854

