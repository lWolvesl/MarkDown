# JUC

## 一、进程与线程

### 1.1 简介

-   进程：进程可以视为程序的一个实例。大部分程序可以同时运行多个实例进程，也有的程序只能启动一个实例进程
-   线程：一个进程内可以分为一到多个线程
    -   一个线程就是一个指令流，将指令流中的一条条指令以一定的顺序交给cpu执行
    -   Java中，线程作为最小的调度单位，进程作为资源分配的最小单位

-   二者对比
    -   进程基本上是互相独立的，而线程存在于进程内，是进程的一个子集
    -   进程拥有共享的资源，如内存空间等，供其内部的线程共享
    -   进程间通信较为复杂
        -   同一台计算机进程通信称为IPC
        -   不同计算机之间的进程通信，需要通过网络，并遵守共同的协议，例如HTTP
    -   线程通信相对简单，因为它们共享进程内的内存
    -   线程更轻量，线程上下文切换成本一般上要比进程上下文切换低



### 1.2 并行与并发

![image-20211123190015152](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211123190015152.png)

-   单核cpu线程还是串行执行的
-   操作系统中有一个组件叫做任务调度器，将cpu的时间片分给不同的线程使用
-   微观串行，宏观并行

![image-20211123190333950](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/image-20211123190333950.png)

-   并行，多核cpu



-   并发，在某一时间段
-   并行，在同一时刻



### 1.3 同步和异步

-   从方法的角度来讲：
    -   若需要等待结果的返回，才能继续运行的就是同步
    -   若不需要等待结果的返回，就能继续运行的就是异步
-   同步在多线程中还有另外一层意思就是让多个线程步调一致

```java
@Slf4j(topic = "c.Sync")
public class Async {
    public static void main(String[] args) throws InterruptedException {
        log.debug("同步1");
        Thread.sleep(800);
        log.debug("同步2");
        log.debug("同步3");
    }
}
```

![截屏2021-11-23 20.11.35](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-23%2020.11.35.png)

```java
@Slf4j(topic = "c.Sync")
public class Sync {
    public static void main(String[] args) {
        new Thread(()->{
            log.debug("线程1开始运行");
            try {
                Thread.sleep(800);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            log.debug("线程1结束运行");
        }).start();
        log.debug("异步");
    }
}
```

![截屏2021-11-23 20.12.16](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-23%2020.12.16.png)

-   加速完成任务
    -   注：多核才有效,但是能够在不同任务之间切换
-   IO不占用cpu，只是一般拷贝文件使用的是阻塞IO
    -   非阻塞IO和异步IO优化

### 1.4 创建线程

```java
@Slf4j(topic = "c.creat")
public class CreateThread {

    /**
     *     通过匿名内部类创建
     */
    @Test
    public void method_01(){
        Thread create = new Thread(){
            @Override
            public void run(){
                log.debug("通过匿名内部类创建的线程");
            }
        };
        log.debug("主线程");
        create.start();
    }

    /**
     * 使用Runnable配合Thread
     */
    @Test
    public void method_02(){
        Runnable runnable = () -> log.debug("使用Runnable创建的线程");
        Thread create = new Thread(runnable);
        create.start();
        log.debug("主线程");
    }


    /**
     * 通过重写Runnable接口创建线程
     */
    @Test
    public void method_03(){
        class createThread implements Runnable {
            @Override
            public void run() {
                log.debug("通过重写Runnable接口创建的线程");
            }
        }

        Thread create = new Thread(new createThread());
        create.start();
        log.debug("主线程");
    }
}
```

-   Thread和Runnable的关系
    -   使用Runnable更容易与线程池等API配合
    -   Runnable让类脱离了Thread继承体系，更为灵活
-   **FuntureTask**
    -   间接实现的Runnable接口，并且附带返回值
    -   通过Callable接口实现

```java
		@Test
    public void method_04() throws ExecutionException, InterruptedException {
        FutureTask<Integer> futureTask = new FutureTask<>(() -> {
            log.debug("通过futureTask创建的线程");
            Thread.sleep(1000);
            return 100;
        });

        Thread create = new Thread(futureTask,"task");
        create.start();
        log.debug("主线程");
        log.debug("{}",futureTask.get());
    }
```

```java
FutureTask.get();
会阻塞直到线程完成return
```

### 1.5 观察线程运行

-   交替执行
-   线程执行的先后，不由我们控制

### 1.6 jconsole远程监控进程

`jconsole`

远程连接配置

`java -Djava.rmi.server.hostname='ip地址' -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote='端口' -Dcom.sun.management.jmxremote.ssl=是否安全连接 -Dcom.sun.management.jmxremote.authenticate=是否认证java类`

ip 12345 false false

在java运行机器启用

### 1.7 线程原理

-   栈与栈帧
    -   每个线程启动，JVM就会分配一个栈
    -   每个栈由多个栈针组成，对应着每次调用方法时所占用的内存
    -   每个线程只能由一个活动栈针，对应着当前正在执行的那个方法

### 1.8 线程的上下文切换

-   某些特定的情况，cpu不再执行当前线程，转而执行另一个线程
    -   线程的cpu时间片完
    -   垃圾回收
    -   更高优先级的线程运行
    -   线程自己调用线程中断方法
-   当线程切换发生时，需要由操作系统保存当前状态，并恢复另一个线程的状态，java虚拟机中对应的概念就是程序计数器，保存下一条jvm所需要执行的质地，是线程私有的
-   线程上下文切换频繁会影响性能

### 1.9 Thread常用方法

-   `TimUnit.SECONDS.sleep()`可读性好，设置单位

### 1.10 线程优先级

`Thread.setPriority()`

-   固定参数

`Thread.MIN_PRIORITY`

`Thread.MAX_PRIORITY`

-   设置优先级不一定会按照设置的实现，具体还是按照操作系统任务调度器分配的

### 1.11 Sleep应用

-   有效防止cpu空转
-   可以用**wait或条件变量**实现类似的效果
-   但是后两种均需要加锁，并且需要唤醒操作，一般适用于要同步进行的场景
-   sleep适用于无需锁同步的场景

### 1.12 join方法

-   正常情况下主线程与分线程并行执行
-   使用jion方法
-   join等待线程运行结束，在执行主线程
-   eg

```java
import lombok.extern.slf4j.Slf4j;

import java.util.concurrent.TimeUnit;

@Slf4j(topic = "c.join")
public class TestJoin {
    static int r = 0;
    public static void main(String[] args) throws InterruptedException {
        log.debug("开始");
        Thread t1 = new Thread(()->{
            log.debug("开始");
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            r = 10;
            log.debug("结束");
        },"t1");
        t1.start();
        t1.join();
        log.debug("结果为{}",r);
        log.debug("结束");
    }
}
```

-   体现了线程同步
-   join中加入参数可以设定最大等待时间

### 1.13 interrupt方法

-   阻塞
-   打断sleep的线程，会情况打断队列

```java
@Slf4j(topic = "c.interrupt")
public class TestInterrupt {
    @Test
    public void test01() throws InterruptedException {
        Thread t1 = new Thread(()->{
            log.debug("sleep...");
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"t1");
        t1.start();
        Thread.sleep(500);
        t1.interrupt();
        log.debug("打断标记：{}",t1.isInterrupted());
    }
}
```

![截屏2021-11-24 13.12.00](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-24%2013.12.00.png)

-   正常的打断

-   

-   ```java
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(()->{
            log.debug("start");
            while (true){
                boolean interrupted = Thread.currentThread().isInterrupted();
                if (interrupted){
                    break;
                }
            }
        },"t1");
        t1.start();
        Thread.sleep(1000);
        log.debug("interrupt");
        t1.interrupt();
    }
    ```

### 1.14 两阶段终止 模式

-   在线程1中终止线程2
-   并给线程2料理后事的机会

-   错误的例子
    -   调用stop方法，立刻杀死线程，但是如果该线程对某个资源加锁，则会导致其他线程永远无法获得锁
    -   使用System.exit(0)，会关闭整个进程
-   正确办法
    -   使用一个监控（守护进程）进行定时监控
    
    -   
    
    -   ```java
        import lombok.extern.slf4j.Slf4j;
        
        import java.util.concurrent.TimeUnit;
        
        @Slf4j(topic = "c.inter")
        public class inter {
            public static void main(String[] args) throws InterruptedException {
                TwoPhaseTermination two = new TwoPhaseTermination();
                two.start();
        
                TimeUnit.SECONDS.sleep(5);
                two.stop();
            }
        }
        
        
        @Slf4j(topic = "c.two")
        class TwoPhaseTermination{
            private Thread monitor;
        
            /**
             * 启动监控线程
             */
            public void start(){
                monitor = new Thread(()->{
                    while (true){
                        Thread current = Thread.currentThread();
                        if (current.isInterrupted()){
                            log.debug("料理后事");
                            break;
                        }
                        try {
                            TimeUnit.SECONDS.sleep(1);
                            log.debug("监控记录");
                        } catch (InterruptedException e) {
                            e.printStackTrace();
        
                            //sleep会重置打断标记
                            current.interrupt();
                        }
                    }
                });
                monitor.start();
            }
            /**
             * 终止监控线程
             */
            public void stop(){
                monitor.interrupt();
            }
        }
        ```