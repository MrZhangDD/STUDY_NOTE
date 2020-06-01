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





文章引自：https://blog.csdn.net/lzx1991610/article/details/102970854