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

-   打断park线程

-   part 可以暂停进程，但是只可暂停一次

-   interrupt打断会继续该进程

-   ```java
    @Slf4j(topic = "c.part")
    public class Part {
        public static void main(String[] args) throws InterruptedException {
            Thread t1 = new Thread(()->{
                log.debug("part.....");
                LockSupport.park();
                log.debug("unpart");
                log.debug("打断状态：{}",Thread.currentThread().isInterrupted());
            });
            t1.start();
    
            TimeUnit.SECONDS.sleep(1);
            t1.interrupt();
        }
    }
    ```

-   重置标记即可重新part打断，即使用Thread.interrupted

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

-   isInterrupted interrupted均为判断是否打断
    -   isInterrupted 判断后不会清楚打断标记
    -   interrupted 判断后会清除打断标记

### 1.15 不推荐的方法

-   stop() 停止线程运行
-   suspend() 挂起（暂停）线程运行
-   resume() 恢复线程运行

### 1.16 守护线程

-   默认情况下，Java所有进程需要等待所有线程都运行结束，才会结束。有一种特殊线程叫做守护线程，只要其他非守护线程运行结束了，即时守护线程的代码没有执行完，也会强制结束。

-   ```java
    import lombok.extern.slf4j.Slf4j;
    
    @Slf4j(topic = "c.daemon")
    public class Daemon {
        public static void main(String[] args) throws InterruptedException {
            Thread t1 = new Thread(()->{
                while (true){
                    if (Thread.currentThread().isInterrupted()){
                        break;
                    }
                }
                log.debug("结束");
            },"t1");
            t1.setDaemon(true);
            t1.start();
    
            Thread.sleep(1000);
            log.debug("结束");
        }
    }
    ```

-   setDaemon() 方法

-   Tomcat中Acceptor和Poller线程都是守护线程，所以Tomcat接收到shutdown后，不会等待它们的任务处理完

### 1.17 五种状态

-   从操作系统层面描述

![1011637916088_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1011637916088_.pic_hd.jpg)

-   初始状态：仅在语言层面创建了线程对象，还未与操作系统线程关联
-   可运行状态：就绪状态，指该线程已经被创建，与操作系统线程关联，可以由CPU调度执行
-   运行状态：指获取了CPU时间片运行汇中的状态
    -   当CPU时间片完时，会从运行状态转换至可运行状态，会产生线程的上下文切换
-   阻塞状态
    -   如果调用了阻塞API，如BIO读写文件，这时线程实际不会用到CPU，会导致上下文切换，进入阻塞状态
    -   当BIO操作完毕，会由操作系统唤醒阻塞的线程，转换至可运行状态
    -   与可运行状态的区别是，对阻塞状态的线程来说只要它们一直不换行，调度器就一直不回考虑它们
-   终止状态
    -   表示线程已经执行完毕，声明周期结束，不会再转换为其他状态

### 1.18 六种状态

-   根据Java API层面描述
-   根据Thread.State，分为6种状态

![1021637916844_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1021637916844_.pic_hd.jpg)

-   NEW：初始状态
-   RUNNABLE：三合一
-   TERMINATED：终止状态
-   阻塞状态细分
    -   BLOCKED：未获得锁
    -   WAITING：无限等待
    -   TIMED_WAITING：有时限等待



### 1.19 例子，多线程泡茶

![截屏2021-11-26 19.49.28](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/%E6%88%AA%E5%B1%8F2021-11-26%2019.49.28.png)

```java
package com.li.n5;

import com.li.sleep.Sleep;
import lombok.extern.slf4j.Slf4j;

/**
 * @author li
 */

@Slf4j(topic = "c.boil")
public class BoilWater {
    public static void main(String[] args) {
        Thread t1 = new Thread(()->{
            log.debug("老王洗水壶");
            Sleep.sleep(1);
            log.debug("老王洗完了水壶");
            Sleep.sleep(1);
            log.debug("老王烧水");
            Sleep.sleep(5);
            log.debug("老王烧开了水");
        },"老王");

        Thread t2 = new Thread(()->{
            log.debug("小李洗茶壶");
            Sleep.sleep(1);
            log.debug("小李洗完了茶壶");
            log.debug("小李洗茶杯");
            Sleep.sleep(2);
            log.debug("小李洗完了茶杯");
            log.debug("小李拿茶叶");
            Sleep.sleep(1);
            log.debug("小李拿到了茶叶");
            try {
                t1.join();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            log.debug("小李泡茶");
        },"小李");


        t1.start();
        t2.start();
    }
}
```

-   运行结果

    >20:01:11:111[小李]c.boil - 小李洗茶壶
    >20:01:11:111[老王]c.boil - 老王洗水壶
    >20:01:12:112[小李]c.boil - 小李洗完了茶壶
    >20:01:12:112[老王]c.boil - 老王洗完了水壶
    >20:01:12:112[小李]c.boil - 小李洗茶杯
    >20:01:13:113[老王]c.boil - 老王烧水
    >20:01:14:114[小李]c.boil - 小李洗完了茶杯
    >20:01:14:114[小李]c.boil - 小李拿茶叶
    >20:01:15:115[小李]c.boil - 小李拿到了茶叶
    >20:01:18:118[老王]c.boil - 老王烧开了水
    >20:01:18:118[小李]c.boil - 小李泡茶

## 二、共享模型之管程

### 2.1 共享带来的问题

-   模拟

-   ```java
    package com.li.safe;
    
    import lombok.extern.slf4j.Slf4j;
    import org.junit.Test;
    
    @Slf4j(topic = "c.unsafe")
    public class SafeUn {
        int count = 0;
        @Test
        public void test01() throws InterruptedException {
            Thread t1 = new Thread(()->{
                for (int i = 0;i<5000;i++){
                    count++;
                }
            },"t1");
            Thread t2 = new Thread(()->{
                for (int i = 0;i<5000;i++){
                    count--;
                }
            },"t2");
            t1.start();
            t2.start();
            t1.join();
            t2.join();
            log.debug("{}",count);
        }
    }
    ```

-   结果可能不为1,由于分时系统导致的上下文切换引起指令交错导致写入数据不一致

-   临界区
    -   多个线程访问共享资源（写操作）
    -   一段代码块如果存在对共享资源的多线程读写操作，称此代码块为**临界区**
-   竞态条件
    -   多个线程在临界区内执行，由于代码块执行顺序不同而导致结果无法预测，称之为发生了**竞态条件**

### 2.2 synchronized解决方案

-   阻塞式：synchronized，lock
-   非阻塞式：原子变量



-   synchronized 对象锁

```java
package com.li.safe;

import lombok.extern.slf4j.Slf4j;
import org.junit.Test;

@Slf4j(topic = "c.unsafe")
public class SafeUn {
    int count = 0;
    @Test
    public void test01() throws InterruptedException {
        Thread t1 = new Thread(()->{
            for (int i = 0;i<5000;i++){
                synchronized (this){
                    count++;
                }
            }
        },"t1");
        Thread t2 = new Thread(()->{
            for (int i = 0;i<5000;i++){
                synchronized (this) {
                    count--;
                }
            }
        },"t2");
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        log.debug("{}",count);
    }
}
```

-   此时输出结果均为0

-   对象锁保证了临界区的原子性

-   面向对象改进  面向过程->面向对象 即实现线程安全

```java
package com.li.safe;

import lombok.extern.slf4j.Slf4j;
import org.junit.Test;

@Slf4j(topic = "c.unsafe")
public class SafeUn {
    Room room = new Room();

    @Test
    public void test01() throws InterruptedException {
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 5000; i++) {
                room.increase();
            }
        }, "t1");
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 5000; i++) {
                room.decrease();
            }
        }, "t2");
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        log.debug("{}", room);
    }
}

class Room {
    private int counter = 0;

    public void increase() {
        synchronized (this) {
            counter++;
        }
    }

    public void decrease() {
        synchronized (this) {
            counter--;
        }
    }

    @Override
    public String toString() {
        return "Room{" +
                "counter=" + counter +
                '}';
    }
}

```

### 2.3 方法上的synchronized

```java
		public synchronized void decrease() {
        counter--;
    }
```

-   等同于

```java
		public void decrease() {
        synchronized (this) {
            counter--;
        }
    }
```

-   实际上是锁着当前对象
-   **线程八锁**
    -   考察synchronized锁住的是哪个对象
    -   锁住的是`this`即为 `Number` 类![1051638152516_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1051638152516_.pic_hd.jpg)
    -   加锁与上一致，只是多了个`sleep()`![1061638152667_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1061638152667_.pic_hd.jpg)
    -   方法三未加synchronized，因此方法三未加锁，一旦启动将并行执行![1071638152773_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1071638152773_.pic_hd.jpg)
    -   情况四是两个对象分别加锁，因此运行方法时均不会互斥![1081638153051_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1081638153051_.pic_hd.jpg)
    -   情况5与情况4相同，两者加锁对象不同，不互斥![1081638153051_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1081638153051_.pic_hd.jpg)
    -   情况6与情况2相同，不同的是static![1091638153496_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1091638153496_.pic_hd.jpg)
    -   情况7和情况6相同，锁住同一个对象![1101638153569_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1101638153569_.pic_hd.jpg)
    -   情况8与情况7一样![1111638154026_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1111638154026_.pic_hd.jpg)

-   static
    -   加static的是全局锁，锁住的是当前类
    -   不加static的是实例锁，锁住的是某个对象

### 2.4 变量的线程安全分析

#### 2.4.1 成员变量和静态变量是否线程安全

-   如果它们没有共享，则线程安全
-   如果它们被共享了
    -   如果只有读操作，则线程安全
    -   如果有写操作，则为临界区需要考虑线程安全

#### 2.4.2 局部变量是否线程安全

-   局部变量是线程安全的
-   局部变量引入的对象未必安全
    -   如果对象没有逃离方法的作用访问，则是线程安全的
    -   如果改对象逃离方法的作用范围，需要考虑线程安全
-   引入对象如子类继承
    -   修饰符在一定程度上可以提供线程安全

#### 2.4.3 售票

```java
package com.li.safe;

import lombok.extern.slf4j.Slf4j;

import java.util.ArrayList;
import java.util.List;
import java.util.Random;
import java.util.Vector;

@Slf4j(topic = "c.cell")
public class ExerciseShell {
    public static void main(String[] args) throws InterruptedException {
        TickWindow tickWindow = new TickWindow(1000);
        List<Integer> amountList = new Vector<>();
        List<Thread> threadList = new ArrayList<>();
        for (int i = 0; i < 2000; i++) {
            Thread thread = new Thread(() -> {
                int sell = tickWindow.sell(randomAmount());
                try {
                    Thread.sleep(randomAmount());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                amountList.add(sell);
            });
            threadList.add(thread);
            thread.start();
        }
        for (Thread thread : threadList) {
            thread.join();
        }
        log.debug("余票:{}",tickWindow.getCount());
        log.debug("卖出了：{}",amountList.stream().mapToInt(i->i).sum());
    }

    static Random random = new Random();
    public static int randomAmount(){
        return random.nextInt(5)+1;
    }

}

class TickWindow {
    private int count;

    public TickWindow(int count) {
        this.count = count;
    }

    public int getCount() {
        return count;
    }

    public int sell(int amount) {
        if (this.count >= amount) {
            this.count -= amount;
            return amount;
        } else {
            return 0;
        }
    }
}
```

-   此时并未使用线程安全，会导致超卖

-   解决方案，在sell方法加入synchronized修饰符

#### 2.4.4 转账

```java
package com.li.safe;

import lombok.extern.slf4j.Slf4j;

import java.util.Random;

@Slf4j(topic = "c.transfer")
public class Transfer {
    public static void main(String[] args) throws InterruptedException {
        Account account1 = new Account(1000);
        Account account2 = new Account(1000);
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                account1.transfer(account2, randomAmount());
            }
        }, "t1");
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                account2.transfer(account1, randomAmount());
            }
        }, "t2");
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        log.debug("{}", account1.getMoney() + account2.getMoney());
    }

    static Random random = new Random();

    public static int randomAmount() {
        return random.nextInt(1000) + 1;
    }
}

class Account {
    private int money;

    public Account(int money) {
        this.money = money;
    }

    public int getMoney() {
        return money;
    }

    public void setMoney(int money) {
        this.money = money;
    }

    public void transfer(Account target, int money) {
        if (this.money >= money) {
            this.setMoney(this.getMoney() - money);
            target.setMoney(target.getMoney() + money);
        }
    }
}
```

-   测试现象很明显
-   解决方案
    -   首先无法在transfer加关键字，会导致死锁
    -   需要对两个共享变量都加锁，可以对Account类加锁

```java
public void transfer(Account target, int money) {
    synchronized (Account.class) {
        if (this.money >= money) {
            this.setMoney(this.getMoney() - money);
            target.setMoney(target.getMoney() + money);
        }
    }
}
```

-   此时就不会出现线程安全问题，但是效率较低

## 三、锁

### 3.1 Monitor-锁

Mark Wrod结构

![1141638251789_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1141638251789_.pic_hd.jpg)

-   Monitor 监视器/管程 操作系统提供
-   每个Java对象都可以关联一个Monitor对象，如果使用synchronized给对象上锁（重量级）之后，该对象头的Mark Word中就被设置指向Monitor指针
-   ![1131638240125_.pic](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1131638240125_.pic.jpg)
-   Monitor中的EntryList存储需要锁的进程
-   Owner为正在拥有此锁的进程

### 3.2 synchronized原理

-   锁的状态一共有四种：无锁状态，偏向锁，轻量级锁和重量级锁

-   | **锁状态** | 25 bit                       | 4bit         | 1bit         | 2bit |      |
    | ---------- | ---------------------------- | ------------ | ------------ | ---- | ---- |
    | 23bit      | 2bit                         | 是否是偏向锁 | 锁标志位     |      |      |
    | 轻量级锁   | 指向栈中锁记录的指针         | 00           |              |      |      |
    | 重量级锁   | 指向互斥量（重量级锁）的指针 | 10           |              |      |      |
    | GC标记     | 空                           | 11           |              |      |      |
    | 偏向锁     | 线程ID                       | Epoch        | 对象分代年龄 | 1    | 01   |
    | 无锁       | 对象的hashCode               | 对象分代年龄 | 0            | 01   |      |

#### 3.2.1 偏向锁

-   某个锁专属于某个线程
-   不需要重复获取锁，释放锁，只需要在运行时查看这个锁是不是偏向自己的
-   第一次使用CAS将线程ID设置到对象的Mark Word头，之后发现这个线程ID是自己的就表示没有竞争，不用重新cas直接归此线程所有
-   ![1231638254097_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1231638254097_.pic_hd.jpg)

##### 3.2.2 偏向状态

-   偏向锁默认存在延迟，初始启动时不一定会显示
-   可以加入VM参数：-XX:BiasedLockingStartupDelay=0  代表无延迟
-   如果未开启偏向锁，则为001
-   禁用偏向锁 `-XX:-UseBiasedLocking`
-   一旦调用 hashCode()方法，会禁用偏向锁
    -   hashcode默认为0，只有调用hashCode时才会生成
    -   因为hashcode需要占用31位，而偏向锁的线程id就占用了54位，一共64位会导致空间不足，自动转换为无偏向锁的Mark Word
-   其他线程使用对象的时候，会将偏向锁升级为轻量级锁
-   重偏向
    -   当撤销偏向20次后，jvm就会给对象加锁时重新设置偏向，并且为批量重偏向
-   批量撤销
    -   当撤销偏向锁超过40次后，jvm会人品偏向错误，根本不应该偏向，这时整个类所有对象都会变为不可偏向的，新建对象也是不可偏向的。

#### 3.2.2 轻量级锁 

![1221638253876_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1221638253876_.pic_hd.jpg)

-   如果一个对象虽然有多线程访问，但是多线程访问的时间是错开的，那么可以用轻量级锁进行优化
-   语法仍是synchronized

-   加锁

>   （1）在代码进入同步块的时候，如果同步对象锁状态为无锁状态（锁标志位为“01”状态，是否为偏向锁为“0”），虚拟机首先将在当前线程的栈帧中建立一个名为锁记录（Lock Record）的空间，用于存储锁对象目前的Mark Word的拷贝，官方称之为 Displaced Mark Word。这时候线程堆栈与对象头的状态如图2.1所示。（2）拷贝对象头中的Mark Word复制到锁记录中。
>
>   （3）拷贝成功后，虚拟机将使用CAS操作尝试将对象的Mark Word更新为指向Lock Record的指针，并将Lock record里的owner指针指向object mark word。如果更新成功，则执行步骤（3），否则执行步骤（4）
>
>   （4）如果这个更新动作成功了，那么这个线程就拥有了该对象的锁，并且对象Mark Word的锁标志位设置为“00”，即表示此对象处于轻量级锁定状态，这时候线程堆栈与对象头的状态如图2.2所示。
>
>   （5）如果这个更新操作失败了，虚拟机首先会检查对象的Mark Word是否指向当前线程的栈帧，如果是就说明当前线程已经拥有了这个对象的锁，那就可以直接进入同步块继续执行。否则说明多个线程竞争锁，轻量级锁就要膨胀为重量级锁，锁标志的状态值变为“10”，Mark Word中存储的就是指向重量级锁（互斥量）的指针，后面等待锁的线程也要进入阻塞状态。 而当前线程便尝试使用自旋来获取锁，自旋就是为了不让线程阻塞，而采用循环去获取锁的过程。

![820406-20160424105442866-2111954866](https://images2015.cnblogs.com/blog/820406/201604/820406-20160424105442866-2111954866.png)

![820406-20160424105540163-1019388398](https://images2015.cnblogs.com/blog/820406/201604/820406-20160424105540163-1019388398.png)

-   删锁

>（1）通过CAS操作尝试把线程中复制的Displaced Mark Word对象替换当前的Mark Word。
>
>（2）如果替换成功，整个同步过程就完成了。
>
>（3）如果替换失败，说明有其他线程尝试过获取该锁（此时锁已膨胀），那就要在释放锁的同时，唤醒被挂起的线程。



-   创建锁记录（Lock Record）对象，每个线程的栈针都会包含一个锁记录的结构，内部可以储存锁定对象的Mark Word

-   ![1161638252002_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1161638252002_.pic_hd.jpg)

-   让锁记录中的Object reference指向锁对象，并尝试用cas替换Object的Mark Word，将Mark Word的值存入锁记录

-   ![1151638252001_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1151638252001_.pic_hd.jpg)

-   如果cas替换成功，对象头中存储了锁记录地址和状态00，表示由该线程给对象加锁

-   ![1171638252175_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1171638252175_.pic_hd.jpg)

    -   cas即为替换操作

-   如果cas失败

    -   如果是其他线程已经持有了该Object的轻量级锁，这表明有竞争，进入锁膨胀过程
    -   如果是自己执行了synchronized锁重入，那么再添加一条Lock Record作为重入的计数
    -   ![1181638252533_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1181638252533_.pic_hd.jpg)
    -   重入会存入null
    -   每次解锁都会删除一个Lock Record

-   退出sync代码块（解锁时），如果有null值的锁记录，表示有重入，此时重置锁记录，表示重入记录减一

-   ![1191638252668_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1191638252668_.pic_hd.jpg)

-   当退出sync代码块的锁记录值不为null时，这时cas将MarkWord 的值恢复给对象头

    -   成功即成功
    -   失败说明轻量级锁已经转变为重量级锁，进入重量级锁解锁流程

    

#### 3.2.3 锁膨胀

-   将轻量级锁升级为重量级锁
-   ![1201638253001_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1201638253001_.pic_hd.jpg)
-   线程加锁失败
    -   为对象申请Monitor锁，让Object指向重量级锁地址
    -   然后自己进入Monitor的EntryList Blocked队列中
    -   ![1211638253113_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1211638253113_.pic_hd.jpg)
    -   Thread-0解锁时，Owner置为null，唤醒Blocked的线程

#### 3.2.4 自旋优化

-   重量锁竞争时，可以通过自旋进行优化，即循环获得锁，可以避免阻塞
-   自旋为多核优化，多核处理器才会有实际体现

#### 3.2.5 锁消除

-   JIT会进一步优化
-   若加锁对象是一个不可能被共享的，则此时加的锁会自动消除

#### 3.2.6 锁粗化

-   比如一个for中调用了多个synchronized，可以被粗化为一个synchronized中有个for循环

### 3.3 wait/notify

![1241638259294_.pic](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1241638259294_.pic.jpg)

![1251638259321_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1251638259321_.pic_hd.jpg)

#### 3.3.1 原理

![1261638259358_.pic_hd](https://typroa-wolves.oss-cn-hangzhou.aliyuncs.com/img-li/1261638259358_.pic_hd.jpg)

-   Owner线程发现条件不满足，调用wait方法，进入WatiSet变为WAITING状态
-   Blocked和waiting的线程都处于阻塞状态，不占用时间片
-   blocked线程会在Owner线程释放锁时唤醒
-   Waiting线程会在Owner线程调用notify或notifyAll时唤醒，唤醒时不代表直接获得锁，而是进入EntryList重新竞争

##### 3.3.2 API

-   必须获得锁的对象才能调用以下方法

    -   `obj.wait()`

    -   `obj.notify()`

    -   `obj.notifyAll()`

-   eg

-   

-   ```java
    package com.li.waitset;
    
    import lombok.extern.slf4j.Slf4j;
    import org.junit.Test;
    
    import java.util.concurrent.TimeUnit;
    
    @Slf4j(topic = "c.wait")
    public class waitSet {
        Object obj = new Object();
        @Test
        public void test01(){
            new Thread(()->{
                synchronized (obj){
                    try {
                        log.debug("开始wait");
                        obj.wait();
                        log.debug("被唤醒");
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            },"t1").start();
            new Thread(()->{
                try {
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (obj){
                    log.debug("唤醒线程");
                    obj.notify();
                }
            },"t2").start();
    
            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            log.debug("流程结束");
        }
    }
    ```