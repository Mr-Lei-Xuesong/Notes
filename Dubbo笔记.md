# 概述

## 分布式系统

* 分布式系统(distributed system)是若干独立计算机的集合，这些计算机对于用户来说就像单个相关系统

> 发展演变

![](.\img\93.jpg)

1. **单一应用架构**
   * 当网站流量很小时，只需一个应用，将所有功能都部署在一起，以减少部署节点和成本；适用于小型网站，小型管理系统，将所有功能都部署到一个功能里，简单易用。用于简化增删改查工作量的数据访问框架(ORM)是关键
   * 缺点：
     * 性能扩展比较难
     * 协同开发问题
     * 不利于升级维护
2. **垂直应用架构**
   * 当访问量逐渐增大，单一应用增加机器带来的加速度越来越小，将应用拆成互不相干的几个应用，以提升效率；通过切分业务来实现各个模块独立部署，降低了维护和部署的难度，团队各司其职更易管理，性能扩展也更方便，更有针对性。用于加速前端页面开发的Web框架(MVC)是关键
   * 缺点：
     * 公用模块无法重复利用，开发性的浪费
3. **分布式服务架构**
   * 当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快速的响应多变的市场需求。用于提高业务复用及整合的**分布式服务框架****(RPC)**是关键
4. 流动计算架构
   * 当服务越来越多，容量的评估，小服务资源的浪费等问题逐渐显现，此时需增加一个调度中心基于访问压力实时管理集群容量，提高集群利用率。用于**提高机器利用率的资源调度和治理中心****(SOA)[ Service Oriented Architecture]是关键**

## RPC

> RPC【Remote Procedure Call】是指远程过程调用，是一种进程间通信方式，他是一种技术的思想，而不是规范。它允许程序调用另一个地址空间（通常是共享网络的另一台机器上）的过程或函数，而不用程序员显式编码这个远程调用的细节。即程序员无论是调用本地的还是远程的函数，本质上编写的调用代码基本相同。

![](.\img\94.jpg)

![](.\img\95.jpg)

* RPC两个核心模块：**通讯，序列化**

# 简介

![](.\img\96.jpg)

* **服务提供者（Provider）**：暴露服务的服务提供方，服务提供者在启动时，向注册中心注册自己提供的服务
* **服务消费者（Consumer）**: 调用远程服务的服务消费方，服务消费者在启动时，向注册中心订阅自己所需的服务，服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
* **注册中心（Registry）**：注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者
* **监控中心（Monitor****）**：服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心

# 整合SpringBoot

* 引入依赖

  ```xml
  <dependency>
      <groupId>com.alibaba.boot</groupId>
      <artifactId>dubbo-spring-boot-starter</artifactId>
      <version>0.2.0</version>
  </dependency>
  ```

* 配置信息

  * 服务端

    ```yaml
    dubbo:
      application:
        name: boot-user-service-provider //应用名称
      registry:
        address: zookeeper://192.168.124.130:2181 //注册中心地址
      protocol:
        name: dubbo 
        port: 20880
    ```

  * 消费端

    ```yaml
    dubbo:
      application:
        name: boot-order-service-consumer
      registry:
        address: zookeeper://192.168.124.130:2181
    ```

* 注解
  * @EnableDubbo：开启基于注解
  * @Service：暴露服务
  * @Reference：注入分布式的远程服务的对象

# 高可用

## zookeeper宕机与dubbo直连

> zookeeper注册中心宕机，还可以消费dubbo暴露的服务

原因：

* 健壮性
  1. 监控中心宕掉不影响使用，只是丢失部分采样数据
  2. 数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务
  3. 注册中心对等集群，任意一台宕掉后，将自动切换到另一台
  4. **注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯**
  5. 服务提供者无状态，任意一台宕掉后，不影响使用
  6. 服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复
* 高可用：通过设计，减少系统不能提供服务的时间

## 集群下dubbo负载均衡配置

> 在集群负载均衡时，Dubbo 提供了多种均衡策略，缺省为 random 随机调用

负载均衡策略

1. **Random LoadBalance**
   * 随机，按权重设置随机概率。在一个截面上碰撞的概率高，但调用量越大分布越均匀，而且按概率使用权重后也比较均匀，有利于动态调整提供者权重。
2. **RoundRobin LoadBalance**
   * 轮循，按公约后的权重设置轮循比率。存在慢的提供者累积请求的问题，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在调到第二台上。
3. **LeastActive LoadBalance**
   * 最少活跃调用数，相同活跃数的随机，活跃数指调用前后计数差。使慢的提供者收到更少请求，因为越慢的提供者的调用前后计数差会越大。
4. **ConsistentHash LoadBalance**
   * 一致性 Hash，相同参数的请求总是发到同一提供者。当某一台提供者挂时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动

## 服务熔断与降级处理

### 1.服务降级

> **当服务器压力剧增的情况下，根据实际业务情况及流量，对一些服务和页面有策略的不处理或换种简单的方式处理，从而释放服务器资源以保证核心交易正常运作或高效运作。**
>
> 可以通过服务降级功能临时屏蔽某个出错的非关键服务，并定义降级后的返回策略

### 2.集群容错

> 在集群调用失败是，Dubbo提供了多种容错方案，缺省为failover重试

* **Failover Cluster**
  * 失败自动切换，当出现失败，重试其它服务器。通常用于读操作，但重试会带来更长延迟。可通过 retries="2" 来设置重试次数(不含第一次)。
* **Failfast Cluster**
  * 快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。
* **Failsafe Cluster**
  * 失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。
* **Failback Cluster**
  * 失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作‘
* **Forking Cluster**
  * 并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过 forks="2" 来设置最大并行数。
* **Broadcast Cluster**
  * 广播调用所有提供者，逐个调用，任意一台报错则报错 [2]。通常用于通知所有提供者更新缓存或日志等本地资源信息

# Dubbo原理

## 1.RPC原理

![](.\img\97.jpg)

* 一次完整的RPC调用流程（同步调用，异步另说）如下：
  1. **服务消费方（client）调用以本地调用方式调用服务**
  2. client stub接收到调用后负责将方法、参数等组装成能够进行网络传输的消息体；
  3. client stub找到服务地址，并将消息发送到服务端
  4. server stub收到消息后进行解码
  5. server stub根据解码结果调用本地的服务
  6. 本地服务执行并将结果返回给server stub
  7. server stub将返回结果打包成消息并发送至消费方
  8. client stub接收到消息，并进行解码
  9. **服务消费方得到最终结果**

## 2.Netty原理

> Netty是一个异步事件驱动的网络应用程序框架， 用于快速开发可维护的高性能协议服务器和客户端。它极大地简化并简化了TCP和UDP套接字服务器等网络编程

* BIO(Blocking IO)

  ![](.\img\98.jpg)

* NIO(Non-Blocking IO)

  <img src=".\img\99.jpg" style="zoom:50%;" />

  * Selector 一般称 为**选择器** ，也可以翻译为 **多路复用器**
  * Connect（连接就绪）、Accept（接受就绪）、Read（读就绪）、Write（写就绪）

* Netty原理：

  <img src="D:\Notes\img\100.jpg" style="zoom:70%;" />

## 3.Dubbo原理

1. 框架设计

   ![](.\img\101.jpg)

   * config 配置层：对外配置接口，以 ServiceConfig, ReferenceConfig 为中心，可以直接初始化配置类，也可以通过 spring 解析配置生成配置类
   * proxy 服务代理层：服务接口透明代理，生成服务的客户端 Stub 和服务器端 Skeleton, 以 ServiceProxy 为中心，扩展接口为 ProxyFactory
   * registry 注册中心层：封装服务地址的注册与发现，以服务 URL 为中心，扩展接口为 RegistryFactory, Registry, RegistryService
   * cluster 路由层：封装多个提供者的路由及负载均衡，并桥接注册中心，以 Invoker 为中心，扩展接口为 Cluster, Directory, Router, LoadBalance
   * monitor 监控层：RPC 调用次数和调用时间监控，以 Statistics 为中心，扩展接口为 MonitorFactory, Monitor, MonitorService
   * protocol 远程调用层：封装 RPC 调用，以 Invocation, Result 为中心，扩展接口为 Protocol, Invoker, Exporter
   * exchange 信息交换层：封装请求响应模式，同步转异步，以 Request, Response 为中心，扩展接口为 Exchanger, ExchangeChannel, ExchangeClient, ExchangeServer
   * transport 网络传输层：抽象 mina 和 netty 为统一接口，以 Message 为中心，扩展接口为 Channel, Transporter, Client, Server, Codec
   * serialize 数据序列化层：可复用的一些工具，扩展接口为 Serialization, ObjectInput, ObjectOutput, ThreadPool

2. 服务暴露

   ![](.\img\102.jpg)

3. 服务引用

   <img src=".\img\103.jpg" style="zoom:150%;" />

