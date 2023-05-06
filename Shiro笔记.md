# 权限管理

## 什么是权限管理

​		基本上涉及到用户参与的系统都要进行权限管理，权限管理属于系统安全的范畴，权限管理实现**对用户访问系统的控制**，按照安全规则或者**安全策略**控制用户可以访问而且只能访问自己被授权的资源。

​		权限管理包括用户**身份认证**和**授权**两部分，简称**认证授权**。对于需要访问控制的资源用户首先经过身份认证，认证通过后用户具有该资源的访问权限方可访问。

## 什么是身份认证

​		**身份认证**，就是判断一个用户是否为合法用户的处理过程。最常用的简单身份认证方式是系统通过核对用户输入的用户名和口令，看其是否与系统中存储的该用户的用户名和口令一致，来判断用户身份是否正确。对于采用指纹等系统，则出示指纹；对于硬件Key等刷卡系统，则需要刷卡。

## 什么是授权

授权，即访问控制，控制谁能访问哪些资源。主体进行身份认证后需要分配权限方可访问系统的资源，对于某些资源没有权限是无法访问的

---

# Shiro简介

![](.\img\55.jpg)

- **Authentication**：身份认证 / 登录，验证用户是不是拥有相应的身份；
- **Authorization**：授权，即权限验证，验证某个已认证的用户是否拥有某个权限；即判断用户是否能做事情，常见的如：验证某个用户是否拥有某个角色。或者细粒度的验证某个用户对某个资源是否具有某个权限；
- **Session** **Management**：会话管理，即用户登录后就是一次会话，在没有退出之前，它的所有信息都在会话中；会话可以是普通 JavaSE 环境的，也可以是如 Web 环境的；
- **Cryptography**：加密，保护数据的安全性，如密码加密存储到数据库，而不是明文存储；
- **Web Support**：Web 支持，可以非常容易的集成到 Web 环境；
- **Caching**：缓存，比如用户登录后，其用户信息、拥有的角色 / 权限不必每次去查，这样可以提高效率；
- **Concurrency**：shiro 支持多线程应用的并发验证，即如在一个线程中开启另一个线程，能把权限自动传播过去；
- **Testing**：提供测试支持；
- **Run As**：允许一个用户假装为另一个用户（如果他们允许）的身份进行访问；
- **Remember Me**：记住我，这个是非常常见的功能，即一次登录后，下次再来的话不用登录了

---

# Shrio架构

## 外部

![](.\img\56.jpg)

* **Subject**：主体，代表了当前 “用户”，这个用户不一定是一个具体的人，与当前应用交互的任何东西都是 Subject，如网络爬虫，机器人等；**即一个抽象概念**；所有 Subject 都绑定到 SecurityManager，与 Subject 的所有交互都会委托给 SecurityManager；可以把 Subject 认为是一个门面；SecurityManager 才是实际的执行者；
* **SecurityManager**：安全管理器；即所有与安全有关的操作都会与 SecurityManager 交互；且它管理着所有 Subject；可以看出它是 Shiro 的核心，它负责与后边介绍的其他组件进行交互，如果学习过 SpringMVC，你可以把它看成 DispatcherServlet 前端控制器；
* **Realm**：域，Shiro 从从 Realm 获取安全数据（如用户、角色、权限），就是说 SecurityManager 要验证用户身份，那么它需要从 Realm 获取相应的用户进行比较以确定用户身份是否合法；也需要从 Realm 得到用户相应的角色 / 权限进行验证用户是否能进行操作；可以把 Realm 看成 DataSource，即安全数据源

## 内部

![](.\img\57.jpg)

- **Subject**：主体，可以看到主体可以是任何可以与应用交互的 “用户”；
- **SecurityManager**：相当于 SpringMVC 中的 DispatcherServlet 或者 Struts2 中的 FilterDispatcher；是 Shiro 的心脏；所有具体的交互都通过 SecurityManager 进行控制；它管理着所有 Subject、且负责进行认证和授权、及会话、缓存的管理。
- **Authenticator**：认证器，负责主体认证的，这是一个扩展点，如果用户觉得 Shiro 默认的不好，可以自定义实现；其需要认证策略（Authentication Strategy），即什么情况下算用户认证通过了；
- **Authrizer**：授权器，或者访问控制器，用来决定主体是否有权限进行相应的操作；即控制着用户能访问应用中的哪些功能；
- **Realm**：可以有 1 个或多个 Realm，可以认为是安全实体数据源，即用于获取安全实体的；可以是 JDBC 实现，也可以是 LDAP 实现，或者内存实现等等；由用户提供；注意：Shiro 不知道你的用户 / 权限存储在哪及以何种格式存储；所以我们一般在应用中都需要实现自己的 Realm；
- **SessionManager**：如果写过 Servlet 就应该知道 Session 的概念，Session 呢需要有人去管理它的生命周期，这个组件就是 SessionManager；而 Shiro 并不仅仅可以用在 Web 环境，也可以用在如普通的 JavaSE 环境、EJB 等环境；所以呢，Shiro 就抽象了一个自己的 Session 来管理主体与应用之间交互的数据；这样的话，比如我们在 Web 环境用，刚开始是一台 Web 服务器；接着又上了台 EJB 服务器；这时想把两台服务器的会话数据放到一个地方，这个时候就可以实现自己的分布式会话（如把数据放到 Memcached 服务器）；
- **SessionDAO**：DAO 大家都用过，数据访问对象，用于会话的 CRUD，比如我们想把 Session 保存到数据库，那么可以实现自己的 SessionDAO，通过如 JDBC 写到数据库；比如想把 Session 放到 Memcached 中，可以实现自己的 Memcached SessionDAO；另外 SessionDAO 中可以使用 Cache 进行缓存，以提高性能；
- **CacheManager**：缓存控制器，来管理如用户、角色、权限等的缓存的；因为这些数据基本上很少去改变，放到缓存中后可以提高访问的性能
- **Cryptography**：密码模块，Shiro 提供了一些常见的加密组件用于如密码加密 / 解密的。

---

# Shiro中的认证

* 认证

* 身份认证，就是判断一个用户是否为合法用户的处理过程。最常用的简单身份认证方式是系统通过核对用户输入的用户名和口令，看其是否与系统中存储的该用户的用户名和口令一致，来判断用户身份是否正确

* shiro中认证的关键对象

  * Subject：主体
    * 访问系统的用户，主体可以是用户、程序等，进行认证的都称为主体
  * Principal：身份信息
    * 是主体（subject）进行身份认证的标识，标识必须具有**唯一性**，如用户名、手机号、邮箱地址等，一个主体可以有多个身份，但是必须有一个主身份（Primary Principal）
  * credential：凭证信息
    * 是只有主体自己知道的安全信息，如密码、证书等

* 认证流程

  ![](.\img\58.jpg)

# 认证的开发

* 引入依赖

  ```xml
  <dependency>
      <groupId>org.apache.shiro</groupId>
      <artifactId>shiro-core</artifactId>
      <version>1.5.3</version>
  </dependency>
  ```

* 引入shiro配置文件

  * 配置文件：名称随意，以 ==.ini== 结尾，放在 resources 目录下

* 源码

  ```java
  public class Authenticator {
      public static void main(String[] args) {
          //1.创建SecurityManager对象
          DefaultSecurityManager manager = new DefaultSecurityManager();
  
          //2.给安全管理器设置Realm
          manager.setRealm(new IniRealm("classpath:shiro.ini"));
  
          //3.SecurityUtils 全局安全工具类
          SecurityUtils.setSecurityManager(manager);
  
          //4.关键对象 Subject对象
          Subject subject = SecurityUtils.getSubject();
  
          //5.创建Token令牌
          UsernamePasswordToken token = new UsernamePasswordToken("lei","123456");
          //用户认证
          try {
              System.out.println("认证状态："+subject.isAuthenticated());
              subject.login(token);
              System.out.println("认证状态："+subject.isAuthenticated());
          } catch (UnknownAccountException e) {
              e.printStackTrace();
              System.out.println("认证失败：用户名不正确");
          }catch (IncorrectCredentialsException e) {
              e.printStackTrace();
              System.out.println("认证失败：密码不正确");
          }
      }
  }
  ```

* 认证流程

  > **最终执行用户名比较是 在==SimpleAccountRealm==类 的 ==doGetAuthenticationInfo== 方法中完成用户名校验**
  >
  > **最终密码校验是在 ==AuthenticatingRealm==类 的 ==assertCredentialsMatch==方法 中**

  * 总结：

  > AuthenticatingRealm 认证realm **doGetAuthenticationInf**
  >
  > AuthorizingRealm 授权realm **doGetAuthorizationInfo**

* 自定义Realm

  ```java
  public class CustomerRealm extends AuthorizingRealm {
      //授权
      @Override
      protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
          return null;
      }
  
      //认证
      @Override
      protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
          //在Token中获取用户名
          String principal = (String) token.getPrincipal();
          System.out.println("用户名："+principal);
          //根据身份信息使用jdbc或mybatis查询相关数据库
          if ("lei".equals(principal)){
              //参数1：返回数据库中正确的用户名  参数2：返回数据库中正确密码   参数3：提供当前realm的名字  this.getName();
              return new SimpleAuthenticationInfo(principal,"123456",this.getName());
          }
          return null;
      }
  }
  ```

* 使用自定义Realm认证

  ```java
  public class TestCustomerRealmAuthenticator {
      public static void main(String[] args) {
          //创建SecurityManager
          DefaultSecurityManager manager = new DefaultSecurityManager();
          //设置自定义Realm
          manager.setRealm(new CustomerRealm());
          //使用安全工具类设置安全管理器
          SecurityUtils.setSecurityManager(manager);
          //通过安全工具类获取Subject
          Subject subject = SecurityUtils.getSubject();
          //创建Token
          UsernamePasswordToken token = new UsernamePasswordToken("lei","123456");
          //用户认证
          try {
              subject.login(token);
          } catch (UnknownAccountException e) {
              e.printStackTrace();
              System.out.println("认证失败：用户名不正确");
          }catch (IncorrectCredentialsException e) {
              e.printStackTrace();
              System.out.println("认证失败：密码不正确");
          }
      }
  }
  ```

# MD5算法

* 作用：一般用来加密或者签名（校验和）
* 特点：MD5算法不可逆如何内容相同无论执行多少次md5生成结果始终是一致
* 网络上提供的MD5在线解密一般是用穷举的方法
* 生成结果：始终是一个16进制32位长度字符串

## MD5的基本使用

```java
public class TestShiroMD5 {
    public static void main(String[] args) {
        //使用MD5
        Md5Hash md5Hash = new Md5Hash("123456");
        System.out.println("MD5："+md5Hash.toHex());

        //使用MD5+Salt处理
        Md5Hash md5Hash1 = new Md5Hash("123456","lei");
        System.out.println("MD5+Salt："+md5Hash1.toHex());

        //使用MD5+Salt+Hash散列处理
        Md5Hash md5Hash2 = new Md5Hash("123456","lei",1024);
        System.out.println("MD5+Salt+Hash："+md5Hash2.toHex());
    }
}
```

## 自定义的Realm，加入md5+salt+hash

```java
public class CustomerMd5Realm extends AuthorizingRealm {
    //授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        return null;
    }

    //认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        //获取用户名
        String principal = (String) token.getPrincipal();
        //随机盐
        String salt = "lei";
        if ("lei".equals(principal)) {
            return new SimpleAuthenticationInfo(principal,//参数1：数据库用户名
                    "3c9439e4806572d541f089f6dd00eb7b", //参数2：数据库MD5+salt+哈希散列 加密之后的密码
                    ByteSource.Util.bytes(salt),//参数3：注册时的随机盐
                    this.getName());//参数4：realm的名字
        }
        return null;
    }
}
```

## 使用MD5+Salt+哈希散列认证

```java
public class TestCustomerMd5RealmAuthenticator {
    public static void main(String[] args) {
        //注入Realm
        CustomerMd5Realm realm = new CustomerMd5Realm();
        //设置Realm使用Hash凭证器
        HashedCredentialsMatcher credentialsMatcher = new HashedCredentialsMatcher();
        //使用的算法  MD5
        credentialsMatcher.setHashAlgorithmName("md5");
        //散列的次数
        credentialsMatcher.setHashIterations(1024);
        //设置realm的认证方式
        realm.setCredentialsMatcher(credentialsMatcher);
        //使用工具类获取subject
        Subject subject = MySecurityManager.getSecurityManager(realm);
        //创建Token令牌
        UsernamePasswordToken token = new UsernamePasswordToken("lei", "123456");
        try {
            subject.login(token);
            System.out.println("认证成功");
        } catch (UnknownAccountException e) {
            e.printStackTrace();
            System.out.println("用户名错误");
        } catch (IncorrectCredentialsException e) {
            e.printStackTrace();
            System.out.println("密码错误");
        }
    }
}
```

# Shiro中的授权

## 授权

> 授权，即访问控制，控制谁能访问哪些资源。主体进行身份认证后需要分配权限方可访问系统的资源，对于某些资源没有权限是无法访问的。

## 关键对象

* **授权可简单理解为who对what(which)进行How操作：**
  * **Who，即主体（Subject）**，主体需要访问系统中的资源。
  * **What，即资源（Resource)**，如系统菜单、页面、按钮、类方法、系统商品信息等。资源包括**资源类型**和**资源实例**，比如商品信息为资源类型，类型为t01的商品为资源实例，编号为001的商品信息也属于资源实例。
  * **How，权限/许可（Permission)**，规定了主体对资源的操作许可，权限离开资源没有意义，如用户查询权限、用户添加权限、某个类方法的调用权限、编号为001用户的修改权限等，通过权限可知主体对哪些资源都有哪些操作许可

## 授权流程

![](.\img\60.jpg)

## 授权方式

* 基于角色的访问控制

  * RBAC基于角色的访问控制（Role-Based Access Control）是以角色为中心进行访问控制

  * ```java
    if(subject.hasRole("admin")){
       //操作什么资源
    }
    ```

* 基于资源的访问控制

  * RBAC基于资源的访问控制（Resource-Based Access Control）是以资源为中心进行访问控制

  * ```java
    if(subject.isPermission("user:update:01")){ //资源实例
      //对资源01用户具有修改的权限
    }
    if(subject.isPermission("user:update:*")){  //资源类型
      //对 所有的资源 用户具有更新的权限
    }
    ```

## 权限字符串

> 权限字符串的规则是：**资源标识符：操作：资源实例标识符**，意思是对哪个资源的哪个实例具有什么操作，“:”是资源/操作/实例的分割符，权限字符串也可以使用*通配符。

* 用户创建权限：user:create，或user:create:*
* 用户修改实例001的权限：user:update:001
* 用户实例001的所有权限：user:*：001

## shiro中授权编程实现方式

* 编程式

  ```java
  Subject subject = SecurityUtils.getSubject();
  if(subject.hasRole(“admin”)) {
  	//有权限
  } else {
  	//无权限
  }
  ```

* 注解式

  * **@RequiresRoles 用来基于角色进行授权**
  * **@RequiresPermissions 用来基于权限进行授权**

  ```java
  @RequiresRoles("admin")
  public void hello() {
  	//有权限
  }
  ```

* 标签式

  ```java
  JSP/GSP 标签：在JSP/GSP 页面通过相应的标签完成：
  <shiro:hasRole name="admin">
  	<!— 有权限—>
  </shiro:hasRole>
  注意: Thymeleaf 中使用shiro需要额外集成!
  ```

## 授权实现

```java
public class CustomerMd5Realm extends AuthorizingRealm {
    //授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        String principal = (String) principals.getPrimaryPrincipal();
        System.out.println("身份信息：" + principal);

        //根据身份信息(用户名) 获取当前用户的角色信息，以及权限信息
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        //将数据库中查询角色信息赋值给权限信息
        ArrayList<String> list = new ArrayList<>();
        list.add("admin");
        list.add("user");
        info.addRoles(list);

        //将数据库中查询权限信息赋值给权限对象
        info.addStringPermission("user:*:01");
        info.addStringPermission("product:*");
        return info;
    }

    //认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        //获取用户名
        String principal = (String) token.getPrincipal();
        //随机盐
        String salt = "lei";
        if ("lei".equals(principal)) {
            return new SimpleAuthenticationInfo(principal,//参数1：数据库用户名
                    "3c9439e4806572d541f089f6dd00eb7b", //参数2：数据库MD5+salt+哈希散列 加密之后的密码
                    ByteSource.Util.bytes(salt),//参数3：注册时的随机盐
                    this.getName());//参数4：realm的名字
        }
        return null;
    }
}
```

```java
public class TestCustomerMd5RealmAuthenticator {
    public static void main(String[] args) {
        //注入Realm
        CustomerMd5Realm realm = new CustomerMd5Realm();
        //设置Realm使用Hash凭证器
        HashedCredentialsMatcher credentialsMatcher = new HashedCredentialsMatcher();
        //使用的算法  MD5
        credentialsMatcher.setHashAlgorithmName("md5");
        //散列的次数
        credentialsMatcher.setHashIterations(1024);
        //设置realm的认证方式
        realm.setCredentialsMatcher(credentialsMatcher);
        //使用工具类获取subject
        Subject subject = MySecurityManager.getSecurityManager(realm);
        //创建Token令牌
        UsernamePasswordToken token = new UsernamePasswordToken("lei", "123456");
        try {
            subject.login(token);
            if (subject.isAuthenticated()) {
                System.out.println("认证成功");
                //单角色权限控制
                System.out.println(subject.hasRole("admin"));//true

                System.out.println("==========");

                //是否具有其中一个
                boolean[] booleans = subject.hasRoles(Arrays.asList("admin", "user", "super"));
                for (boolean aBoolean : booleans) {
                    System.out.println(aBoolean);//true  true false
                }

                System.out.println("==========");
                //多角色权限控制
                System.out.println(subject.hasAllRoles(Arrays.asList("admin", "user")));//true

                System.out.println("==========");

                //基于权限字符串的访问控制  资源标识符:操作:资源类型
                System.out.println("权限:"+subject.isPermitted("user:update:01"));//true
                System.out.println("权限:"+subject.isPermitted("product:update:02"));//true

                System.out.println("==========");

                //分别具有那些权限
                boolean[] permitted = subject.isPermitted("user:*:01", "order:*:10");
                for (boolean b : permitted) {
                    System.out.println("权限："+b);//true  false
                }

                System.out.println("==========");
                //同时具有那些权限
                boolean permittedAll = subject.isPermittedAll("user:*:01", "product:update");//true
                System.out.println(permittedAll);
            }
        } catch (UnknownAccountException e) {
            e.printStackTrace();
            System.out.println("用户名错误");
        } catch (IncorrectCredentialsException e) {
            e.printStackTrace();
            System.out.println("密码错误");
        }
    }
}
```

# SpringBoot整合Shiro+JWT

>整合思路

![](.\img\61.jpg)

## 引入依赖

```xml
<!--引入shiro整合Springboot依赖-->
<dependency>
  <groupId>org.apache.shiro</groupId>
  <artifactId>shiro-spring-boot-starter</artifactId>
  <version>1.5.3</version>
</dependency>
<!--引入jwt依赖-->
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.4.0</version>
</dependency>
```

## JWT工具类

```java
public class JWTUtils {

    //密钥
    private static final String SECRET = "!@#lei";
    //过期时间 24小时
    private static final long EXPIRE_TIME = 60 * 24 * 60 * 1000;

    /**
     * 生成token 一天后过期
     *
     * @param username 用户名
     * @return token
     */
    public static String creatToken(String username) {
        JWTCreator.Builder builder = JWT.create();
        return builder
                .withClaim("username", username)
                .withExpiresAt(new Date(System.currentTimeMillis() + EXPIRE_TIME))
                .sign(Algorithm.HMAC256(SECRET));
    }

    /**
     * 验证token
     *
     * @param token token字符串
     * @return true 通过  ，false 失败
     */
    public static boolean verifyToken(String token) {
        try {
            JWT.require(Algorithm.HMAC256(SECRET)).build().verify(token);
            return true;
        } catch (Exception e) {
            return false;
        }
    }

    /**
     * 获取token中的用户名
     *
     * @param token token字符串
     * @return 用户名
     */
    public static String getTokenInfo(String token) {
        if (token == null || "".equals(token)) {
            return null;
        }
        try {
            return JWT.decode(token).getClaim("username").asString();
        } catch (JWTDecodeException e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

## JWT令牌

```java
public class JWTToken implements AuthenticationToken {

    private String token;

    public JWTToken(String token) {
        this.token = token;
    }

    /**
     * 获取标识信息
     *
     * @return token
     */
    @Override
    public Object getPrincipal() {
        return token;
    }

    /**
     * 获取凭证信息
     *
     * @return token
     */
    @Override
    public Object getCredentials() {
        return token;
    }
}
```

## JWT过滤器

```java
@Component
public class JWTFilter extends BasicHttpAuthenticationFilter {

    /**
     * 是否允许访问，如果带有token，则对token进行检查，否则直接通过
     */
    @Override
    protected boolean isAccessAllowed(ServletRequest request, ServletResponse response, Object mappedValue) {
        //判断请求的请求头是否带上"token"
        if (isLoginAttempt(request, response)) {
            //如果存在，则进入executeLogin方法执行登入，检查token是否正确
            try {
                executeLogin(request, response);
                return true;
            } catch (Exception e) {
                responseError(response, e.getMessage());
            }
        }
        //如果请求头不存在token，则可能是执行登录操作或者是游客状态访问，无需检查token，直接返回true
        return true;
    }

    /**
     * 判断用户是否想要登入
     * 检查header里面是否包含token字段
     */
    @Override
    protected boolean isLoginAttempt(ServletRequest request, ServletResponse response) {
        HttpServletRequest req = (HttpServletRequest) request;
        String token = req.getHeader("Authorization");
        return token != null;
    }

    /**
     * 执行登录操作
     */
    @Override
    protected boolean executeLogin(ServletRequest request, ServletResponse response) throws Exception {
        HttpServletRequest req = (HttpServletRequest) request;
        String token = req.getHeader("Authorization");
        JWTToken jwt = new JWTToken(token);
        getSubject(request, response).login(jwt);
        return true;
    }

    /**
     * 对跨域提供支持
     */
    @Override
    protected boolean preHandle(ServletRequest request, ServletResponse response) throws Exception {
        HttpServletRequest req = (HttpServletRequest) request;
        HttpServletResponse res = (HttpServletResponse) response;
        res.setHeader("Access-Control-Allow-Origin", req.getHeader("Origin"));
        res.setHeader("Access-Control-Allow-Methods", "GET,POST,OPTIONS,PUT,DELETE");
        res.setHeader("Access-Control-Allow-Headers", req.getHeader("Access-Control-Request-Headers"));
        // 跨域时会首先发送一个option请求，这里我们给option请求直接返回正常状态
        if (req.getMethod().equals(RequestMethod.OPTIONS.name())) {
            res.setStatus(HttpStatus.OK.value());
            return false;
        }
        return super.preHandle(request, response);
    }

    /**
     * 将非法请求跳转到 /unauthorized/**
     */
    private void responseError(ServletResponse response, String message) {
        try {
            HttpServletResponse httpServletResponse = (HttpServletResponse) response;
            //设置编码，否则中文字符在重定向时会变为空字符串
            message = URLEncoder.encode(message, "UTF-8");
            httpServletResponse.sendRedirect("/unauthorized" + message);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## Shiro配置类

```java
@Configuration
public class ShiroConfig {

    /**
     * 注入请求拦截器
     *
     * @param securityManager 安全管理器
     * @return factoryBean
     */
    @Bean
    public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("Manager") DefaultWebSecurityManager securityManager) {
        //创建ShiroFilter工厂
        ShiroFilterFactoryBean factoryBean = new ShiroFilterFactoryBean();
        //添加自己的过滤器
        Map<String, Filter> filterMap = new LinkedHashMap<>();
        //设置自定义的JWT过滤器
        filterMap.put("jwt", new JWTFilter());
        //设置Shiro过滤器
        factoryBean.setFilters(filterMap);
        //设置安全管理器
        factoryBean.setSecurityManager(securityManager);
        //设置登录路径
        factoryBean.setLoginUrl("/user/userLogin/");
        //设置无权限时跳转
        factoryBean.setUnauthorizedUrl("/user/unauthorized/");
        //公共资源和受限资源
        Map<String, String> map = new HashMap<>();
        //请求通过自定义的JWTFilter
        map.put("/**", "jwt");
        //设置拦截器
        factoryBean.setFilterChainDefinitionMap(map);
        return factoryBean;
    }

    /**
     * 注入安全管理器
     *
     * @param realm 自定义Realm
     * @return securityManager
     */
    @Bean(name = "Manager")
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("Realm") Realm realm) {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        //设置安全管理器的Realm
        securityManager.setRealm(realm);
        //关闭shiro自带的session
        DefaultSubjectDAO subjectDAO = new DefaultSubjectDAO();
        DefaultSessionStorageEvaluator defaultSessionStorageEvaluator = new DefaultSessionStorageEvaluator();
        defaultSessionStorageEvaluator.setSessionStorageEnabled(false);
        subjectDAO.setSessionStorageEvaluator(defaultSessionStorageEvaluator);
        securityManager.setSubjectDAO(subjectDAO);
        return securityManager;
    }

    /**
     * 注入自定义的Realm
     *
     * @return
     */
    @Bean(name = "Realm")
    public Realm getRealm() {
        MyRealm myRealm = new MyRealm();

        //设置Realm使用的凭证器
        HashedCredentialsMatcher credentialsMatcher = new HashedCredentialsMatcher();
        //使用MD5加密
        credentialsMatcher.setHashAlgorithmName("md5");
        //Hash散列的次数
        credentialsMatcher.setHashIterations(1024);
        //设置Realm的认证方式
        myRealm.setCredentialsMatcher(credentialsMatcher);

        //开启缓存管理器
        myRealm.setCachingEnabled(true);
        //开启认证缓存
        myRealm.setAuthenticationCachingEnabled(true);
        //开启授权缓存
        myRealm.setAuthorizationCachingEnabled(true);
        //设置缓存管理器
        myRealm.setCacheManager(new RedisCacheManager());

        return myRealm;
    }

    /**
     * 开启AOP注解支持
     *
     * @param securityManager 安全管理器
     * @return authorizationAttributeSourceAdvisor
     */
    public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor(@Qualifier("Manager") DefaultWebSecurityManager securityManager) {
        AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor = new AuthorizationAttributeSourceAdvisor();
        authorizationAttributeSourceAdvisor.setSecurityManager(securityManager);
        return authorizationAttributeSourceAdvisor;
    }

    /**
     * 开启Shiro注解支持
     *
     * @return defaultAdvisorAutoProxyCreator
     */
    @Bean
    public DefaultAdvisorAutoProxyCreator defaultAdvisorAutoProxyCreator() {
        DefaultAdvisorAutoProxyCreator defaultAdvisorAutoProxyCreator = new DefaultAdvisorAutoProxyCreator();
        defaultAdvisorAutoProxyCreator.setUsePrefix(true);
        return defaultAdvisorAutoProxyCreator;
    }
    
}
```

## 自定义Realm

```java
public class MyRealm extends AuthorizingRealm {
    //授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("======进入授权方法======");
        //获取身份信息
        String primaryPrincipal = (String) principalCollection.getPrimaryPrincipal();
        //从工厂中获取service对象
        UserService userService = BeanContextUtils.getBean(UserService.class);
        //根据身份信息获取 用户角色 信息
        User user = userService.selectRolesByUserName(primaryPrincipal);

        if (!CollectionUtils.isEmpty(user.getRoles())) {
            SimpleAuthorizationInfo authorizationInfo = new SimpleAuthorizationInfo();
            user.getRoles().forEach(role -> {
                System.out.println("用户：" + user.getUsername() + " 拥有角色 [" + role.getName() + "]");
                //授权角色信息
                authorizationInfo.addRole(role.getName());

                //根据角色id获取对应的权限信息
                List<Perms> perms = userService.selectPermsByRoleId(role.getId());
                if (!CollectionUtils.isEmpty(perms) && perms.get(0) != null) {
                    perms.forEach(perm -> {
                        System.out.println("角色：" + role.getName() + "对应的权限信息：[" + perm.getName() + "]");
                        //授权权限信息
                        authorizationInfo.addStringPermission(perm.getName());
                    });
                }
            });
            return authorizationInfo;
        }
        return null;
    }

    //认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        System.out.println("======进入认证方法======");
        //从传过来的token获取用户名
        String principal = (String) token.getPrincipal();

        //从工厂中获取service对象
        UserService userService = BeanContextUtils.getBean(UserService.class);

        //根据身份信息查询
        User user = userService.findByUserName(principal);

        if (!ObjectUtils.isEmpty(user)) {
            return new SimpleAuthenticationInfo(user.getUsername(), //用户名
                    user.getPassword(),                             //密码
//                    ByteSource.Util.bytes(user.getSalt()),        //没有序列化的随机盐
                    new MyByteSource(user.getSalt()),				//注册时的随机盐(增加了随机盐的序列化)
                    this.getName());                                //realm的名字
        }
        return null;
    }
}
```

## 常见过滤器

|     配置缩写      |          对应的过滤器          |                             功能                             |
| :---------------: | :----------------------------: | :----------------------------------------------------------: |
|     **anon**      |        AnonymousFilter         |         指定url可以匿名访问（访问时不需要认证授权）          |
|     **authc**     |    FormAuthenticationFilter    | 指定url需要form表单登录，默认会从请求中获取username、password,rememberMe等参数并尝试登录，如果登录不了就会跳转到loginUrl配置的路径 |
|    authcBasic     | BasicHttpAuthenticationFilter  |                     指定url需要basic登录                     |
|      logout       |          LogoutFilter          |          登出过滤器，配置指定url就可以实现退出功能           |
| noSessionCreation |    NoSessionCreationFilter     |                         禁止创建会话                         |
|       perms       | PermissionsAuthorizationFilter |                     需要指定权限才能访问                     |
|       port        |           PortFilter           |                     需要指定端口才能访问                     |
|       rest        |   HttpMethodPermissionFilter   |      将http请求方法转化成相应的动词来构造一个权限字符串      |
|       roles       |    RolesAuthorizationFilter    |                     需要指定角色才能访问                     |
|        ssl        |           SslFilter            |                    需要https请求才能访问                     |
|       user        |           UserFilter           |              需要已登录或“记住我”的用户才能访问              |

## Bean工厂工具类

```java
@Component
public class BeanContextUtils implements ApplicationContextAware {

    /**
     * 上下文实例对象
     */
    private static ApplicationContext applicationContext;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        BeanContextUtils.applicationContext = applicationContext;
    }

    /**
     * 获取application上下文对象
     *
     * @return 上下文对象
     */
    public static ApplicationContext getApplicationContext() {
        return applicationContext;
    }

    /**
     * 通过name获取bean
     *
     * @param beanName 类名
     * @param <T>      泛型
     * @return 泛型
     */
    @SuppressWarnings("unchecked")
    public static <T> T getBean(String beanName) {
        assertApplicationContext();
        return (T) getApplicationContext().getBean(beanName);
    }

    /**
     * 通过class获取bean
     *
     * @param requiredType 类.class
     * @param <T>          泛型
     * @return 泛型
     */
    public static <T> T getBean(Class<T> requiredType) {
        return (T) getApplicationContext().getBean(requiredType);
    }

    //断言applicationContext不为空
    private static void assertApplicationContext() {
        if (BeanContextUtils.applicationContext == null) {
            throw new RuntimeException("ApplicationContext属性为null，请检查是否注入了SpringContextHolder");
        }
    }
}
```

## 数据库设计

![](.\img\62.jpg)

## 缓存

* 引入依赖

  ```xml
  <!--引入shiro和ehcache-->
  <dependency>
      <groupId>org.apache.shiro</groupId>
      <artifactId>shiro-ehcache</artifactId>
      <version>1.5.3</version>
  </dependency>
  
  <!--redis整合springboot-->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-redis</artifactId>
  </dependency>
  ```

* 自定义shiro缓存管理器(Redis)，实现==CacheManager==

  ```java
  public class RedisCacheManager implements CacheManager {
      @Override
      public <K, V> Cache<K, V> getCache(String s) throws CacheException {
          return new RedisCache<>(s);
      }
  }
  ```

* 自定义Redis缓存，实现==Cache==

  ```java
  public class RedisCache<K, V> implements Cache<K, V> {
  
      private String cacheName;
  
      public RedisCache() {
      }
  
      public RedisCache(String cacheName) {
          this.cacheName = cacheName;
      }
  
      private RedisTemplate getRedisTemplate() {
          RedisTemplate redisTemplate = BeanContextUtils.getBean("redisTemplate");
          redisTemplate.setKeySerializer(new StringRedisSerializer());
          redisTemplate.setHashKeySerializer(new StringRedisSerializer());
          return redisTemplate;
      }
  
      @Override
      public V get(K k) throws CacheException {
          return (V) getRedisTemplate().opsForHash().get(this.cacheName, k.toString());
      }
  
      @Override
      public V put(K k, V v) throws CacheException {
          getRedisTemplate().opsForHash().put(this.cacheName, k.toString(), v);
          return null;
      }
  
      @Override
      public V remove(K k) throws CacheException {
          return (V) getRedisTemplate().opsForHash().delete(this.cacheName, k.toString());
      }
  
      @Override
      public void clear() throws CacheException {
          getRedisTemplate().delete(this.cacheName);
      }
  
      @Override
      public int size() {
          return getRedisTemplate().opsForHash().size(this.cacheName).intValue();
      }
  
      @Override
      public Set<K> keys() {
          return getRedisTemplate().opsForHash().keys(this.cacheName);
      }
  
      @Override
      public Collection<V> values() {
          return getRedisTemplate().opsForHash().values(this.cacheName);
      }
      
  }
  ```

* **ByteSource**实现序列化操纵

  ```java
  public class MyByteSource implements ByteSource, Serializable {
  
      private byte[] bytes;
  
      private String cachedHex;
  
      private String cacheBase64;
  
      public MyByteSource() {
      }
  
      public MyByteSource(byte[] bytes) {
          this.bytes = bytes;
      }
  
      public MyByteSource(char[] chars) {
          this.bytes = CodecSupport.toBytes(chars);
      }
  
      public MyByteSource(String s) {
          this.bytes = CodecSupport.toBytes(s);
      }
  
      public MyByteSource(ByteSource byteSource) {
          this.bytes = byteSource.getBytes();
      }
  
      public MyByteSource(File file) {
          this.bytes = (new MyByteSource.BytesHelper()).getBytes(file);
      }
  
      public MyByteSource(InputStream inputStream) {
          this.bytes = (new MyByteSource.BytesHelper()).getBytes(inputStream);
      }
  
      public static boolean isCompatible(Object o) {
          return o instanceof byte[] || o instanceof char[] || o instanceof String || o instanceof ByteSource || o instanceof File || o instanceof InputStream;
      }
  
      @Override
      public byte[] getBytes() {
          return this.bytes;
      }
  
      @Override
      public String toHex() {
          if (this.cachedHex == null) {
              this.cachedHex = Hex.encodeToString(this.getBytes());
          }
          return this.cachedHex;
      }
  
      @Override
      public String toBase64() {
          if (this.cacheBase64 == null) {
              this.cacheBase64 = Base64.encodeToString(this.getBytes());
          }
          return this.cacheBase64;
      }
  
      @Override
      public boolean isEmpty() {
          return this.bytes == null || this.bytes.length == 0;
      }
  
      public String toString() {
          return this.toBase64();
      }
  
      public int hashCode() {
          return this.bytes != null && this.bytes.length != 0 ? Arrays.hashCode(this.bytes) : 0;
      }
  
      public boolean equals(Object o) {
          if (o == this) {
              return true;
          } else if (o instanceof ByteSource) {
              ByteSource bs = (ByteSource) o;
              return Arrays.equals(this.getBytes(), bs.getBytes());
          } else {
              return false;
          }
      }
  
      private static final class BytesHelper extends CodecSupport {
          private BytesHelper() {
  
          }
  
          public byte[] getBytes(File file) {
              return this.toBytes(file);
          }
  
          public byte[] getBytes(InputStream inputStream) {
              return this.toBytes(inputStream);
          }
      }
  }
  ```

