# 内存分析

* Java虚拟机的内存可以分为三个区域：**栈stack、堆heap、方法区method area**

## 方法区(method area)

*  **方法区中存放类信息、常量、静态变量、即时编译器编译(JIT)的代码**
*  **JVM只有一个方法区，被所有线程共享!**
*  **方法区实际也是堆，只是用于存储类、常量相关的信息!**
*  用来存放程序中永远是不变或唯一的内容。(类信息【Class对象】、静态变量、字符串常量等)

## 堆(heap)

* **提供所有类实例和数组对象存储区域**
* **jvm只有一个heap区，被所有线程共享，不存放基本类型和对象引用，只存放对象本身。**
* **堆是一个不连续的内存空间，分配灵活，速度慢!**

## 栈(stack)

*  **每一个线程包含一个stack区，只保存基本数据类型的对象和自定义对象的引用（不是对象)；**
*  **每个栈中的数据（基本数据类型和对象引用）都是私有的，其他栈不能访问；**
*  **每个方法被调用都会创建一个栈帧(存储局部变量、操作数、方法出口等)**
*  栈的存储特性是“先进后出，后进先出” 
*  栈是由系统自动分配，速度快!栈是一个连续的内存空间!

# 方法

* 方法(method) 是将具有独立功能的代码块组织成为一个整体，使其具有特殊功能的代码集
  * 方法必须先创建才可以使用，该过程称为方法定义
  * 方法创建后并不是直接运行的，需要手动使用后才执行，该过程称为方法调用

## 方法注意事项

* 方法不能嵌套定义

## 方法重载

* 方法重载指同一个类中定义的多个方法之间的关系，满足下列条件的多个方法相互构成重载
  * 多个方法在同一类中
  * 多个方法具有相同的方法名
  * 多个方法的参数不相同，类型不同或者数量不同

### 重载特点

* 重载仅对应方法的定义，与方法的调用无关，调用方式参照标准格式
* **重载仅针对同一个类中方法的名称与参数进行识别，与返回值无关，不能通过返回值来判定两个方法是否相互构成重载**

## 方法参数转递

### 基本类型

* 形式参数的改变，不影响实际参数的值

### 引用类型

* 形式参数的改变，影响实际参数的值

# 封装

## 封装概述

* 是面向对象三大特征之一(**封装、继承、多态**)
* 是面向对象编程语言对客观世界的模拟，客观世界里成员变量都是隐藏在对象内部的，外界是无法直接操作的

## 封装原则

* 将类的某些信息隐藏在类内部，不允许外部程序直接访问，而是通过该类提供的方法来实现对隐藏信息的操作和访问
* 成员变量**private**，提供对应的**getXxx()/setXxx()**方法

## 封装好处

* 通过方法来控制成员变量的操作，提高了安全性
* 把代码用方法进行封装，提高了代码的复用性

# 构造方法

* 通过new关键字调用
* 构造器虽然有返回值，但是不能定义返回值类型(返回值的类型肯定是本类)，不能在构造器里使用return返回某个值。
* 如果我们没有定义构造器，则编译器会自动定义一个无参的构造函数。如果已定义则编译器不会自动添加!
* 构造器的方法名必须和类名一致!

# String

## String

* String 内容不可变的

## SringBuilder

* StringBuilder 内容可变的

## 构造方法

* *StringBuilder sb=new StringBuilder();*
* *StringBuilder sb=new StringBulider(String s);*  **String转换为StringBuilder**
  * *String s=sb.toString();* **StringBuilder转换为String**
* *sb.append()*  添加数据，并返回对象本身
* *sb.reverse()*  返回相反的字符序列

# ArrayList

## 构造方法

* *ArrayList<泛型> array=new ArrayList();*

## 常用方法

* 增加数据
  * *array.add(String s);*
  * *array.add(int index,String s);*
* 删除数据
  * *array.remove(Object o)* 删除指定元素，返回删除成功
  * *array.remove(int index)* 删除指定索引位置，返回被删除的元素

* 修改数据
  * *array.set(int index,E element)* 修改指定索引位置的元素，返回被修改的元素
* 获取数据
  * *array.get(int index)* 返回索引指定的元素
* 获取数量
  * *array.size()* 返回集合中的元素个数 

# 继承

## 特点

* 子类只能继承一个父类，而父类可以有多个子类

## this

* this.成员变量 访问本类成员变量
* this(...) 访问本类构造方法   **1.this()不能使用在普通方法中，只能在本类的构造方法中使用。2.必须是构造方法中的第一条语句**
* this.成员方法 访问本类成员方法

## super

* super.成员变量 访问父类成员变量
* super(...) 访问父类构造方法  
* super.成员方法 访问父类成员方法

## 构造方法访问特点

* 子类中的所有构造方法都会访问父类的无参构造方法
  * 子类会继承父类的数据，可能还会使用父类的数据，所以，子类初始化之前，一定要完成父类数据的初始化
    * 每一个子类的构造方法的第一条语句默认是：super()

## 方法重写

* 子类中出现了和父类中一模一样的方法声明

# 修饰符

## 权限修饰符

|  修饰符   | 同一个类 | 同一个包中子类和无关类 | 不同包中子类 | 不同包的无关类 |
| :-------: | :------: | :--------------------: | :----------: | :------------: |
|  private  |    √     |                        |              |                |
|   默认    |    √     |           √            |              |                |
| protected |    √     |           √            |      √       |                |
|  public   |    √     |           √            |      √       |       √        |

## 状态修饰符

* final(最终态)
  * 修饰方法：表明该方法是最终方法，不能被重写
  * 修饰变量：表明该变量是常量，不能被再次赋值
  * 修饰类：表明该类是最终类，不能被继承 
  * 修饰局部变量
    * 变量是基本类型：final修饰指的是基本类型的**数据值**不能发生改变
    * 变量是引用类型：final修饰指的是引用类型的**地址值**不能发生改变，但是地址里面的内容是可以发生改变的
* static(静态)
  * 修饰成员变量：
    * 被类的所有对象共享
    * 可以通过类名调用
  * 特点
    * 静态成员方法只能访问静态成员

# 多态

## 前提

* 有继承/实现关系
* 有方法重写
* 有父类引用指向子类对象

## 多态中成员访问特点

* 成员变量：编译看左边，执行看左边

* 成员方法：编译看左边，执行看右边
  * 因为成员方法有重写，而成员变量没有

## 多态的好处和弊端

* 多态的好处：提高了程序的扩展性
  * 具体体现：定义方法的时候，使用父类型作为参数，将来在使用的时候，使用具体的子类型参与操作
* 多态的弊端：不能使用子类的特有功能

## 多态转型

* 向上转型

  ```java
  Animal a=new Cat(); 向上转型
  ```

* 向下转型(类似强制转型)

  ```java
  Cat c=(Cat)a;向下转型
  ```

# 抽象类

## 抽象类特点

* 抽象类和抽象方法必须使用**abstract**关键字修饰
  * *public **abstract** class 类名{};*
  * *public **abstract** void eat();*
* 抽象类中不一定有抽象方法，有抽象方法的类一定是抽象类
* 抽象类不能实例化
  * 抽象类实例化参照多态方式，通过子类对象实例化，这叫抽象类多态
* 抽象类的子类
  * 要么重写抽象类中的所有抽象方法
  * 要么是抽象类

## 成员特点

* 成员变量
  * 可以是变量
  * 也可以是常量
* 构造方法
  * 有构造方法，但是不能实例化
  * 作用：用于子类访问父类数据的初始化
* 成员方法
  * 可以有抽象方法：限定子类必须完成某些动作
  * 也可以有非抽象方法：提高代码复用性

# 接口

## 接口特点

* 接口用关键字**interface**修饰
  * *public **interface**接口名{}*
* 类实现接口用**implements** 接口名{}
* 接口不能实例化
  * 参照多态的方式，通过实现类对象实例化，这叫接口实例化
  * 多态的形式：具体类多态，**抽象类多态**，**接口多态**
  * 多态的前提：有继承或者实现关系；有方法重写；有父(类/接口)引用指向(子/实现)类对象
* 接口的实现类
  * 要么重写接口中的所有抽象方法
  * 要么是抽象类

## 成员特点

* 成员变量
  * 只能是常量
  * 默认修饰符：*public static final*
* 构造方法
  * 接口没有构造方法，因为接口主要是对行为进行抽象的，是没有具体存在的
  * 一个类如果没有父类，默认继承自Object类
* 成员方法
  * 只能是抽象方法
  * 默认修饰符：*public abstract*

## 类和接口的关系

* 类和类的关系
  * 继承关系，只能单继承，但是可以多层继承
* 类和接口的关系
  * 实现关系，可以单实现，也可以多实现，还可以在继承一个类的同时实现多个接口
* 接口和接口的关系
  * 继承关系，可以单继承，也可以多继承

## 抽象类和接口的区别

* 成员区别
  * 抽象类
    * 常量，变量，有构造方法，有抽象方法，也有非抽象方法
  * 接口
    * 常量，抽象方法
* 关系区别
  * 类与类
    * 继承，单继承
  * 类与接口
    * 实现，可以单实现，也可以多实现
  * 接口与接口
    * 继承，单继承，多继承
* 设计理念区别
  * 抽象类
    * 对类抽象，包括属性，行为
  * 接口
    * 对行为抽象，主要是行为
* **抽象类是对事物的抽象，接口是对行为的抽象**

# 形参和返回值

## 类名作为形参和返回值

* 方法的形参是类名，其实需要的是该类的对象
* 方法的返回值是类名，其实返回的是该类的对象

## 抽象类名作为形参和返回值

* 方法的形参是抽象类名，其实需要的是该抽象类的子类对象
* 方法的返回值是抽象类名，其实返回的是该抽象类的子类对象

## 接口名作为形参和返回值

* 方法的形参是接口名，其实需要的是该接口的实现类对象
* 方法的返回值是接口名，其实返回的是该接口的实现类对象

# 内部类

* 在类中定义一个类

## 访问特点

* 内部类可以直接访问外部类的成员，包括私有
* 外部类要访问内部类的成员，必须创建对象

## 成员内部类

* 在类的成员位置：成员内部类
  * 成员内部类用**private**修饰，外部类创建一个成员方法，方法体内创建内部类对象，调用内部类方法
  * 运行直接创建外部类，调用成员方法
* 在类的局部位置：局部内部类
  * 局部内部类是成员方法中定义的类，外界是无法直接使用，需要在方法内部创建对象并使用
  * 该类可以直接访问外部类成员，也可以访问方法内的局部变量

## 匿名内部类

* 匿名内部类也是局部内部类

* 继承了该类或者实现了该接口的子类匿名对象

# 常用API

 ## Math

* 没有构造方法
* 成员方法用**static**修饰，可以直接用类名调用

### 常用方法

* *int abs(int a)* 返回绝对值
* *double ceil(double a)* 返回大于或等于参数的最小double值，等于一个整数
* *double floor(double a)* 返回小于或等于参数的最大double值，等于一个整数
* *int round(float a)* 按照四舍五入返回最接近参数的int
* *int max(int a,int b)* 返回两个int中最大值
* *int min(int a,int b)* 返回两个int中最小值
* *double pow(double a,double b)* 返回a的b次冥
* *double random()* 返回值为double的正值,[0.0--1)

## System

* System包含几个有用的类字段和方法，它不能被实例化

### 常用方法

* *exit(int status)* 终止当前运行的Java虚拟机，非零表示异常终止
* *currentTimeMillis()* 返回当前时间(以毫秒为单位)

## Object

* Object是类层次结构的根，每个类都可以将object作为超类，所有类都直接或者间接的继承自该类

### 构造方法

* *public Object()*

### 常用方法

* *equals()* 比较对象是否相等，默认比较地址，重写可以比较内容，自动生成
* *toString()* 返回对象的字符串表示形式。建议所有子类重写该方法，自动生成

## Arrays

* Arrays类包含用于操作数组和各种方法
  * *toString(int[] a)* 返回指定数组的内容的字符串表示形式
  * *sort(int[] a)* 按照数字顺序排列指定的数组

## 工具类设计思想

* 构造方法用*private* **防止外界创建对象**
* 成员用 *public static* **用类名访问成员方法**

## 基本类型包装类

* 将基本数据类型封装成对象的好处在于可以在对象中定义更多的功能方法操作该数据

* 常用的操作之一：用于基本数据类型与字符串之间的转换

  

| 基本数据类型 |  包装类   |
| :----------: | :-------: |
|     byte     |   Byte    |
|    short     |   Short   |
|     int      |  Integer  |
|     long     |   Long    |
|    float     |   Float   |
|    double    |  Double   |
|     char     | Character |
|   boolean    |  Boolean  |

### Integer类的概述和使用

* Integer：包装一个对象中的原始类型int的值

  | 方法名                                  | 说明                                   |
  | --------------------------------------- | -------------------------------------- |
  | *public Integer(int value)*             | 根据int值创建Integer对象(**过时**)     |
  | *public Integer(String s)*              | 根据String 值创建Integer对象(**过时**) |
  | *public static Integer value(int i)*    | 返回表示指定的int值的Integer实例       |
  | *public static Integer value(String s)* | 返回一个保存指定值的Integer对象String  |

### int 和String的相互转换

* 基本类型包装类的最常见操作就是：用于基本类型和字符串之间的相互转换

#### int 转换为String

* *public static String **valueOf(int i)***：返回int参数的字符串表示形式，该方法是String类中的方法

#### String 转换为 int

* *public static int **parseInt(String s)***：将字符串解析为int类型，该方法是integer类中的方法

### 自动装箱和拆箱

* 装箱：把基本数据类型转换为对应的包装类类型

  ```java
  Integer i=Integer.valueOf(100);
  Integer i=100;//自动装箱
  ```

* 拆箱：把包装类类型转换为对应的基本数据类型

  ```java
  i=i+200;		  //自动拆箱
  i=i.intValue()+200;
  ```

* **在使用包装类类型的时候，如果做操作，最好先判断是否为null**
  
  * **只要是对象，在使用前就必须进行不为null的判断**

## Date

* Date代表了一个特定的事件，精确到毫秒
  * *public Date()* 分配一个Date对象，并初始化，以便它代表它被分配的事件，精确到毫秒
  * *public Date(long date)* 分配一个Date对象，并将其初始化为表示从标准基准事件起指定的毫秒数

### 常用方法

* *public long getTime()* 获取的是日期对象从1970年1月1日00:00:00到现在的毫秒数
* *public void setTime(long time)* 设置事件，给的是毫秒数

 ### SimpleDateFormat类

* *SimpleDateFormat*是一个具体类，用于以区域设置敏感的方式**格式化和解析日期**

* 日期和时间格式由日期和时间模式字符串指定，在日期和时间模式字符串中，从*‘A’*到*'Z'*以及从*'a'*到*'z'*引号的字母被解析为表示日期或时间字符串的组件的模式字母

  

|  y   |  年  |
| :--: | :--: |
|  M   |  月  |
|  d   |  日  |
|  H   |  时  |
|  m   |  分  |
|  s   |  秒  |

#### 构造方法

* *public SimpleDateFormat()* 构造一个public SimpleDateFormat,使用默认模式和日期格式
* *public SimpleDateFormat(String pattern)* 构造一个public SimpleDateFormat使用给定的模式和默认的日期格式

#### 格式化和解析日期

* 格式化(从 **Date** 到 **String**)
  * *public final String **format(Date date)***：将日期格式转化成日期/时间字符串
* 解析(从 **String** 到 **Date**)
  * *public Date **parse(String source)***：从给定字符串的开始解析文本以生成日期

### Canlender类

* Calender 为某一时刻和一组日历字段之间的转换提供了一些方法，并为操作日历字段提供了一些方法
* Calender 提供了一个类方法**getInstance**用于获取Canlender对象，其日历字段已使用当前日期和时间初始化
  * ***Canlender rightNow=Canlender.getInstance();***

#### 常用类

* *public int get(int field)* 返回给定日历字段的值
* *public abstract void add(int field,int amount)* 根据日历的规则，将指定的时间量添加或减去给定的日历字段
* *public final void set(int year,int month,int date)* 设置当前日历的年月日

# 异常

* 程序出现了不正常的情况

## 异常体系

* Throwable
  * Error
  * Exception
    * RuntimeException
    * 非RuntimeException

```
Error：严重问题，不需要处理
Exception：称为异常类，它表示程序本身可以处理问题
	RuntimeException：在编译期是不检查的，出现问题后，需要我们回来修改代码
	非RuntimeException：编译期就必须处理的，否则程序不能通过编译了，就更不能正常运行了
```

## Throwable成员方法

* *public String getMessage()* 返回此throwable的详细消息字符串
* *public String toString()* 返回此可能抛出的简短描述
* *public void printStackTrace()* 把异常的错误信息输出在控制台

## 自定义异常

* 定义一个类继承**Exception**，创建无参、带参构造方法，带参构造方法传入一个**String meassage**，方法体内把参数传给父类**super(meassage)**

# 集合

* 集合的特点：提供一种存储空间可变的存储模型，存储的数据类容量可以随时发生改变

## Collection(单列)

* 是单例集合的顶层接口，它表示一组对象，这些对象也称为Collection的元素
* JDK不提供此接口的任何直接实现，它提供更具体的子接口(如**Set**和**List**)实现

### 常用方法

* *boolean add(E e)* 添加元素
* *boolean remove(Object o)* 从集合中移除指定的元素
* *void clear()* 清空集合中的元素
* *boolean contains(Object o)* 判断集合中是否存在指定的元素
* *boolean isEmpty()* 判断集合是否为空
* *int size()* 集合的长度，也就是集合中元素的个数 

### 遍历

Iterator：迭代器，集合的专用遍历方式

* Iterator<E> iterator()：返回此集合的迭代器，通过集合的iterator()方法得到
* 迭代器是通过集合的iterator()方法得到的，所以我们说它是依赖于集合而存在的

Iterator中常用的方法

* ***E next()***：返回迭代中的下一个元素
* ***boolean hasNext()***：如果迭代具有更多元素，则返回true

## List(可重复)

* 有序集合(也称为序列)，用户可以精确控制列表中每个元素的插入位置，用户可以通过整数索引访问元素，并搜索列表中的元素
* 于Set集合不同，列表通常允许重复的元素

### 特点

* 有序：存储和取出的元素顺序一致
* 可重复：存储的元素可以重复

### 常用方法

* 增加数据
  * *array.add(String s);*
  * *array.add(int index,String s);*
* 删除数据
  * *array.remove(Object o)* 删除指定元素，返回删除成功
  * *array.remove(int index)* 删除指定索引位置，返回被删除的元素

* 修改数据
  * *array.set(int index,E element)* 修改指定索引位置的元素，返回被修改的元素
* 获取数据
  * *array.get(int index)* 返回索引指定的元素
* 获取数量
  * *array.size()* 返回集合中的元素个数 

### ListIterator

**列表迭代器**

* 通过List集合的listiterator()方法得到，所以说它是List集合特有的迭代器
* 用于允许程序员沿任一方向遍历列表的列表迭代器，在迭代器期间修改列表，并获取列表中迭代器的当前位置

#### 常用方法

* *E next()*：返回迭代中的下一个元素
* *boolean hasNext()*：如果迭代具有更多元素，则返回true
* *E previous()*：返回列表中的上一个元素
* *boolean hasPrevious()*：如果此列表迭代器在相反方向遍历列表时具有更过元素，则返回true
* *void add(E e)*：将指定的元素插入列表

### LinkedList集合的特有功能

* *public void addFirst(E e)* 在该列表头插入指定的元素
* *public void addLast(E e)* 将指定的元素追加到此列表的末尾
* *public E getFirst()* 返回此列表中的第一个元素
* *public E getLast()* 返回此列表中的最后一个元素
* *public E removeFirst()* 此列表中删除并返回第一个元素
* *public E removeLast()* 此列表中删除并返回最后一个元素

### List集合子类特点

List集合常用子类：ArrayList、LinkedList

* ArrayList：底层数据结构是数组，查询快，增删慢
* LinkedList：底层数据结构是链表，查询慢，增删快

## Set(不可重复)

### 特点

* 不包含重复元素的集合
* 没有带索引的方法，所以不能使用普通for循环遍历

### HashSet

* 底层数据结构是哈希表
* 对集合的迭代顺序不做任何保证
* 没有带索引的方法，所以不能使用普通for循环遍历
* 由于是Set集合，所以不包含重复元素的集合

### 哈希值

* ***是JDK根据对象的地址或者字符串或者数字算出来的int类型的数值***
* *Object类中有一个方法可以获取**对象的哈希值***
  * *public int hashCode()*: 返回对象的哈希码值
* 对象的哈希值特点
  * 同一个对象多次调用hashCode()方法返回的哈希值是相同的
  * 默认情况下，不同对象的哈希值是不同的。而重写hashCode()方法，可以实现不同对象的哈希值相同

### 如何保证唯一性

HashSet原理

* 我们使用Set集合都是需要去掉重复元素的, 如果在存储的时候逐个equals()比较, 效率较低,哈希算法提高了去重复的效率, 降低了使用equals()方法的次数
* 当HashSet调用add()方法存储对象的时候, 先调用对象的hashCode()方法得到一个哈希值, 然后在集合中查找是否有哈希值相同的对象
  * 如果没有哈希值相同的对象就直接存入集合
  * 如果有哈希值相同的对象, 就和哈希值相同的对象逐个进行equals()比较,比较结果为false就存入, true则不存
* 将自定义类的对象存入HashSet去重复
  * 类中必须重写hashCode()和equals()方法
    * hashCode(): 属性相同的对象返回值必须相同, 属性不同的返回值尽量不同(提高效率)
    * equals(): 属性相同返回true, 属性不同返回false,返回false的时候存储

### LinkedHashSet

* 特点
  * 哈希表和链表实现的Set接口，具有可预测的迭代次序
  * 由链表保证元素有序，也就是说元素的存储和取出顺序是一致的
  * 由哈希表保证元素唯一，也就是说没有重复的元素

### TreeSet

* 特点
  * 元素有序，这里的顺序不是指存储和取出的顺序，而是按照一定的规则进行排序，具体排序方式取决于构造方法
    * *TreeSet()*：根据其元素的自然排序进行排序
    * *TreeSet(Coomparator comparator)*：根据指定的比较器进行排序
  * 没有带索引的方法，所以不能使用普通for循环遍历
  * 由于Set集合，所以不包含重复元素的集合

#### 自然排序Comparable的使用

* 用TreeSet集合存储自定义对象，**无参**构造方法使用的是**自然排序**对元素进行排序
* 自然排序，就是**让元素所属的类实现Comparable接口，重写comparaTo(T o)方法**
* 重写方法时，一定要注意排序规则必须按照要求的主要条件和次要条件来写

#### 比较器排序Comparator的使用

* 用TreeSet集合存储自定义对象，**带参**构造方法使用的是**比较器排序**对元素进行排序
* 比较器排序，就是**让集合构造方法接收Comparator的实现类对象，重写comparaTo(T o1,T o2)方法**
* 重写方法时，一定要注意排序规则必须按照要求的主要条件和次要条件来写

## 泛型

* 是JDK5中引入的特性，它提供了编译时类型安全检测机制，该机制允许在编译时检测到非法的类型
* 参数化类型：
  * **将类型由原来的具体的类型参数化，然后在使用/调用时传入具体的类型**
* 这种参数类型可以用在类、方法、和接口中，分别被称为泛型类、泛型方法、泛型接口

### 泛型定义格式

* *<类型>*：指定一种类型的格式，这里的类型可以看成是形参
* *<类型1,类型2...>*：指定多种类型的格式，多种类型之间用逗号隔开。这里的类型可以看成是形参
* 将来具体调用时候给定的类型可以看成是实参，并且实参的类型只能是引用类型数据

#### 泛型类定义格式

* *修饰符 class 类名 <类型>{}*；

#### 泛型方法定义格式

* *修饰符 <类型> 返回值类型 方法名(类型 变量名){}*

#### 泛型接口定义格式

* *修饰符 interface 接口名<类型>{}*

#### 类型通配符

为了表示各种泛型List的父类，可以使用通配符

* 类型通配符：**<?>**
* List<?>：表示元素类型未知的List，它的元素可以匹配**任何的类型**
* 这种通配符的List仅表示它是各种泛型List的父类，并不能把元素添加到其中
  * 类型通配符上限：**<? extends 类型>**
  * List<? extends Number>：它表示的类型是**Number或者其子类型**
  * 类型通配符上限：**<? super 类型>**
  * List<? super Number>：它表示的类型是**Number或者其父类型**

## 可变参数

可变参数又称参数个数可变，用作方法的形参出现

* 格式：**修饰符 返回值类型 方法名(数据类型... 变量名){}**
* **注意**：如果一个方法里面有多个参数，可变参数放在最后

## 可变参数的使用

Arrays工具类中有一个静态方法

* *public static <T> List<T> asList(T... a)*：返回由指定数组支持的固定大小的列表
* 返回的集合不能做增删操作，可以做修改操作

List接口中有一个静态方法

* *public static <E> List<E> of(E... elements)*：返回包含任意数量元素的不可变列表
* 返回的集合不能做增删改操作

Set接口中有一个静态方法

* *public static <E> Set<E> of(E... elements)*：返回一个包含任意数量元素的不可变集合
* 在给元素的时候，不能给重复的元素
* 返回的集合不能做增删改操作，没有修改方法，因为Set集合没有索引

## Map(双列)

* Map简介
  * **HashMap：**它根据键的hashCode值存储数据，大多数情况下可以直接定位到它的值，因而具有很快的访问速度，但遍历顺序却是不确定的。HashMap **最多允许一条记录的键为null，允许多条记录的值为null**。HashMap 是非线程安全的，即任一时刻有多个线程同时写 HashMap ，可能会导致数据不一致，。如果要满足线程安全，可以使用 Collections 的 SynchronizedMap 方法 或者使用 ConcurrentHashMap
  * **HashTable：**HashTable 是遗留类，很多常用的功能与 HashMap 类似，不同的是它继承 Dictionary，并且是线程安全的，任一时刻只能有一个线程写 HashTable，并发性不如 ConcurrentHashMap，因为 ConcurrentHashMap 引入了分段锁。HashTable 不建议在新代码中使用，不需要线程安全的场合使用 HashMap，需要线程安全的场合使用 ConcurrentHashMap
  * **LinkedHashMap：**LinkedHashMap 是 HashMap 的一个子类，保存了记录的插入顺序，在用 iterator 遍历 LinkedHashMap 时，先得到的记录肯定是先插入的，也可以在构造时带参数，按照访问次序排序
  * **TreeMap：**它实现了 SortedMap 接口，能够把保存的记录根据键（key）排序，默认是按照键值的升序排序，也可以指定排序的比较器，当用 iterator 遍历时得到的记录是排过序的。如果使用排序的映射，建议使用 TreeMap，在使用 TreeMap 时，key 必须实现 Comparable 接口或者在构造 TreeMap 传入自定义的 Comparator，否则会在运行时抛出 java.lang.ClassCastException 类型的异常

![](.\img\50.jpg)

* Map集合概述
  * Interface Map<K,V> K：键的类型；V：值的类型
  * 将键映射到值得对象；不能包含重复的键；每个键可以映射最多一个对象
* 创建Map集合得对象
  * 多态的方式
  * 具体得实现类HashMap

### HashMap底层实现原理

> * `HashMap`的内部存储结构是**数组+链表+红黑树**的结合，当实例化一个HashMap时，会初始化`loadFactor`，在`put`第一对映射关系时，系统会创建一个长度为`initialCapacity(16)`的`Node`数组，这个长度在哈希表中被称为容量(`Capacity`)，在这个数组中可以存放元素的位置我们称之为“桶”(`bucket`)，每个`bucket`都有自己的索引，系统可以根据索引快速的查找`bucket`中的元素
>
> * 每个`bucket`中存储一个元素，即一个`Node`对象，但每一个`Node`对象可以带一个引用变量`next`，用于指向下一个元素，因此，在一个桶中，就有可能生成一个`Node`链。也可能是一个一个`TreeNode`对象，每一个`TreeNode`对象可以有两个叶子结点`left`和`right`，因此，在一个桶中，就有可能生成一个`TreeNode`树。而新添加的元素作为链表的`last`，或树的叶子结点。
> * 当`HashMap`中的元素个数超过`数组大小(数组总大小length,不是数组中个数size)*loadFactor`时，就会进行数组扩容，`loadFactor`的默认值(DEFAULT_LOAD_FACTOR)为`0.75`，这是一个折中的取值。也就是说，默认情况下，数组大小(DEFAULT_INITIAL_CAPACITY)为16，那么当HashMap中元素个数超过`16*0.75=12`（这个值就是代码中的`threshold`值，也叫做临界值）的时候，就把数组的大小扩展为`2*16=32`，即扩大一倍，然后重新计算每个元素在数组中的位置，而这是一个非常消耗性能的操作，所以如果我们已经预知`HashMap`中元素的个数，那么预设元素的个数能够有效的提高`HashMap`的性能。
> * 当HashMap中的其中一个链的对象个数如果达到了`8`个，此时如果capacity没有达到`64`，那么HashMap会`先扩容解决`，如果已经达到了`64`，那么这个链会变成红黑树，结点类型由Node变成TreeNode类型。当然，如果当映射关系被移除后，下次`resize`方法时判断树的结点个数低于`6`个，也会把红黑树再转为链表。

### HashMap源码解析

```java
//创建无参构造方法，初始化负载因子(0.75f)
public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR;
}

//向HashMap中put一个元素，实际调用的是putVal()方法，调用putVal()方法前会先调用hash()方法，根据key计算对应的hash值
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

//根据key计算hash值
static final int hash(Object key) {
    int h;
    //键key 转换成 哈希码（hash值）操作 = 使用hashCode() + 1次位运算 + 1次异或运算（2次扰动）
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);//当key = null时，hash值 = 0，所以HashMap的key 可为null  
}

//向HashMap中put元素实际调用的方法
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    //进入方法后先判断Node数组(table)的容量，如果是第一次put元素，table数组一定为空(因为JDK1.8版本，创建构造方法时，没有初始化Node数组的大小，而是在第一次put元素时初始化容量)，就调用resize()扩容方法，返回数据的默认长度(16)，如果不是第一次put元素，n = tab.length
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    //根据上一个方法返回的数组长度n-1，再与运算key的hash值，得到数组的下标i，根据下标i在数组中拿到所对应的值，判断是否为空，如果为空代表此下标在数组中，没有数据，可以直接插入，如果不为空，代表hash冲突，就往下继续运行
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        //拿数组中已存在数据的hash值与要插入值的hash值做比对，再调用equals()方法比对，如果都为真，表示插入的值与数组中存在的值相同，把原有值赋值给e(下面需要用到)；如果有一个不为真，代表插入的值与原有值不相同，继续执行下面的方法
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        //判断原有值是否为红黑树
        else if (p instanceof TreeNode)
            //如果为红黑树就调用putTreeVal()方法向红黑树中插入值
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {//原有值为链表
            for (int binCount = 0; ; ++binCount) {
                //判断原有值的next是否为空
                if ((e = p.next) == null) {
                    //如果为空，直接把要插入的值赋值给原有值的next中
                    p.next = newNode(hash, key, value, null);
                    //赋值完成后，判断链表的大小是否大于8(如果链表大于8，就需要转红黑树)
                    if (binCount >= TREEIFY_THRESHOLD - 1)
                        treeifyBin(tab, hash);//转红黑树操作
                    break;
                }
                //如果上面条件不为空，说明原有值的next有数据，就又需要新值与旧值比对hash值和equals，如果为真，代表新值与旧值相同，跳出循环
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                //更新p指向下一个节点，继续遍历
                p = e;
            }
        }
        //发现key已存在，直接用新value覆盖旧value，并返回旧value
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    //插入成功后，判断实际存在的键值对数量是否大于临界值，如果大于就需要扩容
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}

//扩容方法
final Node<K,V>[] resize() {
        Node<K,V>[] oldTab = table;//扩容前的数组
        int oldCap = (oldTab == null) ? 0 : oldTab.length;//扩容前的长度
        int oldThr = threshold;//扩容前的临界值
        int newCap, newThr = 0;
        //判断数组容量是否大于0，大于0说明数组已经初始化过
        if (oldCap > 0) {
            //判断当前数组长度是否大于最大数组长度
            if (oldCap >= MAXIMUM_CAPACITY) {
                //如果是，将扩容阈值直接设置为int类型的最大数值并直接返回
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            //如果没有超过最大值，就扩充为原来的2倍
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
    	//无参构造方法的临界值默认为0，如果调用有参构造方法，传入了initialCapacity参数，就会计算出threshold值
        else if (oldThr > 0)
            newCap = oldThr;
        else {//此处就是默认无参构造方法，初始数组容量为16，临界值为12
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) {
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```

### 常用方法

* *V put(K key,V value)*：添加元素

  ![](.\img\51.jpg)

* *V remove(Object key)*：根据键删除键值对元素，返回的是键值对的值

* *void clear()*：移除所有的键值对元素

* *boolean containsKey(Object key)*：判断集合是否包含指定的键

* *boolean containsValue(Object value)*：判断集合是否包含指定的值

* *boolean isEmpty()*：判断集合是否为空

* *int size()*：集合的长度，也就是集合中键值对的个数

### 获取功能

* *V get(Object key)*：根据键获取值
* *Set<K> keySet()：获取所有键的集合*
* *Collection<V> values()：获取所有值的集合*
* *Set<Map.Entry<K,V> entrySet()*：获取所有键值对对象的集合

## Collections(针对集合操作的工具类)

### 特点

* 所有成员都用static修饰，所以可以直接用类名调用
* **Collections.synchronizedList/Map/Set();** 创建线程安全的

### 常用方法

* *public static <T extends Comparable<? super T>> void sort(List<T> list)*：将指定的列表按升序排序
* *public static void reverse(List<?> list)*：反转指定列表中元素的顺序
* *public static void shuffle(List<?> list)*：使用迷人的随机源随机排列指定的列表

# IO流

IO流分类：

* 按照数据的流向
  * 输入流：读数据
  * 输出流：写数据
* 按照数据类型来份
  * 字节流
    * 字节输入流；字节输出流
  * 字符流
    * 字符输入流；字符输出流
* 使用情况分类
  * 如果数据通过Window自带的记事本软件能打开，我们还可以**读懂里面的内容**，就使用字符流，否则使用字节流。如果不知道该使用那种类型的流，就使用字节流

## File类概述

File：它是文件和目录路径名的抽象表示

* 文件和目录是可以通过File封装成对象的
* 对于Flie而言，其封装的并不是一个真正存在的文件，仅仅是一个路径名而已。它可以是存在的，也可以是不存在的。将来是要通过具体的操作把这个路径的内容转换为具体存在的。

### 构造方法

* *File(String pathname)*：通过将给定的路径名字符串转换为抽象路径名来创建新的File实例
* *File(String parent,String child)*：从父路径名字符串和子路径名字符串创建新的File实例
* *File(File parent,String child)*：从父抽象路径名和子路径名字符串创建新的File实例

### 创建功能

* *public boolean createNewFile()*：当具有该名称的文件不存在时，创建一个由该抽象路径名命名的新空文件
  * 如果文件不存在，就创建文件，并返回**true**
  * 如果文件存在，就不创建文件，并返回**false**

* *public boolean mkdir()*：创建由此抽象路径命名的目录
  * 如果目录不存在，就创建目录，并返回**true**
  * 如果目录存在，就不创建目录，并返回**false**
* *public boolean mkdirs()*：创建由此抽象路径命名的目录，包括任何必须但不存在的父目录

### 判断和获取功能

* *public boolean isDirectory()*：测试此抽象路径名表示的File是否为目录
* *public boolean isFile()*：测试此抽象路径名表示的File是否为文件
* *public boolean exists()*：测试此抽象路径名表示的File是否存在
* *public String getAbsolutePath()*：返回此抽象路径名的绝对路径名字符串
* *public String getPath()*：将此抽象路径名转换为路径名字符串
* *public String getName()*：返回由此抽象路径名表示的文件或目录名称
* *public String[] list()*：返回此抽象路径名表示的目录中的文件和目录的名称字符串数组
* *public File[] listFiles()*：返回此抽象路径名表示的目录中的文件和目录的File对象数组

### 删除功能

* *public boolean delete()*：删除由此抽象路径名表示的文件或目录
  * **注意**：如果一个目录中有内容(目录，文件)，不能直接删除，应该先删除目录中的内容，最后才能删除目录

## 字节流抽象基类

* *InputStream*：这个抽象类是表示字节输入流的所有类的超类
* *OutputStream*：这个抽象类是表示字节输出流的所有类的超类
* 子类名特点：子类名称都是以其父类名作为子类名的后缀

## 字节流写数据

**FileOutputStream：文件输出流用于将数据写入File**

* *FileOutputStream(String name)*：创建文件输出流以指定的名称写入文件

**使用字节流输出流写数据的步骤：**

* 创建字节输出流对象(调用系统功能创建了文件，创建字节输出流对象，让字节输出流对象指向文件)
* 调用字节输出流对象的写数据方法
* 释放资源(关闭此文件输出流并释放与此流相关联的任何系统资源)

**字节流写数据的三种方式**

* *void write(int b)*：将指定的字节写入此文件输出流，一次写一个字节数据
* *void write(byte[] b)*：将b.length字节从指定的字节数组写入此文件输出流，一次写一个字节数组数据
* *void write(byte[] b,int off,int len)*：将len字节从指定的字节数组开始，从偏移量off开始写入此文件输出流，一次写一个字节数组的部分数据

**字节流写数据的两个小问题**

* 换行
  * 写完数据后，加换行符
    * windows:**\r\n**
    * linux:**\n**
    * mac:**\r**

* 追加写入
  * *public FileOutputStream(String name,boolean append)*
  * 创建文件输出流以指定的名称写入文件。如果第二个参数为**true**，则字节将写入文件的末尾而不是开头

## 字节流读数据

**FileInputStream：从文件系统中的文件获取输入字节**

* *FileInputStream(String name)*：通过打开与实际文件的连接来创建一个FileInputStream，该文件由文件系统中的路径名name命名

**使用字节输入流读数据的步骤**

* 创建字节输入流对象
* 调用字节输入流对象的读数据方法
* 释放资源

**字节流读数据的三种方式**

* *int read(int b)*：从该输入流读取一个字节的数据
* *int read(byte[] b)*：从该输入流读取最多 b.length个字节的数据到一个字节数组
* *int read(byte[] b,int off,int len)*：从该输入流读取最多 len个字节的数据到字节数组

## 字节缓冲流

* *BufferedOutputStream*：该类实现缓冲输出流。通过设置这样的输出流，应用程序可以向底层输出流写入字节，而不必为写入的每个字节导致底层系统的调用
* *BufferedInputStream*：创建BufferedInputStream将创建一个内部缓冲区数组。当从流中读取或者跳过字节时，内部缓冲区将根据需要从所包含的输入流中重新填充，一次很多字节

**构造方法**

* 字节缓冲输出流：*BufferedOutputStream(OutputStream out)*
* 字节缓冲输入流：*BufferedInputStream(InputStream in)*

**注意**：字节缓冲流**仅仅提供缓冲区**，而真正的读写数X据还是得依靠基本得字节流对象进行操作

## 字符流抽象基类

* *Reader*：这个抽象类是表示字符输入流的所有类的超类
* *Writer*：这个抽象类是表示字符输出流的所有类的超类
* 子类名特点：子类名称都是以其父类名作为子类名的后缀

## 字符流写数据

**构造方法**

* *OutputStreamWriter(OutputStream out)* ：创建一个使用默认字符集的输出字符流
* *OutputStreamWriter(OutputStream out,String charsetName)* ：创建一个使用指定字符集的输出字符流

**字符流写数据的三种方式**

* *void write(int c)*：写一个字符
* *void write(String str)*：写一个字符串
* *void write(char[] cbuf)*：写一个字符数组
* *void write(char[] cnuf,int off,int len)*：写入字符数组的一部分
* *void write(String str,int off,int len)*：写一个字符串的一部分

**注意**

* *void flush()*：刷新流，还可以写数据
* *void close()*：关闭流，释放资源，关闭流之前会刷新流，关闭后不能写数据

### 改进版

* *FileWriter(String fileName)*：创建一个字符输出流
* *FileWriter(String fileName,boolean append)*：创建一个字符输出流，第二个参数**true**，可以追加写入数据

## 字符流读数据

**构造方法**

* *InputStreamReader(InputStream in)* ：创建一个使用默认字符集的输入字符流
* *InputStreamReader(InputStream in,String charsetName)* ：创建一个使用指定字符集的输入字符流

**字符流读数据的两种种方式**

* *int read()*：读一个字符
* *int read(char[] cbuf,int offset,int length)*：将字符读入数组的一部分

### 改进版

* *FileReader(String fileName)*：创建一个字符输入流

## 字符缓冲流

**字符缓冲输出流**

* *BufferedWriter(Writer out)*：将文本写入字符输出流，缓冲字符，以提供单个字符、数组和字符串的高效写入，可以指定缓冲区大小，或者可以接受默认大小。默认值足够大，可用于大多数用途

**字符缓冲输入流**

* *BufferedReader(Reader in)*：将字符输入流读取文本，缓冲字符，以提供字符、数组和行的高效读取，可以指定缓冲区大小，或者可以接受默认大小。默认值足够大，可用于大多数用途

## 字符缓冲流特有功能

**BufferedWriter**

* *void newLine()*：写一行行分隔符，行分隔符字符串由系统决定

**BufferedReader**

* *public String readLine()*：读一行文字。结果包含行的内容的字符串，不包括任何行终止字符，如果流的结尾已经到达，则为null 

## 对象序列化流

* 就是将对象保存到磁盘中，或者在网络中传输对象，这种机制就是使用一个字节序列表示一个对象，该字节序列包含：对象的类型、对象的数据和对象中存储的属性等信息，字节序列写到文件之后，相当于文件中持久保存了一个对象信息，反之，该字节序列还可以从文件中读取回来，重构对象，对它进行反序列化

### 特点

* 将Java对象的原始数据类型和图形写入OutputStream。可以使用ObjectInputStream读取(重构)对象。可以通过使用流的文件来实现对象的持久存储。如果流是网络套接字流，则可以在另一个主机上或另一个进程中重构对象

### 构造方法

* *ObjectOutputSteam(OutputStream out)*：创建一个写入指定的OutputStream的ObjectOutputSteam

### 序列化对象的方法

* *void writeObject(Object obj)*：将指定的对象写入ObjectOutputSteam

### 注意

* 一个对象要想被序列化，该对象所属的类必须实现**Serializable**接口
* Serializable是一个**标记接口**，实现该接口，不需要重写任何方法 
* 用对象序列化流序列化了一个对象后，如果修改了对象所属的类文件，会抛出**InvalidClassException**异常
  * 解决办法：给对象所属类加一个**serialVersionUID**
    * *private static final long serialVersionUID*=42L
  * 如果一个对象中的某个成员变量的值不想被序列化
    * 给该成员变量加**transient**关键字修饰，该关键字标记的成员变量不参与序列化程序

## 对象反序列化流

### 构造方法

* *ObjectInputStream(InputStream in)*：创建从指定的InputStream读取的ObjectInputStream

### 反序列化对象的方法

* *Object readObject()*：从ObjectInputStream读取一个对象

## Properties

* 是一个Map体系的集合类
* Properties可以保存到流中或从流中加载

### 特有方法

* *Object setProperty(String key,String value)*：设置集合的键和值，都是String类型，底层调用Hashtable方法put
* *String getProperty(String key)*：使用此属性列表中指定的键搜索属性
* *Set<String> stringPropertyNmaes()*：从该属性列表中返回一个不可修改的键集，其中键及其对应的值是字符串

### 与IO流结合的方法

* *void load(InputStream inStream)*：从输入字节流读取属性列表(键和元素对)
* *void load(Reader reader)*：从输入字符流读取属性列表(键和元素对)
* *void store(OutputStream out,String comments)*：将此属性列表(键和元素对)写入此Properties表中，以适合于使用load(InputStream)方法的格式写入输出字节流
* *void store(Writer writer,String comments)*：将此属性列表(键和元素对)写入此Properties表中，以适合于使用load(Reader)方法的格式写入输出字节流

# 多线程

**进程**：是正运行的程序

* 是系统进行资源分配和调用的独立单位
* 每一个进程都有它自己的内存空间和系统资源

**线程**：是进程中的单个顺序控制流，是一条执行路径

* 单线程：一个进程如果只有一条执行路径，则称为单线程程序
* 多线程：一个进程如果有多条执行路径，则称为多线程程序

## 多线程的实现方式

方式一：继承**Thread**类

* 定义一个类继承Thread,重写*run()*方法
  * run()方法是用来封装被线程执行的代码
    * *run()*：直接调用，相当于普通方法调用
    * *start()*：启动线程，然后由JVM调用此线程的run()方法

方式二：实现**Runnable**接口

* 定义一个类实现Runnable接口，重写run()方法

**注意**：相比继承Thread类，实现Runnable的好处

* 避免了Java单继承的局限性
* 适合多个相同程序的代码去处理同一个资源的情况，把线程和程序的代码、数据有效分离，较好的体现了面向对象的设计思想

方式三：实现**Callable<T>**接口

* 定义一个类实现Callable接口，重写call()方法

* **new Thread方法需要Runnable接口，FutrueTask实现了Raunable接口**

  ```java
  FutureTask<Integer> futureTask = new FutureTask<>(new MyThread());
  new Thread(futureTask,"A").start();
  ```

## 设置和获取线程名称

* *void setName(String name)*：将此线程的名称更改为等于参数name
* *String getName()*：返回此线程的名称
* 通过构造方法也可以设置线程名称
  * 获取main()方法所在的线程名称
    * *Thread.currentThread().getName()*

## 线程调度

线程有两种调度模式

* 分时调度模型：所有线程轮流使用CPU的使用权限，平均分配每个线程占用CPU的时间片
* 抢占式调度模型：优先让优先级高的线程使用CPU，如果线程的优先级相同，会随机选择一个，优先级高的线程获取的CPU时间片相对多一些
  * Java使用的是抢占式调度模型

## 设置线程优先级

* *public final int getPriority()*：返回此线程的优先级
* *public final void setPriority(int newPriority)*：更改此线程的优先级
  * 线程默认优先级是**5**；线程优先级的范围是**1-10**
  * 线程优先级高仅仅表示线程获取的CPU时间片的几率高。

## 线程控制

* *static void sleep(long millis)*：使当前正在执行的线程停留(暂停执行)指定的毫秒数
* *void join()*：等待这个线程死亡(等这个线程执行完才能继续执行其他线程)
* *void setDaemon(boolean on)*：将此线程标记为守护线程，当运行的线程都是守护线程时，Java虚拟机将退出

## 线程同步

### 同步代码块

锁多条语句操作共享数据，可以使用同步代码块实现

* 格式：

  ```java
  synchronized(任意对象){
  	多条语句操作共享数据的代码
  }
  ```

* synchronized(任意对象)：就相当于给代码加锁了，任意对象就可以看成是一把锁
* 同步的好处与弊端
  * 好处：解决了多线程的数据安全问题
  * 弊端：当线程很多时，因为每个线程都会去判断同步上的锁，很耗费资源，无形中会降低程序的运行效率

### 同步方法

同步方法：就是把synchronized关键字加到方法上

* 格式：
  * 修饰符 **synchronized** 返回值类型 方法名(方法参数){}
* 同步方法的锁对象是：**this**

同步静态方法：就是把synchronized关键字加到静态方法上

* 格式：
  * 修饰符 **static** **synchronized** 返回值类型 方法名(方法参数){}
* 同步静态方法的锁是：**类名.class**

## 线程安全的类

StringBuffer

* 线程安全，可变的字符序列
* 从版本JDK5开始，被StringBuilder替代。通常应该使用StirngBuilder类，因为它支持所有相同的操作，但它更快，因为它不执行同步

Vector

* 从Java 2开始，该类改进了List接口，使其称为Java集合体系的成员。与新的集合实现不同，Vector被同步。

Hashtable

* 该类实现了一个哈希表，它将键映射到值，任何非null对象都可以用作键或者值
* 从Java 2开始，该类进行了改进，实现了Map接口，使其称为Java集合体系的成员，与新的集合实现不同，Hashtable被同步

## Lock锁

Lock实现提供比使用synchronized方法和语句可以获得更广泛的锁定操作

Lock中提供了获得锁和释放锁的方法：

* *void lock()*：获得锁
* *void unlock()*：释放锁

Lock是接口不能直接实例化，采用它的实现类**ReentrantLock**来实例化

* *ReentrantLock()*：创建一个ReentrantLock的实例

## 生产者消费者

为了解耦生产者和消费者的关系，通常会采用共享的数据区域，就像是一个仓库

* 生产者生产数据之后直接放置在共享数据区中，并不需要关心消费者的行为
* 消费者只需要从共享数据中去获取数据，并不需要关心生产者的行为

### Object类的等待和唤醒方法

* *void wait()*：导致当前线程等待，直到另一个线程调用该对象的notify()方法或notifyAll()方法
* *void notify()*：唤醒正在等待对象监视器的单个线程
* *void notifyAll()*：唤醒正在等待对象监视器的所有线程

# 网络编程

概述：在网络通信协议下，实现网络互连的不同计算机上运行的程序间可以进行数据交换

## 网络编程三要素

* IP地址
  * 要想让网络中的计算机能够互相通信，必须为每台计算机指定一个标识号，通过这个标识号来指定要接受数据的计算机和识别发送的计算机，而IP地址就是这个标识号，也就是设备的标识
* 端口
  * 网络的通信，本质上是两个应用程序的通信，每台计算机都有很多的应用程序，IP地址可以唯一标识网络中的设备，那么端口号就可以唯一标识设备中的应用程序，也就是应用程序的标识
* 协议
  * 通过计算机网络可以使多台计算机实现连接，位于同一个网络中的计算机在进行连接和通信时需要遵守一定的规则，这些连接和通信的规则被称为网络通信协议，它对数据的传输格式、传输速率、传输步骤做了统一规定，通信双方必须同时遵守才能完成数据交换。常见的协议有UDP协议和TCP协议

## IP地址

IP地址分为两大类：

* IPv4：给每个连接在网络的主机分配一个32bit地址，按照TCP/IP规定，IP地址用二进制来标识。每个IP地址长32bit，也就是4个字节。为了方便使用，IP地址经常被写成十进制的形式，中间使用”.“分隔不同的字节
* IPv6：由于互联网的蓬勃发展，IP地址的需求量愈来愈大，但是网络地址资源有限，使得IP地址的分配越发紧张，为了扩大地址空间，通过IPv6重新定义地址空间，采用128位地址长度，每个16字节一组，分别组成十六进制数

常用命令：

* *ipconfig*：查看本机IP地址
* *ping IP地址*：检查网络是否联通
* 特殊IP地址：
  * *127.0.0.1*：是回送地址，可以代表本机地址，一般用来测试使用

## InetAddress

表示Internet协议(IP)地址

* *static InetAddress getByName(String host)*：确定主机名称的IP地址，主机名称可以是机器名称，也可以是IP地址
* *String getHostName()*：获取此IP地址的主机名
* *String getHostAddress()*：返回文本显示中的IP地址字符串

## 端口

* 端口号：用两个字节表示的整数，它的取值范围是**0~65535**。其中，0~1023之间的端口号用于一些知名的网络服务和应用，普通的应用程序需要使用1024以上的端口号。如果端口号被另外一个服务或者应用所占用，会导致当前程序启动失败

## 协议

**UDP**协议

* 用户数据报协议(User Datagram Protocol)
* UDP是无连接通信协议，即在数据传输时，数据的发送端和接收端不建立逻辑连接，当一台计算机向另外一台计算机发送数据时，发送端不会确认接收端是否存在，就会发出数据，同样接收端在收到数据时，也不会想发送端反馈是否收到数据
* 在使用UDP协议传送数据时，由于UDP的面向无连接性，不能保证数据的完整性，因此在传输重要数据时不建议使用UDP协议

**TCP协议**

* 传输控制协议(Transmission Control Protocol)
* TCP协议是**面向连接**的通信协议，即传输数据之前，在发送端和接收端建立逻辑连接，然后再传送数据，它提供了两台计算机之间**可靠无差错**的数据传输。在TCP连接中必须要明确客户端与服务端，由客户端向服务器发出连接请求，每次连接的创建都需要经过”三次握手“
* 三次握手：TCP协议中，在发送数据的准备阶段，客户端与服务端之间的三次交互，以保证连接的可靠
  * 第一次握手：客户端向服务器端发出连接请求，等待服务器确认
  * 第二次握手：服务器端向客户端回送一个响应，通知客户端收到了连接请求
  * 第三次握手：客户端再次向服务器端发送确认信息，确认连接
* 完成三次握手，连接建立后，客户端和服务端就可以开始进行数据传输了。由于这种面向连接的特性，TCP协议可以保证传输数据的安全

## UDP发送/接受数据

**发送**

* 创建发送端的Socket对象(**DatagramSocket**)
  * *DatagramSocket()*
* 创建数据，并把数据打包
  * *DatagramPacket(byte[] buf,int leng,InetAddress,int port)*
* 调用DatagramSocket对象的方法发送数据
  * *void send(DatagramPacket p)*
* 关闭发送端
  * *void close()*

------

**接收**

* 创建接收端的Socket对象(**DatagramSocket**)
  * *DatagramSocket(int port)*
* 创建一个数据包，用于接受数据
  * *DatagramPacket(byte[] buf,int lengt)*
* 调用DatagramSocket对象的方法接受数据
  * *void receive(DatagramPacket p)*
* 解析数据包，并把数据在控制台显示
  * *byte[] getData()*：返回数据缓冲区
  * *int getLength()*：返回要发送的数据的长度或者接收到的数据的长度
* 关闭接收端
  * *void close()*

## TCP发送/接受数据

**发送**

* 创建客户端的Socket对象(**Socket**)
  * *Socket(String host,int port)*
* 获取输出流，写数据
  * *OutputStream getOutputStream()*
    * *BufferedWriter bw=new BufferedWriter(new OutputStreamWriter(sk.getOutputStream()));*
    * **适用于字符操作**
* 释放资源
  * *void colse()*

**接收**

* 创建服务器端的Socket对象(**ServerSocket**)
  * *ServerSocket(int port)*
* 监听客户端连接，返回一个Socket对象
  * *Socekt accept()*
* 获取输入流，读数据，并把数据显示在控制台
  * *InputStream getInputStream()*
    * *BufferedReader br=new BufferedReader(new InputStreamReader(accept.getInputStream()));*
    * **适用于字符操作**
* 释放资源
  * *void close()*

**注意**：结束标记使用：*shutdownOutput()*方法

# Lambda表达式

组成Lambda表达式的三要素：**形式参数，箭头，代码块**

## 标准格式

* 格式：**(形式参数)->{代码块}**
* 形式参数：如果有多个参数，参数之间用逗号隔开。如果没有参数，留空
* ->：由英文中画线和大于符号组成，固定写法。代表指向动作
* 代码块，是我们具体要做的事情，方法体

---

使用前提：

* **有一个接口**
* **接口中有且仅有一个抽象方法**

## 省略模式

* 参数类型可以省略。但是有多个参数的情况下，不能只省略一个
* 如果参数有且仅有一个，那么小括号可以省略
* 如果代码块的语句只有一条，可以省略大括号和分号，甚至式return

## Lambda表达式和匿名内部类的区别

所需类型不同

* 匿名内部类：可以是接口，也可以是抽象类，还可以是具体类
* Lambda表达式：只能是接口

使用限制不同

* 如果接口中有且仅有一个抽象方法，可以使用Lambda表达式，也可以使用匿名内部类
* 如果接口中多于一个抽象方法，只能使用匿名内部类，而不能使用Lambda表达式

实现原理不同

* 匿名内部类：编译之后，产生一个单独的.class字节码文件
* Lambda表达式：编译之后，没有单独的.class字节码文件。对应的字节码会在运行的时候动态生成

# 接口组成更新

接口的组成：

* 常量
* 抽象方法
* 默认方法(Java 8)
  * 格式：**default** 返回值类型 方法名(参数列表){  }
  *  默认方法不是抽象方法，不强制被重写，但也可以被实现类重写
* 静态方法(Java 8)
  * 格式：**static** 返回值类型 方法名(参数列表){ }
  * 静态方法只能通过接口名调用，不能通过实现类或者对象名调用
* 私有方法(Java 9)
  * 格式一：**private** 返回值类型 方法名(参数列表){ }
  * 格式二：**private** static 返回值类型 方法名(参数列表){ }
    * **注意**
      * 默认方法可以调用私有的静态方法和非静态方法
      * 静态方法只能调用私有的静态方法

# 方法引用

方法引用符：

* **::**  该符号为引用运算符，而它所在的表达式被称为方法引用

## Lambda表达式支持的方法引用

常见的引用方式

* 引用类方式：就是引用类的静态方法
  * 格式：**类名::静态方法**
* 引用对象的实例方式
  * 格式：**对象::成员方法**
* 引用类的实例方式
  * 格式：**类名::成员方法**
* 引用构造器
  * 格式：**类名::new**

# 函数式接口

**函数式编程：可推导，就是可省略**

注解：**@FunctionalInterface**

## 函数式接口作为方法的返回值

```java
private static Comparator<String> getComparator(){
	return (s1,s2)->s1.length()-s2.length();
}
```

## 常用的函数式接口

### Supplier 供给型接口

Supplier<T> ：包含一个无参的方法

* *T get()*：获得结果
* 该方法不需要参数，它会按照某种实现逻辑(由Lambda表达式实现)返回一个数据
* Supplier<T> 接口也被称为生产型接口，如果我们指定了接口的泛型是什么类型，那么接口中的get()方法就会生产什么类型的数据提供我们使用

### Consumer 消费型接口

Consumer<T> ：包含两个方法

* *void accept(T t)*：对给定的参数执行操作
* *default Consumer<T> andThen(Consumer after)*：返回一个组合的Consumer，依次执行此操作，然后执行after操作
* Consumer<T> 接口也被称为消费型接口，它消费的数据的数据类型由泛型指定

### Predicate 断定型接口

Predicate<T>：常用的四个方法

* *boolean test(T t)*：对给定的参数进行判断(按某种逻辑由Lambda表达式实现)，返回一个布尔值
* *default Predicate<T> negate()*：返回一个逻辑的否定，对应逻辑非
* *default Predicate<T> and(Predicate other)*：返回一个组合判断，对应短路与
* *default Predicate<T> or(Predicate other)*：返回一个组合判断，对应短路或
* Predicate<T> 接口通常用于判断参数是否满足指定的条件

### Functon 函数型接口

Function<T,R> ：常用的两个方法

* *R apply(T t)*：将此函数应用于给定的参数
* *default <V> Function andThen(Function after)*：返回一个组合函数，首先将该函数应用于输入，然后将after函数应用于结果
* Function<T,R>接口通常用途对参数进行处理，转换(处理逻辑由Lambda表达式实现)，然后返回一个新的值

# Stream流

* 生成流
  * Collection体系的集合可以使用默认方法**stream()**生成流
  * Map体系的集合间接的生成流
  * 数组可以通过Stream接口的静态方法**of(T... values)**生成流
* 中间操作
  * *Stream<T> filter(Predicate predicate)*：用于对流中的数据进行过滤
  * *Stream<T> limit(long maxSize)*：返回此流中的元素组成的流，截取前指定参数个数的数据
  * *Stream<T> skip(long n)*：跳过指定参数个数的拘束，返回由该流的剩余元素组成的流
  * *static <T> Stream<T> concat(Stream a,Stream b)*：合并a和b两个流为一个流
  * *Stream<T> distinct()*：返回由该流的不同元素(根据Object.equals(Object))组成的流
  * *Stream<T> sorted()*：返回由此流的元素组成的流，根据自然顺序排序
  * *Stream<T> sorted(Comparator comparator)*：返回由该流的元素组成的流，根据提供的Comparator进行排序
  * *<R> Stream<R> map(Function mapper)*：返回由给定函数应用于此流的元素的结果组成的流
  * *IntStream map ToInt(ToIntFunction mapper)*：返回一个IntStream其中包含将给定函数应用此流的元素的结果
* 终结操作
  * *void forEach(Consumer action)*：对此流的每个元素执行操作
  * *long count()*：返回此流中的元素数

## 收集操作

Stream流的收集方法

* *R collect(Collector collector)*
  * 工具类**Collectors**提供了具体的收集方式
    * *public static <T> Collector toList()*：把元素收集到List集合中
    * *public static <T> Collector toSet()*：把元素收集到Set集合中
    * *public static Collector toMap(Function keyMapper,Function valueMapper)*：把元素收集到Map集合中

# 类加载器

> 当程序要使用某个类时，如果该类还未被加载到内存中，则系统会通过类的加载，类的链接(验证、准备、解析)，类的初始化这三个步骤来对类进行初始化。如果不出现意外情况，JVM将会连续完成这三个步骤，所以有时也把这三个步骤统称为类加载或者类初始化

* 类加载
  * 就是指将class文件读入内存，并为之创建一个java.lang.Class对象
  * 任何类被使用时，系统都会为之建立一个java.lang.Class对象
* 类的连接
  * 验证阶段：用于检验被加载的类是否有正确的内部结构，并和其他类协调一致
  * 准备阶段：为 `static` 变量分配空间，设置默认值
    * static 变量分配空间和赋值是两个步骤，分配空间在准备阶段完成，赋值在初始化阶段完成
    * 如果 static 变量是 **final 的基本类型，以及字符串常量**，那么编译阶段值就确定了，赋值在准备阶段完成
    * 如果 static 变量是 final 的，但属于**引用类型**，那么赋值也会在**初始化阶段完成**
  * 解析阶段：将类的二进制数据中的符号引用替换为直接引用
* 类的初始化
  * 在该阶段，主要就是对类变量进行初始化
  * 类的初始化步骤
    * 假如类还未被加载和连接，则程序先加载并连接该类
    * 假如该类的直接父类还未被初始化，则先初始化其直接父类
    * 假如类中有初始化语句，则系统依次执行这些初始化语句
    * **注意**：在执行第二个步骤的时候，系统对直接父类的初始化步骤也要遵循初始化步骤1-3
  * 类的初始化时机(**类初始化是【懒惰的】**)
    * main 方法所在的类，总会被首先初始化
    * 首次访问这个类的静态变量或静态方法时
    * 子类初始化，如果父类还没初始化，会引发
    * 子类访问父类的静态变量，只会触发父类的初始化
    * Class.forName
    * new 会导致初始化
  * **不会导致类初始化的情况**
    * 访问类的 static final 静态常量（基本类型和字符串）不会触发初始化
    * 类对象.class 不会触发初始化
    * 创建该类的数组不会触发初始化
    * 类加载器的 loadClass 方法
    * Class.forName 的参数 2 为 false 时
* JVM的类加载机制
  * 全盘负责：就是当一个类加载器负责加载某个Class时，该Class所依赖的和引用的其他Class也将由该类加载器负责载入，除非显示使用另外一个类加载器来载入
  * 父类委托：就是当一个类加载器负责加载某个Class时，先让父类加载器试图加载该Class，只有在父类加载器无法加载该类时才会尝试从自己的类路径中加载该类
  * 缓存机制：保证所有加载过的Class都会被缓存，当程序需要使用某个Class对象时，类加载器先从缓存区中搜索该Class,只有当缓存区中不存在该Class对象时，系统才会读取该类对应得二进制数据，并将其转换成Class对象，储存到缓存区
* ClassLoader：是负责加载类的对象
  * Bootstrap类加载器。 它是虚拟机的内置类加载器，通常表示为`null`  ，并且没有父`null` 
  * 平台类加载器可以看到所有*平台类*  ，可以将其用作`ClassLoader`实例的父`ClassLoader` 。  平台类包括由平台类加载器或其祖先定义的Java SE平台API，其实现类和JDK特定的运行时类。 
  * 它也被称为*应用程序类加载器* ，与平台类加载器不同。  系统类加载器通常用于定义应用程序类路径，模块路径和JDK特定工具上的类。  平台类加载器是所有平台类对其可见的系统类加载器的父级或祖先

# 反射

Java反射机制：是指在运行时获取一个类的变量和方法信息。然后通过获取到的信息来创建对象，调用方法的一种机制。由于这种动态性，可以极大的增强程序的灵活性，程序不用在编译期就完成确定，在运行期仍然可以扩展

## 获取Class类的对象

* 使用类的class属性来获取该类对应的Class对象
* 调用**对象的getClass()**方法，返回该对象所属类对应的Class对象
* 使用Class类中静态方法**forNmae(String classNmae)**，该方法需要传入字符串参数，该字符串参数的值是某个类的全路径，也就是**完整包名的路径**

## 获取构造方法并使用

Class类中用于获取构造方法的方法

* *Constructor<?>[] getConstructors()*：返回所有公共构造方法对象的数组
* *Constructor<?>[] getDeclaredConstructors()*：返回所有构造方法对象的数组
* *Constructor<T> getConstructor(Class<?>... parameterTypes)*：返回单个公共构造方法对象
* *Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes)*：返回单个构造方法对象

Constructor类中用于创建对象的方法

* *T newInstance(Object... initargs)*：根据指定的构造方法创建对象

**注意**

* *punlic void setAccessible(boolean flag)*：值为true，取消访问检查

## 获取成员变量并使用

Class类中用于获取成员变量的方法

* *Filed[] getFileds()*：返回所有公共成员变量对象的数组
* *Filed[] getDeclaredFileds()*：返回所有成员变量对象的数组
* *Filed getFiled(String name)*：返回单个公共成员变量对象
* *Filed getDeclareFiled(String name)*：返回单个成员变量对象

Filed类中用于给成员变量赋值的方法

* *void set(Object obj,Object value)*：给obj对象的成员变量赋值为value

## 获取成员方法并使用

Class类中用于获取成员方法的方法

* *Method[] getMethods()*：返回所有公共成员方法对象的数组，包括继承的
* *Method[] getDeclaredMethods()*：返回所有成员方法对象的数组，不包括继承的
* *Method getMethod(String name,Class<?>... parameterTypes)*：返回单个公共成员方法对象
* *Method getDeclaredMethod(String name,Class<?>... parameterTypes)*：返回单个成员方法对象

Method类中用于调用成员方法的方法

* *Object invoke(Object obj,Object... args)*：调用obj对象的成员方法，参数是args，返回值是Object类型

# 模块化

## 模块的基本使用步骤

* 创建模块
* 在模块的**src**目录下新建一个名为**module-info.java**的描述性文件，该文件专门定义模块名，访问权限，模块依赖等信息
* 模块中所未导出2的包都是模块私有的，它们不能在模块之外被访问
  * 模块导出格式：**exports 包名;**
* 一个模块要访问其他的模块，必须明确指定依赖那些模块，未明确指定依赖的模块不能访问
  * 模块依赖格式：**requires 模块名;**

## 模块服务的使用

# 注解

## 作用分类

* 编写文档：通过代码里标识的注解生成文档【生成文档doc文档】
* 代码分析：通过代码里标识的注解对代码进行分析【使用反射】
* 编译检查：通过代码里标识的注解让编译器能够实现基本的编译检查

## JDK内置注解

* @Override 表示当前方法覆盖了父类的方法
* @Deprecation 表示方法已经过时,方法上有横线，使用时会有警告。
* @SuppviseWarnings 表示关闭一些警告信息(通知java编译器忽略特定的编译警告)

## 自定义注解

* 格式：
  * 元注解
  * *public @interface 注解名称{ **属性**  }*

**本质**：注解本质上就是一个接口，该接口默认继承Annotation接口

* 成员变量在`Annotation`定义中以**无参数方法的形式来声明**
  * 要求
    * 属性的返回值类型有下列取值
      * 基本数据类型
      * String
      * 枚举
      * 注解
      * 以上类型的数组
    * 定义了属性，在使用时需要给属性赋值
      * 如果定义属性时，**使用default关键字给属性默认初始化，则使用注解时，可以不进行属性的赋值**
      * 如果只有一个属性需要赋值，并且属性的名称是value，则value可以省略，直接定义值
      * 数组赋值时，值使用{ }包裹。如果数组中只有一个值，则{ }省略
* 元注解：用于描述注解的注解
  * @Target：描述注解能够作用的位置
    * ElementType取值：
      * CONSTRUCTOR：用于构造器上
      * PACKAGE：用于描述包上
      * TYPE：可以作用于类上
      * METHOD：可以作用于方法上
      * FIELD：可以作用于成员变量上
  * @Retention：描述注解被保留的阶段
    * @Retention(RetentionPolicy.RUNTIME)：当前被描述的注解，会保留到class字节码文件中，并被JVM读取到
  * @Documented：描述注解是否被抽取到API文档中
  * @Inherited：描述注解是否被子类继承

## 获取注解中定义的属性值

* 获取注解定义的Class的对象
* 获取指定的注解
  * Class对象.getAnnotation(注解.Class)
* 调用注解中的抽象方法获取配置的属性值