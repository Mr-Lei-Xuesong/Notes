# 概述

> Zookeeper是一个开源的分布式的，为分布式框架提供协调服务的Apache项目；

* 工作机制
  * Zookeeper从设计模式角度来理解：是一个基于观察者模式设计的分布式服务管理框架，它负责存储和管理大家都关心的数据，然后接受观察者的注册，一旦这些数据的状态发生变化，Zookeeper就将负责通知已经在Zookeeper上注册的那些观察者做出相应的反应

  ![](.\img\75.jpg)

* 特点
  1. 一个领导者（Leader），多个跟随者（Follower）组成的集群。
  2. 集群中只要有**半数以上**节点存活，Zookeeper集群就能正常服务。所以Zookeeper适合安装奇数台服务器。
  3. 全局数据一致：每个Server保存一份相同的数据副本，Client无论连接到哪个Server，数据都是一致的。
  4. 更新请求顺序执行，来自同一个Client的更新请求按其发送顺序依次执行。
  5. 数据更新原子性，一次数据更新要么成功，要么失败。
  6. 实时性，在一定时间范围内，Client能读到最新数据。

* 数据结构
  * ZooKeeper数据模型的结构与Linux文件系统很类似，整体上可以看作是一棵树，每个节点称做一个ZNode。每一个ZNode默认能够存储**1MB**的数据，每个ZNode都可以**通过其路径唯一标识**

  ![](.\img\76.jpg)

* 应用场景

  > 提供的服务包括：统一命名服务、统一配置管理、统一集群管理、服务器节点动态上下线、软负载均衡等。

# 配置参数

> Zookeeper中的配置文件zoo.cfg中参数

1. tickTime=2000：通信心跳时间，Zookeeper服务器与客户端心跳时间，单位毫秒

   ![](.\img\77.jpg)

2. initLimit=10：LF初始通信时限

   ![](D:\Notes\img\78.jpg)

3. syncLimit=5：LF同步通信时限

   ![](D:\Notes\img\79.jpg)

4. dataDir：保存Zookeeper中的数据
   * **注意**：默认的tmp目录，容易被Linux系统定期删除，所以一般不用默认的tmp目录

5. clientPort=2181：客户端连接端口，通常不做修改

# 集群

## 启动

1. 在每个zookeeper文件下的zkData目录下创建**myid**，里面标注**唯一编号**

2. 在配置文件**zoo.cfg**里面增加配置信息

   ```
   server.1=ip地址:2888:3888
   server.2=ip地址:2888:3888
   server.3=ip地址:2888:3888
   ```

   > **server.A=B:C:D**

   * A是一个数字，表示这个是第几号服务器
     * 集群模式下配置一个文件myid，这个文件在dataDir目录下，这个文件里面有一个数据就是A的值，**Zookeeper启动时读取此文件，拿到里面的数据与zoo.cfg里面的配置信息比较从而判断到底是哪个server**

   * B是这个服务器的地址
   * C是这个服务器Follower与集群中的Leader服务器交换信息的端口
   * D是万一集群中的Leader服务器挂了，需要一个端口来重新进行选举，选出一个新的Leader，而这个端口就是用来执行选举时服务器相互通信的端口

3. 挨个启动zookeeper服务器`bin/zkServer.sh start`，查看状态`bin/zkServer.sh status`

## 选举机制

### 第一次启动

![](.\img\80.jpg)

1. 服务器1启动，发起一次选举。服务器1投自己一票。此时服务器1票数一票，不够半数以上（3票），选举无法完成，服务器1状态保持为LOOKING
2. 服务器2启动，再发起一次选举。服务器1和2分别投自己一票并交换选票信息：此时服务器1发现服务器2的myid比自己目前投票推举的（服务器1）大，更改选票为推举服务器2。此时服务器1票数0票，服务器2票数2票，没有半数以上结果，选举无法完成，服务器1，2状态保持LOOKING
3. 服务器3启动，发起一次选举。此时服务器1和2都会更改选票为服务器3。此次投票结果：服务器1为0票，服务器2为0票，服务器3为3票。此时服务器3的票数已经超过半数，服务器3当选Leader。服务器1，2更改状态为FOLLOWING，服务器3更改状态为LEADING
4. 服务器4启动，发起一次选举。此时服务器1，2，3已经不是LOOKING状态，不会更改选票信息。交换选票信息结果：服务器3为3票，服务器4为1票。此时服务器4服从多数，更改选票信息为服务器3，并更改状态为FOLLOWING
5. 服务器5启动，同4一样

### 非第一次启动

1. 当ZooKeeper集群中的一台服务器出现以下两种情况之一时，就会开始进入Leader选举
   1. 服务器初始化启动
   2. 服务器运行期间无法和Leader保持连接
2. 而当一台机器进入Leader选举流程时，当前集群也可能会处于以下两种状态
   1. 集群中本来就已经存在一个Leader
      * 对于这种已经存在Leader的情况，机器试图去选举Leader时，会被告知当前服务器的Leader信息，对于该机器来说，仅仅需要和Leader机器建立连接，并进行状态同步即可
   2. 集群中确实不存在Leader
      * 假设ZooKeeper由5台服务器组成，SID(服务ID)分别为1、2、3、4、5，ZXID(事务ID)分别为8、8、8、7、7，并且此时SID为3的服务器是Leader。某一时刻，3和5服务器出现故障，因此开始进行Leader选举
3. **选举Leader规则：**
   1. **EPOCH大的直接胜出**
   2. **EPOCH相同，事务id大的胜出**
   3. **事务id相同，服务器id大的胜出**

## 集群启动停止脚本

```shell
#!/bin/bash

case $1 in
"start"){
	for i in 192.168.124.130 192.168.124.131 192.168.124.132
	do
		echo --------------- zookeeper $i 启动 ---------------
		ssh $i "/usr/local/zookeeper-3.5.7/bin/zkServer.sh start"
	done
}
;;
"stop"){
	for i in 192.168.124.130 192.168.124.131 192.168.124.132
	do
		echo --------------- zookeeper $i 停止 ---------------
		ssh $i "/usr/local/zookeeper-3.5.7/bin/zkServer.sh stop"
	done
}
;;
"status"){
	for i in 192.168.124.130 192.168.124.131 192.168.124.132
	do
		echo --------------- zookeeper $i 状态 ---------------
		ssh $i "/usr/local/zookeeper-3.5.7/bin/zkServer.sh status"
	done
}
;;
esac
```

# 客户端命令行操作

## 命令行语法

| 命令基本语法 | 功能描述                                                     |
| :----------: | ------------------------------------------------------------ |
|     help     | 显示所有操作命令                                             |
|   ls path    | 使用ls 命令来查看当前znode的子节点[可监听]<br />-w监听子节点变化<br />-s附加次级信息 |
|    create    | 普通创建<br />-s   含有序列<br />-e   临时（重启或者超时消失） |
|   get path   | 获得节点的值[可监听]<br />-w监听节点内容变化<br />-s附加次级信息 |
|     set      | 设置节点的具体值                                             |
|     stat     | 查看节点状态                                                 |
|    delete    | 删除节点                                                     |
|  deleteall   | 递归删除节点                                                 |

## znode节点数据信息

* 查看当前znode中所包含的内容

  * `ls /`

* 查看当前节点详细数据

  * `ls -s /`

  ![](.\img\81.jpg)

  1. czxid：创建节点的事务zxid
  2. ctime：znode被创建的毫秒数（从1970年开始）
  3. mzxid：znode最后更新的事务zxid
  4. mtime：znode最后修改的毫秒数（从1970年开始）
  5. pZxid：znode最后更新的子节点zxid
  6. cversion：znode子节点变化号，znode子节点修改次数
  7. dataversion：znode数据变化号
  8. aclVersion：znode访问控制列表的变化号
  9. ephemeralOwner：如果是临时节点，这个是znode拥有者的session id。如果不是临时节点则是0
  10. dataLength：znode的数据长度
  11. numChildren：znode子节点数量

## 节点类型

* 持久（Persistent）：客户端和服务器端断开连接后，创建的节点不删除

* 短暂（Ephemeral）：客户端和服务器端断开连接后，创建的节点自己删除

  ![](.\img\82.jpg)

* 持久化目录节点客户端与Zookeeper断开连接后，该节点依旧存在
* 持久化顺序编号目录节点客户端与Zookeeper断开连接后，该节点依旧存在，只是Zookeeper给该节点名称进行顺序编号
* 临时目录节点客户端与Zookeeper断开连接后，该节点被删除
* 临时顺序编号目录节点客户端与Zookeeper断开连接后，该节点被删除，只是Zookeeper给该节点名称进行顺序编号
* **说明**：创建znode时设置顺序标识，znode名称后会附加一个值，顺序号是一个单调递增的计数器，由父节点维护
* **注意：在分布式系统中，顺序号可以被用于为所有的事件进行全局排序，这样客户端可以通过顺序号推断事件的顺序**

1. 创建普通永久节点

   `create /test "测试"`

2. 创建普通永久带序号节点

   `create -s /test "测试"`

3. 创建短暂节点

   `create -e /test "测试"`

4. 创建短暂带序号节点

   `create -e -s /test "测试"`

5. 获得节点的值

   `get -s /test`

6. 修改节点数据值

   `set /test "aaaa"`

## 监听器原理

> 客户端注册监听它关心的目录节点，当目录节点发生变化（数据改变、节点删除、子目录节点增加删除）时，ZooKeeper会通知客户端。监听机制保证ZooKeeper保存的任何的数据的任何改变都能快速的响应到监听了该节点的应用程序

1. 首先要有一个main()线程
2. 在main线程中创建Zookeeper客户端，这时就会创建两个线程，一个负责网络连接通信（connet），一个负责监听（listener）
3. 通过connect线程将注册的监听事件发送给Zookeeper
4. 在Zookeeper的注册监听器列表中将注册的监听事件添加到列表中
5. Zookeeper监听到有数据或路径变化，就会将这个消息发送给listener线程
6. listener线程内部调用了process()方法

![](.\img\83.jpg)

* 常见的监听

  * 监听节点数据的变化
    * `getpath[watch]`

  * 监听子节点增减的变化
    * `lspath[watch]`

* 节点的值变化监听

  * 在node-1主机上注册监听/test节点数据变化

    `get -w /test`

  * 在node-2主机上修改/test节点的数据

    `set /test "测试"`

  * **此时节点node-1主机上就会收到数据变化的监听**

* 节点的子节点变化监听(路径变化)

  * 在node-1主机上注册监听/test节点的子节点变化

    `ls -w /test`

  * 在node-2主机上创建/test的子节点/demo

    `create /test/demo "样例"`

  * **此时节点node-1主机上就会收到子节点变化的监听**

## 节点的删除与查看

* 删除节点（只能一节一节删除）

  `delete /test`

* 递归删除所有节点(可以在父节点删除所有)

  `deleteall /test`

* 查看节点状态

  `stat /test`

# 客户端API操作

> 前提保证zookeeper集群服务端启动

* 引入依赖

  ```xml
  <dependency>
      <groupId>org.apache.zookeeper</groupId>
      <artifactId>zookeeper</artifactId>
      <version>3.5.7</version>
  </dependency>
  ```

* 创建客户端

  ```java
  public class zkClient {
  	//节点地址
      private String connectString = "192.168.124.130:2181,192.168.124.131:2181,192.168.124.132:2181";
      //超时时间
      private Integer sessionTimeout = 100000;
      //ZK客户端
      private ZooKeeper zooKeeper;
  
      /**
       * zk客户端初始化
       */
      @Before
      public void init() throws IOException {
          zooKeeper = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
              //监听
              @Override
              public void process(WatchedEvent watchedEvent) {
                  List<String> children = null;
                  try {
                      children = zooKeeper.getChildren("/", true);
                      for (String child : children) {
                          System.out.println(child);
                      }
                  } catch (KeeperException | InterruptedException e) {
                      e.printStackTrace();
                  }
              }
          });
      }
  
      /**
       * 创建节点
       */
      @Test
      public void createNode() throws KeeperException, InterruptedException {
          //参数1：要创建的节点的路径；参数2：节点数据；参数3：节点权限；参数4：节点类型
          String nodeCreated = zooKeeper.create("/demo", "Hello,World".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
      }
  
      /**
       * 监听节点变化
       */
      @Test
      public void getChildren() throws KeeperException, InterruptedException {
          List<String> children = zooKeeper.getChildren("/", true);
          for (String child : children) {
              System.out.println(child);
          }
  
          //延时关闭
          Thread.sleep(Long.MAX_VALUE);
      }
  
      /**
       * 判断节点是否存在
       */
      @Test
      public void exist() throws KeeperException, InterruptedException {
          Stat stat = zooKeeper.exists("/demo", false);
          System.out.println(stat == null ? "not exist" : "exist");
      }
  }
  ```

# 写数据流程

1. 请求直接发送给Leader节点

   ![](.\img\84.jpg)

2. 请求发送给Follwer节点

   ![](.\img\85.jpg)

# 服务器动态上下线监听

## 创建服务端

```java
public class DistributeServer {
    //ZK客户端
    private ZooKeeper zooKeeper = null;

    public static void main(String[] args) throws IOException, KeeperException, InterruptedException {
        DistributeServer server = new DistributeServer();
        //1.连接zk服务集群
        server.connectZK();
        //2.注册服务器到zk集群
        server.registerServer(args[0]);
        //3.启动业务逻辑
        server.business(args[0]);
    }

    /**
     * 启动业务逻辑
     */
    private void business(String hostname) throws InterruptedException {
        System.out.println(hostname + " is working...");
        Thread.sleep(Long.MAX_VALUE);
    }

    /**
     * 服务器注册到ZK集群
     */
    private void registerServer(String hostname) throws KeeperException, InterruptedException {
        //父节点名称
        String parentNode = "/servers/";
        String create = zooKeeper.create(parentNode + hostname, hostname.getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);
        System.out.println(hostname + " is online " + create);
    }

    /**
     * 连接ZK服务集群
     */
    private void connectZK() throws IOException {
        //超时时间
        int sessionTimeout = Integer.MAX_VALUE;
        //节点地址
        String connectString = "192.168.124.130:2181,192.168.124.131:2181,192.168.124.132:2181";
        zooKeeper = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {

            }
        });
    }
}
```

## 创建客户端

```java
public class DistributeClient {
    //ZK客户端
    private ZooKeeper zooKeeper = null;

    public static void main(String[] args) throws IOException, KeeperException, InterruptedException {
        DistributeClient client = new DistributeClient();
        //1.连接zk服务集群
        client.connectZK();
        //2.监听/servers下面子节点的增加或删除
        client.getServerList();
        //业务逻辑
        client.business();
    }

    private void business() throws InterruptedException {
        System.out.println("client is working...");
        Thread.sleep(Long.MAX_VALUE);
    }

    /**
     * 监听ZK集群里服务器上、下线状态
     */
    private void getServerList() throws KeeperException, InterruptedException {
        //1.获取服务器子节点信息，并且对父节点进行监听
        List<String> children = zooKeeper.getChildren("/servers", true);
        //2.存储服务器信息列表
        ArrayList<String> servers = new ArrayList<>();
        //3.遍历所有节点，获取节点中的主机名称信息
        for (String child : children) {
            byte[] data = zooKeeper.getData("/servers/" + child, false, null);
            servers.add(new String(data));
        }
        //4.打印服务器列表信息
        System.out.println(servers);
    }

    /**
     * 连接ZK服务集群
     */
    private void connectZK() throws IOException {
        //超时时间
        int sessionTimeout = Integer.MAX_VALUE;
        //节点地址
        String connectString = "192.168.124.130:2181,192.168.124.131:2181,192.168.124.132:2181";
        zooKeeper = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {
                try {
                    getServerList();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
    }
}
```

# 分布式锁

> 比如说"进程1"在使用该资源的时候，会先去获得锁，"进程1"获得锁以后会对该资源保持独占，这样其他进程就无法访问该资源，"进程1"用完该资源以后就将锁释放掉，让其他进程来获得锁，那么通过这个锁机制，我们就能保证了分布式系统中多个进程能够有序的访问该临界资源。那么我们把这个分布式环境下的这个锁叫作分布式锁

![](.\img\86.jpg)

## 创建分布式锁客户端

```java
public class DistributedLock {
    //ZK客户端
    private ZooKeeper zooKeeper;
    //ZK创建计数器线程
    private final CountDownLatch countDownLatch = new CountDownLatch(1);
    //ZK等待计数器线程
    private final CountDownLatch waitDownLatch = new CountDownLatch(1);
    //根节点名称
    private String rootNode = "/locks";
    //子节点前缀名称
    private String subNode = "/seq-";
    //当前client等待的子节点路径
    private String waitPath;
    //当前client创建的子节点
    String currentNode;

    public DistributedLock() throws IOException, InterruptedException, KeeperException {
        //节点地址
        String connectString = "192.168.124.130:2181,192.168.124.131:2181,192.168.124.132:2181";
        //超时时间
        int sessionTimeout = Integer.MAX_VALUE;
        zooKeeper = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {
                //当ZK客户端连接成功，就释放countDownLatch
                if (watchedEvent.getState() == Event.KeeperState.SyncConnected) {
                    countDownLatch.countDown();
                }
                //发生了waitDownLatch的删除时间就释放waitDownLatch
                if (watchedEvent.getType() == Event.EventType.NodeDeleted && watchedEvent.getPath().equals(waitPath)) {
                    waitDownLatch.countDown();
                }
            }
        });

        //等待ZK正常连接后，再往下执行程序
        this.countDownLatch.await();
        //判断根节点/locks是否存在
        Stat stat = zooKeeper.exists(rootNode, false);
        //如果根节点不存在，则创建根节点，根节点类型为永久节点
        if (stat == null) {
            System.out.println("根节点不存在，即将创建永久类型根节点");
            //创建根节点
            zooKeeper.create(rootNode, "locks".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
        }
    }

    //对ZK加锁
    public void zkLock() {
        try {
            //在根节点下创建临时顺序节点，返回值为创建的节点路径
            currentNode = zooKeeper.create(rootNode + subNode, null, ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);
            Thread.sleep(10);
            //判断创建的节点是否是最小的序号节点，如果是获取到锁；否则就监听比他小的节点
            List<String> childrenNodes = zooKeeper.getChildren(rootNode, false);
            //如果childrenNodes只有一个值，就直接获取锁；如果有多个节点，就需要判断那个节点序号最小
            if (childrenNodes.size() == 1) {
                return;
            } else {
                //对根节点下的所有临时顺序节点进行从小到大排序
                Collections.sort(childrenNodes);
                //获取当前节点名称 seq-xxxxxxxxx
                String thisNode = currentNode.substring((rootNode + "/").length());
                //通过seq-xxxxxxxxx获取到该节点在children集合的位置
                int indexNode = childrenNodes.indexOf(thisNode);
                if (indexNode == -1) {
                    System.out.println("数据异常");
                } else if (indexNode == 0) {//就只有一个节点，直接获取到锁
                    return;
                } else {
                    //监听前一个节点的变化
                    this.waitPath = rootNode + "/" + childrenNodes.get(indexNode - 1);
                    //在waitPath上注册监听器，当waitPath被删除时，ZK会回调监听器的process方法
                    zooKeeper.getData(waitPath, true, null);
                    //等待监听
                    this.waitDownLatch.await();
                    return;
                }

            }
        } catch (Exception e) {
            e.printStackTrace();
        }

    }

    public void unZKLock() {
        try {
            zooKeeper.delete(this.currentNode, -1);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## 测试

```java
public class DistributedLockTest {
    public static void main(String[] args) throws InterruptedException, IOException, KeeperException {
        final DistributedLock lock1 = new DistributedLock();
        final DistributedLock lock2 = new DistributedLock();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    //获取到锁
                    lock1.zkLock();
                    System.out.println("线程1->获取到锁");
                    Thread.sleep(5 * 1000);
                    //释放锁
                    lock1.unZKLock();
                    System.out.println("线程1->释放锁");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    //获取到锁
                    lock2.zkLock();
                    System.out.println("线程2->获取到锁");
                    Thread.sleep(5 * 1000);
                    //释放锁
                    lock2.unZKLock();
                    System.out.println("线程2->释放锁");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }
}
```

# Curator框架

> Curator是一个专门解决分布式锁的框架，解决了原生JavaAPI开发分布式遇到的问题

* 引入依赖

  ```xml
  <dependency>
      <groupId>org.apache.curator</groupId>
      <artifactId>curator-framework</artifactId>
      <version>4.3.0</version>
  </dependency>
  <dependency>
      <groupId>org.apache.curator</groupId>
      <artifactId>curator-recipes</artifactId>
      <version>4.3.0</version>
  </dependency>
  <dependency>
      <groupId>org.apache.curator</groupId>
      <artifactId>curator-client</artifactId>
      <version>4.3.0</version>
  </dependency>
  ```

* 创建客户端

  ```java
  public class CuratorLockTest {
      public static void main(String[] args) {
          //创建分布式锁1
          InterProcessMutex lock1 = new InterProcessMutex(getCuratorFramework(), "/locks");
  
          //创建分布式锁2
          InterProcessMutex lock2 = new InterProcessMutex(getCuratorFramework(), "/locks");
  
          new Thread(new Runnable() {
              @Override
              public void run() {
                  try {
                      lock1.acquire();
                      System.out.println("线程1->获取到锁");
  
                      lock1.acquire();
                      System.out.println("线程1->再次获取到锁");
  
                      Thread.sleep(5 * 1000);
  
                      lock1.release();
                      System.out.println("线程1->释放锁");
  
                      lock1.release();
                      System.out.println("线程1->再次释放锁");
                  } catch (Exception e) {
                      e.printStackTrace();
                  }
              }
          }).start();
  
          new Thread(new Runnable() {
              @Override
              public void run() {
                  try {
                      lock2.acquire();
                      System.out.println("线程2->获取到锁");
  
                      lock2.acquire();
                      System.out.println("线程2->再次获取到锁");
  
                      Thread.sleep(5 * 1000);
  
                      lock2.release();
                      System.out.println("线程2->释放锁");
  
                      lock2.release();
                      System.out.println("线程2->再次释放锁");
                  } catch (Exception e) {
                      e.printStackTrace();
                  }
              }
          }).start();
      }
  
      private static CuratorFramework getCuratorFramework() {
          ExponentialBackoffRetry repeat = new ExponentialBackoffRetry(3000, 3);
  
          CuratorFramework client = CuratorFrameworkFactory.builder().connectString("192.168.124.130:2181,192.168.124.131:2181,192.168.124.132:2181").
                  connectionTimeoutMs(Integer.MAX_VALUE).
                  sessionTimeoutMs(Integer.MAX_VALUE).
                  retryPolicy(repeat).build();//连接失败后重试
          //启动客户端
          client.start();
          System.out.println("Zookeeper启动成功。。。");
          return client;
      }
  }
  ```

# Paxos算法

> 一种基于消息传递且具有高度容错特性的一致性算法
>
> 快速正确的在一个分布式系统中对某个数据值达成一致，并且保证不论发生任何异常，都不会破坏整个系统的一致性

## 描述

* 在一个Paxos系统中，首先将所有节点划分为Proposer（提议者），Acceptor（接受者），和Learner（学习者）。（注意：每个节点都可以身兼数职）

  ![](.\img\87.jpg)

* 一个完整的Paxos算法流程分为三个阶段：
  1. Prepare准备阶段
     * Proposer向多个Acceptor发出Propose请求Promise
     * Acceptor针对收到的Propose请求进行Promis
  2. Accept接受阶段
     * Proposer收到多数Acceptor承诺的Promise后，向Acceptor发出Propose请求
     * Acceptor针对收到的Propose请求进行Accept处理
  3. Learn学习阶段：Proposer将形成的决议发送给所有Learners

## 流程

![](.\img\88.jpg)

1. Prepare:Proposer生成全局唯一且递增的ProposalID，向所有Acceptor发送Propose请求，这里无需携带提案内容，只携带ProposalID即可
2. Promise:Acceptor收到Propose请求后，做出“两个承诺，一个应答”
   1. 不再接受ProposalID小于等于（注意：这里是<=）当前请求的Propose请求
   2. 不再接受ProposalID小于（注意：这里是<）当前请求的Accept请求
   3. 不违背以前做出的承诺下，回复已经Accept过的提案中ProposalID最大的那个提案的Value和ProposalID，没有则返回空值
3. Propose:Proposer收到多数Acceptor的Promise应答后，从应答中选择ProposalID最大的提案的Value，作为本次要发起的提案。如果所有应答的提案Value均为空值，则可以自己随意决定提案Value。然后携带当前ProposalID，向所有Acceptor发送Propose请求
4. Accept:Acceptor收到Propose请求后，在不违背自己之前做出的承诺下，接受并持久化当前ProposalID和提案Value
5. Learn:Proposer收到多数Acceptor的Accept后，决议形成，将形成的决议发送给所有Learner

# ZAB协议

> Zab借鉴了Paxos算法，是特别为Zookeeper设计的支持崩溃恢复的原子广播协议。基于该协议，Zookeeper设计为只有一台客户端（Leader）负责处理外部的写事务请求，然后Leader客户端将数据同步到其他Follower节点。即Zookeeper只有一个Leader可以发起提案

> Zab协议包括两种基本的模式：**消息广播、崩溃恢复**

## 消息广播

![](.\img\89.jpg)

1. 客户端发起一个写操作请求
2. Leader服务器将客户端的请求转化为事务Proposal提案，同时为每个Proposal分配一个全局的ID，即zxid
3. Leader服务器为每个Follower服务器分配一个单独的队列，然后将需要广播的Proposal依次放到队列中去，并且根据FIFO策略进行消息发送
4. Follower接收到Proposal后，会首先将其以事务日志的方式写入本地磁盘中，写入成功后向Leader反馈一个Ack响应消息
5. Leader接收到超过半数以上Follower的Ack响应消息后，即认为消息发送成功，可以发送commit消息(真正的内容)
6. Leader向所有Follower广播commit消息，同时自身也会完成事务提交。Follower接收到commit消息后，会将上一条事务提交
7. Zookeeper采用Zab协议的核心，就是只要有一台服务器提交了Proposal，就要确保所有的服务器最终都能正确提交Proposal

* ZAB协议针对事务请求的处理过程类似于一个两阶段提交过程
  1. 广播事务阶段
  2. 广播提交操作

## 崩溃恢复

### 异常假设

> 一旦Leader服务器出现崩溃或者由于网络原因导致Leader服务器失去了与过半Follower的联系，那么就会进入崩溃恢复模式

![](.\img\90.jpg)

1. 假设两种服务器异常情况
   1. 假设一个事务在Leader提出之后，Leader挂了
   2. 一个事务在Leader上提交了，并且过半的Follower都响应Ack了，但是Leader在Commit消息发出之前挂了
2. Zab协议崩溃恢复要求满足以下两个要求
   1. 确保已经被Leader提交的提案Proposal，必须最终被所有的Follower服务器提交。（**已经产生的提案，Follower必须执行**）
   2. 确保丢弃已经被Leader提出的，但是没有被**提交**的Proposal。（**丢弃胎死腹中的提案**）

### Leader选举

![](.\img\91.jpg)

* Leader选举：根据上述要求，Zab协议需要保证选举出来的Leader需要满足以下条件
  1. 新选举出来的Leader不能包含未提交的Proposal。即新Leader必须都是已经提交了Proposal的Follower服务器节点
  2. 新选举的Leader节点中含有最大的zxid。这样做的好处是可以避免Leader服务器检查Proposal的提交和丢弃工作

### 数据恢复

![](.\img\92.jpg)

* 同步数据流程：
  1. 完成Leader选举后，在正式开始工作之前（接收事务请求，然后提出新的Proposal），Leader服务器会首先确认事务日志中的所有的Proposal是否已经被集群中过半的服务器Commit
  2. Leader服务器需要确保所有的Follower服务器能够接收到每一条事务的Proposal，并且能将所有已经提交的事务Proposal应用到内存数据中。等到Follower将所有尚未同步的事务Proposal都从Leader服务器上同步过，并且应用到内存数据中以后，Leader才会把该Follower加入到真正可用的Follower列表中

# CAP

* CAP理论：一个分布式系统不可能同时满足以下三种

  1. 一致性（C:Consistency）
  2. 可用性（A:Available）
  3. 分区容错性（P:PartitionTolerance）

  * 这三个基本需求，最多只能同时满足其中的两项，因为P是必须的，因此往往选择就在CP或者AP中

* 一致性（C:Consistency）

  * 在分布式环境中，一致性是指数据在多个副本之间是否能够保持数据一致的特性。在一致性的需求下，当一个系统在数据一致的状态下执行更新操作后，应该保证系统的数据仍然处于一致的状态

* 可用性（A:Available）

  * 可用性是指系统提供的服务必须一直处于可用的状态，对于用户的每一个操作请求总是能够在有限的时间内返回结果

* 分区容错性（P:PartitionTolerance）

  * 分布式系统在遇到任何网络分区故障的时候，仍然需要能够保证对外提供满足一致性和可用性的服务，除非是整个网络环境都发生了故障

> ZooKeeper保证的是CP（一致性和分区容错性）
>
> 1. ZooKeeper不能保证每次服务请求的可用性。（注：在极端环境下，ZooKeeper可能会丢弃一些请求，消费者程序需要重新请求才能获得结果）。所以说，ZooKeeper不能保证服务可用性
> 2. 进行Leader选举时集群都是不可用

