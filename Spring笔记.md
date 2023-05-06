# Spring

## 简介

* Spring是一个开源免费的框架 , 容器
* Spring是一个轻量级的框架 , 非侵入式的
* 支持事务的处理，对框架整合的支持
* **Spring是一个轻量级的控制反转(IOC)和面向切面(AOP)的框架**

## 组成

* Spring 框架是一个分层架构，由 7 个定义良好的模块组成。Spring 模块构建在核心容器之上，核心容器定义了创建、配置和管理 bean 的方式
* 组成 Spring 框架的每个模块（或组件）都可以单独存在，或者与其他一个或多个模块联合实现。每个模块的功能如下：
  * **核心容器**：核心容器提供 Spring 框架的基本功能。核心容器的主要组件是 `BeanFactory`，它是工厂模式的实现。`BeanFactory` 使用*控制反转*（IOC） 模式将应用程序的配置和依赖性规范与实际的应用程序代码分开
  * **Spring 上下文**：Spring 上下文是一个配置文件，向 Spring 框架提供上下文信息。Spring 上下文包括企业服务，例如 JNDI、EJB、电子邮件、国际化、校验和调度功能
  * **Spring AOP**：通过配置管理特性，7程功能 , 集成到了 Spring 框架中。所以，可以很容易地使 Spring 框架管理任何支持 AOP的对象。Spring AOP 模块为基于 Spring 的应用程序中的对象提供了事务管理服务。通过使用 Spring AOP，不用依赖组件，就可以将声明性事务管理集成到应用程序中
  * **Spring DAO**：JDBC DAO 抽象层提供了有意义的异常层次结构，可用该结构来管理异常处理和不同数据库供应商抛出的错误消息。异常层次结构简化了错误处理，并且极大地降低了需要编写的异常代码数量（例如打开和关闭连接）。Spring DAO 的面向 JDBC 的异常遵从通用的 DAO 异常层次结构
  * **Spring ORM**：Spring 框架插入了若干个 ORM 框架，从而提供了 ORM 的对象关系工具，其中包括 JDO、Hibernate 和 iBatis SQL Map。所有这些都遵从 Spring 的通用事务和 DAO 异常层次结构
  * **Spring Web 模块**：Web 上下文模块建立在应用程序上下文模块之上，为基于 Web 的应用程序提供了上下文。所以，Spring 框架支持与 Jakarta Struts 的集成。Web 模块还简化了处理多部分请求以及将请求参数绑定到域对象的工作
  * **Spring MVC 框架**：MVC 框架是一个全功能的构建 Web 应用程序的 MVC 实现。通过策略接口，MVC 框架变成为高度可配置的，MVC 容纳了大量视图技术，其中包括 JSP、Velocity、Tiles、iText 和 POI

# IOC

* **控制反转IOC(Inversion of Control)，是一种设计思想，DI(依赖注入)是实现IOC的一种方法**，面向对象编程 的过程中, 对象的创建与对象间的依赖关系完全硬编码在程序中，而使用IOC控制对象，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方
* **IOC是Spring框架的核心内容**，使用多种方式完美的实现了IOC，可以使用XML配置，也可以使用注解，新版本的Spring也可以零配置实现IoC
* Spring容器在初始化时先读取配置文件，根据配置文件或元数据创建与组织对象存入容器中，程序使用时再从Ioc容器中取出需要的对象
* **控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IOC容器，其实现方法是依赖注入（Dependency Injection,DI)**
* **IOC** : 对象由Spring 来创建 , 管理 , 装配
* **底层原理**：XML解析、工厂模式、反射
* **主要接口**：
  * BeanFactory: IOC容器的基本实现，是Spring内部使用的接口，不提供开发人员进行使用
    * **加载配置文件的时候，不会创建对象，只有在使用对象的时候才会创建对象**
  * ApplicationContenxt: BeanFactory接口的子接口，提供更多更强大的功能，一般由开发人员进行使用
    * 加载配置文件的时候就会创建配置文件里面的对象
* IOC与DI的区别：
  * **DI是IOC中一种具体实现，表示依赖注入（注入属性），依赖注入是在创建对象的基础之上完成**

# XML配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        ">
    <context:annotation-config/>

</beans>
```

* bean的配置：bean就是java对象,由Spring创建和管理

  ```xml
  <bean id="" name="" class=""></bean>
      id 是bean的标识符,要唯一,如果没有配置id,name就是默认标识符
      如果配置id,又配置了name,那么name是别名
      name可以设置多个别名,可以用逗号,分号,空格隔开
      如果不配置id和name,可以根据applicationContext.getBean(.class)获取对象;
      class是bean的全限定名=包名+类名
  ```

* import：将多个配置文件，导入合并一个

  ```xml
  <import resource="路径"/>
  ```

# 依赖注入

## 构造器注入

* **constructor-arg：通过有参构造函数注入**

* 第一种根据index参数下标设置

  ```xml
  <bean id="自定义" class="全限定路径类名">
      <!-- index指构造方法参数顺序, 下标从0开始 -->
      <constructor-arg index="0" value="属性值"/>
  </bean>
  ```

* 第二种根据参数名字设置

  ```xml
  <bean id="自定义" class="全限定路径类名">
      <!-- name指参数名 -->
      <constructor-arg name="参数名" value="属性值"/>
  </bean>
  ```

* 第三种根据参数类型设置

  ```xml
  <bean id="自定义" class="全限定路径类名">
      <!--按着传值顺序赋值-->
      <constructor-arg type="类型" value="属性值"/>
  </bean>
  ```

## Set方式注入

* **property：通过setter对应的方法注入**

  ```xml
  <bean id="自定义" class="全限定路径类名">
      <property name="属性名" value="属性值"/>
  </bean>
  
  <bean id="address" class="com.demo.domain.Address">
          <property name="address" value="大竹"/>
  </bean>
  <bean id="student" class="com.demo.domain.Student">
      <!--第一种，普通值注入，value-->
      <property name="name" value="雷雪松"/>
      <!--第二种，Bean注入，ref-->
      <property name="address" ref="address"/>
      <!--第三种，数组注入，array-->
      <property name="books">
          <array>
              <value>红楼梦</value>
              <value>三国演义</value>
              <value>水浒传</value>
              <value>西游记</value>
          </array>
      </property>
      <!--第四种，List注入，list-->
      <property name="hobbys">
          <list>
              <value>听歌</value>
              <value>睡觉</value>
              <value>敲代码</value>
          </list>
      </property>
      <!--第五种，Map注入，map-->
      <property name="card">
          <map>
              <entry key="身份证" value="12345678910987654321"/>
              <entry key="电话号码" value="1314520"/>
          </map>
      </property>
      <!--第六种，Set注入，set-->
      <property name="games">
          <set>
              <value>LOL</value>
              <value>DNF</value>
          </set>
      </property>
      <!--第七种，null注入，null-->
      <property name="wife">
          <null/>
      </property>
      <!--第八种，Properties注入，props-->
      <property name="info">
          <props>
              <prop key="学号">1861020336</prop>
          </props>
      </property>
  </bean>
  
  
  ```

## 其他方式注入

* **p 命名空间注入** : 注入属性的值：property

* 需要在头文件中加入约束文件

  ```xml
  xmlns:p="http://www.springframework.org/schema/p"
  ```

* **c 命名空间注入 :** 通过构造器注入：constructor-arg

* 需要在头文件中加入约束文件

  ```xml
  xmlns:c="http://www.springframework.org/schema/c"
  ```

## 提取集合注入公共属性

* 导入名称空间

  ```xml
  xmlns:util="http://www.springframework.org/schema/util"
   xsi:schemaLocation="http://www.springframework.org/schema/util
          			 http://www.springframework.org/schema/util/spring-util.xsd">
  ```

* 提取公共部分

  ```xml
  <util:list[map、array] id="唯一标识">
      <value>值</value>
  </util:list>
  ```

## Bean管理

> Spring有两种类型bean，一种普通bean，另外一种工厂bean（FactoryBean）

* 普通bean：在配置文件中定义了bean类型就是返回类型
* 工厂bean：在配置文件定义bena类型可以和返回类型不一样
  1. 创建类，让这个类作为工厂，实现接口FactoryBean
  
  2. 实现接口里面的方法，在实现的方法中定义返回的bean类型
  
     ```java
     public class MyBean implements FactoryBean<User> {
     
         public User getObject() throws Exception {
             return new User("工厂bena", 18);
         }
     
         public Class<?> getObjectType() {
             return null;
         }
     
         public boolean isSingleton() {
             return false;
         }
     }
     ```

# Bean作用域

* Spring IOC容器创建一个Bean实例时，可以为Bean指定实例的作用域，作用域包括singleton（单例模式）、prototype（原型模式）、request（HTTP请求）、session（会话）、global-session（全局会话)
  * **singleton**：单例模式，在整个Spring IoC容器中，使用singleton定义的Bean将只有一个实例
  * **prototype**：原型模式，每次通过容器的getBean方法获取prototype定义的Bean时，都将产生一个新的Bean实例
  * **request**：对于每次HTTP请求，使用request定义的Bean都将产生一个新实例，即每次HTTP请求将会产生不同的Bean实例。只有在Web应用中使用Spring时，该作用域才有效
  * **session**：对于每次HTTP Session，使用session定义的Bean豆浆产生一个新实例。同样只有在Web应用中使用Spring时，该作用域才有效
  * **globalsession**：每个全局的HTTP Session，使用session定义的Bean都将产生一个新实例。典型情况下，仅在使用portlet context的时候有效。同样只有在Web应用中使用Spring时，该作用域才有效
* 作用域配置：**scope=""**

# Bean生命周期

> 从对象创建到对象销毁的过程

1. 通过构造器创建bean实例(无参构造)
2. 为bean的属性设置值和对其他bean引用(调用set方法)
4. 调用bean的初始化的方法(init)
5. bean对象获取到了
6. 当容器关闭时候，调用bean的销毁方法(destory)

# Bean自动装配

* 自动装配：根据指定装配规则(属性名称或属性类型)，Spring自动将匹配的属性值进行注入

* Spring会在上下文中自动寻找，并自动给Bean装配属性

* 实现方式：

  * 组件扫描(component scanning)：spring会自动发现应用上下文中所创建的bean；
  * 自动装配(autowiring)：spring自动满足bean之间的依赖，也就是我们说的IOC/DI；

* 有三种装配方式：

  * 在xml中显示的配置
  * 在Java中显示的配置
  * 隐式的bean发现机制和自动装配

* **byName**：按名称自动装配

  * 将查找其类中所有的set方法名，例如setCat，获得将set去掉并且首字母小写的字符串，即cat。
  * 去spring容器中寻找bean中id为此名称的对象。
  * 如果有，就取出注入；如果没有，就报空指针异常

  ```xml
  <bean id="自定义" class="实体类全限定名" autowire="byName">
  </bean>
  ```
  
* **byType**：按类型自定装配

  * 将查找其类中所有的属性类型
  * 去spring容器中寻找bean中class对应类型的对象
  * 如果有，就取出注入；如果没有，就报不唯一的异常

  ```xml
  <bean id="自定义" class="实体类全限定名" autowire="byType">
  </bean>
  ```

## 注解

* 导入约束：context

  ```xml
  xmlns:context="http://www.springframework.org/schema/context"
  
  http://www.springframework.org/schema/context
  http://www.springframework.org/schema/context/spring-context.xsd
  ```

* 开启注解支持

  ```
  <context:annotation-config/>
  ```

**注解的前提是自动装配的属性在IOC容器中存在，且符合类型byType**

### @Autowired

* @Autowired是按**类型**自动转配的，不支持id匹配
* @Autowired(required=false) ： false，对象可以为null；true，对象必须存对象，不能为null

#### @Qualifier(vlaue="id名")

* @Qualifier不能单独使用
* 在@Autowired下面加上@Qualifier则可以根据byName的方式自动装配

### @Resource(name="id名")

* 使用前先导包

  ```xml
  <dependency>
      <groupId>javax.annotation</groupId>
      <artifactId>javax.annotation-api</artifactId>
      <version>1.3.1</version>
  </dependency>
  ```

* @Resource如有指定的name属性，**先按该属性进行byName方式查找装配**；
* **其次再进行默认的byName方式进行装配**；
* 如果以上都不成功，则按byType的方式自动装配。
* 都不成功，则报异常

# 使用注解开发

* 导入约束：aop

  ```xml
  xmlns:aop="http://www.springframework.org/schema/aop"
  
  http://www.springframework.org/schema/aop
  http://www.springframework.org/schema/context/spring-aop.xsd
  ```

* 指定扫描的包

  ```xml
  <context:component-scan base-package="被扫描的包名路径"/>
  
  
  # 设置扫描那些注解
  <context:component-scan base-package="被扫描的包名路径" use-default-filters="false">
  	<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
  </context:component-scan>
  
  # 设置不扫描那些注解
  <context:component-scan base-package="被扫描的包名路径">
  	<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
  </context:component-scan>
  ```

## Bean的实现

* **@Component**
  * 默认是类名的小写
  * 可以添加自定义名

## 属性的实现

* **@Value("值")**
  * 可以添加在属性上，也可以添加在set方法上

## 衍生注解

* **@Controller：web层**
* **@Service：service层**
* **@Repository：dao层**

## 作用域

* **@Scope("")**
  * singleton：默认的，Spring会采用单例模式创建这个对象。关闭工厂 ，所有的对象都会销毁。
  * prototype：多例模式。关闭工厂 ，所有的对象不会销毁。内部的垃圾回收机制会回收

## 基于Java类进行配置

```java
@Configuration
public class MyConfig {

    @Bean
    public Dog dog(){
        return new Dog();
    }
}
```

```java
@Test
public void test1() {
    ApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
    Dog dog = context.getBean("dog", Dog.class);
    System.out.println(dog);
}
```

# 代理模式

## 静态代理

* 抽象角色 : 一般使用接口或者抽象类来实现
* 真实角色 : 被代理的角色
* 代理角色 : 代理真实角色 ; 代理真实角色后 , 一般会做一些附属的操作 .
* 客户 : 使用代理角色来进行一些操作 

* 好处：
  * 可以使得我们的真实角色更加纯粹 . 不再去关注一些公共的事情 .
  * 公共的业务由代理来完成 . 实现了业务的分工 ,
  * 公共业务发生扩展时变得更加集中和方便 .
* 缺点 :
  * 类多了 , 多了代理类 , 工作量变大了 . 开发效率降低

## 动态代理

* 动态代理的角色和静态代理的一样 .
* 动态代理的代理类是动态生成的 . 静态代理的代理类是我们提前写好的
* 动态代理分为两类 : 一类是基于接口动态代理 , 一类是基于类的动态代理
  - 基于接口的动态代理----JDK动态代理
    - 核心 : InvocationHandler 和 Proxy
  - 基于类的动态代理--**CGLIB**

```java
Object invoke(Object proxy, M method, Object[] args)；
//proxy - 调用该方法的代理实例 
//method -所述方法对应于调用代理实例上的接口方法的实例。 方法对象的声明类将是该方法声明的接口，它可以是代理类继承该方法的代理接口的超级接口。 
//args -包含的方法调用传递代理实例的参数值的对象的阵列，或null如果接口方法没有参数。 原始类型的参数包含在适当的原始包装器类的实例中，例如java.lang.Integer或java.lang.Boolean 。
```

```java
public class ProxyInvocationHandler implements InvocationHandler {
    //被代理的接口
    private Object object;

    public void setObject(Object object) {
        this.object = object;
    }

    //生成得到代理类
    public Object getProxy() {
        return Proxy.newProxyInstance(this.getClass().getClassLoader(), object.getClass().getInterfaces(), this);
    }

    //处理代理实例，并返回结果
    //动态代理的的本质，就是使用反射机制实现
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        Object result = method.invoke(object, args);
        return result;
    }
}
```

# AOP

* AOP（Aspect Oriented Programming）意为：面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率

## Aop在Spring中的作用

**提供声明式事务；允许用户自定义切面**

- 横切关注点：跨越应用程序多个模块的方法或功能。即是，与我们业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。如日志 , 安全 , 缓存 , 事务等等 ....

- 切面（ASPECT）：横切关注点 被模块化 的特殊对象。即，它是一个类。

- 通知（Advice）：切面必须要完成的工作。即它是类中的一个方法。

  - |   通知类型   |        连接点        |                    实现接口                    |
    | :----------: | :------------------: | :--------------------------------------------: |
    |   前置通知   |        方法前        |   org.springframework.aop.MethodBeforeAdvice   |
    |   后置通知   |        方法后        |  org.springframework.aop.AfterReturningAdvice  |
    |   环绕通知   |       方法前后       |   org.springframework.aop.MethodInterceptor    |
    | 异常抛出通知 |     方法抛出异常     |      org.springframework.aop.ThrowsAdvice      |
    |   引介通知   | 类中增加新的方法属性 | org.springframework.aop.IntrodutionInterceptor |

- 目标（Target）：被通知对象。

- 代理（Proxy）：向目标对象应用通知之后创建的对象。

- 切入点（PointCut）：实际被真正增强的方法，称为切入点。

- 连接点（JointPoint）：类里面那些方法可以被增强，这些方法称为连接点

## 切入点表达式

* 语法结构

  ```java
  execution([权限修饰符][返回类型][类全路径][方法名称]([参数列表]))
  ```

  * 对com.demo.dao.BookDao类里面的add方法进行增强

    ```java
    execution(* com.demo.dao.BookDao.add(..))
    ```

  * 对com.demo.dao.BookDao类里面的所有方法进行增强

    ```java
    execution(* com.demo.dao.BookDao.*(..))
    ```

  * 对com.demo.dao包里面所有类，类里面所有方法进行增强

    ```java
    execution(* com.demo.dao.*.*(..))
    ```

## 导入依赖

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

# 整合MyBatis

## 导入依赖

```xml
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.10</version>
</dependency>
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.10</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.4</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.6</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.4.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.4.RELEASE</version>
</dependency>
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.5</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>2.0.4</version>
</dependency>
```

 ## 核心配置编写

* 要和 Spring 一起使用 MyBatis，需要在 Spring 应用上下文中定义至少两样东西：一个 `SqlSessionFactory` 和至少一个数据映射器类

* 在基础的 MyBatis 用法中，是通过 `SqlSessionFactoryBuilder` 来创建 `SqlSessionFactory` 的。 而在 MyBatis-Spring 中，则使用 `SqlSessionFactoryBean` 来创建

  ```xml
  <!--sqlSessionFactory-->
  <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
      <!--获取数据源-->
      <property name="dataSource" ref="datasource"/>
      <!--绑定MyBatis配置文件-->
      <property name="configLocations" value="classpath:MyBatis配置文件名称"/>
      <!--绑定MyBatis映射文件-->
      <property name="mapperLocations" value="classpath:映射文件的全限定名"/>
      <!--别名-->
      <property name="typeAliasesPackage" value="实体类包名"/>
  </bean>
  ```

* `SqlSessionFactory` 需要一个 `DataSource`（数据源）

  ```xml
  <!--DataSource：使用Spring的数据源替换MyBatis的配置-->
  <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
      <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
      <property name="url" value="jdbc:mysql:///数据库名"/>
      <property name="username" value="root"/>
      <property name="password" value="root"/>
  </bean>
  ```

* `SqlSessionTemplate` 是 MyBatis-Spring 的核心。作为 `SqlSession` 的一个实现

  ```xml
  <!--sqlSessionTemplate：就是我们使用得sqlSession-->
  <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
      <!--只能使用构造器注入sqlSessionFactory，因为它没有set方法-->
      <constructor-arg index="0" ref="sqlSessionFactory"/>
  </bean>
  ```

* 创建一个接口实现类

  ```java
  public class UserMapperImpl implements UserMapper {
      private SqlSessionTemplate sqlSession;
  
      public void setSqlSession(SqlSessionTemplate sqlSession) {
          this.sqlSession = sqlSession;
      }
  
      @Override
      public List<User> selectUser() {
          UserMapper mapper = sqlSession.getMapper(UserMapper.class);
          return mapper.selectUser();
      }
  }
  ```

* Bean注入到IOC容器种

  ```xml
  <bean id="userMapper" class="com.demo.mapper.Impl.UserMapperImpl">
      <property name="sqlSession" ref="sqlSession"/>
  </bean>
  ```

# 声明式事务

* **事务ACID原则**
  * 原子性
    * 事务是原子性操作，由一系列动作组成，事务的原子性确保动作要么全部完成，要么完全不起作用
  * 一致性
    * 一旦所有事务动作完成，事务就要被提交。数据和资源处于一种满足业务规则的一致性状态中
  * 隔离性
    * 可能多个事务会同时处理相同的数据，因此每个事务都应该与其他事务隔离开来，防止数据损坏
  * 持久性
    * 事务一旦完成，无论系统发生什么错误，结果都不会受到影响。通常情况下，事务的结果被写到持久化存储器中

## 事务管理

* **编程式事务管理**
  * 将事务管理代码嵌到业务方法中来控制事务的提交和回滚
  * 缺点：必须在每个事务操作业务逻辑中包含额外的事务管理代码
* **声明式事务管理**
  * 一般情况下比编程式事务好用。
  * 将事务管理代码从业务方法中分离出来，以声明的方式来实现事务管理。
  * 将事务管理作为横切关注点，通过aop方法模块化。Spring中通过Spring AOP框架支持声明式事务管理。

---

**步骤**

* 导入约束

  ```xml
  xmlns:tx="http://www.springframework.org/schema/tx"
  
  http://www.springframework.org/schema/tx
  http://www.springframework.org/schema/tx/spring-tx.xsd">
  ```

* 配置

  ```xml
   <!--配置声明式事务-->
      <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
          <property name="dataSource" ref="dataSource" />
      </bean>
   <!--开启事务注解-->
      <tx:annotation-driven transaction-manager="transactionManager"/>
  ```

* 在业务层添加**@Transactional**注解管理事务

## @Transactional参数

* propagation(事务传播行为)：多事务方法之间调用，这个过程中事务是如何进行管理的

  * 事务方法：对数据库表数据进行变化的操作

    |   传播属性    |                             描述                             |
    | :-----------: | :----------------------------------------------------------: |
    |   REQUIRED    | 如果有事务在运行，当前的方法就在这个事务内运行，否则,就启动一个新的事务，并在自己的事务内运行 |
    | REQUIRED_NEW  | 当前的方法必须启动新事务，并在它自己的事务内运行.如果有事务正在运行，应该将它挂起 |
    |   SUPPORTS    | 如果有事务在运行，当前的方法就在这个事务内运行.否则它可以不运行在事务中. |
    | NOT_SUPPORTED |   当前的方法不应该运行在事务中，如果有运行的事务，将它挂起   |
    |   MANDATORY   | 当前的方法必须运行在事务内部，如果没有正在运行的事务，就抛出异常 |
    |     NEVER     |  当前的方法不应该运行在事务中.如果有运行的事务，就抛出异常   |
    |    NESTED     | 如果有事务在运行，当前的方法就应该在这个事务的嵌套事务内运行，否则，就启动一个新的事务，并在它自己的事务内运行. |

* isolation(事务隔离级别)：多事务之间操作不会产生影响，如果在并发情况下不考虑事务隔离性就会产生很多问题

  |                            | 脏读 | 不可重复读 | 幻读 |
  | -------------------------- | :--: | :--------: | :--: |
  | READ UNCOMMITTED(读未提交) |  有  |     有     |  有  |
  | READ COMMITTED(读已提交)   |  无  |     有     |  有  |
  | REPEATABLE READ(可重复读)  |  无  |     无     |  有  |
  | SERIALIZABLE(串行化)       |  无  |     无     |  无  |

  * 脏读：一个未提交事务读取到，另一个未提交事务的数据
  * 不可重复读：一个未提交事务，读取到另一个提交事务修改的数据
  * 幻(虚)读：一个未提交事务，读取到另一个事务添加的数据

* timeout(超时时间)：事务在一定时间内提交，不然就回滚。秒为单位

* readOnly(是否只读)：默认false,设置为true只能进行查询操作

* rollbackFor(回滚)：设置出现那些异常进行事务的回滚

* noRollbackFor(不回滚)：设置出现那些异常不进行事务回滚

# WebFlux

## 介绍

* Spring5中添加的新模块，用于web开发,功能和SpringMVC类似，Webflux使用响应式编程

* Webflux是一种异步非阻塞的框架，核心是基于Reactor的相关API实现

* 异步非阻塞

  * **异步和同步针对调用者**：调用者发送请求，如果等着对方回应之后才去做其他事情就是**同步**；如果发送请求之后不等着对方回应就去做其他事情就是**异步**
  * **阻塞和非阻塞针对被调用者**：被调用者收到请求之后，昨晚请求任务之后才给出反馈就是阻塞；收到请求之后马上给出反馈然后再去做事情就是非阻塞

* Webflux特点：

  1. 非阻塞：在有限的资源下，提高系统吞吐量和伸缩性，以Reactor为基础实现响应式编程
  2. 函数式编程

* 与SpringMVC比较

  ![](.\img\70.jpg)

  * 两个框架都可以使用注解方式，都运行在Tomet等容器中
  * SpringMVC采用命令式编程，Webflux采用异步响应式编程

## 响应式编程

* 响应式编程是一种面向数据流和变化传播的编程范式。这意味着可以在编程语言中很方便地表达静态或动态的数据流，而相关的计算模型会自动将变化的值通过数据流进行传播
* 电子表格程序就是响应式编程的一个例子。单元格可以包含字面值或类似"=B1+C1"的公式，而包含公式的单元格的值会依据其他单元格的值的变化而变化

### 基于Reactor实现

* 响应式编程操作中，Reactor是满足Reactive规范框架

* Reactor有两个核心类，**Mono**和**Flux**，这两个类实现接口Publisher，提供丰富操作符。**Flux对象实现发布者，返回N个元素；Mono实现发布者，返回0个或1个元素**

* Flux和Mono都是数据流的发布者，使用Flux和Mono都可以发出三种数据信号：**元素值，错误信号，完成信号**，错误信号和完成信号都代表终止信号，终止信号用于告诉订阅者数据流结束了，错误信号终止数据流同时把错误信息传递给订阅者

* 三种信号特点：

  1. 错误信号和完成信号都是终止信号，不能共存的

  2. 如果没有发送任何元素值，而是直接发送错误或者完成信号，表示是空数据流

  3. 如果没有错误信号，没有完成信号，表示是无限数据流

* **调用just或者其他方法只是声明数据流，数据流并没有发出，只有进行订阅之后才会触发数据流，不订阅什么都不会发生的**

* 操作符：

  * **map**：元素映射为新的元素

    ![](.\img\71.jpg)

  * **flatMap**：元素映射为流；把每个元素转换流，把转换之后多个流合并大的流

    ![](.\img\72 .jpg)

## SpringWebFlux执行流程和核心API

> SpringWebFlux基于Reactor，默认使用容器是Netty，Netty是高性能NIO框架，异步非阻塞的框架

* SpringWebFlux执行过程和SpringMVC相似

  * SpringWebFlux核心控制器**DispatcherHandler**，实现接口WebHandler

    ```java
    public interface WebHandler {
        Mono<Void> handle(ServerWebExchange var1);
    }
    ```

    ```java
    public Mono<Void> handle(ServerWebExchange exchange) { //http请求响应信息
        if (this.handlerMappings == null) {
            return this.createNotFoundError();
        } else {
            return CorsUtils.isPreFlightRequest(exchange.getRequest()) ? this.handlePreFlight(exchange) : Flux.fromIterable(this.handlerMappings).concatMap((mapping) -> {
                return mapping.getHandler(exchange);  //根据请求地址获取对应mapping
            }).next().switchIfEmpty(this.createNotFoundError()).flatMap((handler) -> {
                return this.invokeHandler(exchange, handler);  //调用具体的业务方法
            }).flatMap((result) -> {
                return this.handleResult(exchange, result);   //处理结果返回
            });
        }
    }
    ```

  * **DispatcherHandler**，负责请求的处理
  * **HandlerMapping**：请求查询到处理的方法
  * **HandlerAdapter**：真正负责请求处理
  * **HandlerResultHandler**：响应结果处理

* SpringWebflux实现函数式编程，两个接口：**RouterFunction（路由处理）**和**HandlerFunction（处理函数）**

* SpringWebflux请 求 和 响 应 不 再 是ServletRequest和ServletResponse，而是**ServerRequest**和**ServerResponse**
