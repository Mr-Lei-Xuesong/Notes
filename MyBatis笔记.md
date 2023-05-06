------

# 概述

* MyBatis是一个持久层框架，它封装了jdbc操作的很多细节，使开发者只需关注sql语句本身，而无需关注注册驱动，创建连接等繁杂过程。它使用了ORM思想实现了结果集的封装
* ORM：对象关系映射，把数据库表和实体类及实体类的属性对应起来，直接操作实体类就实现操作数据库表

# 入门

> 每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先配置的 Configuration 实例来构建出 SqlSessionFactory 实例

# 环境搭建

* 创建Maven工程并导入坐标

  ```xml
  <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.4.5</version>
  </dependency>
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.6</version>
  </dependency>
  ```

* 创建实体类和Dao接口

* 创建MyBatis的主配置文件

  * MyBatis-Config.xml

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE configuration
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <!--MyBatis的主配置文件-->
    <configuration>
        <!--配置环境-->
        <environments default="mysql">
            <!-- 配置,mysql的环境-->
            <environment id="mysql">
                <!--配置事务类型-->
                <transactionManager type="JDBC"></transactionManager>
                <!--配置数据源(连接池)-->
                <dataSource type="POOLED">
                    <!--配置连接数据库的4个基本信息-->
                    <property name="driver" value="com.mysql.jdbc.Driver"/>
                    <property name="url" value="jdbc:mysql:///表名"/>
                    <property name="username" value="root"/>
                    <property name="password" value="root"/>
                </dataSource>
            </environment>
        </environments>
        <!--指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件-->
        <mappers>
            <mapper resource="映射文件全限定名"/>
        </mappers>
    </configuration>
    ```

* 创建映射配置文件

  * **必须和dao接口的包结构相同**

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="dao接口的全限定类名">
        <!--配置查询所有-->
        <select id="dao接口下的方法名" resultType="返回结果集实体类的全限定类名" parameterType="参数类型">
            sql语句
        </select>
    </mapper>
    ```

* MyBatis工具类

  ```java
  public class MyBatisUtils {
      private static SqlSessionFactory sqlSessionFactory;
  
      static {
          try {
              //读取配置文件
              InputStream is = Resources.getResourceAsStream("MyBatis-Config.xml");
              //创建SqlSessionFactory工厂
              sqlSessionFactory = new SqlSessionFactoryBuilder().build(is);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
      public static SqlSession getSqlSession() {
          return sqlSessionFactory.openSession(true);//true为开启事务提交
      }
  }
  ```
  
* 资源过滤

  ```xml
  <resources>
      <resource>
          <directory>src/main/java</directory>
          <includes>
              <include>**/*.properties</include>
              <include>**/*.xml</include>
          </includes>
          <filtering>false</filtering>
      </resource>
      <resource>
          <directory>src/main/resources</directory>
          <includes>
              <include>**/*.properties</include>
              <include>**/*.xml</include>
          </includes>
          <filtering>false</filtering>
      </resource>
  </resources>
  ```

# 配置解析(configuration)

* MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。 配置文档的顶层结构如下

  ```
  configuration（配置）
      properties（属性）
      settings（设置）
      typeAliases（类型别名）
      typeHandlers（类型处理器）
      objectFactory（对象工厂）
      plugins（插件）
      environments（环境配置）
      environment（环境变量）
      transactionManager（事务管理器）
      dataSource（数据源）
      databaseIdProvider（数据库厂商标识）
      mappers（映射器）
  ```

* properties（属性）：外部进行配置，并可以进行动态替换

  * 可以通过引入db.properties文件属性来实现配置文件数据

    ```xml
    <properties resource="db.properties"/>
    ```

  * db.properties

    ```xml
    driver=com.mysql.jdbc.Driver
    url=jdbc:mysql:///mybatis
    username=root
    password=root
    ```

* settings（设置）

  | 设置名                           | 描述                                                         | 有效值                                                       | 默认值                                                |
  | :------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :---------------------------------------------------- |
  | cacheEnabled                     | 全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。     | true \| false                                                | true                                                  |
  | lazyLoadingEnabled               | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 | true \| false                                                | false                                                 |
  | aggressiveLazyLoading            | 开启时，任一方法的调用都会加载该对象的所有延迟加载属性。 否则，每个延迟加载属性会按需加载（参考 `lazyLoadTriggerMethods`)。 | true \| false                                                | false （在 3.4.1 及之前的版本中默认为 true）          |
  | multipleResultSetsEnabled        | 是否允许单个语句返回多结果集（需要数据库驱动支持）。         | true \| false                                                | true                                                  |
  | useColumnLabel                   | 使用列标签代替列名。实际表现依赖于数据库驱动，具体可参考数据库驱动的相关文档，或通过对比测试来观察。 | true \| false                                                | true                                                  |
  | useGeneratedKeys                 | 允许 JDBC 支持自动生成主键，需要数据库驱动支持。如果设置为 true，将强制使用自动生成主键。尽管一些数据库驱动不支持此特性，但仍可正常工作（如 Derby）。 | true \| false                                                | False                                                 |
  | autoMappingBehavior              | 指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示关闭自动映射；PARTIAL 只会自动映射没有定义嵌套结果映射的字段。 FULL 会自动映射任何复杂的结果集（无论是否嵌套）。 | NONE, PARTIAL, FULL                                          | PARTIAL                                               |
  | autoMappingUnknownColumnBehavior | 指定发现自动映射目标未知列（或未知属性类型）的行为。`NONE`: 不做任何反应`WARNING`: 输出警告日志（`'org.apache.ibatis.session.AutoMappingUnknownColumnBehavior'` 的日志等级必须设置为 `WARN`）`FAILING`: 映射失败 (抛出 `SqlSessionException`) | NONE, WARNING, FAILING                                       | NONE                                                  |
  | defaultExecutorType              | 配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（PreparedStatement）； BATCH 执行器不仅重用语句还会执行批量更新。 | SIMPLE REUSE BATCH                                           | SIMPLE                                                |
  | defaultStatementTimeout          | 设置超时时间，它决定数据库驱动等待数据库响应的秒数。         | 任意正整数                                                   | 未设置 (null)                                         |
  | defaultFetchSize                 | 为驱动的结果集获取数量（fetchSize）设置一个建议值。此参数只可以在查询设置中被覆盖。 | 任意正整数                                                   | 未设置 (null)                                         |
  | defaultResultSetType             | 指定语句默认的滚动策略。（新增于 3.5.2）                     | FORWARD_ONLY \| SCROLL_SENSITIVE \| SCROLL_INSENSITIVE \| DEFAULT（等同于未设置） | 未设置 (null)                                         |
  | safeRowBoundsEnabled             | 是否允许在嵌套语句中使用分页（RowBounds）。如果允许使用则设置为 false。 | true \| false                                                | False                                                 |
  | safeResultHandlerEnabled         | 是否允许在嵌套语句中使用结果处理器（ResultHandler）。如果允许使用则设置为 false。 | true \| false                                                | True                                                  |
  | mapUnderscoreToCamelCase         | 是否开启驼峰命名自动映射，即从经典数据库列名 A_COLUMN 映射到经典 Java 属性名 aColumn。 | true \| false                                                | False                                                 |
  | localCacheScope                  | MyBatis 利用本地缓存机制（Local Cache）防止循环引用和加速重复的嵌套查询。 默认值为 SESSION，会缓存一个会话中执行的所有查询。 若设置值为 STATEMENT，本地缓存将仅用于执行语句，对相同 SqlSession 的不同查询将不会进行缓存。 | SESSION \| STATEMENT                                         | SESSION                                               |
  | jdbcTypeForNull                  | 当没有为参数指定特定的 JDBC 类型时，空值的默认 JDBC 类型。 某些数据库驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER。 | JdbcType 常量，常用值：NULL、VARCHAR 或 OTHER。              | OTHER                                                 |
  | lazyLoadTriggerMethods           | 指定对象的哪些方法触发一次延迟加载。                         | 用逗号分隔的方法列表。                                       | equals,clone,hashCode,toString                        |
  | defaultScriptingLanguage         | 指定动态 SQL 生成使用的默认脚本语言。                        | 一个类型别名或全限定类名。                                   | org.apache.ibatis.scripting.xmltags.XMLLanguageDriver |
  | defaultEnumTypeHandler           | 指定 Enum 使用的默认 `TypeHandler` 。（新增于 3.4.5）        | 一个类型别名或全限定类名。                                   | org.apache.ibatis.type.EnumTypeHandler                |
  | callSettersOnNulls               | 指定当结果集中值为 null 的时候是否调用映射对象的 setter（map 对象时为 put）方法，这在依赖于 Map.keySet() 或 null 值进行初始化时比较有用。注意基本类型（int、boolean 等）是不能设置成 null 的。 | true \| false                                                | false                                                 |
  | returnInstanceForEmptyRow        | 当返回行的所有列都是空时，MyBatis默认返回 `null`。 当开启这个设置时，MyBatis会返回一个空实例。 请注意，它也适用于嵌套的结果集（如集合或关联）。（新增于 3.4.2） | true \| false                                                | false                                                 |
  | logPrefix                        | 指定 MyBatis 增加到日志名称的前缀。                          | 任何字符串                                                   | 未设置                                                |
  | logImpl                          | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。        | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | 未设置                                                |
  | proxyFactory                     | 指定 Mybatis 创建可延迟加载对象所用到的代理工具。            | CGLIB \| JAVASSIST                                           | JAVASSIST （MyBatis 3.3 以上）                        |
  | vfsImpl                          | 指定 VFS 的实现                                              | 自定义 VFS 的实现的类全限定名，以逗号分隔。                  | 未设置                                                |
  | useActualParamName               | 允许使用方法签名中的名称作为语句参数名称。 为了使用该特性，你的项目必须采用 Java 8 编译，并且加上 `-parameters` 选项。（新增于 3.4.1） | true \| false                                                | true                                                  |
  | configurationFactory             | 指定一个提供 `Configuration` 实例的类。 这个被返回的 Configuration 实例用来加载被反序列化对象的延迟加载属性值。 这个类必须包含一个签名为`static Configuration getConfiguration()` 的方法。（新增于 3.2.3） | 一个类型别名或完全限定类名。                                 | 未设置                                                |

* typeAliases（类型别名）:可为 Java 类型设置一个缩写名字,它仅用于 XML 配置，意在降低冗余的全限定类名书写

  ```xml
  <typeAliases>
    <typeAlias alias="别名" type="全限定类名"/>
  </typeAliases>
  ```
  * 扫描实体类的包，它默认别名就是这个类的类名首字母小写,如果类上有别名注解**@Alias**就是用注解定义的别名

  ```xml
  <typeAliases>
    <package name="cn.demo.domain"/>
  </typeAliases>
  ```

* mappers（映射器）：指定执行sql语句的文件位置，与Dao接口名相同

  ```xml
  <mappers>
  	<mapper resource="cn/demo/dao/xxx.xml"/>
  </mappers>
  ```

# 结果集Map映射

* 如果数据库字段名与实体类属性名不一致，就用Map结果映射

```xml
<resultMap id="映射ID" type="实体类">
	<result column="数据库表中的字段名(id)" property="实体类中的属性(uid)"/>
</resultMap>

<select id="Dao接口下的方法名" parameterType="参数类型" resultMap="映射ID">
    select * from user where id=#{uid};
</select>
```

# 模糊查询

```java
//模糊查询名字
List<User> getLikeUser(String name);
```

```xml
<!--模糊查询名字-->
<select id="getLikeUser" parameterType="String" resultType="User">
    select * from user where username like "%"#{name}"%";
</select>
```

# 日志

* 在MyBatis核心配置文件中，配置日志,STDOUT_LOGGING为标准日志工厂

  ```xml
  <settings>
  	<setting name="logImpl" value="STDOUT_LOGGING"/>
  </settings>
  ```

## Log4j

* 导入jar包

  ```xml
  <!-- https://mvnrepository.com/artifact/log4j/log4j -->
  <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
  </dependency>
  ```

* 配置log4j.properties文件

  ```properties
  #将等级为DEBUG的日志信息输出到console和file这两个目的地，console和file的定义在下面的代码
  log4j.rootLogger=DEBUG,console,file
  
  #控制台输出的相关设置
  log4j.appender.console = org.apache.log4j.ConsoleAppender
  log4j.appender.console.Target = System.out
  log4j.appender.console.Threshold=DEBUG
  log4j.appender.console.layout = org.apache.log4j.PatternLayout
  log4j.appender.console.layout.ConversionPattern=[%c]-%m%n
  
  #文件输出的相关设置
  log4j.appender.file = org.apache.log4j.RollingFileAppender
  log4j.appender.file.File=./log/msg.log
  log4j.appender.file.MaxFileSize=10mb
  log4j.appender.file.Threshold=DEBUG
  log4j.appender.file.layout=org.apache.log4j.PatternLayout
  log4j.appender.file.layout.ConversionPattern=[%p][%d{yyyy-MM-dd  HH:mm:ss}][%c]%m%n
  
  #日志输出级别
  log4j.logger.org.mybatis=DEBUG
  log4j.logger.java.sql=DEBUG
  log4j.logger.java.sql.Statement=DEBUG
  log4j.logger.java.sql.ResultSet=DEBUG
  log4j.logger.java.sql.PreparedStatement=DEBUG
  ```

* 在MyBatis核心配置文件中，配置日志

  ```xml
  <settings>
  	<setting name="logImpl" value="LOG4J"/>
  </settings>
  ```

### 使用

* 导入相应的包：

  ```java
  import org.apache.log4j.Logger
  ```

* 创建对象：

  ```java
  Logger logger=Logger.getLogger(加载类.class)
  ```

* 日志级别
  * info
  * error
  * debug

# 分页

* 语法：

  ```
  select * from 表名 limit 起始值,查询条数
  ```

## 方法一

* Dao接口

  ```java
  List<User> getUserByLimit(Map<String,Integer> map);
  ```

* Mapper.xml

  ```xml
  <select id="getUserByLimit" parameterType="map" resultMap="UserMap">
  	select * from user limit #{startIndex},#{pageSize};
  </select>
  ```

* 测试

  ```java
  SqlSession sqlSession = MyBatisUtils.getSqlSession();
  UserMapper mapper = sqlSession.getMapper(UserMapper.class);
  
  HashMap<String, Integer> map = new HashMap<>();
  map.put("startIndex",0);
  map.put("pageSize",5);
  
  List<User> userList = mapper.getUserByLimit(map);
  for (User user : userList) {
  System.out.println(user);
  }
  sqlSession.close();
  ```

## 方法二

* Dao接口

  ```java
  List<User> getUserByRowBounds();
  ```

* Mapper.xml

  ```xml
  <select id="getUserByRowBounds" resultMap="UserMap">
  	select * from user
  </select>
  ```

* 测试

  ```java
  SqlSession sqlSession = MyBatisUtils.getSqlSession();
  RowBounds rowBounds=new RowBounds(0,5);
  List<User> userList = sqlSession.selectList("cn.demo.dao.UserMapper.getUserByRowBounds",null,rowBounds);
  for (User user : userList) {
  	System.out.println(user);
  }
  sqlSession.close();
  ```

# 使用注解开发

* 需要在MyBatis核心配置文件中绑定接口

  ```java
  <mappers>
  	<mapper class="cn.demo.dao.UserMapper"/>
  </mappers>
  ```

* 注解在接口中的方法上实现

  ```java
  @Select("select * from user")
  List<User> getUsers();
  ```

* @Param()注解
  * sql语句中引用的条件名就是Param的属性名
  * 基本类型的参数或者String类型，需要加上
  * 引用类型不用加

# Lombok

* 安装插件

* Maven导入坐标

  ```xml
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.10</version>
  </dependency>
  ```

* 在实体类上加注解：
  * @Data：无参构造方法、GET和SET、equals、canEqual、toString、hashCode
  * @AllArgsConstructor：有参构造方法 
  * @NoArgsConstructor：无参构造方法

# 多对一和一对多

## 多对一

### 按照结果嵌套处理

```xml
<resultMap id="stuAndTea" type="Student">
	<result property="id" column="sid"/>
	<result property="name" column="sname"/>
	<!--关联对象property 关联对象在Student实体类中的属性-->
	<association property="teacher" javaType="Teacher">
		<result property="name" column="tname"/>
		<result property="id" column="tid"/>
	</association>
</resultMap>

<select id="getStudent" resultMap="stuAndTea">
	select s.id sid,s.name sname,t.id tid,t.name tname from student s,teacher t where s.tid=t.id;
</select>
```

### 按照查询嵌套处理

```xml
<resultMap id="stuAndTea" type="Student">
	<!--association关联属性  property属性名 column在多的一方的表中的列名 javaType属性类型 select查询语句-->
   	<association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
</resultMap>
<select id="getStudent" resultMap="stuAndTea">
    select * from student;
</select>
<!--这里传递过来的id，只有一个属性的时候，下面可以写任何值association中column多参数配置：
        column="{key=value,key=value}"     传递查询参数名称=字段名称
        其实就是键值对的形式，key是传给下个sql的取值名称，value是sql查询的字段名。
    -->
<select id="getTeacher" resultType="Teacher">
    select * from teacher where id=#{tid};
</select>
```

## 一对多

### 按照结果嵌套查询

```xml
<resultMap id="teaAndStu" type="Teacher">
    <result property="id" column="tid"/>
    <result property="name" column="tname"/>
    <collection property="students" ofType="Student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
    </collection>
</resultMap>
<select id="getTeacher" parameterType="int" resultMap="teaAndStu">
    select s.id sid,s.name sname,t.id tid,t.name tname from student s,teacher t where s.tid=t.id and t.id=#{tid}
</select>
```

### 按照查询嵌套处理

```xml
<select id="getTeacher" parameterType="int" resultMap="teaAndStu">
        select * from teacher where id=#{tid};
</select>
<resultMap id="teaAndStu" type="Teacher">
    <collection property="students" column="id" javaType="ArrayList" ofType="Student" select="getStudents"/>
</resultMap>
<select id="getStudents" resultType="Student">
    select * from student where tid=#{id};
</select>
```

## 总结

* 关联-association
* 集合-collection
* 所以association是用于一对一和多对一，而collection是用于一对多的关系
* JavaType和ofType都是用来指定对象类型的
  - JavaType是用来指定实体类中属性的类型
  - ofType指定的是映射到list集合属性中实体类的类型

# 动态SQL

* **动态SQL指的是根据不同的查询条件 , 生成不同的Sql语句**
* 动态 SQL 元素和 JSTL 或基于类似 XML 的文本处理器相似

## if

* if：提供了可选的查找文本功能，test条件内容为true才会执行，否者不执行

  ```xml
  select * from blog
  <where>
      <if test="title != null">
          and title=#{title}
      </if>
      <if test="author != null">
          and author=#{author}
      </if>
  </where>
  ```

## choose

* choose (when, otherwise)

  ```xml
  select * from blog
  <where>
  	<choose>
  		<when test="title != null">
  			title = #{title}
  		</when>
  		<when test="author != null">
  			and author = #{author}
  		</when>
  		<otherwise>
  			1=1
  		</otherwise>
  	</choose>
  </where>
  ```

## trim

* trim (where, set)

  ```xml
  <trim prefix="WHERE" prefixoverride="AND |OR">
  	prefix：前缀　　　　　　
  	prefixoverride：去掉第一个and或者是or　
  </trim>
  
  <trim prefix="set" suffixoverride="," suffix=" where id = #{id} ">
  	prefix：前缀
  	suffixoverride：去掉最后一个逗号
  	suffix：后缀
  </trim>
  ```

  * *where* 元素只会在子元素返回任何内容的情况下才插入 “WHERE” 子句。而且，若子句的开头为 “AND” 或 “OR”，*where* 元素也会将它们去除
  * *set* 元素会动态地在行首插入 SET 关键字，并会删掉额外的逗号

## SQL片段

* 抽取SQL语句公共部分，方便复用

  ```xml
  <sql id="指定ID名">
  	公共部分
  </sql>
  ```

* 在需要重复的地方使用**include**标签引用

  ```xml
  <include refid="ID名"/>
  ```

## foreach

```xml
<foreach collection="" item="" open="" close="" separator="">
         sql语句
</foreach>
```

* collection:指定输入对象中的集合属性       
* item:每次遍历生成的对象        
* open:开始遍历时的拼接字符串       
* close:结束时拼接的字符串        
* separator:遍历对象之间需要拼接的字符串
* 当使用 Map 对象（或者 Map.Entry 对象的集合）时，index 是键，item 是值

# 缓存

## 缓存简介

* 存在内存中的临时数据。
* 将用户经常查询的数据放在缓存（内存）中，用户去查询数据就不用从磁盘上(关系型数据库数据文件)查询，从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题。
* 减少和数据库的交互次数，减少系统开销，提高系统效率

## MyBatis缓存

* MyBatis系统中默认定义了两级缓存：**一级缓存**和**二级缓存**
  - 默认情况下，只有一级缓存开启。（SqlSession级别的缓存，也称为本地缓存）
  - 二级缓存需要手动开启和配置，他是基于namespace级别的缓存。
  - 为了提高扩展性，MyBatis定义了缓存接口Cache。我们可以通过实现Cache接口来自定义二级缓存

## 一级缓存

* 一级缓存也叫本地缓存：

  - 与数据库同一次会话期间查询到的数据会放在本地缓存中。
  - 以后如果需要获取相同的数据，直接从缓存中拿，没必须再去查询数据库；
  - 只在一次sqlSession会话中有效

* 缓存失效的情况

  * 查询不同的语句

  * 增删改操作，改变原来的数据，必定会刷新缓存

  * 手动清理

    ```java
    sqlSession.clearCache();
    ```

## 二级缓存

* 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存
* 基于namespace级别的缓存，一个名称空间，对应一个二级缓存；
* 工作机制
  - 一个会话查询一条数据，这个数据就会被放在当前会话的一级缓存中；
  - 如果当前会话关闭了，这个会话对应的一级缓存就没了；但是一级缓存中的数据被保存到二级缓存中；
  - 新的会话查询信息，就可以从二级缓存中获取内容；
  - 不同的mapper查出的数据会放在自己对应的缓存（map）中

* 步骤

  * 序列化实体类

  * 在核心配置文件中开启全局缓存(默认开启)

    ```xml
    <setting name="cacheEnabled" value="true"/>
    ```

  * 在Mapper.xml文件中开启二级缓存

    ```xml
    <cache/>
    
    可选参数：
        eviction="FIFO"
        flushInterval="60000"
        size="512"
        readOnly="true"
    创建了一个 FIFO 缓存，每隔 60 秒刷新，最多可以存储结果对象或列表的 512 个引用，返回的对象被认为是只读的，因此对它们进行修改可能会在不同线程中的调用者产生冲突。
    ```

## 自定义EhCache

* 导包

  ```xml
  <dependency>
      <groupId>org.mybatis.caches</groupId>
      <artifactId>mybatis-ehcache</artifactId>
      <version>1.1.0</version>
  </dependency>
  ```

* Mapper.xml指定使用缓存

  ```xml
  <cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
  ```

* 编写ehcache.xml文件

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:noNamespaceSchemaLocation="http://ehcache.org/ehcache.xsd"
           updateCheck="false">
      <!--
         diskStore：为缓存路径，ehcache分为内存和磁盘两级，此属性定义磁盘的缓存位置。参数解释如下：
         user.home – 用户主目录
         user.dir  – 用户当前工作目录
         java.io.tmpdir – 默认临时文件路径
       -->
      <diskStore path="./tmpdir/Tmp_EhCache"/>
      
      <defaultCache
              eternal="false"
              maxElementsInMemory="10000"
              overflowToDisk="false"
              diskPersistent="false"
              timeToIdleSeconds="1800"
              timeToLiveSeconds="259200"
              memoryStoreEvictionPolicy="LRU"/>
   
      <cache
              name="cloud_user"
              eternal="false"
              maxElementsInMemory="5000"
              overflowToDisk="false"
              diskPersistent="false"
              timeToIdleSeconds="1800"
              timeToLiveSeconds="1800"
              memoryStoreEvictionPolicy="LRU"/>
      <!--
         defaultCache：默认缓存策略，当ehcache找不到定义的缓存时，则使用这个缓存策略。只能定义一个。
       -->
      <!--
        name:缓存名称。
        maxElementsInMemory:缓存最大数目
        maxElementsOnDisk：硬盘最大缓存个数。
        eternal:对象是否永久有效，一但设置了，timeout将不起作用。
        overflowToDisk:是否保存到磁盘，当系统当机时
        timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
        timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。最大时间介于创建时间和失效时间之间。仅当eternal=false对象不是永久有效时使用，默认是0.，也就是对象存活时间无穷大。
        diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.
        diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
        diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
        memoryStoreEvictionPolicy：当达到maxElementsInMemory限制时，Ehcache将会根据指定的策略去清理内存。默认策略是LRU（最近最少使用）。你可以设置为FIFO（先进先出）或是LFU（较少使用）。
        clearOnFlush：内存数量最大时是否清除。
        memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。
        FIFO，first in first out，这个是大家最熟的，先进先出。
        LFU， Less Frequently Used，就是上面例子中使用的策略，直白一点就是讲一直以来最少被使用的。如上面所讲，缓存的元素有一个hit属性，hit值最小的将会被清出缓存。
        LRU，Least Recently Used，最近最少使用的，缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
     -->
  
  </ehcache>
  ```

  

