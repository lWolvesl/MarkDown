# Zookeeper

- 重点内容
  - Zookeeper分布式锁案例
  - Paxos算法
  - ZAB协议
  - CAP
  - 源码
- Zookeeper3.5.7

## 一、Zookeeper入门

### 1.1 概述

- Zookeeper是一个开源的分布式的，为分布式款假提供协调服务的Apache项目
- 从设计模式：观察者模式设计的分布式服务器管理框架
- 负值存储和管理数据，然后接受观察者的注册
- 一旦数据状态发生变化，Zookeeper就负责通知已经在Zookeeper上注册的那些观察者做出响应的反应。
- Zookeeper=文件系统+通知机制

### 1.2 特点

![image-20211108153441938](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108153441938.png)

- 由一个Leader多个Follower组成的集群
- 集群中只要有半数以上节点存活，Zookeeper就能正常服务，所以Zookeeper适合安装奇数台服务器
- 全局一直：每个Server保存一份相同的数据副本，Client无论链接到哪个Server，数据均一致
- 更新请求顺序执行，来自同一个Client到更新请求按其发送顺序依次执行。
- 数据更新原子性，要么成功要么失败

### 1.3 数据结构

- 底层与Unix文件系统很类似，树形结构
- 每个节点称为ZNode，每个ZNode默认存储1MB数据，每个ZNode都可以通过其路径唯一标识

![image-20211108154258043](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108154258043.png)

### 1.4 应用场景

- 统一命名管理
- 统一配置管理
- 统一集群管理
- 服务器节点动态上下线
- 软负载均衡

#### 1.4.1 统一命名服务

![image-20211108154410676](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108154410676.png)

- IP不易记忆，但域名可以

#### 1.4.2 统一配置管理

- 一般一个集群中的每个节点的配置信息上完全一致的，如Kafka集群
- 对配置文件修改后，可以快速部署到每个节点哈桑
- 可以将配置文件写入到Zookeeper对一个ZNode中
- 各个客户端服务器监听这个ZNode

![image-20211108160955891](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108160955891.png)

#### 1.4.3 统一集群管理

- 实时掌握每个节点的状态是必要的，可以根据节点实时状态做出调整
- 对ZNode监听

![image-20211108161113605](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108161113605.png)

#### 1.4.4 服务器动态上下线

![image-20211108161224719](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108161224719.png)

#### 1.4.5 软负载均衡

- 在Zookeeper中记录每台服务器的访问数，让访问数最少的服务器去处理最新的客户端请求

![image-20211108161404633](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108161404633.png)

- 在Nginx中有相同的功能框架

## 二、安装

### 2.1 本地模式安装

- 版本 **3.5.7** 企业中使用较多的版本
- 服务端启动

![截屏2021-11-08 16.27.29](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-08%2016.27.29.png)

- 客户端启动

![截屏2021-11-08 16.29.17](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-08%2016.29.17.png)

- 退出
  - `quit`
- 查看状态
  - `zkServer.sh status`

![截屏2021-11-08 16.31.03](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-08%2016.31.03.png)

### 2.2 配置解读

![截屏2021-11-08 16.33.01](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-08%2016.33.01.png)

- tickTime=2000:通信心跳时间，周期性同步数据，单位毫秒
- initLimit=10:LF初始通信时限，Leader和Follower初次连接时能容忍的最多心跳数
- syncLimit=5:LF同步通信时限，建立连接后的超时
- dataDir：保存数据位置
- clientPort=2181：默认通信端口

## 三、集群

### 3.1 集群操作

#### 3.1.1 集群安装

- 三个节点部署Zookeeper，均应安装Zookeeper
- 安装Zookeeper除配置外完全一致
  - 在zkData目录下创建myid文件
  - 在myid中添加与server对应的编号,唯一
  - conf中增加配置
  - `server.A=B:C:D`
    - A:id
    - B:本服务器地址
    - C:这个服务器的Follower与集群中的Leader服务器交换信息的端口
    - D:是灾备选举时的端口
    - zookeeper集群的所有服务器都应拥有所有其他主机的serverid
  - ![截屏2021-11-08 17.05.44](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-08%2017.05.44.png)

#### 3.1.2 选举机制（面试重点）

- 首次选举（初始化）假设共有5台机器
  - 服务器1启动，发起选举，服务器1投自己一票。此时仅有服务器1，选举无法完成，服务器1状态保持为LOOKING
  - 服务器2启动，发起选举，服务器1和2首先分别投自己一票并交换信息：此时服务器1发现服务器2的myid比自己目前投票选举的服务器1大,此时服务器1会把票投给服务器2，服务器1为0票，服务器2为2票，服务器1和2状态保持为LOOKING
  - 服务器3启动，发起选举，此次投票结果：服务器3为3票。服务器1位Leader，服务器3的权重大，服务器3胜出，服务器1和2投票给服务器3，此时投票数大于半数。服务器1和2状态变为FOLLOWING，服务器3状态改为LEADING
  - 服务器4启动，发起选举，服务器123均为LOOKING状态，服务器4投自己一票，服务器3已经胜出并状态为Leader，服务器4变为FOLLOWING
  - 服务器5启动，与4一样当小弟
  -  
  - SID：与myid一致唯一标识一台主机
  - ZXID：事务ID，用来标识一次服务器的变更，每次写操作都会有事务id
  - Epoch：每个Leader任期的代号，没有Leader时同一轮投票过程中的逻辑时钟值相同。每次投票完这个数据会增加

![截屏2021-11-08 17.36.38](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-08%2017.36.38.png)

- 非第一次启动
  - 当集群中的一台服务器出现下述两种情况之一时就会进入Leader选举
    - 服务器初始化
    - 服务器运行期间无法与Leader保持连接
  - 当一台机器进入Leader选举流程时，当前集群可能出现以下状态
    - 集群中存在Leader
      - 仅仅和Leader建立连接，并进行状态同步即可
    - 集群中不存在Leader
      - 选取规则：1⃣️Epoch大的胜出2⃣️ZXID大的胜出3⃣️SID大的胜出

#### 3.1.3 shell脚本

![image-20211108173945028](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108173945028.png)

### 3.2 命令行语法

#### 3.2.1 常用命令

![image-20211108174258050](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108174258050.png)

![image-20211108174439439](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108174439439.png)

#### 3.2.2 ZNode信息

- ls -s /

![截屏2021-11-08 17.46.27](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-08%2017.46.27.png)

#### 3.2.3 节点类型

- 持久｜短暂｜有序号｜无序号
- 持久：客户端与服务器断开后，创建的节点不删除
- 短暂：客户端与服务器断开后，创建的节点自己删除

![image-20211108175131640](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108175131640.png)

- 创建ZNode时设置顺序标识，ZNode名称后会附加一个值，顺序号是一个单调递增的计数器，由父节点维护
  - 注意：在分布式系统中，顺序号可以被用于位所有的事件进行全局排序，这样客户端可以通过顺序号推断事件的顺序

![image-20211108193944186](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108193944186.png)

![image-20211108195958067](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108195958067.png)

![image-20211108200031472](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108200031472.png)

- -e为短暂节点
- 修改节点 `set /xxx "xxx"`

#### 3.2.4 监听器

- 客户端注册监听它关心的目录节点，当目录节点发生变化时，Zookeeper会通知客户端
- 监听机制保证ZooKeeper保持的任何的数据的任何改变都能快速的监听了该节点的应用程序

##### 3.2.4.1 原理

![image-20211108200606322](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211108200606322.png)

- 首先要有一个main线程
- 在main线程中创建Zookeeper客户端，这时就会有创建两个线程，一个负责网络连接通信，一个负责监听
- 通过connect线程将注册的监听事件发给zookeeper
- 在zookeeper的注册监听器列表中将注册的监听事件添加到列表中
- zookeeper监听到有数据或路径变化，就会将这个消息发送给listener线程
- listener线程内部调用了process方法

##### 3.2.4.2 常见的监听

- 监听节点数据的变化
    - `get path [watch]`
- 监听子节点增减的变化
    - `ls path [watch]`



- `get -w xxx`

![截屏2021-11-08 20.36.01](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-08%2020.36.01.png)

- 自己修改不会弹出提示



- `ls -w /xxx`

![截屏2021-11-09 14.14.10](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-09%2014.14.10.png)

- 注册监听一次，只能监听一次，若想再次监听，必须再次注册

##### 3.2.4.3 其他命令

- 删除节点 `delete /xxx`:若该节点下有节点，则无法删除

- 删除节点（含下属节点）`deleteall /xxx`

### 3.3 客户端API操作

#### 3.3.1 IDEA

- 导入依赖

```xml
<dependencies>
        <!-- https://mvnrepository.com/artifact/org.apache.zookeeper/zookeeper -->
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.5.9</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>2.14.1</version>
        </dependency>

</dependencies>
```

- log4j

```properties
log4j.rootLogger=INFO, stdout
log4j.appender.stdout = org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d   %p  [%c] - %m%n
log4j.appender.logfile=org.apache.log4j.FileAppender
log4j.appender.logfile.File=target/spring.log
log4j.appender.logfile.layout=org.apache.log4j.PatternLayout
log4j.appender.logfile.layout.ConversionPattern=%d   %p  [%c] - %m%n
```

- 初始化连接

```java
		private String connection = "www.wolves.top:30081,www.wolves.top:30082,www.wolves.top:30083";
    private int session = 2000;
    private ZooKeeper zooKeeper;

    @Test
    public void init() throws IOException {
        zooKeeper = new ZooKeeper(connection, session, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {

            }
        });
  	}
```

- create()方法

![截屏2021-11-09 16.55.57](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-09%2016.55.57.png)

```java
@Test
public void create() throws InterruptedException, KeeperException {
 		String node = zooKeeper.create("/li", "ss.avi".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
}
```

![截屏2021-11-09 17.00.13](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-09%2017.00.13.png)

#### 3.3.2 通过API实现监控

```java
public void getChildren() throws InterruptedException, KeeperException {
		List<String> children = zooKeeper.getChildren("/", true);
		for (String child:children){
				System.out.println(child);
   	}
}
```

-   此方法无法监听，若要监听，需要放入初始化方法中

```java
						@Override
            public void process(WatchedEvent watchedEvent) {
                List<String> children = null;
                try {
                    children = zooKeeper.getChildren("/", true);
                } catch (KeeperException | InterruptedException e) {
                    e.printStackTrace();
                }
                for (String child:children){
                    System.out.println(child);
                }
                System.out.println("--------------");
            }
```

-   此时控制台会打印变化
-   其他

![截屏2021-11-09 17.12.47](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-09%2017.12.47.png)

### 3.4 客户端向服务器写数据流程

-   直接访问Leader
    -   只要超过半数写入了此数据，则向客户端返回已写入

![image-20211109171708216](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211109171708216.png)

-   直接访问Follower
    -   客户端只与对接的Follower通信，Follower无写权限，会请求Leader进行写权限，当写入超过半数，Leader告诉Follower已经完成写操作，再由Follower返回Client完成信号

![image-20211109172000697](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211109172000697.png)

## 四、服务器动态上下线监听

#### 4.1 需求

-   分布式系统中主节点可以有多台，可以动态上下线，任意一台客户端都能实时感知到主节点服务器的上下线。

#### 4.2 需求分析

-   即在集群中创建一个servers节点，当有服务器上线的时候，九向servers中写入一个节点，下线时就delete掉

![image-20211109173051206](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211109173051206.png)

#### 4.3 具体实现

-   创建/servers根节点

![截屏2021-11-09 17.34.35](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-09%2017.34.35.png)

-   代码

```java
package case1;

import org.apache.zookeeper.KeeperException;
import org.apache.zookeeper.WatchedEvent;
import org.apache.zookeeper.Watcher;
import org.apache.zookeeper.ZooKeeper;

import java.util.ArrayList;
import java.util.List;

public class DistributeClient {
    private String connection = "www.wolves.top:30081,www.wolves.top:30082,www.wolves.top:30083";
    private int sessionTime = 2000;
    private ZooKeeper zooKeeper;

    public static void main(String[] args) throws Exception {
        DistributeClient client = new DistributeClient();
        //获取连接
        client.getConnect();

        //监听/servers下子节点的增加和删除
        client.getServerList();

        //启动业务
        client.business();
    }

    private void business() throws InterruptedException {
        Thread.sleep(Long.MAX_VALUE);
    }

    private void getServerList() throws InterruptedException, KeeperException {
        List<String> children = zooKeeper.getChildren("/servers", true);
        List<String> list = new ArrayList<>();
        for (String child : children) {
            byte[] data = zooKeeper.getData("/servers/" + child, false, null);
            list.add(new String(data));
        }
        System.out.println(list);
    }

    private void getConnect() throws Exception {
        zooKeeper = new ZooKeeper(connection, sessionTime, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {
                try {
                    getServerList();
                } catch (InterruptedException | KeeperException e) {
                    e.printStackTrace();
                }
            }
        });
    }
}

```

```java
package case1;

import org.apache.zookeeper.*;

import java.io.IOException;

public class DistributeServer {
    private String connection = "www.wolves.top:30081,www.wolves.top:30082,www.wolves.top:30083";
    private int sessionTime = 2000;
    private ZooKeeper zooKeeper;

    public static void main(String[] args) throws Exception {
        DistributeServer server = new DistributeServer();
        //获取连接
        server.getConnect();

        //注册服务器到集群
        server.regist(args[0]);

        //启动业务
        server.business();
    }

    private void business() throws InterruptedException {
        Thread.sleep(Long.MAX_VALUE);
    }

    private void regist(String hostName) throws InterruptedException, KeeperException {
        String create = zooKeeper.create("/servers/server"+hostName.substring(17), hostName.getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);
        System.out.println(hostName + "is online");
    }

    private void getConnect() throws IOException {
        zooKeeper = new ZooKeeper(connection, sessionTime, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {

            }
        });
    }
}

```

#### 4.4 测试

-   此时已经实现服务器上下线

![截屏2021-11-09 18.08.15](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-09%2018.08.15.png)

## 五、分布式锁案例

-   进程使用资源时先去获得锁，获得锁后对资源实现独占，这样其他进程就无法访问该资源。进程结束后将锁释放掉让其他进程可以获取，通过这个锁机制，能够保证分布式系统中多个进程能有序的访问该临界资源。在此分布式环境下 的这个锁叫做分布式锁。
-   序号小的可以先获得锁

![image-20211109182318970](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211109182318970.png)

#### 5.1 原生Zookeeper实现分布式锁

```java
package case2;

import org.apache.zookeeper.*;
import org.apache.zookeeper.data.Stat;

import java.util.Collections;
import java.util.List;
import java.util.concurrent.CountDownLatch;

public class DistributedLock {

    private String connection = "www.wolves.top:30081,www.wolves.top:30082,www.wolves.top:30083";
    private int sessionTime = 2000;
    private ZooKeeper zooKeeper;

    private CountDownLatch connectLatch = new CountDownLatch(1);

    private String waitPath;

    private CountDownLatch waitLatch = new CountDownLatch(1);

    public DistributedLock() throws Exception {
        //获取连接
        zooKeeper = new ZooKeeper(connection, sessionTime, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {
                //两个Latch释放
                if (watchedEvent.getState() == Event.KeeperState.SyncConnected){
                    connectLatch.countDown();
                }
                if (watchedEvent.getType() == Event.EventType.NodeDeleted && watchedEvent.getPath().equals(waitPath)){
                    waitLatch.countDown();
                }
            }
        });
        //等待zk连接正常后，往下走程序
        connectLatch.await();

        //判断是否存在根节点Locks
        Stat stat = zooKeeper.exists("/locks", false);
        if (stat == null) {
            zooKeeper.create("/locks", "locks".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
        }
        //
    }

    //对zk加锁
    public void Lock() throws InterruptedException, KeeperException {
        //创建带序号的临时节点
        String mode = zooKeeper.create("/locks/seq-", null, ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);
        //判断是否是最小的节点，若是则获得锁，若不是则监听他序号前的一个节点
        List<String> children = zooKeeper.getChildren("/locks", false);
        if (children.size() == 1){
            return;
        }else {
            Collections.sort(children);
            //获取节点名称
            String thisMode = mode.substring(7);
            //通过节点名获取到位置
            int index = children.indexOf(thisMode);

            if (index == -1){
                System.out.println("数据异常");
            }else if(index == 0){
                return;
            }else {
                waitPath = "/locks/"+children.get(index-1);
                zooKeeper.getData(waitPath,true,null);
                waitLatch.wait();
                return;
            }

        }
    }

    //对zk解锁
    public void unlock() {
        
    }
}

```

-   测试

```java
package case2;

public class DistributedLockTest {
    public static void main(String[] args) throws Exception {
        final DistributedLock lock1 = new DistributedLock();
        final DistributedLock lock2 = new DistributedLock();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    lock1.Lock();
                    System.out.println("线程1启动");
                    Thread.sleep(5000);
                    lock1.unlock();
                    System.out.println("线程1解锁");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    lock2.Lock();
                    System.out.println("线程2启动");
                    Thread.sleep(5000);
                    lock2.unlock();
                    System.out.println("线程2解锁");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }
}

```



![截屏2021-11-09 19.37.42](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-09%2019.37.42.png)

#### 5.2 Curator框架实现分布式

-   原生API缺点
    -   会话异步处理需要自己就处理
    -   Watch需要重复注册，否则不生效
    -   开发复杂性高
    -   不支持多点删除和创建，需要自己递归

-   依赖

```xml
```



-   代码

```java
package case3;

import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.framework.recipes.locks.InterProcessMutex;
import org.apache.curator.retry.ExponentialBackoffRetry;

public class CuratorLockTest {
    public static void main(String[] args) {
        //创建分布式锁1
        InterProcessMutex lock1 = new InterProcessMutex(getCuratorFramework(),"/locks");

        //创建分布式锁2
        InterProcessMutex lock2 = new InterProcessMutex(getCuratorFramework(),"/locks");

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    lock1.acquire();
                    System.out.println("线程1获取到锁");
                    lock1.acquire();
                    System.out.println("线程1再次获取到锁");
                    Thread.sleep(5000);
                    lock1.release();
                    System.out.println("线程1释放锁");
                    lock1.release();
                    System.out.println("线程1再次释放锁");
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
                    System.out.println("线程2获取到锁");
                    lock2.acquire();
                    System.out.println("线程2再次获取到锁");
                    Thread.sleep(5000);
                    lock2.release();
                    System.out.println("线程2释放锁");
                    lock2.release();
                    System.out.println("线程2再次释放锁");
                } catch (Exception e) {
                    e.printStackTrace();
                }

            }
        }).start();
    }

    private static CuratorFramework getCuratorFramework(){
        CuratorFramework client = CuratorFrameworkFactory.builder().connectString("www.wolves.top:30081,www.wolves.top:30082,www.wolves.top:30083")
                .connectionTimeoutMs(2000)
                .sessionTimeoutMs(2000)
                .retryPolicy(new ExponentialBackoffRetry(3000, 3)).build();
        client.start();
        return client;
    }
}

```

![截屏2021-11-09 19.59.15](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-09%2019.59.15.png)

## 六、重点问题

### 6.1 选举机制

-   半数机制，超过半数的投票通过，即确定Leader
-   第一次启动选举规则：
    -   投票超过半数时，服务器ID大的胜出
-   第二次启动选举规则：（依次）
    -   EPOCH大的胜出
    -   事务ID大大胜出
    -   服务器ID大的胜出

-   数据结构
    -   服务器ID
    -   选举状态
        -   FOLLOWING 随从状态
        -   LEADING 领导者状态
        -   LOOKING 竞选状态
        -   OBSERVING 观察状态（不投票）
    -   事务ID（ZXID）：id越大说明数据越新，选举时权重也就越大
    -   逻辑时钟EPOCH：每个Leader任期的代号，没有Leader时同一轮投票过程中的逻辑时钟值相同。每次投票完这个数据会增加，如果某台机器宕机，那么此机器就不会参与投票，则时钟也会比其他的机器低

### 6.2 生产集群安装多少zk合适

-   **奇数台**
-   生产经验
    -   10台服务器：3台zk
    -   20台服务器：5台zk
    -   100台服务器：11台zk
    -   200台服务器：11台zk
    -   服务器台数多：
        -   好处：提高可靠性
        -   坏处：提高通信延迟

## 七、算法源码

-   提高模式
