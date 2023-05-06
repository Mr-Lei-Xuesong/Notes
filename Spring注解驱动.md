# 组件注册

* **@Configuration**：用于标注配置类
* **@Bean**：给容器中注册一个Bean;类型为返回值的类型，id默认是用方法名作为id
* **@ComponentScan**：
  * value：指定要扫描的包
  * includeFilters = Filter[]：指定扫描的时候只需要包含那些组件
  * excludeFilters = Filter[]：指定扫描的时候按照什么规则排除那些组件
    * FilterType.ANNOTATION：按照注解
    * FilterType.ASSIGNABLE_TYPE：按照给定的类型
    * FilterType.ASPECTJ：使用ASPECTJ表达式
    * FilterType.REGEX：使用正则指定
    * FilterType.CUSTOM：使用自定义规则，**需要自己创建类实现TypeFilter类**
  * useDefaultFilters：默认为true,加载全部
* **@Scope**：调整作用域
  * prototype：多实例的 	**IOC容器启动并不会去调用方法创建对象放在容器中，每次获取的时候才会调用方法创建对象**
  * singleton：单实例的（默认）**IOC容器启动会调用方法创建对象放到IOC容器中，以后每次拿都是从容器中拿**
  * request：同一次请求创建一个实例
  * session：同一个session创建一个实例
* **@Lazy**：懒加载
  * 单实例Bean：默认在容器启动的时候创建对象
  * **懒加载：容器启动不会创建对象。第一次使用(获取)Bean创建对象，并初始化**
* **@Conditional**：按照一定的条件进行判断，满足条件给容器中注册Bean，**传入Condition数组，使用时需要自己创建类实现Condition，重写match方法**
* **@Import**：快速给容器中导入一个组件
  * Import(类名),容器中就会自动注册这个组件，id默认是组件的全名
  * ImportSelector：返回需要导入的组件的全类名的数组，**需要自己创建类实现ImportSelector,重写selectImports方法**
  * ImportBeanDefinitionRegistrar：自定义手动注册Bean，**需要自己创建类实现ImportBeanDefinitionRegistrar，重写registerBeanDefinitions方法**
* FactoryBean:工厂Bean,交给spring用来生产Bean到spring容器中.**需要自己创建类实现FactoryBean，重写getObject、getObjectType、isSingleton方法，可以通过前缀&来获取工厂Bean本身**

# 生命周期

* 创建------》初始化------》销毁
* 指定初始化和销毁
  * 通过@Bean指定**init-method**和**destroy-method**
* 通过让Bean实现**InitializingBean(定义初始化逻辑)**和**DisposableBean(定义销毁逻辑)**
* 使用JSR250:
  * **@PostConstruct**：在Bean创建完成并且属性赋值完成；来执行初始化
  * **@PreDestory**：在Bean从容器中移除之前调用销毁工作
* **BeanPostProcessor：Bean的后置处理器；在Bean初始化前后进行一些处理工作**
  * postProcessBeforeInitialization：在初始化之前工作
  * postProcessAfterInitialization：在初始化之后工作

# 自动装配

* **@Autowired**：自动注入
  * 默认优先按照类型去容器中找对应的组件
  * 如果找到多个相同类型的组件，再将属性的名称作为组件的id去容器中查找
  * **@Qualifier()**：指定需要装配组件的id，而不是属性名
  * 自动装配默认一定要将属性值赋值好，否则会报错；可以使用**@Autowired(required=false)**；
  * **@Primary**：让容器进行自动装配的时候，默认使用首选Bean
* @Resource：可以和@Autowired一样实现自动装配功能，默认是按照组件名进行装配的
* @Inject：需要导入javax.inject的包，和@Autowired一样的功能

# AOP原理

* **@EnableAspectJAutoProxy**开启AOP功能

  ```
  @Import({AspectJAutoProxyRegistrar.class})给容器中导入AspectJAutoProxyRegistrar
  利用AspectJAutoProxyRegistrar自定义给容器中注册Bean
  internalAutoProxyCreator=AnnotationAwareAspectJAutoProxyCreator
  给容器中注册一个AnnotationAwareAspectJAutoProxyCreator
  ```

* **AnnotationAwareAspectJAutoProxyCreator**后置处理器

* 容器的创建流程

  * registerBeanPostProcessors()注册后置处理器；创建AnnotationAwareAspectJAutoProxyCreator对象
  * finishBeanFactoryInitialization()初始化剩下的单实例Bean
    * 创建业务逻辑组件和切面组件
    * AnnotationAwareAspectJAutoProxyCreator拦截组件的创建过程
    * 组件创建之后，判断组件是否需要增强
      * 是：切面的通知方法，包装成增强器(Advisor);给业务逻辑组件创建一个代理对象
    * 执行目标方法：
      * 代理对象执行目标方法
      * CglibAopProxy.intercept()
        * 得到目标方法的拦截器链(增强器包装成拦截器MethodIntercepter)
        * 利用拦截器的链式机制，依次进入每一个拦截器进行执行
        * 效果：
          * 正常执行：前置通知-->目标方法-->后置通知-->返回通知
          * 出现异常：前置通知-->目标方法-->后置通知-->异常通知

# 声明式事务

* **@EnableTransactionManagement**：开启基于注解的事务管理功能

* 在需要的方法上加**@Transactional**

* 配置事务管理器来管理事务

  ```java
  @Bean
  public PlatformTransactionManager transactionManager() throws Exception {
  	return new DataSourceTransactionManager(dataSource());
  }
  ```

  