# RocketMQ

## 问题

1. 消息队列是什么
2. 消息队列的作用
3. rocket相较于其他产品有什么优势
4. 是怎么实现某些优势的
5. rocket的设计架构、设计理念
6. rocket的模块设计
7. **大量的final关键字的使用**，在局部变量内的使用，以及成员变量的使用
8. ExecutorService框架
9. CountDownLatch

## 模块

### 1. NameServer

1. `NameServer`启动

   > NameServer的启动类——`org.apache.rocketmq.namesrv.NamesrvStartup`
   >
   > ```java
   > 	// main函数启动，动用自定义的main0方法
   >     public static void main(String[] args) {
   >         main0(args);
   >     }
   > 
   >     public static NamesrvController main0(String[] args) {
   > 
   >         try {
   >           /* 创建NamesrvController */
   >           // step1:解析配置文件或命令行，填充NameServerConfig/NettyServerConfig
   >           // step2:根据启动属性，创建创建NamesrvController实例
   >             NamesrvController controller = createNamesrvController(args);
   >           // step3:注册JVM的hook函数并启动服务器，以便监听Broker，消息生产者的网络请求 
   >             start(controller);
   >             String tip = "The Name Server boot success. serializeType=" + RemotingCommand.getSerializeTypeConfigInThisServer();
   >             log.info(tip);
   >             System.out.printf("%s%n", tip);
   >             return controller;
   >         } catch (Throwable e) {
   >             e.printStackTrace();
   >             System.exit(-1);
   >         }
   > 
   >         return null;
   >     }
   > ```
   >
   > 

#### 1.1 设计架构

#### 1.2 对比zk

### 2 client

#### 2.1 发送者

> RocketMQ 消息发送的三种方式
>
> - sync：发送者向 MQ 执行发送消息的 API 时，同步等待，直到消息服务器返回发送结果
> - async：
> - oneway



