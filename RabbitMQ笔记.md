# 概述

## 简介

> MQ(messagequeue)，从字面意思上看，本质是个队列，FIFO先入先出，只不过队列中存放的内容是message而已，还是一种跨进程的通信机制，用于上下游传递消息。在互联网架构中，MQ是一种非常常见的上下游“逻辑解耦+物理解耦”的消息通信服务。使用了MQ之后，消息发送上游只需要依赖MQ，不用依赖其他服务

## 作用

1. 流量消峰

   > 如果订单系统最多能处理一万次订单，这个处理能力应付正常时段的下单时绰绰有余，正常时段我们下单一秒后就能返回结果。但是在高峰期，如果有两万次下单操作系统是处理不了的，只能限制订单超过一万后不允许用户下单。使用消息队列做缓冲，我们可以取消这个限制，把一秒内下的订单分散成一段时间来处理，这时有些用户可能在下单十几秒后才能收到下单成功的操作，但是比不能下单的体验要好

2. 应用解耦

   > 以电商应用为例，应用中有订单系统、库存系统、物流系统、支付系统。用户创建订单后，如果耦合调用库存系统、物流系统、支付系统，任何一个子系统出了故障，都会造成下单操作异常。当转变成基于消息队列的方式后，系统间调用的问题会减少很多，比如物流系统因为发生故障，需要几分钟来修复。在这几分钟的时间里，物流系统要处理的内存被缓存在消息队列中，用户的下单操作可以正常完成。当物流系统恢复后，继续处理订单信息即可，中单用户感受不到物流系统的故障，提升系统的可用性

   ![](.\img\130.jpg)

3. 异步处理

   > 有些服务间调用是异步的，例如A调用B，B需要花费很长时间执行，但是A需要知道B什么时候可以执行完，以前一般有两种方式，A过一段时间去调用B的查询api查询。或者A提供一个callback api，B执行完之后调用api通知A服务。这两种方式都不是很优雅，使用消息总线，可以很方便解决这个问题，A调用B服务后，只需要监听B处理完成的消息，当B处理完成后，会发送一条消息给MQ，MQ会将此消息转发给A服务。这样A服务既不用循环调用B的查询api，也不用提供callbackapi。同样B服务也不用做这些操作。A服务还能及时的得到异步处理成功的消息

   ![](.\img\131.jpg)

## MQ分类

1. ActiveMQ

   > 优点：单机吞吐量万级，时效性ms级，可用性高，基于主从架构实现高可用性，消息可靠性较低的概率丢失数据
   >
   > 缺点:官方社区现在对ActiveMQ5.x**维护越来越少，高吞吐量场景较少使用**

2. Kafka

   > 大数据的杀手锏，谈到大数据领域内的消息传输，则绕不开Kafka，这款为**大数据而生**的消息中间件，以其**百万级TPS**的吞吐量名声大噪，迅速成为大数据领域的宠儿，在数据采集、传输、存储的过程中发挥着举足轻重的作用。目前已经被LinkedIn，Uber, Twitter, Netflix等大公司所采纳
   >
   > 优点: 性能卓越，单机写入TPS约在百万条/秒，最大的优点，就是**吞吐量高**。时效性ms级可用性非常高，kafka是分布式的，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用,消费者采用Pull方式获取消息, 消息有序, 通过控制能够保证所有消息被消费且仅被消费一次;有优秀的第三方KafkaWeb管理界面Kafka-Manager；在日志领域比较成熟，被多家公司和多个开源项目使用；功能支持：功能较为简单，主要支持简单的MQ功能，在大数据领域的实时计算以及日志采集被大规模使用
   >
   > 缺点：Kafka单机超过64个队列/分区，Load会发生明显的飙高现象，队列越多，load越高，发送消息响应时间变长，使用短轮询方式，实时性取决于轮询间隔时间，消费失败不支持重试；支持消息顺序，但是一台代理宕机后，就会产生消息乱序，**社区更新较慢**

3. RocketMQ

   > RocketMQ出自阿里巴巴的开源产品，用Java语言实现，在设计时参考了Kafka，并做出了自己的一些改进。被阿里巴巴广泛应用在订单，交易，充值，流计算，消息推送，日志流式处理，binglog分发等场景
   >
   > 优点:**单机吞吐量十万级**,可用性非常高，分布式架构,**消息可以做到0丢失**,MQ功能较为完善，还是分布式的，扩展性好,**支持10亿级别的消息堆积**，不会因为堆积导致性能下降,源码是java我们可以自己阅读源码，定制自己公司的MQ
   >
   > 缺点：支持的客户端语言不多，目前是java及c++，其中c++不成熟；社区活跃度一般,没有在MQ核心中去实现JMS等接口,有些系统要迁移需要修改大量代码

4. RabbitMQ

   > 2007年发布，是一个在AMQP(高级消息队列协议)基础上完成的，可复用的企业消息系统，是当前最主流的消息中间件之一
   >
   > 优点:由于erlang语言的**高并发特性**，性能较好；**吞吐量到万级**，MQ功能比较完备,健壮、稳定、易用、跨平台、**支持多种语言**如：Python、Ruby、.NET、Java、JMS、C、PHP、ActionScript、XMPP、STOMP等，支持AJAX文档齐全；开源提供的管理界面非常棒，用起来很好用,**社区活跃度高**；更新频率相当高
   >
   > 缺点：商业版需要收费,学习成本较高

## MQ选择

1. Kafka

   > Kafka主要特点是基于Pull的模式来处理消息消费，追求高吞吐量，一开始的目的就是用于日志收集和传输，适合产生**大量数据**的互联网服务的数据收集业务。**大型公司**建议可以选用，如果有**日志采集**功能，肯定是首选kafka了

2. RocketMQ

   > 天生为**金融互联网**领域而生，对于可靠性要求很高的场景，尤其是电商里面的订单扣款，以及业务削峰，在大量交易涌入时，后端可能无法及时处理的情况。RoketMQ在稳定性上可能更值得信赖，这些业务场景在阿里双11已经经历了多次考验，如果你的业务有上述并发场景，建议可以选择RocketMQ

3. RabbitMQ

   > 结合erlang语言本身的并发优势，性能好**时效性微秒级，社区活跃度也比较高**，管理界面用起来十分方便，如果你的**数据量没有那么大**，中小型公司优先选择功能比较完备的RabbitMQ

# RabbitMQ

## RabbitMQ概念

> RabbitMQ是一个消息中间件：它接受并转发消息。你可以把它当做一个快递站点，当你要发送一个包裹时，你把你的包裹放到快递站，快递员最终会把你的快递送到收件人那里，按照这种逻辑RabbitMQ是一个快递站，一个快递员帮你传递快件。RabbitMQ与快递站的主要区别在于，它不处理快件而是接收，存储和转发消息数据

## 四大核心概念

* 生产者：产生数据发送消息的程序是生产者
* 交换机：交换机是RabbitMQ非常重要的一个部件，一方面它接受来自生产者的消息，另一方面它将消息推送到队列中。交换机必须确切知道如何处理它接收到的消息，是将这些消息推送到特定队列还是推送到多个队列，亦或是把消息丢弃，这个得有交换机类型决定
* 队列：队列是RabbitMQ内部使用的一种数据结构，尽管消息流经RabbitMQ和应用程序，但它们只能存储在队列中。队列仅受主机的内存和磁盘限制的约束，本质上是一个大的消息缓冲区。许多生产者可以将消息发送到一个队列，许多消费者可以尝试从一个队列接受数据。这就是我们使用队列的方式
* 消费者：消费与接受具有相似的含义。消费者大多时候是一个等待接受消息的程序。请注意生产者，消费者和消息中间件很多时候并不在同一机器上。同一个应用程序既可以是生产者又可以是消费者

## 内部结构

<img src=".\img\132.jpg" style="zoom:200%;" />

1. **Message**
   * 消息，消息是不具名的，它由消息头和消息体组成。消息体是不透明的，而消息头则由一些列的可选属性组成，这些属性包括routing-key(路由键)、priority(相对于其他消息的优先权)、delivery-mode(指出该消息可能需要持久性存储)等。

2. **Publisher**
   * 消息的生产者，也是一个向交换器发布消息的客户端应用程序
3. **Exchange**
   * 交换器，用来接受生产者发送的消息并将这些消息路由给服务器中的队列
4. **Binding**
   * 绑定，用于消息队列和交换器之间的关联。一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则，所以可以将交换器理解成一个由绑定构成的路由表
5. **Queue**
   * 消息队列，用来保存消息直到发送给消费者。它是消息的容器，也是消息的终点。一个消息可投入一个或多个队列。消息一直在队列里面，等待消费者连接到这个队列将其取走
6. **Connection**
   * 网络连接，比如一个TCP连接
7. **Chanel**
   * 信道，多路复用连接中的一条独立的双向数据流通道。信道是建立在真实的TCP连接内地虚拟连接，AMQP命令都是通过信道发出去的，不管是发布消息、订阅队列还是接受消息，这些动作都是通过信道完成。因为对于操作系统来说建立和销毁TCP都是非常昂贵的开销，所以引入了信道的概念，以复用一条TCP连接

8. **Consumer**
   * 消息的消费者，表示一个从消息队列中取得消息的客户端应用程序
9. **Virtual Host**
   * 虚拟主机，表示一批交换器、消息队列和相关对象。虚拟主机是共享相同的身份认证和加密环境的独立服务器域。每个vhost本质上就是一个mini版的RabbitMQ服务器，拥有自己的队列、交换器、绑定和权限机制。vhost是AMQP概念的基础，必须在连接时指定，RabbitMQ默认是vhost是/
10. **Broker**
    * 表示消息队列服务器实体

# Work Queues

> 工作队列(又称任务队列)的主要思想是避免立即执行资源密集型任务，而不得不等待它完成。相反我们安排任务在之后执行。我们把任务封装为消息并将其发送到队列。在后台运行的工作进程将弹出任务并最终执行作业。当有多个工作线程时，这些工作线程将一起处理这些任务。

## 轮询分发消息

> 启动两个工作线程，一个消息发送线程，生产者发送的消息会被工作线程轮询的接受

* 代码实现

  ```java
  public class RabbitMqUtils {
  
      public static Channel getChannel() throws Exception {
          //创建一个连接工厂
          ConnectionFactory factory = new ConnectionFactory();
          factory.setHost("192.168.124.130");
          factory.setUsername("admin");
          factory.setPassword("123");
          //创建连接
          Connection connection = factory.newConnection();
          //获取信道
          return connection.createChannel();
      }
  }
  ```

  ```java
  public class Worker01 {
  
      public static final String QUEUE_NAME = "hello";
  
      public static void main(String[] args) throws Exception {
          Channel channel = RabbitMqUtils.getChannel();
  
          //接受消息的消息体
          DeliverCallback deliverCallback = (consumerTag, message) -> {
              System.out.println("接收到消息：" + new String(message.getBody()));
          };
  
          //消息接受被取消时，执行回调
          CancelCallback cancelCallback = (consumerTag) -> {
              System.out.println(consumerTag + "消费者取消消费接口回调逻辑");
          };
  
          System.out.println("C2等待接受消息：");
          //消费者消费消息
          channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);
      }
  }
  ```

  ```java
  public class Task01 {
  
      public static final String QUEUE_NAME = "hello";
  
      public static void main(String[] args) throws Exception {
          Channel channel = RabbitMqUtils.getChannel();
  
          channel.queueDeclare(QUEUE_NAME, false, false, false, null);
  
          //从控制台当中接受消息
          Scanner scanner = new Scanner(System.in);
          while (scanner.hasNext()) {
              String message = scanner.next();
              channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
              System.out.println("发送消息完成：" + message);
          }
  
      }
  }
  ```

## 消息应答

### 概念

> 消费者完成一个任务可能需要一段时间，如果其中一个消费者处理一个长的任务并仅只完成了部分突然它挂掉了，会发生什么情况。RabbitMQ一旦向消费者传递了一条消息，便立即将该消息标记为删除。在这种情况下，突然有个消费者挂掉了，我们将丢失正在处理的消息。以及后续发送给该消费这的消息，因为它无法接收到。
>
> 为了保证消息在发送过程中不丢失，rabbitmq引入消息应答机制，消息应答就是:**消费者在接收到消息并且处理该消息之后，告诉rabbitmq它已经处理了，rabbitmq 可以把该消息删除了**

### 自动应答

> 消息发送后立即被认为已经传送成功，这种模式需要在**高吞吐量和数据传输安全性方面做权衡**,因为这种模式如果消息在接收到之前，消费者那边出现连接或者channel关闭，那么消息就丢失了,当然另一方面这种模式消费者那边可以传递过载的消息，没有对传递的消息数量进行限制，当然这样有可能使得消费者这边由于接收太多还来不及处理的消息，导致这些消息的积压，最终使得内存耗尽，最终这些消费者线程被操作系统杀死，所以这种模式仅适用在消费者可以高效并以某种速率能够处理这些消息的情况下使用

### 消息应答的方法

1. **Channel.basicAck(long deliveryTag,boolean multiple)	用于肯定确认**

   * multiple的true和false代表不同意思：
     1. true代表批量应答channel上未应答的消息
        * 比如说channel上右传送tag的消息5，6，7，8，当前tag是8，那么此时的这些还未应答的消息都会被确认收到消息应
     2. false同上面相比
        * 只会应答tag=8的消息，5，6，7，这三个消息依然不会被确认收到消息应答

   * RabbitMQ已知道该消息并且成功的处理消息，可以将其丢弃了

2. **Channel.basicNack            用于否定确定**

3. **Channel.basicReject          用于否定确认**

   * 与basicNack相比少一个参数
   * 不处理该消息，直接拒绝，可以将其丢弃了

### 消息自动重新入伍

> 如果消费者由于某些原因失去连接(其通道已关闭，连接已关闭或TCP连接丢失)，导致消息未发送ACK确认，RabbitMQ将了解到消息未完全处理，**并将对其重新排队**。如果此时其他消费者可以处理，它将很快将其重新分发给另一个消费者。这样，即使某个消费者偶尔死亡，也可以确保不会丢失任何消息。

![](.\img\133.jpg)

### 消息手动应答

> 默认消息采用的是自动应答，想实现消息消费过程中不丢失，需要把自动应答改为手动应答

* 代码实现

  ```java
  public class Task02 {
  
      public static final String TASK_QUEUE_NAME = "ack_queue";
  
      public static void main(String[] args) throws Exception {
          Channel channel = RabbitMqUtils.getChannel();
  
          //声明队列
          channel.queueDeclare(TASK_QUEUE_NAME, false, false, false, null);
  
          //从控制台当中接受输入消息
          Scanner scanner = new Scanner(System.in);
          while (scanner.hasNext()) {
              String message = scanner.next();
              channel.basicPublish("", TASK_QUEUE_NAME, null, message.getBytes(StandardCharsets.UTF_8));
              System.out.println("发送消息完成：" + message);
          }
      }
  }
  ```

  ```java
  public class Worker03 {
  
      public static final String TASK_QUEUE_NAME = "ack_queue";
  
      public static void main(String[] args) throws Exception {
          Channel channel = RabbitMqUtils.getChannel();
  
          System.out.println("C3等待接受消息处理时间较短");
  
          //接受消息的消息体
          DeliverCallback deliverCallback = (consumerTag, message) -> {
              System.out.println("接收到消息：" + new String(message.getBody()));
              //睡眠1S
              SleepUtils.sleep(1);
              /**
               * 手动应答
               * 1.消息的标记 tag
               * 2.是否批量应答  false表示不批量应答
               */
              channel.basicAck(message.getEnvelope().getDeliveryTag(), false);
          };
  
          //消息接受被取消时，执行回调
          CancelCallback cancelCallback = (consumerTag) -> {
              System.out.println(consumerTag + "消费者取消消费接口回调逻辑");
          };
  
          //手动应答消息
          boolean autoAck = false;
          //消费者消费消息
          channel.basicConsume(TASK_QUEUE_NAME, autoAck, deliverCallback, cancelCallback);
      }
  }
  ```

  ```java
  public class Worker04 {
  
      public static final String TASK_QUEUE_NAME = "ack_queue";
  
      public static void main(String[] args) throws Exception {
          Channel channel = RabbitMqUtils.getChannel();
  
          System.out.println("C4等待接受消息处理时间较长");
  
          //接受消息的消息体
          DeliverCallback deliverCallback = (consumerTag, message) -> {
              System.out.println("接收到消息：" + new String(message.getBody()));
              //睡眠1S
              SleepUtils.sleep(30);
              /**
               * 手动应答
               * 1.消息的标记 tag
               * 2.是否批量应答  false表示不批量应答
               */
              channel.basicAck(message.getEnvelope().getDeliveryTag(), false);
          };
  
          //消息接受被取消时，执行回调
          CancelCallback cancelCallback = (consumerTag) -> {
              System.out.println(consumerTag + "消费者取消消费接口回调逻辑");
          };
  
          //手动应答消息
          boolean autoAck = false;
          //消费者消费消息
          channel.basicConsume(TASK_QUEUE_NAME, autoAck, deliverCallback, cancelCallback);
      }
  }
  ```

## 持久化

> 如何保障当RabbitMQ服务停掉以后消息生产者发送过来的消息不丢失。默认情况下RabbitMQ退出或由于某种原因崩溃时，它忽视队列和消息，除非告知它不要这样做。确保消息不会丢失需要做两件事：我们需要**将队列和消息都标记为持久化**。

### 队列实现持久化

```java
//让消息队列持久化
boolean durable=true;
channel.queueDeclare(TASK_QUEUE_NAME, durable, false, false, null);
```

* 如果之前声明的队列不是持久化，需要把原先队列先删除，或者重新创建一个持久化的队列，不然会出现错误

### 消息实现持久化

* 要想让消息实现持久化需要在消息生产者修改代码，MessageProperties.PERSISTENT_TEXT_PLAIN添加这个属性

```java
channel.basicPublish("", TASK_QUEUE_NAME, MessageProperties.PERSISTENT_TEXT_PLAIN, message.getBytes(StandardCharsets.UTF_8));
```

### 不公平分发

> 在最开始的时候使用RabbitMQ分发消息采用的轮训分发，但是在某种场景下这种策略并不是很好，比方说有两个消费者在处理任务，其中有个消费者1处理任务的速度非常快，而另外一个消费者2处理速度却很慢，这个时候我们还是采用轮训分发的化就会到这处理速度快的这个消费者很大一部分时间处于空闲状态，而处理慢的那个消费者一直在干活，这种分配方式在这种情况下其实就不太好，但是RabbitMQ并不知道这种情况它依然很公平的进行分发

* **设置参数`channel.basicQos(1)`**

  ```java
  int prefetchCount = 1;
  channel.basicQos(prefetchCount);
  ```

### 预取值

> 本身消息的发送就是异步发送的，所以在任何时候，channel上肯定不止只有一个消息另外来自消费者的手动确认本质上也是异步的。因此这里就存在一个未确认的消息缓冲区，因此希望开发人员能**限制此缓冲区的大小，以避免缓冲区里面无限制的未确认消息问题**。这个时候就可以通过使用basic.qos方法设置“预取计数”值来完成的。**该值定义通道上允许的未确认消息的最大数量**。一旦数量达到配置的数量，RabbitMQ将停止在通道上传递更多消息，除非至少有一个未处理的消息被确认，例如，假设在通道上有未确认的消息5、6、7，8，并且通道的预取计数设置为4，此时RabbitMQ将不会在该通道上再传递任何消息，除非至少有一个未应答的消息被ack。比方说tag=6这个消息刚刚被确认ACK，RabbitMQ将会感知这个情况到并再发送一条消息。消息应答和QoS预取值对用户吞吐量有重大影响。通常，增加预取将提高向消费者传递消息的速度。**虽然自动应答传输消息速率是最佳的，但是，在这种情况下已传递但尚未处理的消息的数量也会增加，从而增加了消费者的RAM消耗(随机存取存储器)**应该小心使用具有无限预处理的自动确认模式或手动确认模式，消费者消费了大量的消息如果没有确认的话，会导致消费者连接节点的内存消耗变大，所以找到合适的预取值是一个反复试验的过程，不同的负载该值取值也不同100到300范围内的值通常可提供最佳的吞吐量，并且不会给消费者带来太大的风险。预取值为1是最保守的。当然这将使吞吐量变得很低，特别是消费者连接延迟很严重的情况下，特别是在消费者连接等待时间较长的环境中。对于大多数应用来说，稍微高一点的值将是最佳的

# 发布确认

> 生产者将信道设置成confirm模式，一旦信道进入confirm模式，**所有在该信道上面发布的消息都将会被指派一个唯一的ID**(从1 开始)，一旦消息被投递到所有匹配的队列之后，broker就会发送一个确认给生产者(包含消息的唯一ID)，这就使得生产者知道消息已经正确到达目的队列了，如果消息和队列是可持久化的，那么确认消息会在将消息写入磁盘之后发出，broker回传给生产者的确认消息中delivery-tag域包含了确认消息的序列号，此外broker也可以设置basic.ack的multiple域，表示到这个序列号之前的所有消息都已经得到了处理。
>
> confirm模式最大的好处在于他是异步的，一旦发布一条消息，生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调方法来处理该确认消息，如果RabbitMQ因为自身内部错误导致消息丢失，就会发送一条nack消息，生产者应用程序同样可以在回调方法中处理该nack消息。

## 开启发布确认的方法

* 发布确认默认是没有开启的，如果要开启需要调用方法confirmSelect，每当你要想使用发布确认，都需要在channel上调用该方法

  `channel.confirmSelect();`

## 单个确认发布

> 这是一种简单的确认方式，它是一种**同步确认发布**的方式，也就是发布一个消息之后只有它被确认发布，后续的消息才能继续发布,waitForConfirmsOrDie(long)这个方法只有在消息被确认的时候才返回，如果在指定时间范围内这个消息没有被确认那么它将抛出异常。
>
> 这种确认方式有一个最大的缺点就是:**发布速度特别的慢**，因为如果没有确认发布的消息就会阻塞所有后续消息的发布，这种方式最多提供每秒不超过数百条发布消息的吞吐量。当然对于某些应用程序来说这可能已经足够了。

```java
public static void publishMessageIndividually() throws Exception {
    Channel channel = RabbitMqUtils.getChannel();
    //队列的声明
    String queueName = UUID.randomUUID().toString();
    channel.queueDeclare(queueName, true, false, false, null);
    //开启发布确认
    channel.confirmSelect();
    //开始时间
    long startTime = System.currentTimeMillis();
    //批量发消息
    for (int i = 0; i < MESSAGE_COUNT; i++) {
        String message = i + "";
        channel.basicPublish("", queueName, null, message.getBytes());
        //单个消息就马上进行发布确认
        boolean flag = channel.waitForConfirms();
        if (flag) {
            System.out.println("消息发送成功");
        }
    }
    //结束时间
    long endTime = System.currentTimeMillis();
    System.out.println("发布" + MESSAGE_COUNT + "个单独确认消息，耗时" + (endTime - startTime) + "ms");
}
```

## 批量确认发布

> 与单个等待确认消息相比，先发布一批消息然后一起确认可以极大地提高吞吐量，当然这种方式的缺点就是:当发生故障导致发布出现问题时，不知道是哪个消息出现问题了，我们必须将整个批处理保存在内存中，以记录重要的信息而后重新发布消息。当然这种方案仍然是同步的，也一样阻塞消息的发布。

```java
public static void publishMessageBatch() throws Exception {
    Channel channel = RabbitMqUtils.getChannel();
    //队列的声明
    String queueName = UUID.randomUUID().toString();
    channel.queueDeclare(queueName, true, false, false, null);
    //开启发布确认
    channel.confirmSelect();
    //开始时间
    long startTime = System.currentTimeMillis();
    //批量确认信息大小
    int batchSize = 100;
    //批量发送消息，批量发布确认
    for (int i = 0; i < MESSAGE_COUNT; i++) {
        String message = i + "";
        channel.basicPublish("", queueName, null, message.getBytes());
        //判断达到100条消息的时候，批量确认一次
        if (i % batchSize == 0) {
            //发布确认
            channel.waitForConfirms();
        }
    }
    //结束时间
    long endTime = System.currentTimeMillis();
    System.out.println("发布" + MESSAGE_COUNT + "个批量确认消息，耗时" + (endTime - startTime) + "ms");
}
```

## 异步确认发布

> 异步确认利用回调函数来达到消息可靠性传递的，这个中间件也是通过函数回调来保证是否投递成功

![](D:\Notes\img\134.jpg)

```java
public static void publishMessageAsync() throws Exception {
    Channel channel = RabbitMqUtils.getChannel();
    //队列的声明
    String queueName = UUID.randomUUID().toString();
    channel.queueDeclare(queueName, true, false, false, null);
    //开启发布确认
    channel.confirmSelect();
    /**
     * 线程安全有序的一个哈希表，适用于高并发的情况
     * 1.轻松的将序号与消息进行关联
     * 2.秦颂批量删除条目，只要给到序号
     * 3.支持高并发(多线程)
     */
    ConcurrentSkipListMap<Long, String> concurrentSkipListMap = new ConcurrentSkipListMap<>();

    /*
     * 消息确认成功，回调函数
     * 1. 消息的标记
     * 2. 是否为批量确认
     */
    ConfirmCallback ackCallback = (deliveryTag, multiple) -> {
        /**
         * 2.删除已经确认的消息
         */
        //判断是否是批量
        if (multiple) {
            ConcurrentNavigableMap<Long, String> confirmed = concurrentSkipListMap.headMap(deliveryTag);
            confirmed.headMap(deliveryTag);
        } else {
            concurrentSkipListMap.remove(deliveryTag);
        }
        System.out.println("确认的消息：" + deliveryTag);

    };
    //消息确认失败，回调函数
    ConfirmCallback nackCallback = (deliveryTag, multiple) -> {
        /**
         * 打印未确认的消息
         */
        String message = concurrentSkipListMap.get(deliveryTag);
        System.out.println("未确认的消息：" + message + "=====" + "未确认的消息tag:" + deliveryTag);
    };
    //准备消息的监听器，监听消息是否成功-异步
    channel.addConfirmListener(ackCallback, nackCallback);
    //开始时间
    long startTime = System.currentTimeMillis();
    //批量发送消息
    for (int i = 0; i < MESSAGE_COUNT; i++) {
        String message = i + "";
        channel.basicPublish("", queueName, null, message.getBytes());
        /**
         * 1.记录下所有要发送的消息
         */
        concurrentSkipListMap.put(channel.getNextPublishSeqNo(), message);
    }
    //结束时间
    long endTime = System.currentTimeMillis();
    System.out.println("发布" + MESSAGE_COUNT + "个异步确认消息，耗时" + (endTime - startTime) + "ms");
}
```

# 交换机

> RabbitMQ消息传递模型的核心思想是: **生产者生产的消息从不会直接发送到队列**。实际上，通常生产者甚至都不知道这些消息传递传递到了哪些队列中。
>
> 相反，**生产者只能将消息发送到交换机(exchange)**，交换机工作的内容非常简单，一方面它接收来自生产者的消息，另一方面将它们推入队列。交换机必须确切知道如何处理收到的消息。是应该把这些消息放到特定队列还是说把他们到许多队列中还是说应该丢弃它们。这就的由交换机的类型来决定。

## Exchange 类型

> Exchange分发消息时根据类型的不同分发策略有区别，目前共四种类型： direct、fanout、topic、headers

* Direct Exchange

  * 消息中的路由键(routing key)如果和 Binding中的(bindingkey)一致，交换器就将消息发到对应的队列中。它是**完全匹配、单播的模式**

    ![](.\img\37.jpg)

* Fanout Exchange

  * 每个发到 fanout 类型交换器的消息都会分到所有绑定的队列上去。fanout交换器不处理路由键， 只是简单的将队列绑定到交换器上，每个发送到交换器的消息都会被转发到与该交换器绑定的所有队列上。很像**子网广播**，每台子网内的 主机都获得了一份复制的消息。fanout 类型转发消息是最快的。

    ![](.\img\36.jpg)

* Topic Exchange

  * topic 交换器通过模式匹配分配消息的路由键属性，将路由键和某个模式进行匹配，此时队列需要绑定到一个模式上。它将路由键和绑定键的字符串切分成单词，这些单词之间用点隔开。 它同样也会识别两个通配符：**符号“#”和符号 “*”。#匹配0个或多个单词， *匹配一个单词**。

    ![](.\img\mq.png)

# 死信队列

> 死信，顾名思义就是无法被消费的消息，字面意思可以这样理解，一般来说，producer将消息投递到broker或者直接到queue里了，consumer从queue取出消息进行消费，但某些时候由于特定的原因**导致queue中的某些消息无法被消费，这样的消息如果没有后续的处理，就变成了死信，有死信自然就有了死信队列。**

## 死信的来源

1. 消息TTL(存活时间)过期
2. 队列达到最大长度(队列满了，无法再添加数据到mq中)
3. 消息被拒绝(basic.reject和basic.nack)并且requeue=false

## 代码架构图

![](.\img\135.jpg)

## 消息TTL过期

1. 生产者代码

   ```java
   public class Producer {
   
       //普通交换机名称
       public static final String NORMAL_EXCHANGE = "normal_exchange";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMqUtils.getChannel();
           //设置消息的TTL时间 单位ms
           AMQP.BasicProperties properties = new AMQP.BasicProperties().builder().expiration("10000").build();
           for (int i = 1; i < 11; i++) {
               String message = "info" + i;
               channel.basicPublish(NORMAL_EXCHANGE, "normal", properties, message.getBytes(StandardCharsets.UTF_8));
           }
       }
   }
   ```

2. 消费者01代码

   ```java
   public class Consumer01 {
   
       //普通交换机名称
       public static final String NORMAL_EXCHANGE = "normal_exchange";
       //普通队列名称
       public static final String NORMAL_QUEUE = "normal_queue";
       //死信交换机名称
       public static final String DEAD_EXCHANGE = "dead_exchange";
       //死信队列名称
       public static final String DEAD_QUEUE = "dead_queue";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMqUtils.getChannel();
           //声明死信和普通交换机，类型为direct
           channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.DIRECT);
           channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.DIRECT);
   
           //声明死信队列
           channel.queueDeclare(DEAD_QUEUE, false, false, false, null);
           //死信队列绑定死信交换机与routingKey
           channel.queueBind(DEAD_QUEUE, DEAD_EXCHANGE, "dead");
   
           //正常队列绑定死信队列信息
           HashMap<String, Object> params = new HashMap<>();
           //正常队列设置死信交换机 参数key是固定值
           params.put("x-dead-letter-exchange", DEAD_EXCHANGE);
           //正常队列设置死信routingKey，参数key是固定值
           params.put("x-dead-letter-routing-key", "dead");
   
           //声明普通队列
           channel.queueDeclare(NORMAL_QUEUE, false, false, false, params);
           channel.queueBind(NORMAL_QUEUE, NORMAL_EXCHANGE, "normal");
   
           System.out.println("C1等待接受消息...");
           DeliverCallback deliverCallback = (consumerTag, delivery) -> {
               String message = new String(delivery.getBody(), StandardCharsets.UTF_8);
               System.out.println("C1接收到消息:" + message);
           };
           channel.basicConsume(NORMAL_QUEUE, true, deliverCallback, consumerTag -> {
           });
       }
   }
   ```

3. 消费者02代码

   ```java
   public class Consumer02 {
   
       //死信队列名称
       public static final String DEAD_QUEUE = "dead_queue";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMqUtils.getChannel();
   
           System.out.println("等待接受死信队列消息....");
           DeliverCallback deliverCallback = (consumerTag, message) -> {
               System.out.println("C2接收死信队列的消息:" + new String(message.getBody(), StandardCharsets.UTF_8));
           };
           channel.basicConsume(DEAD_QUEUE, true, deliverCallback, consumerTag -> {
           });
       }
   }
   
   ```

## 队列达到最大长度

1. 生产者代码

   ```java
   public class Producer {
   
       //普通交换机名称
       public static final String NORMAL_EXCHANGE = "normal_exchange";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMqUtils.getChannel();
           for (int i = 1; i < 11; i++) {
               String message = "info" + i;
               channel.basicPublish(NORMAL_EXCHANGE, "normal", null, message.getBytes(StandardCharsets.UTF_8));
               System.out.println("生产者发送消息:" + message);
           }
       }
   }
   ```

2. 消费者01代码

   ```java
   public class Consumer01 {
   
       //普通交换机名称
       public static final String NORMAL_EXCHANGE = "normal_exchange";
       //普通队列名称
       public static final String NORMAL_QUEUE = "normal_queue";
       //死信交换机名称
       public static final String DEAD_EXCHANGE = "dead_exchange";
       //死信队列名称
       public static final String DEAD_QUEUE = "dead_queue";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMqUtils.getChannel();
           //声明死信和普通交换机，类型为direct
           channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.DIRECT);
           channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.DIRECT);
   
           //声明死信队列
           channel.queueDeclare(DEAD_QUEUE, false, false, false, null);
           //死信队列绑定死信交换机与routingKey
           channel.queueBind(DEAD_QUEUE, DEAD_EXCHANGE, "dead");
   
           //正常队列绑定死信队列信息
           HashMap<String, Object> params = new HashMap<>();
           //正常队列设置死信交换机 参数key是固定值
           params.put("x-dead-letter-exchange", DEAD_EXCHANGE);
           //正常队列设置死信routingKey，参数key是固定值
           params.put("x-dead-letter-routing-key", "dead");
           //设置正常队列长度的限制
           params.put("x-max-length", 6);
   
           //声明普通队列
           channel.queueDeclare(NORMAL_QUEUE, false, false, false, params);
           channel.queueBind(NORMAL_QUEUE, NORMAL_EXCHANGE, "normal");
   
           System.out.println("C1等待接受消息...");
           DeliverCallback deliverCallback = (consumerTag, delivery) -> {
               String message = new String(delivery.getBody(), StandardCharsets.UTF_8);
               System.out.println("C1接收到消息:" + message);
           };
           channel.basicConsume(NORMAL_QUEUE, true, deliverCallback, consumerTag -> {
           });
       }
   }
   ```

3. 消费者02代码同上

## 消息被拒

1. 生产者代码同上

2. 消费者代码01

   ```java
   public class Consumer01 {
   
       //普通交换机名称
       public static final String NORMAL_EXCHANGE = "normal_exchange";
       //普通队列名称
       public static final String NORMAL_QUEUE = "normal_queue";
       //死信交换机名称
       public static final String DEAD_EXCHANGE = "dead_exchange";
       //死信队列名称
       public static final String DEAD_QUEUE = "dead_queue";
   
       public static void main(String[] args) throws Exception {
           Channel channel = RabbitMqUtils.getChannel();
           //声明死信和普通交换机，类型为direct
           channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.DIRECT);
           channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.DIRECT);
   
           //声明死信队列
           channel.queueDeclare(DEAD_QUEUE, false, false, false, null);
           //死信队列绑定死信交换机与routingKey
           channel.queueBind(DEAD_QUEUE, DEAD_EXCHANGE, "dead");
   
           //正常队列绑定死信队列信息
           HashMap<String, Object> params = new HashMap<>();
           //正常队列设置死信交换机 参数key是固定值
           params.put("x-dead-letter-exchange", DEAD_EXCHANGE);
           //正常队列设置死信routingKey，参数key是固定值
           params.put("x-dead-letter-routing-key", "dead");
           //设置正常队列长度的限制
           params.put("x-max-length", 6);
   
           //声明普通队列
           channel.queueDeclare(NORMAL_QUEUE, false, false, false, params);
           channel.queueBind(NORMAL_QUEUE, NORMAL_EXCHANGE, "normal");
   
           System.out.println("C1等待接受消息...");
           DeliverCallback deliverCallback = (consumerTag, delivery) -> {
               String message = new String(delivery.getBody(), StandardCharsets.UTF_8);
               if (message.equals("info5")) {
                   System.out.println("C1接收到消息:" + message + "并拒绝签收该消息");
                   //requeue设置为false 代表拒绝重新入队，该队列如果配置了死信交换机，就将消息发送到死信队列中
                   channel.basicReject(delivery.getEnvelope().getDeliveryTag(), false);
               } else {
                   System.out.println("C1接收到消息:" + message);
                   channel.basicAck(delivery.getEnvelope().getDeliveryTag(), false);
               }
           };
           channel.basicConsume(NORMAL_QUEUE, false, deliverCallback, consumerTag -> {
           });
       }
   }
   ```

3. 消费者02代码同上

# 延迟队列

> 延时队列,队列内部是有序的，最重要的特性就体现在它的延时属性上，延时队列中的元素是希望在指定时间到了以后或之前取出和处理，简单来说，延时队列就是用来存放需要在指定时间被处理的元素的队列

## 使用场景

1. 订单在十分钟之内未支付则自动取消
2. 新创建的店铺，如果在十天内都没有上传过商品，则自动发送消息提醒
3. 用户注册成功后，如果三天内没有登陆则进行短信提醒
4. 用户发起退款，如果三天内没有得到处理则通知相关运营人员
5. 预定会议后，需要在预定的时间点前十分钟通知各个与会人员参加会议

## RabbitMQ中的TTL

> TTL是RabbitMQ中一个消息或者队列的属性，表明一条消息或者该队列中的所有消息的最大存活时间，单位是毫秒。换句话说，如果一条消息设置了TTL属性或者进入了设置TTL属性的队列，那么这条消息如果在TTL设置的时间内没有被消费，则会成为"死信"。如果同时配置了队列的TTL和消息的TTL，那么较小的那个值将会被使用，有两种方式设置TTL

### 消息设置TTL

```java
rabbitTemplate.convertAndSend("X", "XC", message, correlationData -> {
            correlationData.getMessageProperties().setExpiration(ttlTime);
            return correlationData;
        });
```

### 队列设置TTL

```java
//声明队列的TTL
args.put("x-message-ttl", 40000);
return QueueBuilder.durable(QUEUE_B).withArguments(args).build();
```

### 两者区别

> 如果设置了队列的TTL属性，那么一旦消息过期，就会被队列丢弃(如果配置了死信队列被丢到死信队列中)，而第二种方式，消息即使过期，也不一定会被马上丢弃，因为**消息是否过期是在即将投递到消费者之前判定的**，如果当前队列有严重的消息积压情况，则已过期的消息也许还能存活较长时间；另外，还需要注意的一点是，如果不设置TTL，表示消息永远不会过期，如果将TTL设置为0，则表示除非此时可以直接投递该消息到消费者，否则该消息将会被丢弃

## 队列TTL

> 创建两个队列QA和QB，两者队列TTL分别设置为10S和40S，然后创建一个交换机X和死信交换机Y，它们的类型都是Direct，创建一个死信队列QD
>
> ![](D:\Notes\img\136.jpg)

### 配置类代码

```java
@Configuration
public class RabbitMQConfig {
    //普通交换机
    public static final String X_EXCHANGE = "X";
    //死信交换机
    public static final String Y_DEAD_LETTER_EXCHANGE = "Y";
    //普通队列
    public static final String QUEUE_A = "QA";
    public static final String QUEUE_B = "QB";
    //死信队列
    public static final String DEAD_LETTER_QUEUE = "QD";

    /**
     * 声明普通X交换机
     */
    @Bean("xExchange")
    public DirectExchange xExchange() {
        return new DirectExchange(X_EXCHANGE);
    }

    /**
     * 声明死信Y交换机
     */
    @Bean("yExchange")
    public DirectExchange yExchange() {
        return new DirectExchange(Y_DEAD_LETTER_EXCHANGE);
    }

    /**
     * 声明普通A队列，TTL为10S，并绑定到对应的死信Y交换机
     */
    @Bean("queueA")
    public Queue queueA() {
        HashMap<String, Object> args = new HashMap<>(3);
        //声明当前队列绑定的死信交换机
        args.put("x-dead-letter-exchange", Y_DEAD_LETTER_EXCHANGE);
        //声明当前队列的死信路由key
        args.put("x-dead-letter-routing-key", "YD");
        //声明队列的TTL 单位ms
        args.put("x-message-ttl", 10000);
        return QueueBuilder.durable(QUEUE_A).withArguments(args).build();
    }

    /**
     * 普通A队列绑定普通X交换机
     */
    @Bean
    public Binding queueABindingX(@Qualifier("queueA") Queue queueA, @Qualifier("xExchange") DirectExchange xExchange) {
        return BindingBuilder.bind(queueA).to(xExchange).with("XA");
    }

    /**
     * 声明普通B队列，TTL为40S，并绑定到对应的死信Y交换机
     */
    @Bean("queueB")
    public Queue queueB() {
        HashMap<String, Object> args = new HashMap<>(3);
        //声明当前队列绑定的死信交换机
        args.put("x-dead-letter-exchange", Y_DEAD_LETTER_EXCHANGE);
        //声明当前队列的死信路由key
        args.put("x-dead-letter-routing-key", "YD");
        //声明队列的TTL
        args.put("x-message-ttl", 40000);
        return QueueBuilder.durable(QUEUE_B).withArguments(args).build();
    }

    /**
     * 普通B队列绑定普通X交换机
     */
    @Bean
    public Binding queueBBindingX(@Qualifier("queueB") Queue queueB, @Qualifier("xExchange") DirectExchange xExchange) {
        return BindingBuilder.bind(queueB).to(xExchange).with("XB");
    }

    /**
     * 声明死信D队列
     */
    @Bean("queueD")
    public Queue queueD() {
        return QueueBuilder.durable(DEAD_LETTER_QUEUE).build();
    }

    /**
     * 死信D队列绑定死信Y交换机
     */
    @Bean
    public Binding deadLetterBindingQAD(@Qualifier("queueD") Queue queueD, @Qualifier("yExchange") DirectExchange yExchange) {
        return BindingBuilder.bind(queueD).to(yExchange).with("YD");
    }
}
```

### 消息生产者代码

```java
@RestController
@Slf4j
@RequestMapping("/ttl")
public class SendMsgController {

    private final RabbitTemplate rabbitTemplate;

    @Autowired
    public SendMsgController(RabbitTemplate rabbitTemplate) {
        this.rabbitTemplate = rabbitTemplate;
    }

    @GetMapping("/sendMsg/{message}")
    public void sendMsg(@PathVariable String message) {
        log.info("当前时间:{},发送一条信息给两个TTL队列：{}", new Date().toString(), message);
        rabbitTemplate.convertAndSend("X", "XA", "消息来自TTL为10S的队列:" + message);
        rabbitTemplate.convertAndSend("X", "XB", "消息来自TTL为40S的队列:" + message);
    }
}
```

### 消费者代码

```java
@Slf4j
@Component
public class DeadLetterQueueConsumer {

    @RabbitListener(queues = "QD")
    public void receiveD(Message message, Channel channel) throws Exception {
        String msg = new String(message.getBody());
        log.info("当前时间:{},收到死信队列信息{}", new Date().toString(), msg);
    }
}
```

## 延时队列优化

> 新增一个队列QC，该队列不设置TTL时间
>
> ![](D:\Notes\img\137.jpg)

### 配置类代码

```java
@Configuration
public class RabbitMQConfig {
    //普通队列
    public static final String QUEUE_C = "QC";

    /**
     * 声明普通C队列，并绑定到对应的死信Y交换机
     */
    @Bean("queueC")
    public Queue queueC() {
        HashMap<String, Object> args = new HashMap<>(2);
        //声明当前队列绑定的死信交换机
        args.put("x-dead-letter-exchange", Y_DEAD_LETTER_EXCHANGE);
        //声明当前队列的死信路由key
        args.put("x-dead-letter-routing-key", "YD");
        return QueueBuilder.durable(QUEUE_C).withArguments(args).build();
    }

    /**
     * 普通C队列绑定普通X交换机
     */
    @Bean
    public Binding queueCBindingX(@Qualifier("queueC") Queue queueC, @Qualifier("xExchange") DirectExchange xExchange) {
        return BindingBuilder.bind(queueC).to(xExchange).with("XC");
    }
}
```

### 消息生产者代码

```java
@RestController
@Slf4j
@RequestMapping("/ttl")
public class SendMsgController {

    private final RabbitTemplate rabbitTemplate;

    @Autowired
    public SendMsgController(RabbitTemplate rabbitTemplate) {
        this.rabbitTemplate = rabbitTemplate;
    }

    @GetMapping("/sendExpirationMsg/{message}/{ttlTime}")
    public void sendMsg(@PathVariable String message, @PathVariable String ttlTime) {
        rabbitTemplate.convertAndSend("X", "XC", message, correlationData -> {
            correlationData.getMessageProperties().setExpiration(ttlTime);
            return correlationData;
        });
        log.info("当前时间：{},发送一条时长{}毫秒TTL信息给队列C：{}" + new Date().toString(), ttlTime, message);
    }
}
```

> **缺陷**：消息可能并不会按时“死亡“，因为RabbitMQ只会检查第一个消息是否过期，如果过期则丢到死信队列，如果第一个消息的延时时长很长，而第二个消息的延时时长很短，第二个消息并不会优先得到执行

## RabbitMQ插件实现延迟队列

### 安装延时队列插件

1. 在官网下载`rabbitmq_delayed_message_exchange`插件，然后解压放置到RabbitMQ的插件目
2. 加载插件`rabbitmq-plugins enable rabbitmq_delayed_message_exchange`

### 插件实现延迟队列

> 新增了一个队列delayed.queue,一个自定义交换机delayed.exchange
>
> ![](D:\Notes\img\138.jpg)

### 配置文件类代码

> 自定义的交换机中，这是一种新的交换类型，该类型消息支持延迟投递机制消息传递后并不会立即投递到目标队列中，而是存储在mnesia(一个分布式数据系统)表中，当达到投递时间时，才投递到目标队列中

```java
@Configuration
public class DelayedQueueConfig {
    public static final String DELAYED_QUEUE_NAME = "delayed.queue";
    public static final String DELAYED_EXCHANGE_NAME = "delayed.exchange";
    public static final String DELAYED_ROUTING_KEY = "delayed.routingkey";

    /**
     * 声明延迟队列
     */
    @Bean("delayedQueue")
    public Queue delayedQueue() {
        return new Queue(DELAYED_QUEUE_NAME);
    }

    /**
     * 声明延迟交换机
     */
    @Bean("delayedExchange")
    public CustomExchange delayedExchange() {
        HashMap<String, Object> args = new HashMap<>();
        //自定义交换机的类型
        args.put("x-delayed-type", "direct");
        return new CustomExchange(DELAYED_EXCHANGE_NAME, "x-delayed-message", true, false, args);
    }

    /**
     * 延迟队列绑定延迟交换机
     */
    @Bean
    public Binding bindingDelayedQueue(@Qualifier("delayedQueue") Queue delayedQueue, @Qualifier("delayedExchange") CustomExchange delayedExchange) {
        return BindingBuilder.bind(delayedQueue).to(delayedExchange).with(DELAYED_ROUTING_KEY).noargs();
    }
}
```

### 消息生产者代码

```java
@RestController
@Slf4j
@RequestMapping("/ttl")
public class SendMsgController {

    private final RabbitTemplate rabbitTemplate;

    @Autowired
    public SendMsgController(RabbitTemplate rabbitTemplate) {
        this.rabbitTemplate = rabbitTemplate;
    }

    @GetMapping("/sendDelayMsg/{message}/{delayTime}")
    public void sendMsg(@PathVariable String message, @PathVariable Integer delayTime) {
        rabbitTemplate.convertAndSend("delayed.exchange", "delayed.routingkey", message, correlationData -> {
            correlationData.getMessageProperties().setDelay(delayTime);
            return correlationData;
        });
        log.info("当前时间:{},发送一条延时{}毫秒的信息给队列delayed.queue:{}", new Date().toString(), delayTime, message);
    }
}
```

### 消息消费者代码

```java
@Slf4j
@Component
public class DeadLetterQueueConsumer {

    @RabbitListener(queues = "delayed.queue")
    public void receiveDelayedQueue(Message message) {
        String msg = new String(message.getBody());
        log.info("当前时间:{},收到延时队列的消息:{}", new Date().toString(), msg);
    }
}
```

# 发布确认高级

> 在生产环境中由于一些不明原因，导致rabbitmq重启，在RabbitMQ重启期间生产者消息投递失败，导致消息丢失，需要手动处理和恢复。如何才能进行RabbitMQ的消息可靠投递呢？特别是在这样比较极端的情况，RabbitMQ集群不可用的时候，无法投递的消息该如何处理呢

## 发布确认

### 确认机制方案

![](.\img\139.jpg)

### 配置文件

```yaml
spring:
  rabbitmq:
    publisher-confirm-type: correlated
```

* NONE：禁用发布确认模式，是默认值
* CORRELATED：发布消息成功到交换器后触发回调方法
* SIMPLE：和CORRELATED效果类似

### 添加配置类

```java
@Configuration
public class ConfirmConfig {

    public static final String CONFIRM_EXCHANGE_NAME = "confirm.exchange";
    public static final String CONFIRM_QUEUE_NAME = "confirm.queue";

    /**
     * 声明确认交换机
     */
    @Bean("confirmExchange")
    public DirectExchange confirmExchange() {
        return new DirectExchange(CONFIRM_EXCHANGE_NAME);
    }

    /**
     * 声明确认队列
     */
    @Bean("confirmQueue")
    public Queue confirmQueue() {
        return QueueBuilder.durable(CONFIRM_QUEUE_NAME).build();
    }

    /**
     * 队列绑定交换机
     */
    @Bean
    public Binding queueBBinding(@Qualifier("confirmQueue") Queue confirmQueue, @Qualifier("confirmExchange") DirectExchange confirmExchange) {
        return BindingBuilder.bind(confirmQueue).to(confirmExchange).with("key1");
    }
}
```

### 消息生产者

```java
/**
 * Author:   lxs
 * Date:     2021/9/28 18:17
 * Description: 消息生产者-发布确认高级
 */
@RestController
@Slf4j
@RequestMapping("/confirm")
public class ProducerController {

    public static final String CONFIRM_EXCHANGE_NAME = "confirm.exchange";

    private final RabbitTemplate rabbitTemplate;

    @Autowired
    public ProducerController(RabbitTemplate rabbitTemplate) {
        this.rabbitTemplate = rabbitTemplate;
    }

    @GetMapping("/sendMessage/{message}")
    public void sendMessage(@PathVariable String message) {
        //指定消息id为1
        CorrelationData correlationData1 = new CorrelationData(UUID.randomUUID().toString());
        String routingKey = "key1";
        rabbitTemplate.convertAndSend(CONFIRM_EXCHANGE_NAME, routingKey, message + routingKey, correlationData1);
        //指定消息为2
        CorrelationData correlationData2 = new CorrelationData(UUID.randomUUID().toString());
        routingKey = "key2";
        rabbitTemplate.convertAndSend(CONFIRM_EXCHANGE_NAME, routingKey, message + routingKey, correlationData2);
        log.info("发送消息内容：{}", message);
    }
}
```

### 回调接口

```java
/**
 * Author:   lxs
 * Date:     2021/9/28 18:18
 * Description: 回调接口
 */
@Component
@Slf4j
public class MyCallBack implements RabbitTemplate.ConfirmCallback {


    private final RabbitTemplate rabbitTemplate;

    @Autowired
    public MyCallBack(RabbitTemplate rabbitTemplate) {
        this.rabbitTemplate = rabbitTemplate;
    }

    /**
     * @PostConstruct 是Java注解
     * 该注解被用来修饰一个非静态的void（）方法。被@PostConstruct修饰的方法会在服务器加载Servlet的时候运行，并且只会被服务器执行一次
     */
    @PostConstruct
    public void init() {
        //注入
        rabbitTemplate.setConfirmCallback(this);
    }

    /**
     * 交换机不管是否收到消息的一个回调方法
     *
     * @param correlationData 消息相关数据
     * @param ack             交换机是否收到消息，true收到消息，false未收到消息
     * @param cause           失败原因
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        String id = correlationData != null ? correlationData.getId() : "";
        if (ack) {
            log.info("交换机已经收到id为：{}的消息", id);
        } else {
            log.info("交换机还未收到id为：{}消息，由于原因:{}", id, cause);
        }
    }
}
```

### 消息消费者

```java
@Component
@Slf4j
public class ConfirmConsumer {

    public static final String CONFIRM_QUEUE_NAME = "confirm.queue";

    @RabbitListener(queues = CONFIRM_QUEUE_NAME)
    public void receiveMsg(Message message) {
        String msg = new String(message.getBody());
        log.info("接受到队列" + CONFIRM_QUEUE_NAME + "消息：{}", msg);
    }
}
```

### 结果分析

> 两条消息都成功被交换机接收，也收到了交换机的确认回调，但消费者只收到了一条消息，因为第二条消息的routingkey与队列的Bingdingkey不一致，也没有其他队列能接受这个消息，所以消息被直接丢弃了。

## 回退消息

> 在仅开启了生产者确认机制的情况下，交换机接收到消息后，会直接给消息生产者发送确认消息，如果发现该消息不可路由，那么消息会被直接丢弃，此时生产者是不知道消息被丢弃这个事件的
>
> **通过设置mandatory参数可以在当消息传递过程中不可达目的地时将消息返回给生产者**

### 配置文件

```yaml
spring:
  rabbitmq:
    publisher-returns: true
```

### 回调接口代码

```java
/**
 * Author:   lxs
 * Date:     2021/9/28 18:18
 * Description: 回调接口
 */
@Component
@Slf4j
public class MyCallBack implements RabbitTemplate.ConfirmCallback, RabbitTemplate.ReturnsCallback {


    private final RabbitTemplate rabbitTemplate;

    @Autowired
    public MyCallBack(RabbitTemplate rabbitTemplate) {
        this.rabbitTemplate = rabbitTemplate;
    }

    /**
     * @PostConstruct 是Java注解
     * 该注解被用来修饰一个非静态的void（）方法。被@PostConstruct修饰的方法会在服务器加载Servlet的时候运行，并且只会被服务器执行一次
     */
    @PostConstruct
    public void init() {
        //将回调接口注入到rabbitTemplate
        rabbitTemplate.setConfirmCallback(this);
        //将消息返回给生产者注入到rabbitTemplate
        rabbitTemplate.setReturnsCallback(this);
    }

    /**
     * 交换机不管是否收到消息的一个回调方法
     *
     * @param correlationData 消息相关数据
     * @param ack             交换机是否收到消息，true收到消息，false未收到消息
     * @param cause           失败原因
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        String id = correlationData != null ? correlationData.getId() : "";
        if (ack) {
            log.info("交换机已经收到id为：{}的消息", id);
        } else {
            log.info("交换机还未收到id为：{}消息，由于原因:{}", id, cause);
        }
    }

    /**
     * 可以在消息传递过程中不可达目的时将消息返回给生产者
     */
    @Override
    public void returnedMessage(ReturnedMessage returnedMessage) {
        //消息体
        String msg = new String(returnedMessage.getMessage().getBody());
        //退回原因
        String replyText = returnedMessage.getReplyText();
        //交换机
        String exchange = returnedMessage.getExchange();
        //路由
        String routingKey = returnedMessage.getRoutingKey();
        log.error("消息：{}被服务器退回，退回原因：{}，交换机是：{}，路由key：{}", msg, replyText, exchange, routingKey);
    }
}
```

## 备份交换机

> 备份交换机可以理解为RabbitMQ中交换机的“备胎”，当我们为某一个交换机声明一个对应的备份交换机时，就是为它创建一个备胎，当交换机接收到一条不可路由消息时，将会把这条消息转发到备份交换机中，由备份交换机来进行转发和处理，通常备份交换机的类型为Fanout，这样就能把所有消息都投递到与其绑定的队列中，然后我们在备份交换机下绑定一个队列，这样所有那些原交换机无法被路由的消息，就会都进入这个队列了

### 代码架构图

![](.\img\140.jpg)

### 添加配置类

```java
/**
 * Author:   lxs
 * Date:     2021/9/28 18:07
 * Description: 发布确认高级
 */
@Configuration
public class ConfirmConfig {

    public static final String CONFIRM_EXCHANGE_NAME = "confirm.exchange";
    public static final String CONFIRM_QUEUE_NAME = "confirm.queue";
    public static final String BACKUP_EXCHANGE_NAME = "backup.exchange";
    public static final String BACKUP_QUEUE = "backup.queue";
    public static final String WARING_QUEUE_NAME = "waring.queue";

    /**
     * 声明确认交换机的备份交换机
     */
    @Bean("confirmExchange")
    public DirectExchange confirmExchange() {
        return ExchangeBuilder.directExchange(CONFIRM_EXCHANGE_NAME)
                .durable(true)
                .withArgument("alternate-exchange", BACKUP_EXCHANGE_NAME).build();
    }

    /**
     * 声明备份交换机
     */
    @Bean("backupExchange")
    public FanoutExchange backupExchange() {
        return new FanoutExchange(BACKUP_EXCHANGE_NAME);
    }

    /**
     * 声明确认队列
     */
    @Bean("confirmQueue")
    public Queue confirmQueue() {
        return QueueBuilder.durable(CONFIRM_QUEUE_NAME).build();
    }

    /**
     * 声明备份队列
     */
    @Bean("backupQueue")
    public Queue backupQueue() {
        return QueueBuilder.durable(BACKUP_QUEUE).build();
    }

    /**
     * 声明警告队列
     */
    @Bean("waringQueue")
    public Queue waringQueue() {
        return QueueBuilder.durable(WARING_QUEUE_NAME).build();
    }

    /**
     * 队列绑定交换机
     */
    @Bean
    public Binding queueBBinding(@Qualifier("confirmQueue") Queue confirmQueue, @Qualifier("confirmExchange") DirectExchange confirmExchange) {
        return BindingBuilder.bind(confirmQueue).to(confirmExchange).with("key1");
    }

    /**
     * 声明报警队列绑定关系
     */
    @Bean
    public Binding waringBinding(@Qualifier("waringQueue") Queue waringQueue, @Qualifier("backupExchange") FanoutExchange backupExchange) {
        return BindingBuilder.bind(waringQueue).to(backupExchange);
    }

    /**
     * 声明备份队列绑定关系
     */
    @Bean
    public Binding backupBinding(@Qualifier("backupQueue") Queue backupQueue, @Qualifier("backupExchange") FanoutExchange backupExchange) {
        return BindingBuilder.bind(backupQueue).to(backupExchange);
    }
}
```

### 报警消费者

```java
/**
 * Author:   lxs
 * Date:     2021/9/29 15:22
 * Description: 报警消费者
 */
@Component
@Slf4j
public class WarningConsumer {

    public static final String WARING_QUEUE_NAME = "waring.queue";

    @RabbitListener(queues = WARING_QUEUE_NAME)
    public void receiveWaringMsg(Message message) {
        String msg = new String(message.getBody());
        log.error("报警发现不可路由消息:{}", msg);
    }
}
```

# 其他知识点

## 幂等性

### 概念

> 用户对于同一操作发起的一次请求或者多次请求的结果是一致的，不会因为多次点击而产生了副作用。举个最简单的例子，那就是支付，用户购买商品后支付，支付扣款成功，但是返回结果的时候网络异常，此时钱已经扣了，用户再次点击按钮，此时会进行第二次扣款，返回结果成功，用户查询余额发现多扣钱了，流水记录也变成了两条。在以前的单应用系统中，我们只需要把数据操作放入事务中即可，发生错误立即回滚，但是再响应客户端的时候也有可能出现网络中断或者异常等等

### 消息重复消费

> 消费者在消费MQ中的消息时，MQ已把消息发送给消费者，消费者在给MQ返回ack时网络中断，故MQ未收到确认信息，该条消息会重新发给其他的消费者，或者在网络重连后再次发送给该消费者，但实际上该消费者已成功消费了该条消息，造成消费者消费了重复的消息

### 解决思路

> MQ消费者的幂等性的解决一般使用全局ID或者写个唯一标识比如时间戳或者UUID或者订单消费者消费MQ中的消息也可利用MQ的该id来判断，或者可按自己的规则生成一个全局唯一id，每次消费消息时用该id先判断该消息是否已消费过。

### 消费端的幂等性保障

> 在海量订单生成的业务高峰期，生产端有可能就会重复发生了消息，这时候消费端就要实现幂等性，这就意味着我们的消息永远不会被消费多次，即使我们收到了一样的消息。业界主流的幂等性有两种操作:
>
> a.唯一ID+指纹码机制,利用数据库主键去重, 
>
> b.利用redis的原子性去实现

## 优先级队列

### 使用场景

> 在我们系统中有一个订单催付的场景，我们的客户在天猫下的订单,淘宝会及时将订单推送给我们，如果在用户设定的时间内未付款那么就会给用户推送一条短信提醒，很简单的一个功能对吧，但是，tmall商家对我们来说，肯定是要分大客户和小客户的对吧，比如像苹果，小米这样大商家一年起码能给我们创造很大的利润，所以理应当然，他们的订单必须得到优先处理，而曾经我们的后端系统是使用redis来存放的定时轮询，大家都知道redis只能用List做一个简简单单的消息队列，并不能实现一个优先级的场景，所以订单量大了后采用RabbitMQ进行改造和优化,如果发现是大客户的订单给一个相对比较高的优先级，否则就是默认优先级

* 注意：要让队列实现优先级需要做的事情有如下事情:队列需要设置为优先级队列，消息需要设置消息的优先级，消费者需要等待消息已经发送到队列中才去消费因为，这样才有机会对消息进行排序

* 生产者代码

  ```java
  public class Producer {
  
      private final static String QUEUE_NAME = "hello";
  
      public static void main(String[] args) throws Exception {
          //通过工具类获取信道
          Channel channel = RabbitMqUtils.getChannel();
          HashMap<String, Object> arguments = new HashMap<>();
          arguments.put("x-max-priority", 10);
          /**
           * 生成一个队列
           * 1.队列名称
           * 2.队列里面的消息是否持久化(磁盘)，默认消息存储在内存中
           * 3.该队列是否只供一个消费者进行消费，是否进行共享；true可以多个消费者消费
           * 4.是否自动删除，最后一个消费者断开连接以后，该队列是否自动删除；true自动删除，false就不自动删除
           * 5.其他参数
           */
          channel.queueDeclare(QUEUE_NAME, true, false, false, arguments);
  
          for (int i = 1; i < 11; i++) {
              String message = "info" + i;
              if (i == 5) {
                  AMQP.BasicProperties properties = new AMQP.BasicProperties().builder().priority(5).build();
                  channel.basicPublish("", QUEUE_NAME, properties, message.getBytes());
              } else {
                  channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
              }
          }
          System.out.println("消息发送完毕");
      }
  
  }
  
  ```

* 消费者代码

  ```java
  public class Consumer {
  
      private final static String QUEUE_NAME = "hello";
  
      public static void main(String[] args) throws Exception {
          //通过工具类获取信道
          Channel channel = RabbitMqUtils.getChannel();
          System.out.println("等待接受消息......");
          //声明接受消息
          DeliverCallback deliverCallback = (consumerTag, delivery) -> {
              String message = new String(delivery.getBody());
              System.out.println(message);
          };
          //取消消息时的回调
          CancelCallback cancelCallback = (consumerTag) -> {
              System.out.println("消息消费被中断");
          };
          /**
           * 消费者消费消息
           * 1.消费那个队列
           * 2.消费成功之后是否要自动应答；true代表自动应答 false手动应答
           * 3.消费者未成功消费的回调
           */
          channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);
      }
  }
  ```

## 惰性队列

### 使用场景

> RabbitMQ从3.6.0版本开始引入了惰性队列的概念。惰性队列会尽可能的将消息存入磁盘中，而在消费者消费到相应的消息时才会被加载到内存中，它的一个重要的设计目标是能够支持更长的队列，即支持更多的消息存储。当消费者由于各种各样的原因(比如消费者下线、宕机亦或者是由于维护而关闭等)而致使长时间内不能消费消息造成堆积时，惰性队列就很有必要了。
>
> 默认情况下，当生产者将消息发送到RabbitMQ的时候，队列中的消息会尽可能的存储在内存之中，这样可以更加快速的将消息发送给消费者。即使是持久化的消息，在被写入磁盘的同时也会在内存中驻留一份备份。当RabbitMQ需要释放内存的时候，会将内存中的消息换页至磁盘中，这个操作会耗费较长的时间，也会阻塞队列的操作，进而无法接收新的消息。虽然RabbitMQ的开发者们一直在升级相关的算法，但是效果始终不太理想，尤其是在消息量特别大的时候。

### 两种模式

> 队列具备两种模式：default和lazy。默认的为default模式，在3.6.0之前的版本无需做任何变更。lazy模式即为惰性队列的模式，可以通过调用channel.queueDeclare方法的时候在参数中设置，也可以通过Policy的方式设置，如果一个队列同时使用这两种方式设置的话，那么Policy的方式具备更高的优先级。如果要通过声明的方式改变已有队列的模式的话，那么只能先删除队列，然后再重新声明一个新的。
>
> 在队列声明的时候可以通过“x-queue-mode”参数来设置队列的模式，取值为“default”和“lazy”。下面示例中演示了一个惰性队列的声明细节

```java
Map<String, Object> args = new HashMap<String, Object>();
args.put("x-queue-mode", "lazy");
channel.queueDeclare("myqueue",false,false,false,args)
```

# 集群

## 搭建步骤

1. 修改3台机器的主机名称

   ```shell
   vim /etc/hostname
   ```

2. 配置各个节点的hosts文件，让各个节点都能互相识别对方

   ```shell
   vim /etc/hosts
   ```

3. 以确保各个节点的cookie文件使用的是同一个值，在node1上执行远程同步操作命令

   ```shell
   scp /var/lib/rabbitmq/.erlag.cookie root@node2:/var/lib/rabbitmq/.erlang.cookie
   scp /var/lib/rabbitmq/.erlag.cookie root@node3:/var/lib/rabbitmq/.erlang.cookie
   ```

4. 在三台节点上分别执行以下命令(启动RabbitMQ服务，顺带启动Erlang虚拟机和RabbitMQ应用服务)

   ```shell
   rabbitmq-server -derached
   ```

5. 在节点2执行

   ```shell
   rabbitmqctl stop_app
   rabbitmqctl reset
   rabbitmqctl join_cluster rabbit@node1
   rabbitmqctl start_app
   ```

6. 在节点3执行

   ```shell
   rabbitmqctl stop_app
   rabbitmqctl reset
   rabbitmqctl join_cluster rabbit@node2
   rabbitmqctl start_app
   ```

7. 查看集群状态

   ```shell
   rabbitmqctl cluster_status
   ```

8. 解除集群节点(node2和node3机器分别执行)

   ```shell
   rabbitmqctl stop_app
   rabbitmqctl reset
   rabbitmqctl start_app
   rabbitmqctl cluster_status
   rabbitmqctl forget_cluster_node rabbit@node2(在node1机器上执行)
   ```

## 镜像队列

> 引入镜像队列(MirrorQueue)的机制，可以将队列镜像到集群中的其他Broker节点之上，如果集群中的一个节点失效了，队列能自动地切换到镜像中的另一个节点上以保证服务的可用性。

### 搭建步骤

1. 启动三台集群节点

2. 随便找一个节点添加policy

   ![](.\img\141.jpg)

* 就算整个集群只剩下一台机器了依然能消费队列里面的消息

