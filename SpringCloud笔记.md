# 简介

> SpringCloud=分布式微服务架构的一站式解决方案，是多种微服务架构技术的集合体，俗称微服务全家桶

> ![](.\img\150.jpg)
>
> ![](.\img\160.jpg)

## 版本选择

> https://start.spring.io/actuator/info

# Eureka

## 服务治理

> Spring Cloud 封装了 Netflix 公司开发的 Eureka 模块来实现服务治理
>
> 在传统的rpc远程调用框架中，管理每个服务与服务之间依赖关系比较复杂，管理比较复杂，所以需要使用服务治理，管理服务于服务之间依赖关系，可以实现服务调用、负载均衡、容错等，实现服务发现与注册。 

## 服务注册与发现

> Eureka采用了CS的设计架构，**Eureka Server作为服务注册功能的服务器，它是服务注册中心**。而系统中的**其他微服务，使用 Eureka的客户端连接到 Eureka Server并维持心跳连接**。这样系统的维护人员就可以通过 Eureka Server 来监控系统中各个微服务是否正常运行。当服务器启动的时候，会把当前自己服务器的信息 比如:服务地址通讯地址等以**别名**方式注册到注册中心上。另一方（消费者|服务提供者），以该**别名**的方式去注册中心上获取到实际的服务通讯地址，然后再实现本地RPC调用RPC远程调用框架核心设计思想：在于注册中心，因为使用注册中心管理每个服务与服务之间的一个依赖关系(服务治理概念)。在任何rpc远程框架中，都会有一个注册中心(存放服务地址相关信息(接口地址))

## 两个组件

### Eureka Server

>  Eureka Server提供服务注册服务，各个微服务节点通过配置启动后，会在EurekaServer中进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观看到。

1. 依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
   </dependency>
   ```

2. 配置文件

   ```yaml
   eureka:
     instance:
       #eureka服务端的实例名称
       hostname: localhost 
     client:
       #false表示不向注册中心注册自己。
       register-with-eureka: false
       #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
       fetch-registry: false
       service-url:
         #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址。
         # 地址指向自己就是单机版，指向其他就是集群版
         #      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/  #单机版
         defaultZone: http://127.0.0.1:7002/eureka/ # 集群版
     server:
       # 关闭自我保护机制，保证不可用服务被及时剔除
       enable-self-preservation: false
       # Eureka服务端在接收到最后一次心跳后等待时间上限，单位为秒(默认为90秒)，超时将剔除服务
       eviction-interval-timer-in-ms: 2000
   ```

3. 启动类

   ```java
   @EnableEurekaServer
   ```

### Eureka Client

> EurekaClient通过注册中心进行访问是一个Java客户端，用于简化Eureka Server的交互，客户端同时也具备一个内置的、**使用轮询(round-robin)负载算法的负载均衡器**。在应用启动后，将会向Eureka Server**发送心跳(默认周期为30秒)**。如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，**EurekaServer将会从服务注册表中把这个服务节点移除（默认90秒）**

1. 依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
   </dependency>
   ```

2. 配置文件

   ```yaml
   eureka:
     client:
       #表示是否将自己注册进EurekaServer 默认为true
       register-with-eureka: true
       #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
       fetch-registry: true
       service-url:
         #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址。
         #      defaultZone: http://localhost:7001/eureka 单机版
         defaultZone: http://localhost:7001/eureka,http://127.0.0.1:7002/eureka # 集群版
     instance:
       # 别名
       instance-id: payment8001
       # 访问路径可以显示IP地址
       prefer-ip-address: true
       # Eureka客户端向服务端发送心跳的时间间隔,单位为秒(默认是30秒)
       lease-renewal-interval-in-seconds: 1
       # Eureka服务端在接收到最后一次心跳后等待时间上限，单位为秒(默认为90秒)，超时将剔除服务
       lease-expiration-duration-in-seconds: 2
   ```

3. 主启动类

   ```java
   @EnableEurekaClient
   @EnableDiscoveryClient
   
   //@EnableEurekaClient和@EnableDiscoveryClient
   //共同点：都是能够让注册中心能够发现，扫描到该服务
   //不同点：@EnableEurekaClient只适用于Eureka作为注册中心，@EnableDiscoveryClient可以是其他注册中心
   ```

# Zookeeper

## 服务提供者

1. 依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
   </dependency>
   ```

2. 配置文件

   ```yaml
   spring:
     application:
       name: payment
     cloud:
       zookeeper:
         # 地址和端口号
         connect-string: 192.168.124.130:2181
   ```

3. 启动类

   ```java
   @EnableDiscoveryClient
   ```

## 服务消费者

> 同上

# RestTemplate

1. 配置类

   ```java
   @Configuration
   public class ApplicationContextConfig {
   
       @Bean
       @LoadBalanced //使用@LoadBalanced注解赋予RestTemplate负载均衡的能力
       public RestTemplate getRestTemplate() {
           return new RestTemplate();
       }
   }
   ```

2. 使用

   1. 引入类

      ```java
      private final RestTemplate restTemplate;
      ```

   2. 常用方法
      1. getForObject(String url,Class<? extends Object> responseType)   返回对象为响应体中数据转化成的对象，可以理解为Json
      2. getForEntity(String url,Class<? extends Object> responseType)  返回对象为ResponseEntity对象，包含了响应中的一些重要信息，比如响应头，响应状态码，响应体等
      3. postForObject(String url,Object request,Class<T> reponseType)
      4. postForEntity(String url,Object request,Class<T> reponseType)

# Ribbon

## 简介

> Spring Cloud Ribbon是基于Netflix Ribbon实现的一套**客户端负载均衡的工具**。 Ribbon是Netflix发布的开源项目，**主要功能是提供客户端的软件负载均衡算法和服务调用**。Ribbon客户端组件提供一系列完善的配置项如连接超时，重试等。简单的说，就是在配置文件中列出Load Balancer（简称LB）后面所有的机器，Ribbon会自动的帮助你基于某种规则（如简单轮询，随机连接等）去连接这些机器。我们很容易使用Ribbon实现自定义的负载均衡算法。                

## LB(负载均衡)

* 集中式LB

  > 即在服务的消费方和提供方之间使用独立的LB设施(可以是硬件，如F5, 也可以是软件，如nginx), 由该设施负责把访问请求通过某种策略转发至服务的提供方；

* 进程内LB

  > 将LB逻辑集成到消费方，消费方从服务注册中心获知有哪些地址可用，然后自己再从这些地址中选择出一个合适的服务器。 
  >
  > Ribbon就属于进程内LB，它只是一个类库，集成于消费方进程，消费方通过它来获取到服务提供方的地址。 

## IRule

> IRule：根据特定算法从服务列表中选取一个要访问的服务

* RoundRobinRule：轮询
* RandomRule：随机
* RetryRule：先按照RoundRobinRule的策略获取服务，如果获取服务失败则在指定时间内会进行重试，获取可用的服务
* WeightedResponseTimeRule：对RoundRobinRule的扩展，响应速度越快的实例选择权重越大，越容易被选择
* BestAvailableRule：会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然后选择一个并发量最小的服务
* AvailabilityFilteringRule：先过滤掉故障实例，再选择并发较小的实例
* ZoneAvoidanceRule：默认规则,复合判断server所在区域的性能和server的可用性选择服务器

### 负载均衡算法

> **rest接口第几次请求数%服务器集群总数量=实际调用服务器位置下标**。每次服务重启动后rest接口计数从1开始
>
> **注意**：官方文档明确给出了警告：**这个自定义配置类不能放在@ComponentScan所扫描的当前包下以及子包下**，否则我们自定义的这个配置类就会被所有的Ribbon客户端所共享，达不到特殊化定制的目的了。 

1. 新建包

2. 创建类

   ```java
   @Configuration
   public class MySelfRule {
   
       @Bean
       public IRule myRule() {
           return new RandomRule();
       }
   }
   ```

3. 启动类

   ```java
   @RibbonClient(name = "服务名称",configuration= MySelfRule.class)
   ```

# OpenFeign

## 简介

>  Feign是一个声明式HTTP客户端，它的目的就是让远程调用更加简单。Feign提供了HTTP请求的模板，**通过编写简单的接口和插入注解**，就可以定义好HTTP请求的参数、格式、地址等信息。
>
>  Fegin整合了Ribbon(负载均衡)和Hystrix(服务熔断)，可以让我们不再需要显示地使用这两个组件
>
>  它的使用方法是**定义一个服务接口然后在上面添加注解**。Feign也支持可拔插式的编码器和解码器。Spring Cloud对Feign进行了封装，使其支持了Spring MVC标准注解和HttpMessageConverters。Feign可以与Eureka和Ribbon组合使用以支持负载均衡

## 使用

1. 依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-openfeign</artifactId>
   </dependency>
   ```

2. 主启动类

   ```java
   // 开启Feign
   @EnableFeignClients
   ```

3. 业务接口类

   ```java
   @Component
   @FeignClient(value = "服务名称")
   public interface PaymentFeignService {
   
       @PostMapping("/payment/create")
       void create(@RequestBody Payment payment);
   
       @GetMapping("/payment/get/{id}")
       Payment getPaymentById(@PathVariable("id") Long id);
   
       @GetMapping("/payment/feign/timeout")
       String paymentFeignTimeout();
   
   }
   ```

> 1. @RequestBody将对象转为json
> 2. 找到@FeignClient(value = "服务名称")对应的服务，给标注的路径发送请求
> 3. 服务方收到请求，将调用方转的json放在请求体中
>
> **注意**：`json中的属性无论在调用发和接收方都要一一对应`

## 超时控制

> OpenFeign默认等待1秒钟，超过后报错 

* 设置超时时间

  ```yaml
  # 设置Feign客户端超时时间
  ribbon:
    # 指的是建立连接所用的时间，适用于网络状况正常的情况下，两段连接所用的时间
    ReadTimeout: 5000
    # 指的是建立连接后从服务器读取到可用资源所i用的时间
    ConnectTimeout: 5000
  ```

## 日志打印功能

>  Feign 提供了日志打印功能，我们可以通过配置来调整日志级别，从而了解 Feign 中 Http 请求的细节。就是对Feign接口的调用情况进行监控和输出 

> NONE：默认的，不显示任何日志；
>
> BASIC：仅记录请求方法、URL、响应状态码及执行时间；
>
> HEADERS：除了 BASIC 中定义的信息之外，还有请求和响应的头信息；
>
> FULL：除了 HEADERS 中定义的信息之外，还有请求和响应的正文及元数据。

* 配置日志

  ```java
  @Configuration
  public class FeignConfig {
  
      @Bean
      Logger.Level feignLoggerLevel() {
          return Logger.Level.FULL;
      }
  }
  ```

* 配置文件

  ```yaml
  logging:
    level:
      全类名: debug
  ```

# Hystrix

## 分布式系统面临的问题

> 分布式系统面临的问题复杂分布式体系结构中的应用程序有数十个依赖关系，每个依赖关系在某些时候将不可避免地失败。 服务雪崩：多个微服务之间调用的时候，假设微服务A调用微服务B和微服务C，微服务B和微服务C又调用其它的微服务，这就是所谓的“扇出”。如果扇出的链路上某个微服务的调用响应时间过长或者不可用，对微服务A的调用就会占用越来越多的系统资源，进而引起系统崩溃，所谓的“雪崩效应”.
>
> 对于高流量的应用来说，单一的后端依赖可能会导致所有服务器上的所有资源都在几秒钟内饱和。比失败更糟糕的是，这些应用程序还可能导致服务之间的延迟增加，备份队列，线程和其他系统资源紧张，导致整个系统发生更多的级联故障。这些都表示需要对故障和延迟进行隔离和管理，以便单个依赖关系的失败，不能取消整个应用程序或系统。所以，通常当你发现一个模块下的某个实例失败后，这时候这个模块依然还会接收流量，然后这个有问题的模块还调用了其他的模块，这样就会发生级联故障，或者叫雪崩。

## 简介

>  Hystrix是一个用于处理分布式系统的**延迟**和**容错**的开源库，在分布式系统里，许多依赖不可避免的会调用失败，比如超时、异常等，Hystrix能够保证在一个依赖出问题的情况下，**不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性**。 
>
> “断路器”本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控（类似熔断保险丝），**向调用方返回一个符合预期的、可处理的备选响应（FallBack），而不是长时间的等待或者抛出调用方无法处理的异常**，这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。   

## 服务降级(FallBack)

>服务器忙，请稍后再试，不让客户端等待并立刻返回一个友好提示，fallback

* 哪些情况会触发降级
  * 程序运行异常
  * 超时
  * 服务熔断触发服务降级
  * 线程池/信号量打满也会导致服务降级

### 使用

> 服务端

1. 依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
   </dependency>
   ```

2. 降级配置(业务层)

   ```java
   //一旦调用服务方法失败并抛出了错误信息后，会自动调用@HystrixCommand标注好的fallbackMethod调用类中的指定方法
   @HystrixCommand(fallbackMethod = "paymentInfo_TimeoutHandler", commandProperties = {
       @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "3000")
       //设置自身调用超时时间的峰值，峰值内可以正常运行，超过了需要有兜底的方法处理，作服务降级fallback
   })
   ```

3. 主启动类

   ```java
   @EnableCircuitBreaker
   ```

> **客户端**

1. 依赖相同

2. 配置文件

   ```yaml
   feign:
     hystrix:
       enabled: true
   ```

3. 降级配置(接口层)

   ```java
   //一旦调用服务方法失败并抛出了错误信息后，会自动调用@HystrixCommand标注好的fallbackMethod调用类中的指定方法
   @HystrixCommand(fallbackMethod = "paymentInfo_TimeoutHandler", commandProperties = {
       @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "3000")
       //设置自身调用超时时间的峰值，峰值内可以正常运行，超过了需要有兜底的方法处理，作服务降级fallback
   })
   ```

4. 主启动类

   ```java
   @EnableHystrix
   ```

> **全局FallBack处理**
>
> > 接口层处理
> >
> > 1. 在对应接口类上
> >
> >    ```java
> >    @DefaultProperties(defaultFallback = "全局FallBack方法名")
> >    ```
> >
> > 2. 新建全局FallBack处理方法
> >
> > 3. 对应方法上
> >
> >    ```java
> >    @HystrixCommand
> >    ```
>
> > **服务层处理**
> >
> > 1. 新建FallBack类，实现业务层，并实现方法，方法体做服务降级处理
> >
> > 2. 业务层类上
> >
> >    ```java
> >    @FeignClient(value = "服务别名", fallback = FallBack类.class)
> >    ```

## 服务熔断(Break)

> 熔断机制是应对雪崩效应的一种微服务链路保护机制。当扇出链路的某个微服务出错不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。**当检测到该节点微服务调用响应正常后，恢复调用链路**。 在Spring Cloud框架里，熔断机制通过Hystrix实现。Hystrix会监控微服务间调用的状况，当失败的调用到一定阈值，**缺省是5秒内20次调用失败，就会启动熔断机制。熔断机制的注解是@HystrixCommand。**   
>
> * 服务的降级->进而熔断->恢复调用链路

### 使用

```java
@HystrixCommand(fallbackMethod = "paymentCircuitBreaker_FallBack", commandProperties = {
            @HystrixProperty(name = "circuitBreaker.enabled", value = "true"),//是否开启断路器
            @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"),//请求次数
            @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "10000"),//时间窗口期
            @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "60"),//失败率达到多少后跳闸
    })
```

```java
//========================All
@HystrixCommand(fallbackMethod = "str_fallbackMethod",
                groupKey = "strGroupCommand",        
                commandKey = "strCommand",        
                threadPoolKey = "strThreadPool",        
                commandProperties = {                
                    // 设置隔离策略，THREAD 表示线程池 SEMAPHORE：信号池隔离                
                    @HystrixProperty(name = "execution.isolation.strategy", value = "THREAD"),                
                    // 当隔离策略选择信号池隔离的时候，用来设置信号池的大小（最大并发数）                
                    @HystrixProperty(name = "execution.isolation.semaphore.maxConcurrentRequests", value = "10"),                
                    // 配置命令执行的超时时间               
                    @HystrixProperty(name = "execution.isolation.thread.timeoutinMilliseconds", value = "10"),                
                    // 是否启用超时时间                
                    @HystrixProperty(name = "execution.timeout.enabled", value = "true"),                
                    // 执行超时的时候是否中断                
                    @HystrixProperty(name = "execution.isolation.thread.interruptOnTimeout", value = "true"),                
                    // 执行被取消的时候是否中断                
                    @HystrixProperty(name = "execution.isolation.thread.interruptOnCancel", value = "true"),                
                    // 允许回调方法执行的最大并发数                
                    @HystrixProperty(name = "fallback.isolation.semaphore.maxConcurrentRequests", value = "10"),                
                    // 服务降级是否启用，是否执行回调函数                
                    @HystrixProperty(name = "fallback.enabled", value = "true"),                
                    // 是否启用断路器                
                    @HystrixProperty(name = "circuitBreaker.enabled", value = "true"),                
                    // 该属性用来设置在滚动时间窗中，断路器熔断的最小请求数。例如，默认该值为 20 的时候，                
                    // 如果滚动时间窗（默认10秒）内仅收到了19个请求， 即使这19个请求都失败了，断路器也不会打开。               
                    @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "20"),                
                    // 该属性用来设置在滚动时间窗中，表示在滚动时间窗中，在请求数量超过                
                    // circuitBreaker.requestVolumeThreshold 的情况下，如果错误请求数的百分比超过50,                
                    // 就把断路器设置为 "打开" 状态，否则就设置为 "关闭" 状态。                
                    @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "50"),                
                    // 该属性用来设置当断路器打开之后的休眠时间窗。 休眠时间窗结束之后，                
                    // 会将断路器置为 "半开" 状态，尝试熔断的请求命令，如果依然失败就将断路器继续设置为 "打开" 状态，                
                    // 如果成功就设置为 "关闭" 状态。                
                    @HystrixProperty(name = "circuitBreaker.sleepWindowinMilliseconds", value = "5000"),                
                    // 断路器强制打开                
                    @HystrixProperty(name = "circuitBreaker.forceOpen", value = "false"),                
                    // 断路器强制关闭                
                    @HystrixProperty(name = "circuitBreaker.forceClosed", value = "false"),                
                    // 滚动时间窗设置，该时间用于断路器判断健康度时需要收集信息的持续时间                
                    @HystrixProperty(name = "metrics.rollingStats.timeinMilliseconds", value = "10000"),                
                    // 该属性用来设置滚动时间窗统计指标信息时划分"桶"的数量，断路器在收集指标信息的时候会根据                
                    // 设置的时间窗长度拆分成多个 "桶" 来累计各度量值，每个"桶"记录了一段时间内的采集指标。                
                    // 比如 10 秒内拆分成 10 个"桶"收集这样，所以 timeinMilliseconds 必须能被 numBuckets 整除。否则会抛异常                
                    @HystrixProperty(name = "metrics.rollingStats.numBuckets", value = "10"),                
                    // 该属性用来设置对命令执行的延迟是否使用百分位数来跟踪和计算。如果设置为 false, 那么所有的概要统计都将返回 -1。                
                    @HystrixProperty(name = "metrics.rollingPercentile.enabled", value = "false"),                
                    // 该属性用来设置百分位统计的滚动窗口的持续时间，单位为毫秒。                
                    @HystrixProperty(name = "metrics.rollingPercentile.timeInMilliseconds", value = "60000"),                
                    // 该属性用来设置百分位统计滚动窗口中使用 “ 桶 ”的数量。                
                    @HystrixProperty(name = "metrics.rollingPercentile.numBuckets", value = "60000"),                
                    // 该属性用来设置在执行过程中每个 “桶” 中保留的最大执行次数。如果在滚动时间窗内发生超过该设定值的执行次数，                
                    // 就从最初的位置开始重写。例如，将该值设置为100, 滚动窗口为10秒，若在10秒内一个 “桶 ”中发生了500次执行，                
                    // 那么该 “桶” 中只保留 最后的100次执行的统计。另外，增加该值的大小将会增加内存量的消耗，并增加排序百分位数所需的计算时间。            
                    @HystrixProperty(name = "metrics.rollingPercentile.bucketSize", value = "100"),                
                    // 该属性用来设置采集影响断路器状态的健康快照（请求的成功、 错误百分比）的间隔等待时间。                
                    @HystrixProperty(name = "metrics.healthSnapshot.intervalinMilliseconds", value = "500"),                
                    // 是否开启请求缓存                
                    @HystrixProperty(name = "requestCache.enabled", value = "true"),                
                    // HystrixCommand的执行和事件是否打印日志到 HystrixRequestLog 中                
                    @HystrixProperty(name = "requestLog.enabled", value = "true")},        
                threadPoolProperties = {                
                    // 该参数用来设置执行命令线程池的核心线程数，该值也就是命令执行的最大并发量                
                    @HystrixProperty(name = "coreSize", value = "10"),                
                    // 该参数用来设置线程池的最大队列大小。当设置为 -1 时，线程池将使用 SynchronousQueue 实现的队列，                
                    // 否则将使用 LinkedBlockingQueue 实现的队列。                
                    @HystrixProperty(name = "maxQueueSize", value = "-1"),                
                    // 该参数用来为队列设置拒绝阈值。 通过该参数， 即使队列没有达到最大值也能拒绝请求。                
                    // 该参数主要是对 LinkedBlockingQueue 队列的补充,因为 LinkedBlockingQueue                
                    // 队列不能动态修改它的对象大小，而通过该属性就可以调整拒绝请求的队列大小了。                
                    @HystrixProperty(name = "queueSizeRejectionThreshold", value = "5")}
)
public String strConsumer() {
    return "hello 2020";
}
public String str_fallbackMethod(){    
    return "*****fall back str_fallbackMethod";
} 
```

### 熔断类型

* 熔断打开：请求不再进行调用当前服务，内部设置时钟一般为MTTR（平均故障处理时间)，当打开时长达到所设时钟则进入半熔断状态
* 熔断关闭：熔断关闭不会对服务进行熔断
* 熔断半开：部分请求根据规则调用当前服务，如果请求成功且符合规则则认为当前服务恢复正常，关闭熔断

## 熔断在什么情况下开始起作用

>  涉及到断路器的三个重要参数：**快照时间窗、请求总数阀值、错误百分比阀值**。
>
> 1：快照时间窗：断路器确定是否打开需要统计一些请求和错误数据，而统计的时间范围就是快照时间窗，默认为最近的10秒。 
>
> 2：请求总数阀值：在快照时间窗内，必须满足请求总数阀值才有资格熔断。默认为20，意味着在10秒内，如果该hystrix命令的调用次数不足20次，即使所有的请求都超时或其他原因失败，断路器都不会打开。 
>
> 3：错误百分比阀值：当请求总数在快照时间窗内超过了阀值，比如发生了30次调用，如果在这30次调用中，有15次发生了超时异常，也就是超过50%的错误百分比，在默认设定50%阀值情况下，这时候就会将断路器打开。 

### 熔断器开启或者关闭的条件

> 1. 当满足一定的阀值的时候（默认10秒内超过20个请求次数）
>
> 2. 当失败率达到一定的时候（默认10秒内超过50%的请求失败）
>
> 3. 到达以上阀值，断路器将会开启
>
> 4. 当开启的时候，所有请求都不会进行转发
>
> 5. 一段时间之后（默认是5秒），这个时候断路器是半开状态，会让其中一个请求进行转发。
>    如果成功，断路器会关闭，若失败，继续开启。重复4和5

### 断路器打开之后

> 1：再有请求调用的时候，**将不会调用主逻辑，而是直接调用降级fallback**。通过断路器，实现了自动地发现错误并将降级逻辑切换为主逻辑，减少响应延迟的效果。
>
> 2：原来的主逻辑要如何恢复呢？对于这一问题，hystrix也为我们实现了自动恢复功能。当断路器打开，对主逻辑进行熔断之后，hystrix会启动一个休眠时间窗，在这个时间窗内，降级逻辑是临时的成为主逻辑，当休眠时间窗到期，断路器将进入半开状态，释放一次请求到原来的主逻辑上，如果此次请求正常返回，那么断路器将继续闭合，主逻辑恢复，如果这次请求依然有问题，断路器继续进入打开状态，休眠时间窗重新计时。

## 服务限流(FlowLimit)

> 秒杀高并发等操作，严禁一窝蜂的过来拥挤，大家排队，一秒钟N个，有序进行

## 工作流程

> ![](.\img\149.jpg)
>
> 1. 创建 HystrixCommand（用在依赖的服务返回单个操作结果的时候） 或 HystrixObserableCommand（用在依赖的服务返回多个操作结果的时候） 对象。
>
> 2. 命令执行。其中 HystrixComand 实现了下面前两种执行方式；而 HystrixObservableCommand 实现了后两种执行方式：execute()：同步执行，从依赖的服务返回一个单一的结果对象， 或是在发生错误的时候抛出异常。queue()：异步执行， 直接返回 一个Future对象， 其中包含了服务执行结束时要返回的单一结果对象。observe()：返回 Observable 对象，它代表了操作的多个结果，它是一个 Hot Obserable（不论 "事件源" 是否有 "订阅者"，都会在创建后对事件进行发布，所以对于 Hot Observable 的每一个 "订阅者" 都有可能是从 "事件源" 的中途开始的，并可能只是看到了整个操作的局部过程）。toObservable()： 同样会返回 Observable 对象，也代表了操作的多个结果，但它返回的是一个Cold Observable（没有 "订阅者" 的时候并不会发布事件，而是进行等待，直到有 "订阅者" 之后才发布事件，所以对于 Cold Observable 的订阅者，它可以保证从一开始看到整个操作的全部过程）。
>
> 3. 若当前命令的请求缓存功能是被启用的， 并且该命令缓存命中， 那么缓存的结果会立即以 Observable 对象的形式返回。
>
> 4. 检查断路器是否为打开状态。如果断路器是打开的，那么Hystrix不会执行命令，而是转接到 fallback 处理逻辑（第 8 步）；如果断路器是关闭的，检查是否有可用资源来执行命令（第 5 步）。
>
> 5. 线程池/请求队列/信号量是否占满。如果命令依赖服务的专有线程池和请求队列，或者信号量（不使用线程池的时候）已经被占满， 那么 Hystrix 也不会执行命令， 而是转接到 fallback 处理逻辑（第8步）。
>
> 6. Hystrix 会根据我们编写的方法来决定采取什么样的方式去请求依赖服务。HystrixCommand.run() ：返回一个单一的结果，或者抛出异常。HystrixObservableCommand.construct()： 返回一个Observable 对象来发射多个结果，或通过 onError 发送错误通知。
>
> 7. Hystrix会将 "成功"、"失败"、"拒绝"、"超时" 等信息报告给断路器， 而断路器会维护一组计数器来统计这些数据。断路器会使用这些统计数据来决定是否要将断路器打开，来对某个依赖服务的请求进行 "熔断/短路"。
>
> 8. 当命令执行失败的时候， Hystrix 会进入 fallback 尝试回退处理， 我们通常也称该操作为 "服务降级"。而能够引起服务降级处理的情况有下面几种：第4步： 当前命令处于"熔断/短路"状态，断路器是打开的时候。第5步： 当前命令的线程池、 请求队列或 者信号量被占满的时候。第6步：HystrixObservableCommand.construct() 或 HystrixCommand.run() 抛出异常的时候。
>
> 9. 当Hystrix命令执行成功之后， 它会将处理结果直接返回或是以Observable 的形式返回。
>
>    tips：如果我们没有为命令实现降级逻辑或者在降级处理逻辑中抛出了异常， Hystrix 依然会返回一个 Observable 对象， 但是它不会发射任何结果数据， 而是通过 onError 方法通知命令立即中断请求，并通过onError()方法将引起命令失败的异常发送给调用者。

## 服务监控

* 概述

  >  除了隔离依赖服务的调用以外，Hystrix还提供了准实时的调用监控（Hystrix Dashboard），Hystrix会持续地记录所有通过Hystrix发起的请求的执行信息，并以统计报表和图形的形式展示给用户，包括每秒执行多少请求多少成功，多少失败等。Netflix通过hystrix-metrics-event-stream项目实现了对以上指标的监控。Spring Cloud也提供了Hystrix Dashboard的整合，对监控内容转化成可视化界面。  

* 依赖

  ```xml
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
  </dependency>
  ```

* 主启动类

  ```java
  //开启Hystrix监控
  @EnableHystrixDashboard
  ```

* **注意**

  * 所有微服务提供者都需要监控依赖配置

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    ```

* 访问

  ```
  http://localhost:端口号/hystrix
  ```

> 微服务提供者方需要加一个配置类
>
> ```java
> /**
>   * 此配置是为了服务监控而配置的
>   */
> @Bean
> public ServletRegistrationBean<HystrixMetricsStreamServlet> getServlet() {
>     HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
>     ServletRegistrationBean<HystrixMetricsStreamServlet> registrationBean = new ServletRegistrationBean<>(streamServlet);
>     registrationBean.setLoadOnStartup(1);
>     registrationBean.addUrlMappings("/hystrix.stream");
>     registrationBean.setName("HystrixMetricsStreamServlet");
>     return registrationBean;
> }
> ```

# GateWay

## 简介

> ![](.\img\151.jpg)
>
> SpringCloud Gateway 是 Spring Cloud 的一个全新项目，Gateway是在Spring生态系统之上构建的API网关服务，基于**(异步非阻塞模型)**Spring 5，Spring Boot 2和 Project Reactor等技术。Gateway旨在提供一种简单而有效的方式来对API进行路由，以及提供一些强大的过滤器功能， 例如：熔断、限流、重试等
>
> SpringCloud Gateway 作为 Spring Cloud 生态系统中的网关，目标是替代 Zuul，在Spring Cloud 2.0以上版本中，没有对新版本的Zuul 2.0以上最新高性能版本进行集成，仍然还是使用的Zuul 1.x非Reactor模式的老版本。而为了提升网关的性能，SpringCloud Gateway是基于WebFlux框架实现的，而WebFlux框架底层则使用了高性能的Reactor模式通信框架Netty。 
>
> Spring Cloud Gateway的目标提供统一的路由方式且基于 Filter 链的方式提供了网关基本的功能，例如：安全，监控/指标，和限流。      

## 特性

> Spring Cloud Gateway 具有如下特性： 
>
> * 基于Spring Framework 5, Project Reactor 和 Spring Boot 2.0 进行构建；
> * 动态路由：能够匹配任何请求属性；可以对路由指定 Predicate（断言）和 Filter（过滤器）；
> * 集成Hystrix的断路器功能；
> * 集成 Spring Cloud 服务发现功能；
> * 易于编写的 Predicate（断言）和 Filter（过滤器）；
> * 请求限流功能；
> * 支持路径重写。 

## Zuul1.x模型

> Springcloud中所集成的Zuul版本，采用的是Tomcat容器，使用的是传统的Servlet IO处理模型。 Servlet的生命周期由servlet container进行生命周期管理。container启动时构造servlet对象并调用servlet init()进行初始化；container运行时接受请求，并为每个请求分配一个线程（一般从线程池中获取空闲线程）然后调用service()。container关闭时调用servlet destory()销毁servlet； 上述模式的缺点：servlet是一个简单的网络IO模型，当请求进入servlet container时，servlet container就会为其绑定一个线程，在并发不高的场景下这种模型是适用的。但是一旦高并发(比如抽风用jemeter压)，线程数量就会上涨，而线程资源代价是昂贵的（上线文切换，内存消耗大）严重影响请求的处理时间。在一些简单业务场景下，不希望为每个request分配一个线程，只需要1个或几个线程就能应对极大并发的请求，这种业务场景下servlet模型没有优势。所以Zuul 1.X是基于servlet之上的一个阻塞式处理模型，即spring实现了处理所有request请求的一个servlet（DispatcherServlet）并由该servlet阻塞式处理处理。所以Springcloud Zuul无法摆脱servlet模型的弊端   

## GateWay模型

>  传统的Web框架，比如说：struts2，springmvc等都是基于Servlet API与Servlet容器基础之上运行的。但是在Servlet3.1之后有了异步非阻塞的支持。而WebFlux是一个典型非阻塞异步的框架，它的核心是基于Reactor的相关API实现的。相对于传统的web框架来说，它可以运行在诸如Netty，Undertow及支持Servlet3.1的容器上。非阻塞式+函数式编程（Spring5必须让你使用java8） Spring WebFlux 是 Spring 5.0 引入的新的响应式框架，区别于 Spring MVC，它不需要依赖Servlet API，它是完全异步非阻塞的，并且基于 Reactor 来实现响应式流规范。 

## 为什么选择GateWay

> 1. Zuul1.0已经进入了维护阶段，而且Gateway是SpringCloud团队研发的，是亲儿子产品，值得信赖。而且很多功能Zuul都没有用起来也非常的简单便捷。 Gateway是基于异步非阻塞模型上进行开发的，性能方面不需要担心。
> 2. Zuul 1.x，是一个基于阻塞 I/ O 的 API Gateway
> 3. Zuul 1.x 基于Servlet 2. 5使用阻塞架构它不支持任何长连接(如 WebSocket) Zuul 的设计模式和Nginx较像，每次 I/ O 操作都是从工作线程中选择一个执行，请求线程被阻塞到工作线程完成，但是差别是Nginx 用C++ 实现，Zuul 用 Java 实现，而 JVM 本身会有第一次加载较慢的情况，使得Zuul 的性能相对较差。
> 4. Zuul 2.x理念更先进，想基于Netty非阻塞和支持长连接，但SpringCloud目前还没有整合。 Zuul 2.x的性能较 Zuul 1.x 有较大提升。在性能方面，根据官方提供的基准测试， Spring Cloud Gateway 的 RPS（每秒请求数）是Zuul 的 1. 6 倍。
> 5. Spring Cloud Gateway 建立 在 Spring Framework 5、 Project Reactor 和 Spring Boot 2 之上， 使用非阻塞 API。5、Spring Cloud Gateway 还 支持 WebSocket， 并且与Spring紧密集成拥有更好的开发体验 

## 三大核心概念

### 路由(Router)

> 路由是构建网关的基本模块，它由ID，目标URI，一系列的断言和过滤器组成，如果断言为true则匹配该路由

### 断言(Predicate)

> 参考的是Java8的java.util.function.Predicate
> 开发人员可以匹配HTTP请求中的所有内容(例如请求头或请求参数)，如果请求与断言相匹配则进行路由

### 过滤(Filter)

> 指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求路由前或者之后对请求进行修改。

### 总结

>  web请求，通过一些匹配条件，定位到真正的服务节点。并在这个转发过程的前后，进行一些精细化控制。
>
> predicate就是我们的匹配条件；
>
> 而filter，就可以理解为一个无所不能的拦截器。有了这两个元素，再加上目标uri，就可以实现一个具体的路由了 

## 工作流程

> ![](.\img\152.jpg) 
>
> 客户端向 Spring Cloud Gateway 发出请求。然后在 Gateway Handler Mapping 中找到与请求相匹配的路由，将其发送到 Gateway Web Handler。 Handler 再通过指定的过滤器链来将请求发送到我们实际的服务执行业务逻辑，然后返回。
>
> 过滤器之间用虚线分开是因为过滤器可能会在发送代理请求之前（“pre”）或之后（“post”）执行业务逻辑。 Filter在“pre”类型的过滤器可以做参数校验、权限校验、流量监控、日志输出、协议转换等，在“post”类型的过滤器中可以做响应内容、响应头的修改，日志的输出，流量监控等有着非常重要的作用。 
>
> * 核心逻辑
>   * 路由转发+执行过滤器链

## 使用

1. 依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-gateway</artifactId>
   </dependency>
   ```

2. 配置文件

   ```yaml
   server:
     port: 9527
   
   spring:
     application:
       name: gateway
     cloud:
       gateway:
         routes:
           - id: payment_routh            # 路由的Id,没有固定规则，但要求唯一，建议配合服务名
             uri: http://localhost:8001   # 匹配后提供服务的路由地址
             predicates:
               - Path=/payment/get/**     # 断言，路径相匹配的进行路由
           - id: payment_routh
             uri: http://localhost:8001
             predicates:
               - Path=/payment/discovery
   
   eureka:
     instance:
       hostname: gateway-service
     client:
       service-url:
         register-with-eureka: true
         fetch-registry: true
         defaultZone: http://localhost:7001/eureka
   ```

## 动态路由的使用

1. 配置文件

   ```yaml
   server:
     port: 9527
   
   spring:
     application:
       name: gateway
     cloud:
       gateway:
         discovery:
           locator:
             enabled: true # 开启从注册中心动态创建路由的功能，利用微服务名进行路由
         routes:
           - id: payment_routh            
             uri: lb://PAYMENT #微服务名称  # 协议为lb，表示启用GateWay的负载均衡功能
             predicates:
               - Path=/payment/get/**
           - id: payment_routh
             uri: lb://PAYMENT
             predicates:
               - Path=/payment/discovery
   
   eureka:
     instance:
       hostname: gateway-service
     client:
       service-url:
         register-with-eureka: true
         fetch-registry: true
         defaultZone: http://localhost:7001/eureka
   ```

## Predicate的使用

1. 配置文件

   ```yaml
   server:
     port: 9527
   
   spring:
     application:
       name: gateway
     cloud:
       gateway:
         discovery:
           locator:
             enabled: true 
         routes:
           - id: payment_routh            
   #          uri: http://localhost:8001   
             uri: lb://PAYMENT
             predicates:
               - Path=/payment/get/**
           - id: payment_routh
   #          uri: http://localhost:8001
             uri: lb://PAYMENT
             predicates:
               - Path=/payment/discovery # 路径
               - After=2022-05-24T21:38:31.307+08:00[Asia/Shanghai] # 表示在特定时间之后才能访问
               - Before= # 表示在特定时间之前才能访问
               - Between= # 表示在特点两个时间之间才能访问
               - Cookie= # K,V键值对，K是Cookie名称，一个是正则表达式。路由规则会通过获取对应的Cookie名称的值，如果匹配上就会执行路由，否则就不执行
               - Header= # K,V键值对，一个是属性名称，一个是正则表达式，这个属性值的正则表达式匹配则执行
               - Host= # 接受一组匹配的域名列表，它通过参数种的主机地址作为匹配规则
               - Method= # 请求方法：GET,POST
               - Query= # 接受一组数据，它通过参数名称作为匹配规则
               
   eureka:
     instance:
       hostname: gateway-service
     client:
       service-url:
         register-with-eureka: true
         fetch-registry: true
         defaultZone: http://localhost:7001/eureka
   ```

## Filter的使用

> 路由过滤器可用于修改进入的HTTP请求和返回的HTTP响应，路由过滤器只能指定路由进行使用
>
> Spring Cloud GateWay内置了多种路由过滤器，他们都有GatewayFilter的工厂类来产生
>
> * 生命周期
>   * pre 前置
>   * post 后置
> * 种类
>   * GatewayFilter
>   * GlobalFilter

1. 配置文件

   ```yaml
   server:
     port: 9527
   
   spring:
     application:
       name: gateway
     cloud:
       gateway:
         discovery:
           locator:
             enabled: true
         routes:
           - id: payment_routh            
             uri: lb://PAYMENT 
             filters:
             	- AddRequestParameter=X-Request-Id,1025 # 过滤器工厂会在匹配的请求头加上一对请求头，名称为X-Request-Id，值为1025
             	predicates:
               	- Path=/payment/get/**
   
   eureka:
     instance:
       hostname: gateway-service
     client:
       service-url:
         register-with-eureka: true
         fetch-registry: true
         defaultZone: http://localhost:7001/eureka
   ```

### 自定义过滤器

> 功能：全局日志记录、统一网关鉴权
>
> **使用：实现GlobalFilter, Ordered**

* 案例代码

  ```java
  @Configuration
  @Slf4j
  public class MyLogGatewayFilter implements GlobalFilter, Ordered {
  
      /**
       * 过滤器操作
       */
      @Override
      public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
          log.info("进入了全局过滤器：" + new Date());
          String username = exchange.getRequest().getQueryParams().getFirst("username");
          if (username == null) {
              log.error("用户名为空");
              exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
              return exchange.getResponse().setComplete();
          }
          return chain.filter(exchange);
      }
  
      /**
       * 加载过滤器顺序，优先级越小，越先加载
       */
      @Override
      public int getOrder() {
          return 0;
      }
  }
  ```

# Config

## 简介

> SpringCloud Config为微服务架构中的微服务提供集中化的外部配置支持，配置服务器为**各个不同微服务应用**的所有环境提供了一个**中心化的外部配置**。 SpringCloud Config分为**服务端和客户端**两部分。 服务端也称为**分布式配置中心，它是一个独立的微服务应用**，用来连接配置服务器并为客户端提供获取配置信息，加密/解密信息等访问接口。客户端则是通过指定的配置中心来管理应用资源，以及与业务相关的配置内容，并在启动的时候从配置中心获取和加载配置信息配置服务器默认采用git来存储配置信息，这样就有助于对环境配置进行版本管理，并且可以通过git客户端工具来方便的管理和访问配置内容。 
>
> ![](.\img\153.jpg)

## 服务端配置

1. 依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-config-server</artifactId>
   </dependency>
   ```

2. 配置文件

   ```yaml
   server:
     port: 3344
   
   spring:
     application:
       name: config-center
     cloud:
       config:
         server:
           git:
             uri: https://github.com/Mr-Lei-Xuesong/SpringCloud-Config.git # GitHub上面的git仓库名字
             # 搜索目录
             search-paths:
               - SpringCloud-Config
         # 读取分支
         label: main
   
   
   # 服务注册到eureka
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:7001/eureka
   ```

3. 主启动类

   ```java
   @EnableConfigServer
   ```

4. 配置读取规则

   ```xml
   /{label}/{application}-{profile}.yml
   
   例：http://localhost:3344/main/config-test.yml
   ```

## 客户端配置

1. 依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-config</artifactId>
   </dependency>
   ```

### bootstrap.yml

>  applicaiton.yml是用户级的资源配置项
>
> bootstrap.yml是系统级的，**优先级更加高** 
>
> Spring Cloud会创建一个“Bootstrap Context”，作为Spring应用的`Application Context`的父上下文。初始化的时候，`Bootstrap Context`负责从外部源加载配置属性并解析配置。这两个上下文共享一个从外部获取的`Environment`。
>
> `Bootstrap`属性有高优先级，默认情况下，它们不会被本地配置覆盖。 `Bootstrap context`和`Application Context`有着不同的约定，所以新增了一个`bootstrap.yml`文件，保证`Bootstrap Context`和`Application Context`配置的分离。 
>
> 要将Client模块下的application.yml文件改为bootstrap.yml,这是很关键的，因为bootstrap.yml是比application.yml先加载的。bootstrap.yml优先级高于application.yml  

2. 配置文件

   ```yaml
   server:
     port: 3355
   
   spring:
     application:
       name: config-client
     cloud:
       # Config客户端配置
       config:
         label: main # 分支名称
         name: config # 配置文件名称
         profile: dev # 读取后缀名称
         uri: http://localhost:3344 # 配置中心地址
   
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:7001/eureka
   ```

## 客户端动态刷新

1. 配置文件

   ```yaml
   # 暴露监控端点
   management:
     endpoints:
       web:
         exposure:
           include: "*"
   ```

2. 接口类上

   ```java
   @RefreshScope
   ```

3. 访问接口

   ```
   POST http://localhost:3355/actuator/refresh
   ```

# Bus

## 简介

>  **Spring Cloud Bus 配合 Spring Cloud Config 使用可以实现配置的动态刷新**。Spring Cloud Bus能管理和传播分布式系统间的消息，就像一个分布式执行器，可用于广播状态更改、事件推送等，也可以当作微服务间的通信通道。
>
> ### 什么是总线
>
>  微服务架构的系统中，通常会使用**轻量级的消息代理**来构建一个**共用的消息主题**，并让系统中所有微服务实例都连接上来。由于**该主题中产生的消息会被所有实例监听和消费，所以称它为消息总线**。在总线上的各个实例，都可以方便地广播一些需要让其他连接在该主题上的实例都知道的消息。
>
> ### 基本原理
>
> ConfigClient实例都监听MQ中同一个topic(默认是springCloudBus)。当一个服务刷新数据的时候，它会把这个信息放入到Topic中，这样其它监听同一Topic的服务就能得到通知，然后去更新自身的配置。
>
> ![](.\img\155.jpg)
>
> ### 为什么不选择客户端来做消息总线
>
> 1. 打破了微服务的职责单一性，因为微服务本身是业务模块，它本不应该承担配置刷新的职责。
> 2. 破坏了微服务各节点的对等性。
> 3. 有一定的局限性。例如，微服务在迁移时，它的网络地址常常会发生变化，此时如果想要做到自动刷新，那就会增加更多的修改

## 使用

> 利用消息总线触发一个服务端ConfigServer的**/bus-refresh**端点，而刷新所有客户端的配置

1. 安装RabbitMQ

2. 服务端

   1. 依赖

      ```xml
      <dependency>
          <groupId>org.springframework.cloud</groupId>
          <artifactId>spring-cloud-starter-bus-amqp</artifactId>
      </dependency>
      ```

   2. 配置文件

      ```yaml
      server:
        port: 3344
      
      spring:
        application:
          name: config-center
        cloud:
          config:
            server:
              git:
                uri: https://github.com/Mr-Lei-Xuesong/SpringCloud-Config.git # GitHub上面的git仓库名字
                # 搜索目录
                search-paths:
                  - SpringCloud-Config
            # 读取分支
            label: main
        # RabbitMq相关配置
        rabbitmq:
          host: 175.178.2.155
          port: 5672
          username: guest
          password: guest
      
      
      # 服务注册到eureka
      eureka:
        client:
          service-url:
            defaultZone: http://localhost:7001/eureka
      
      # 暴露Bus刷新配置的端点
      management:
        endpoints:
          web:
            exposure:
              include: 'bus-refresh'
      ```

3. 客户端

   1. 依赖同上

   2. 配置文件

      ```yaml
      server:
        port: 3355
      
      spring:
        application:
          name: config-client
        cloud:
          # Config客户端配置
          config:
            label: main # 分支名称
            name: config # 配置文件名称
            profile: dev # 读取后缀名称
            uri: http://localhost:3344 # 配置中心地址
        # RabbitMq相关配置
        rabbitmq:
          host: 175.178.2.155
          port: 5672
          username: guest
          password: guest
      
      eureka:
        client:
          service-url:
            defaultZone: http://localhost:7001/eureka
      
      # 暴露监控端点
      management:
        endpoints:
          web:
            exposure:
              include: "*"
      ```

> 修改Git上的文件，服务端发送Post请求`http://localhost:3344/actuator/bus-refresh`
>
> 公式：**http://localhost:配置中心的端口号/actuator/bus-refresh/{destination}**
>
> * destination:
>   * 服务名称:端口号

# Stream

## 简介

> Spring Cloud Stream 是一个构建消息驱动微服务的框架。 
>
> 应用程序通过 inputs 或者 outputs 来与 Spring Cloud Stream中binder对象交互。
>
> 通过我们配置来binding(绑定) ，而 Spring Cloud Stream 的 **binder对象负责与消息中间件交互**。
>
> 所以，我们只需要搞清楚如何与 Spring Cloud Stream 交互就可以方便使用消息驱动的方式。 
>
> 通过使用Spring Integration来连接消息代理中间件以实现消息事件驱动。
>
> Spring Cloud Stream 为一些供应商的消息中间件产品提供了个性化的自动化配置实现，引用了发布-订阅、消费组、分区的三个核心概念。目前仅支持RabbitMQ、Kafka。  
>
> **屏蔽底层消息中间件的差异,降低切换成本，统一消息的编程模型**

## 设计思想

### 标准MQ

> 生产者/消费者之间靠消息媒介传递信息内容----Message
>
> 消息必须走特定通道-----消息通道MessageChannel
>
> 消息通道里的消息如何处理，谁负责收发处理-----消息通道MessageChannel的子接口SubscribableChannel，由MessageHandler消息处理器所订阅

### Stream

>  在没有绑定器这个概念的情况下，我们的SpringBoot应用要直接与消息中间件进行信息交互的时候，由于各消息中间件构建的初衷不同，像RabbitMQ有exchange，kafka有Topic和Partitions分区，它们的实现细节上会有较大的差异性。**通过定义绑定器作为中间层，完美地实现了应用程序与消息中间件细节之间的隔离。通过向应用程序暴露统一的Channel通道，使得应用程序不需要再考虑各种不同的消息中间件实现。通过定义绑定器Binder作为中间层，实现了应用程序与消息中间件细节之间的隔离。**
>
> Stream对消息中间件的进一步封装，可以做到代码层面对中间件的无感知，甚至于动态的切换中间件(rabbitmq切换为kafka)，使得微服务开发的高度解耦，服务可以关注更多自己的业务流程 通过定义绑定器Binder作为中间层，实现了应用程序与消息中间件细节之间的隔离。Binder可以生成Binding，Binding用来绑定消息容器的生产者和消费者，它有两种类型，INPUT和OUTPUT，**INPUT对应于消费者，OUTPUT对应于生产者**。     
>
> **Stream中的消息通信方式遵循了发布-订阅模式**
>
> ![](.\img\156.jpg)

## Stream标准流程

* Binder：很方便的连接中间件，屏蔽差异
* Channel：通道，是队列Queue的一种抽象，在消息通讯系统中就是实现存储和转发的媒介，通过Channel对队列进行配置
* Source和Sink：简单的可理解为参照对象是Spring Cloud Stream自身，从Stream发布消息就是输出，接受消息就是输入。

## 常用API和常用注解

![](.\img\157.jpg)

## 使用

> 消息生产者

1. 依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
   </dependency>
   ```

2. 配置文件

   ```yaml
   server:
     port: 8801
   
   spring:
     application:
       name: stream-provider
     cloud:
       stream:
         binders: # 在此处配置要绑定的RabbitMQ的服务信息
           defaultRabbit: # 表示定义的名称，用于和binding整合
             type: rabbit # 消息组件类型
             environment: # 设置RabbitMQ的相关的环境配置
               spring:
                 rabbitmq:
                   host: 175.178.2.155
                   port: 5672
                   username: guest
                   password: guest
         bindings: # 服务的整合处理
           output: # 表示输出通道
             destination: studyExchange # 表示要使用的Exchange名称
             content-type: application/json # 设置消息类型，本次为json,文本则设置"text/plain"
             binder: defaultRabbit # 设置要绑定的消息服务的具体位置
   
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:7001/eureka
     instance:
       lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔(默认是30秒)
       lease-expiration-duration-in-seconds: 5 # 超过5秒就剔除注册中心(默认90秒)
       instance-id: send-8801.com # 在信息列表时显示主机名称
       prefer-ip-address: true # 访问的路径变为ip地址
   
   ```

3. 创建消息业务层和实现层

   ```java
   @EnableBinding(Source.class)//定义消息的推送管道
   @Slf4j
   public class MessageProviderImpl implements MessageProvider {
   
       @Resource
       private MessageChannel output;//消息发送管道
   
       @Override
       public String send() {
           String serial = UUID.randomUUID().toString();
           //发送消息
           output.send(MessageBuilder.withPayload(serial).build());
           log.info("=======serial=======" + serial);
           return null;
       }
   }
   ```

4. 创建接口层，调用服务层

> 消息消费者

1. 依赖同上

2. 配置文件

   ```yaml
   server:
     port: 8802
   
   spring:
     application:
       name: stream-consumer
     cloud:
       stream:
         binders: # 在此处配置要绑定的RabbitMQ的服务信息
           defaultRabbit: # 表示定义的名称，用于和binding整合
             type: rabbit # 消息组件类型
             environment: # 设置RabbitMQ的相关的环境配置
               spring:
                 rabbitmq:
                   host: 175.178.2.155
                   port: 5672
                   username: guest
                   password: guest
         bindings: # 服务的整合处理
           input: # 表示输入通道
             destination: studyExchange # 表示要使用的Exchange名称
             content-type: application/json # 设置消息类型，本次为json,文本则设置"text/plain"
             binder: defaultRabbit # 设置要绑定的消息服务的具体位置
   
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:7001/eureka
     instance:
       lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔(默认是30秒)
       lease-expiration-duration-in-seconds: 5 # 超过5秒就剔除注册中心(默认90秒)
       instance-id: receive-8802.com # 在信息列表时显示主机名称
       prefer-ip-address: true # 访问的路径变为ip地址
   
   ```

3. 创建消息接收接口

   ```java
   @RestController
   @EnableBinding(Sink.class)//指信道channel和exchange绑定在一起
   @Slf4j
   public class ReceiveMessageListenerController {
   
       @Value("${server.port}")
       private String serverPort;
   
       //监听队列，用于消费者的队列的消息接收
       @StreamListener(Sink.INPUT)
       public void input(Message<String> message) {
           log.info(serverPort + "------->接收到消息：" + message.getPayload());
       }
   }
   ```

## 分组消费与持久化

> ### 重复消费问题
>
> 比如在如下场景中，订单系统我们做集群部署，都会从RabbitMQ中获取订单信息，那如果一个订单同时被两个服务获取到，那么就会造成数据错误，我们得避免这种情况。
>
> 这时我们就可以使用Stream中的消息分组来解决
>
> **在Stream中处于同一个组(group)中的多个消费者是竞争关系，就能够保证消息只会被其中一个应用消费一次。不同组是可以全面消费的(重复消费)，** 
>
> ### 解决
>
> * 配置文件
>
>   ```yaml
>   server:
>     port: 8802
>   
>   spring:
>     application:
>       name: stream-consumer
>     cloud:
>       stream:
>         binders: 
>           defaultRabbit: 
>             type: rabbit 
>             environment: 
>               spring:
>                 rabbitmq:
>                   host: 175.178.2.155
>                   port: 5672
>                   username: guest
>                   password: guest
>         bindings: 
>           input: 
>             destination: studyExchange 
>             content-type: application/json 
>             binder: defaultRabbit 
>             group: consumer # 相同功能的服务放在同一组内
>   
>   eureka:
>     client:
>       service-url:
>         defaultZone: http://localhost:7001/eureka
>     instance:
>       lease-renewal-interval-in-seconds: 2 
>       lease-expiration-duration-in-seconds: 5 
>       instance-id: receive-8802.com 
>       prefer-ip-address: true 
>   ```

# Sleuth

## 简介

>  在微服务框架中，一个由客户端发起的请求在后端系统中会经过多个不同的的服务节点调用来协同产生最后的请求结果，每一个前段请求都会形成一条复杂的分布式服务调用链路，链路中的任何一环出现高延时或错误都会引起整个请求最后的失败。    
>
>  **Spring Cloud Sleuth提供了一套完整的服务跟踪的解决方案**。在分布式系统中提供追踪解决方案并且兼容支持了zipkin
>
>  ### 完整的调用链路
>
>  * 表示一请求链路，一条链路通过Trace Id唯一标识，Span标识发起的请求信息，各span通过parent id 关联起来 
>
>   ![](.\img\158.jpg)
>
>   ![](.\img\159.jpg)
>
>   * 名词解释
>     * Trace:类似于树结构的Span集合，表示一条调用链路，存在唯一标识
>     * span:表示调用链路来源，通俗的理解span
>     * 就是一次请求信息

## 使用

1. 下载zipkin的jar包

   ```
   https://repo1.maven.org/maven2/io/zipkin/java/zipkin-server/2.12.9/
   运行jar包
   访问http://localhost:9411/zipkin/
   ```

> 服务端

1. 依赖

   ```xml
   <dependency>            
       <groupId>org.springframework.cloud</groupId>            
       <artifactId>spring-cloud-starter-zipkin</artifactId>        
   </dependency>
   ```

2. 配置文件

   ```yaml
   spring:
     zipkin:
       base-url: http://localhost:9411
       sleuth:
         sampler:
           # 采样率值介于0到1之间，1表示全部采集
           probability: 1
   ```

> 客户端同上

# Nacos

## 简介

> 一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。
>
> **Nacos就是注册中心 + 配置中心的组合**
>
> 下载地址：`https://github.com/alibaba/nacos/releases`
>
> Windows启动nacos：`nacos/bin/startup.cmd`
>
> 访问地址：`http://localhost:8848/nacos`
>
> 默认账号密码：nacos
>
> ### 各注册中心比对
>
> ![](.\img\162.jpg)
>
> **Nacos 支持AP和CP模式的切换**
>
> `C是所有节点在同一时间看到的数据是一致的；而A的定义是所有的请求都会收到响应`。 
>
> 何时选择使用何种模式？
>
> 一般来说，如果不需要存储服务级别的信息且服务实例是通过nacos-client注册，并能够保持心跳上报，那么就可以选择AP模式。当前主流的服务如 Spring cloud 和 Dubbo 服务，都适用于AP模式，AP模式为了服务的可能性而减弱了一致性，因此AP模式下只支持注册临时实例。 
>
> 如果需要在服务级别编辑或者存储配置信息，那么 CP 是必须，K8S服务和DNS服务则适用于CP模式。CP模式下则支持注册持久化实例，此时则是以 Raft 协议为集群运行模式，该模式下注册实例之前必须先注册服务，如果服务不存在，则会返回错误。
>
> curl -X PUT '$NACOS_SERVER:8848/nacos/v1/ns/operator/switches?entry=serverMode&value=CP' 

## 注册中心使用

> 服务端

1. 依赖

   ```xml
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
   </dependency>
   ```

2. 配置文件

   ```yaml
   server:
     port: 9001
   
   spring:
     application:
       name: payment-nacos
     cloud:
       nacos:
         discovery:
           server-addr: localhost:8848
   
   management:
     endpoints:
       web:
         exposure:
           include: '*'
   ```

3. 启动类

   ```java
   @EnableDiscoveryClient
   ```

> 消费端

1. 依赖同上

2. 配置文件

   ```yaml
   server:
     port: 80
   spring:
     application:
       name: order-nacos
     cloud:
       nacos:
         discovery:
           server-addr: localhost:8848
   
   # 消费者将要去访问的微服务名称(注册成功进Nacos的微服务提供者)
   service-url:
     nacos-user-service: http://payment-nacos
   
   ```

3. 启动类同上

## 配置中心使用

1. 依赖

   ```xml
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
   </dependency>
   ```

2. 配置文件

   1. bootstrap.yml

      ```yaml
      server:
        port: 3377
      
      spring:
        application:
          name: config-nacos-client
        cloud:
          nacos:
            discovery:
              server-addr: localhost:8848 # Nacos服务注册中心地址
            config:
              server-addr: localhost:8848 # Nacos作为配置中心地址
              file-extension: yaml # 读取指定yaml格式的配置
      
      # ${spring.application.name}-${spring.profile.active}.${spring.cloud.nacos.config.file-extension}
      ```

   2. application.yml

      ```yaml
      spring:
        profiles:
          active: dev # 表示开发环境
      ```

3. 接口类上

   ```java
   @RefreshScope//支持Nacos的动态刷新功能
   ```

> 在Nacos网页上，添加配置信息
>
> 公式：`${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}`**对应：Data Id的名称**
>
> * prefix 默认为 spring.application.name 的值
> * spring.profile.active 即为当前环境对应的 profile，可以通过配置项 spring.profile.active 来配置。
> * file-exetension 为配置内容的数据格式，可以通过配置项 spring.cloud.nacos.config.file-extension 来配置

## 配置中心-分类配置

* Namespace+Group+Data ID三者关系

> 最外层的**Namespace是可以用于区分部署环境**的，Group和DataID逻辑上区分两个目标对象。
>
> 默认情况：Namespace=public，Group=DEFAULT_GROUP
>
> 比方说我们现在有三个环境：开发、测试、生产环境，我们就可以创建三个Namespace，不同的Namespace之间是隔离的。 Group默认是DEFAULT_GROUP，Group可以把不同的微服务划分到同一个分组里面去。     

* Group方案

  ```yaml
  spring:
    application:
      name: config-nacos-client
    cloud:
      nacos:
        discovery:
          server-addr: localhost:8848
        config:
          server-addr: localhost:8848
          file-extension: yaml 
          group: DEV_GROUP # 读取不同组
  ```

* Namespace方案

  ```yaml
  server:
    port: 3377
  
  spring:
    application:
      name: config-nacos-client
    cloud:
      nacos:
        discovery:
          server-addr: localhost:8848 # Nacos服务注册中心地址
        config:
          server-addr: localhost:8848 # Nacos作为配置中心地址
          file-extension: yaml # 读取指定yaml格式的配置
          group: DEV_GROUP # 组的名称
          namespace: 3f4950ce-12b1-4086-acdc-6c9f90b952e9 # Namescape Id
  ```

## 集群

>  默认Nacos使用嵌入式数据库(`derby`)实现数据的存储。所以，如果启动多个默认配置下的Nacos节点，数据存储是存在一致性问题的。为了解决这个问题，Nacos采用了集中式存储的方式来支持集群化部署，目前只支持MySQL的存储。

### 持久化配置

1. 进入nacos的conf目录下，找到`nacos-mysql.sql`脚本，运行到数据库

2. 修改nacos的conf目录下的application.properties文件内容，新增如下内容

   ```properties
   spring.datasource.platform=mysql
   
   db.num=1
   db.url.0=jdbc:mysql:///数据库名?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
   db.user=账号
   db.password=密码
   ```

### 集群配置

1. 修改`nacos/conf/cluster.conf`文件

   ```shell
   主机ip:端口号1
   主机ip:端口号2
   主机ip:端口号3
   ```

2. 修改`nacos/bin/startup.sh`文件

   ```shell
   while getopts ":m:f:s:p:" opt
   do
       case $opt in
           m)
               MODE=$OPTARG;;
           f)
               FUNCTION_MODE=$OPTARG;;
           s)
               SERVER=$OPTARG;;
           p)
               PORT=$OPTARG;;
           ?)
           echo "Unknown parameter"
           exit 1;;
       esac
   done
   
   
   nohup $JAVA -Dserver.port=${PORT} ${JAVA_OPT} nacos.nacos >> ${BASE_DIR}/logs/start.out 2>&1 &
   ```

3. 修改Nginx的`nginx/conf/nginx.conf`配置文件

   ![](.\img\164.jpg)

4. 启动三台nacos服务`./startup.sh -p 端口号`，启动nginx服务

# Sentinel

## 简介

> Sentinel 是面向分布式服务架构的高可用流量防护组件，主要以流量为切入点，从限流、流量整形、熔断降级、系统负载保护、热点防护等多个维度来帮助开发者保障微服务的稳定性
>
> Sentinel 具有以下特性:
>
> * **丰富的应用场景**：Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、集群流量控制、实时熔断下游不可用应用等。
>
> * **完备的实时监控**：Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。
>
> * **广泛的开源生态**：Sentinel 提供开箱即用的与其它开源框架 / 库的整合模块，例如与 Spring Cloud、Dubbo、gRPC 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。
>
> * **完善的 SPI 扩展点**：Sentinel 提供简单易用、完善的 SPI 扩展接口。您可以通过实现扩展接口来快速地定制逻辑。例如定制规则管理、适配动态数据源等。
>
>   ![](.\img\165.jpg)

## 使用

> 1. 下载Sentinel
>    * https://github.com/alibaba/Sentinel/releases
>
> 2. 启动
>
>    * java -jar sentinel-dashboard-1.7.0.jar
>
> 3. 访问sentinel管理界面
>
>    * http://localhost:8080
>
>    * 登录账号密码均为sentinel

1. 依赖

   ```xml
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
   </dependency>
   
   <!--SpringCloud alibaba sentinel-datasource-nacos：后续做持久化用到-->
   <dependency>
       <groupId>com.alibaba.csp</groupId>
       <artifactId>sentinel-datasource-nacos</artifactId>
   </dependency>
   ```

2. 配置文件

   ```yaml
   server:
     port: 8401
   spring:
     application:
       name: sentinel-service
     cloud:
       nacos:
         discovery:
           # Nacos服务注册中心地址
           server-addr: localhost:8848
       sentinel:
         transport:
           # Sentinel Dashboard地址
           dashboard: localhost:8080
           # 默认8719端口，假如被占用会自动从8719开始一次+1扫描，直至找到被占用的端口。
           port: 8719
   
   management:
     endpoints:
       web:
         exposure:
           include: '*'
   ```

## 控流规则

> ![](.\img\166.jpg)
>
> * 资源名：唯一名称，**默认请求路径**
> * 针对来源：**Sentinel可以针对调用者进行限流，填写微服务名**，默认default(不区分来源)
> * 阈值类型/单机阈值：
>   * QPS(每秒钟的请求数量)：当调用该api的QPS达到阈值的时候，进行限流
>   * 线程数：当调用该api的线程数达到阈值的时候，进行限流
> * 是否集群：不需要集群
> * 流控模式：
>   * 直接：api达到限流条件时，直接限流
>   * 关联：当关联的资源达到阈值时，就限流自己
>   * 链路：只记录指定链路上的流量(指定资源从入口资源进来的流量，如果达到阈值，就行限流)【api级别的针对来源】
> * 流控效果：
>   * 快速失败：直接失败，抛异常
>   * Warm Up：根据codeFactor(冷加载因子，默认3)的值，从阈值/codeFactor，经过预热时长，才达到设置的QPS阈值
>   * 排队等待：均速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS，否则无效

## 降级规则

> 1. Sentinel熔断降级会在调用链路中某个资源出现不稳定状态时（例如调用超时或异常比例升高）
>
> 2. 对这个资源的调用进行限制，让请求快速失败，避免影响到其他的资源而导致的联机错误。
>
> 3. 当资源被降级后，在接下来的降级时间窗口内，对该资源的调用都将自动熔断(默认抛出DegradeException）
>
> ![](.\img\167.jpg)
>
> * RT(秒级)
>   * **平均响应时间超出阈值，且通过的请求>=5(5是一个固定值)，两个条件同时满足后触发降级。窗口期过后关闭断路器**。
>   * RT最大49000，更大的需要通过 -Dcsp.sentinel.statistic.max.rt=XXXX才能生效。
>   * 例如：
>     * 设置平均响应时间200ms，时间窗口1s
>       * 每秒请求数超过5个，并且请求的响应时间超过0.2s秒时，则下一个请求1s内不可用。
>       * 如果1S内发送过来6个请求，但是我在200ms内处理完了就不触发
>   * **注意**：虽然叫做平均响应时间，但是含义是1秒内发过来的所有请求，处理花费的时间。所以应该是：处理1s内所有请求花费的时间
> * 异常比例（秒级）
>   * OPS >= 5且异常比例（秒级统计）超过阈值时，触发降级；
>   * 时间窗口内服务降级，时间窗口结束，关闭降级。
> * 异常数（分钟级）
>   * 异常数（分钟统计）超过阈值时，触发降级；时间窗口结束后，关闭降级。
>   * 时间窗口要大于60s

## 热点key限流

> 很多时候我们希望统计某个热点数据中访问频次做高的数据（Top K），并对其进行访问限制
>
> **热点参数限制会统计传入参数中的热点参数，并根据配置的限流阈值与模式，对包含热点参数的资源调用进行限制**。    
>
> 热点参数限流可以看作是一种特殊的流量控制，仅对包含热点参数的资源调用生效。        
>
> Sentinel利用LRU策略统计最近最常访问的热点参数，及任何人令牌桶算法来进行参数级别的流控。    
>
> 热点参数限流支持集群模式。

### 实例代码

```java
@GetMapping("/testHotKey")
@SentinelResource(value = "testHotKey", blockHandler = "handleException")
public String testHotKey(@RequestParam(value = "p1", required = false) String p1,
                         @RequestParam(value = "p2", required = false) String p2) {
    return "------testHotKey";
}

public String handleException(String p1, String p2, BlockException ex) {
    return "-----testHotKey,(ㄒoㄒ)~~";
}
```

![](.\img\168.jpg)

### 参数例外项

> 上一个案例演示了第一个参数p1，为其绑定了一个热点参数限流规则。当其相关的资源QPS超过    1/s时，触发热点参数限流规则，马上限流。
>
> 特例情况：        
>
> ​		我们期望p1参数当它是某个特例时，它的限流值和平时不一样。
>
> 特例：假如当p1的值等于5时，它的阈值可以达到200
>
> 配置规则：
>
> ​	 当参数p1的值为5时，阈值是200/s        
>
> ​	 当参数p1的值不为5，阈值是1/s
>
> ![](.\img\171.jpg)

## 系统规则

> Sentinel系统自适应限流从整体维度对应用入口流量进行控制。        
>
> 对整个系统进行的规则配置，触发后整个系统限流，类似全局配置。
>
> ![](.\img\172.jpg)
>
> * Load自适应(仅对Linux/Unix-like机器生效)：系统的load作为启发指标，进行自适应系统保护。当系统load超过设定的启发值，且系统当前的并发线程数超过估算的系统容量时才会触发系统保护(BBR阶段)/系统容量由系统`maxQPS*minRt`估算得出。设定参考值一般是`CPU cores * 2.5`
> * CPU usage(1.5.0+版本)：当系统CPU使用率超过阈值即触发系统保护(取值范围0.0~1.0)
> * 平均RT：当单台机器上所有入口流量的平均TR达到阈值即触发系统保护，单位是毫秒
> * 并发线程数：当单台机器上所有入口流量的并发线程达到阈值即触发系统保护
> * 入口QPS：当单台机器上所有入口流量的QPS达到阈值即触发系统保护

## @SentinelResource

### 按资源名称限流+后续处理

```java
@GetMapping("/byResourceAAAA")
@SentinelResource(value = "byResource", blockHandler = "handlerException")
public JsonResult byResource() {
    return new JsonResult(200, "按资源名称限流测试OK", new Payment(2020L, "serial001"));
}

public JsonResult handlerException(BlockException e) {
    return new JsonResult(404, e.getClass().getCanonicalName() + "\t 服务不可用", null);
}
```

![](.\img\173.jpg)

### 按照URL地址限流+后续处理

```java
@GetMapping("/rateLimit/byUrl")
@SentinelResource(value = "byUrl")
public JsonResult byUrl() {
    return new JsonResult(200, "按照URL限流测试OK", new Payment(2020L, "serial002"));
}
```

![](.\img\174.jpg)

> **既可以用URL也可以用Resource来指定限流规则**

### 客户自定义限流处理类

1. 创建自定义限流处理类

   ```java
   public class CustomerBlockHandler {
   
       public static JsonResult handlerException(BlockException e) {
           return new JsonResult(4444, "全局GLOBAL的客户自定义处理---1", null);
       }
   
       public static JsonResult handlerException2(BlockException e) {
           return new JsonResult(4444, "全局GLOBAL的客户自定义处理----2", null);
       }
   
   }
   ```

2. 指定自定义限流处理类和其中的方法作为兜底的方法

   * **自定义限流处理类：blockHandlerClass = CustomerBlockHandler.class**
   * **自定义限流处理方法：blockHandler = "handlerException2"**

   ```java
   /**
     * 测试全局的自定义限流处理方法：指定自定义限流处理类和其中的方法作为兜底的方法
    */
   @GetMapping("/rateLimit/CustomerBlockHandler")
   @SentinelResource(value = "CustomerBlockHandler",
                     blockHandlerClass = CustomerBlockHandler.class, blockHandler = "handlerException2")
   public JsonResult CustomerBlockHandler(){
       return new JsonResult(200, "全局的客户自定义处理", new Payment(2020L, "serial003"));
   }
   ```

3. 添加限流规则

   ![](.\img\175.jpg)

## 服务熔断

### Ribbon系列

1. 启动nacos和sentinel

2. 创建两个微服务提供者(payment-nacos-three，payment-nacos-four)

3. 创建一个微服务消费者(order-nacos-two)

```java
@RestController
@Slf4j
@RequestMapping("/order")
public class CircuitBreakerController {

    public static final String SERVER_URL = "http://payment-nacos";

    @Resource
    private RestTemplate restTemplate;

    @RequestMapping("/fallback/{id}")
//    @SentinelResource(value = "fallback") 没有配置
//    @SentinelResource(value = "fallback", fallback = "fallbackHandler")//fallback只负责业务异常
//    @SentinelResource(value = "fallback", blockHandler = "blockHandler")//blockHandler只负责sentinel控制台配置违规
    @SentinelResource(value = "fallback", blockHandler = "blockHandler",fallback = "fallbackHandler")//两个都配的话，以sentinel为主
    public JsonResult fallback(@PathVariable Long id) {
        JsonResult result = restTemplate.getForObject(SERVER_URL + "/paymentSQL/" + id, JsonResult.class, id);
        if (id == 4) {
            throw new IllegalArgumentException("IllegalArgumentException，非法参数异常....");
        } else if (result.getData() == null) {
            throw new NullPointerException("NullPointerException，该ID没有对应记录，空指针异常");
        }
        return result;
    }

    //fallback
    public JsonResult fallbackHandler(@PathVariable Long id, Throwable e) {
        Payment payment = new Payment(id, "null");
        return JsonResult.build(444, "==fallback==业务异常兜底异常处理，异常为：" + e.getMessage(), payment);
    }

    //blockHandler
    public JsonResult blockHandler(@PathVariable Long id, BlockException e) {
        Payment payment = new Payment(id, "null");
        return JsonResult.build(445, "==blockHandler==sentinel异常处理，异常为：" + e.getMessage(), payment);
    }
}

```

### openFeign系列

1. pom文件

   ```xml
   <!--openFeign-->
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-openfeign</artifactId>
   </dependency>
   ```

2. 配置文件

   ```yaml
   # 激活Sentinel对Feign的支持
   feign:
     sentinel:
       enabled: true
   ```

3. 主启动类

   ```java
   @EnableFeignClients
   ```

4. 业务层

   ```java
   @FeignClient(value = "payment-nacos",fallback = PaymentFallbackService.class)
   //我们要到nacos中找payment-nacos这个微服务，并且指明了接口出错时的兜底方法PaymentFallbackService
   public interface PaymentService {
   
       @GetMapping(value = "/paymentSQL/{id}") //去找payment-nacos服务中的相应接口
       public JsonResult paymentSQL(@PathVariable("id") Long id);
   
   }
   ```

   ```java
   @Service
   public class PaymentFallbackService implements PaymentService {
       @Override
       public JsonResult paymentSQL(Long id) {
           return JsonResult.build(4444444, "服务降级返回---PaymentFallbackService", new Payment(id, "errorSerial...."));
       }
   }
   ```

5. 控制层

   ```java
   @RestController
   @Slf4j
   @RequestMapping("/order")
   public class CircuitBreakerController {
   
       @Resource
       private PaymentService paymentService;
   
       @GetMapping(value = "/paymentSQL/{id}")
       public JsonResult paymentSQL(@PathVariable("id") Long id){
           return paymentService.paymentSQL(id);
       }
   }
   ```

## 规则持久化

> 微服务新增的限流规则后，微服务关闭后就会丢失，当时配置都限流规则都是临时的。
>
>  将限流配置规则持久化进Nacos保存，只要刷新某个rest地址，sentinel控制台的流控规则就能看到。只要nacos里面的配置不删除，针对微服务上的sentinel上的流控规则就持续存在。    （也可以持久化到文件，redis，数据库等）

1. 依赖

   ```xml
   <dependency>
       <groupId>com.alibaba.csp</groupId>
       <artifactId>sentinel-datasource-nacos</artifactId>
   </dependency>
   ```

2. 配置文件

   ```yaml
   spring:
     cloud:
       sentinel:
         datasource:
           ds1: # 数据源1
             nacos:
               server-addr: localhost:8848
               data-id: ${spring.application.name}
               group-id: DEFAULT_GROUP
               data-type: json
               rule-type: flow # 流控规则
   ```

3. 在Nacos网站上**新建配置**

![](.\img\176.jpg)

4. Json内容
   * resource：资源名称
   * limitApp：来源应用(默认default)
   * grade：阈值类型，0表示线程数，1表示QPS
   * count：单机阈值
   * strategy：流控模式，0表示直接，1表示关联，2表示链路
   * controlBehavior：流控效果，0表示快速失败，1表示Warm Up，2表示排队等待
   * clusterMode：是否集群

# Seata

## 简介

> Seata是一款开源的分布式事务解决方案，致力于在微服务架构下提供高性能的和简单易用的分布式事务服务。

> **术语：**
>
> * 分布式事务处理过程的一个ID+三个组件模型
>   * 全局唯一的事务ID：Transaction ID XID
>     * 在这个事务ID下的所有事务会被统一控制
>   * 三个组件
>     * Transaction Coordinator(TC)：事务协调器，维护全局事务的运行状态，负责协调并驱动全局事务的提交或回滚
>     * Transaction Manager(TM)：事务管理器，控制全局事务的边界，负责开启一个全局事务，并最终发起全局提交或全局回滚的协议
>     * Resource Manager(RM)：资源管理器，控制分支事务，负责分支注册，状态汇报，并接收事务协调器的指令，驱动分支(本地)事务的提交和回滚

> Seata一个典型的分布式控制事务的流程：
>
> 1. TM向TC申请开启一个全局事务，全局事务创建成功并生成一个全局唯一的XID
>
> 2. XID在微服务调用链路的上下文中传播；
>
> 3. RM向TC注册分支事务，将其纳入XID对应全局事务的管辖
>
> 4. TM向TC发起针对XID的全局提交或回滚决议
>
> 5. TC调度XID下管辖的全局分支事务完成提交或回滚请求
>
>    ![](.\img\177.jpg)

## 安装

1. 下载地址：`https://github.com/seata/seata/releases`

2. 修改seata/conf目录下的file.conf配置文件：`主要修改：自定义事务组名称+事务日志存储模式为db+数据库连接信息`
3. mysql数据库新建库seata：`建表db_store.sql在seata/conf目录里面`
4. 修改seata/conf目录下的registry.conf配置文件：`注明注册中心是nacos，并修改nacos的连接信息`

5. 启动nacos，在启动seata

## 使用

1. 依赖

   ```xml
   <!--seata-->
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
       <!-- 因为兼容版本问题,所以需要剔除它自带的seata的包 -->
       <exclusions>
           <exclusion>
               <artifactId>seata-all</artifactId>
               <groupId>io.seata</groupId>
           </exclusion>
       </exclusions>
   </dependency>
   <dependency>
       <groupId>io.seata</groupId>
       <artifactId>seata-all</artifactId>
       <version>0.9.0</version>
   </dependency>
   
   <!--openFeign：微服务之间的调用不适用ribbon就是用feign-->
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-openfeign</artifactId>
   </dependency>
   
   <!--web/actuator这两个一般一起使用，写在一起-->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-web</artifactId>
   </dependency>
   <!--监控-->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-actuator</artifactId>
   </dependency>
   
   <!--spring cloud alibaba nacos-->
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
   </dependency>
   <!--SpringCloud alibaba sentinel-datasource-nacos：后续做持久化用到-->
   <dependency>
       <groupId>com.alibaba.csp</groupId>
       <artifactId>sentinel-datasource-nacos</artifactId>
   </dependency>
   <!--SpringCloud alibaba Sentinel-->
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
   </dependency>
   
   <!--Mybatis和SpringBoot的整合-->
   <dependency>
       <groupId>com.baomidou</groupId>
       <artifactId>mybatis-plus-boot-starter</artifactId>
   </dependency>
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>druid</artifactId>
       <version>1.1.16</version>
   </dependency>
   
   <!--mysql-connector-java-->
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
   </dependency>
   <!--jdbc-->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-jdbc</artifactId>
   </dependency>
   <!--热部署-->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-devtools</artifactId>
       <scope>runtime</scope>
       <optional>true</optional>
   </dependency>
   
   <dependency>
       <groupId>org.projectlombok</groupId>
       <artifactId>lombok</artifactId>
       <optional>true</optional>
   </dependency>
   
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-test</artifactId>
       <scope>test</scope>
   </dependency>
   ```
   
2. 配置文件

   ```yaml
   server:
     port: 2003
   
   spring:
     application:
       name: seata-account
     cloud:
       alibaba:
         seata:
           tx-service-group: seata_tx_group
       nacos:
         discovery:
           server-addr: localhost:8848
     datasource:
       url: jdbc:mysql:///seata_order?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=GMT%2B8
       username: root
       password: leixuesong
   
   feign:
     hystrix:
       enabled: false
   
   logging:
     level:
       io:
         seata: info
   
   mybatis-plus:
     mapper-locations: classpath:mapper/*.xml
     type-aliases-package: com.demo.domain
   ```

   