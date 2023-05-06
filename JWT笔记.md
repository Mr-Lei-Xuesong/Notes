# JWT简介

1. 授权
   1. 这是使用JWT的最常见方案。一旦用户登录，每个后续请求将包括JWT，从而允允许的路由，服务和资源。单点登录是当今广泛使用JWT的一项功能，因为它的开销很小并且可以在不同的域中轻松使用
2. 信息交换
   1. JSON Web Token是在各方之间安全地传输信息的好方法。因为可以对JWT进行签名（例如，使用公钥/私钥对），所以您可以确保发件人是他们所说的人。此外，由于签名是使用标头和有效负载计算的，因此您还可以验证内容是否遭到篡改。

> 注意：jwt跟session不一样，jwt存储在客户端，session存储在服务器端，服务器断电后session就没了，而jwt因为存储在客户端，所以就不会被影响，只要jwt不过期，就可以继续使用。

------

1. 基于传统的Session认证
   1. http协议本身是一种无状态的协议，而这就意味着如果用户向我们的应用提供了用户名和密码来进行用户认证，那么下一次请求时，用户还要再一次进行用户认证才行，因为根据http协议，我们并不能知道是哪个用户发出的请求，所以为了让我们的应用能识别是哪个用户发出的请求，我们只能在服务器存储一份用户登录的信息，这份登录信息会在响应时传递给浏览器，告诉其保存为cookie,以便下次请求时发送给我们的应用，这样我们的应用就能识别请求来自哪个用户了,这就是传统的基于session认证。

2. 认证流程

   ![](.\img\68.jpg)

3. 暴露问题
   1. 每个用户经过我们的应用认证之后，我们的应用都要在服务端做一次记录，以方便用户下次请求的鉴别，通常而言session都是保存在内存中，而随着认证用户的增多，服务端的开销会明显增大
   2. 用户认证之后，服务端做认证记录，如果认证的记录被保存在内存中的话，这意味着用户下次请求还必须要请求在这台服务器上,这样才能拿到授权的资源，这样在分布式的应用上，相应的限制了负载均衡器的能力。这也意味着限制了应用的扩展能力。
   3. 因为是基于cookie来进行用户识别的, cookie如果被截获，用户就会很容易受到跨站请求伪造的攻击 

1. 基于JWT认证

   ![](.\img\69.jpg)

   1. 认证流程
      1. 前端通过Web表单将自己的用户名和密码发送到后端的接口。这一过程一般是一个HTTP POST请求。建议的方式是通过SSL加密的传输（https协议），从而避免敏感信息被嗅探
      2. 后端核对用户名和密码成功后，将用户的id等其他信息作为JWT Payload（负载），将其与头部分别进行Base64编码拼接后签名，形成一个JWT(Token)。形成的JWT就是一个形同 **Header.Payload.Signature**的字符串。后端将JWT字符串作为登录成功的返回结果返回给前端。前端可以将返回的结果保存在localStorage或sessionStorage上，退出登录时前端删除保存的JWT即可
      3. 前端在每次请求时将JWT放入HTTP Header中的Authorization位。(解决XSS和XSRF问题) 
      4. 后端检查是否存在，如存在验证JWT的有效性。例如，检查签名是否正确；检查Token是否过期；检查Token的接收方是否是自己（可选）
      5. 验证通过后后端使用JWT中包含的用户信息进行其他逻辑操作，返回相应结果
   2. jwt优势
      1. 简洁(Compact): 可以通过URL，POST参数或者在HTTP header发送，因为数据量小，传输速度也很快
      2. 自包含(Self-contained)：负载中包含了所有用户所需要的信息，避免了多次查询数据库
      3. 因为Token是以JSON加密的形式保存在客户端的，所以JWT是跨语言的，原则上任何web形式都支持。 不需要在服务端保存会话信息，特别适用于分布式微服务

# JWT结构

> **Header.Payload.Signature**

* 令牌组成

  * 标头(Header)
  * 有效载荷(Payload)
  * 签名(Signature)

  因此，JWT通常如下所示:xxxxx.yyyyy.zzzzz

1. Header

   * 标头通常由两部分组成：令牌的类型（即JWT）和所使用的签名算法，例如HMAC SHA256或RSA。它会使用 Base64 编码组成 JWT 结构的第一部分

   * Base64是一种编码，也就是说，它是可以被翻译回原来的样子来的。它并不是一种加密过程

     ```json
     {
       "alg": "HS256",
       "typ": "JWT"
     }
     ```

2. Payload

   * 令牌的第二部分是有效负载，其中包含声明。声明是有关实体（通常是用户）和其他数据的声明。同样的，它会使用 Base64 编码组成 JWT 结构的第二部分

     ```json
     {
       "sub": "1234567890",
       "name": "John Doe",
       "admin": true
     }
     ```

3. Signature

   * 前面两部分都是使用 Base64 进行编码的，即前端可以解开知道里面的信息。Signature 需要使用编码后的 header 和 payload 以及我们提供的一个密钥，然后使用 header 中指定的签名算法（HS256）进行签名。签名的作用是保证 JWT 没有被篡改过

     ```java
     HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload),secret);
     ```

   * 签名的目的
     * 最后一步签名的过程，实际上是对头部以及负载内容进行签名，防止内容被窜改。如果有人对头部以及负载的内容解码之后进行修改，再进行编码，最后加上之前的签名组合形成新的JWT的话，那么服务器端会判断出新的头部和负载形成的签名和JWT附带上的签名是不一样的。如果要对新的头部和负载进行签名，在不知道服务器加密时用的密钥的话，得出来的签名也是不一样的。
   * 信息安全问题
     * 在JWT中，不应该在负载里面加入任何敏感的数据。在上面的例子中，我们传输的是用户的User ID。这个值实际上不是什么敏感内容，一般情况下被知道也是安全的。但是像密码这样的内容就不能被放在JWT中了。如果将用户的密码放在了JWT中，那么怀有恶意的第三方通过Base64解码就能很快地知道你的密码了。因此JWT适合用于向Web应用传递一些非敏感信息。JWT还经常用于设计用户认证和授权系统，甚至实现Web应用的单点登录。

# 使用JWT

1. 引入依赖

   ```xml
   <dependency>
       <groupId>com.auth0</groupId>
       <artifactId>java-jwt</artifactId>
       <version>3.4.0</version>
   </dependency>
   ```

2. 生成令牌

   ```java
   Calendar instance = Calendar.getInstance();
   instance.add(Calendar.SECOND, 20);
   String sign = JWT.create()
       .withClaim("userId", 21)//Payload
       .withClaim("username", "lei")
       .withExpiresAt(instance.getTime())//指定令牌的过期时间
       .sign(Algorithm.HMAC256("@#xuesong!"));//Signature
   
   
   System.out.println("=====jwt=====："+sign);
   ```

3. 根据令牌和签名解析数据

   ```java
   //创建JWT验证对象
   JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256("@#xuesong!")).build();
   
   DecodedJWT verify = jwtVerifier.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJleHAiOjE2MjU0MDc5MTksInVzZXJJZCI6MjEsInVzZXJuYW1lIjoibGVpIn0.taILY4FH5d7GYh7mc8kti9XW0c_0SoK7EjdSXW0h_SY");
   
   System.out.println(verify.getClaim("userId").asInt());
   System.out.println(verify.getClaim("username").asString());
   ```

4. 常见异常

   ```markdown
   SignatureVerificationException:				签名不一致异常
   TokenExpiredException:    				    令牌过期异常
   AlgorithmMismatchException:				    算法不匹配异常
   InvalidClaimException:					   失效的payload异常
   ```

# 封装工具类

```java
/**
 * Author:   lxs
 * Date:     2021/7/4 21:42
 * Description: JWT工具类
 */
public class JWTUtils {

    //密钥
    private static final String regret = "!@#$%^&*lei";
    //过期时间 24小时
    private static final long EXPIRE_TIME = 60 * 24 * 60 * 1000;

    /**
     * 生成token 一天后过期
     *
     * @param map 封装的信息
     * @return token
     */
    public static String creatToken(Map<String, String> map) {
        JWTCreator.Builder builder = JWT.create();
        //map遍历传入jwt
        map.forEach(builder::withClaim);
        return builder
                .withExpiresAt(new Date(System.currentTimeMillis() + EXPIRE_TIME))
                .sign(Algorithm.HMAC256(regret));
    }

    /**
     * 验证token
     *
     * @param token token字符串
     * @return true 通过  ，false 失败
     */
    public static boolean verifyToken(String token) {
        try {
            JWT.require(Algorithm.HMAC256(regret)).build().verify(token);
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
        return JWT.decode(token).getClaim("username").asString();
    }
}
```

