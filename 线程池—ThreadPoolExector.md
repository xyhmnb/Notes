## 简单介绍

==ThreadPoolExecutor==源码继承结构图：

![image-20200918164201804](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20200918164201804.png)

==ThreadPoolExecutor==接口实现类，继承抽象类==AbstractExecutorService==，而抽象类实现==ExecutorService==接口，所以基本上==ExecutorService==方法它都有，同时还可以继承该类实现定制化

==AbstractExecutorService==定义的方法基本和==ExecutorService==接口的方法大致一样

![image-20200918172100519](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20200918172100519.png)

既然是接口的实现类，那就可以实现更多的功能，更多的定制化。

## 源码查看

#### 构造函数（4种）

![image-20200917175513306](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20200917175513306.png)

```shell
corePoolSize	#核心线程数
maximumPoolSize	#最大线程数
keepAliveTime	#线程空闲时间，该参数在线程数大于核心线程数时才有效，超过就会关闭线程
unit	#时间单位
workQueue	#阻塞任务队列
ThreadFactory	#线程创建工厂
handler	#拒绝策略
```

线程池刚被创建时并不会自动创建**corePoolSize**个线程为0，而是根据提交任务来新建，当前线程数为**poolSize**

1. **poolSize** < **corePoolSize**时，无论是否有空闲线程，都会创建新线程处理新提交的任务。当设置**allowCoreThreadTimeOut(true)**时，线程池中**corePoolSize**线程空闲时间达到**keepAliveTime**也将关闭，默认为==false==
2. **poolSize>=corePoolSize**时，新提交任务将被放入**workQueue**，等待线程池中任务调度执行
3. **poolSize>=corePoolSize**且**workQueue**已满时
   1. **poolSize<maximumPoolSize**时，将**新增线程**来处理任务
   2. **poolSize=maximumPoolSize**时，线程池的处理能力已经达到极限。拒绝新增加的任务，交给**RejectedExecutionHandler**处理
4. 当线程池中超过**corePoolSize**线程，空闲时间达到**keepAliveTime**时，关闭空闲线程

总结：线程池的判断就像夹心饼，**|corePoolSize|workQueue|maximumPoolSize|**，按照顺序一个一个判断，而且在队列满了以后，新提交的任务会创建新线程执行，而不是消费阻塞队列的任务(即如果**poolSize**达到**maximumPoolSize**，而且一直运行没有空闲线程，那么**workQueue**永远不会被执行)

> 以上仅针对**ThreadPoolExecutor**，不保证**Executors**中构造线程池方法。==注意==，在Dubbo中，有一种线程池**EagerThreadPoolExecutor**，是**|corePoolSize|maximumPoolSize|workQueue|**

##### allowCoreThreadTimeout

设置为==true==后，允许核心线程超时，只用来控制核心线程默认为==false==

##### keepAliveTime

某线程空闲时间超过该值就会关闭——前提条件有**poolSize<corePoolSize**，**allCoreThreadTimeout=true**。同时满足前两个条件的情况下，再去判断时间。

##### rejectedExecutionHandler

任务拒绝处理器，两种情况会拒绝处理任务：

1. 线程数达到最大线程数且队列已满，会拒绝新任务
2. 当线程池调用==shutdown==后，会等待剩余线程池执行完毕，如果此时新提交任务就会被拒绝。

不指定默认使用==AbortPolicy==会抛出异常

==ThreadPoolExecutor==有几个内部实现类可以来处理：

* AbortPolicy	丢弃任务，抛运行时异常
* CallerRunsPolicy 执行任务
* DiscardPolicy  忽视，什么都不会发生
* DiscardOldestPolicy  从队列中踢出最先进入队列（最后一个执行）的任务

当然也可以实现==RejectedExecutionHandler==接口，自定义处理器

#### 使用

在==Executors==中的四种线程池构造方法都用到了==ThreadPoolExecutor==

##### newFixedThreadPool

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
}
```

corePoolSize与maximumPoolSize大小相同，同时使用了一个无界LinkedBlockingQueue存放阻塞任务，因此多余的任务将存在再阻塞队列，不会由RejectedExecutionHandler处理

* corePoolSize与maximumPoolSize相等，即其线程全为核心线程，是一个固定大小的线程池，是其优势；
* keepAliveTime = 0 默认对核心线程无效，即FixedThreadPool全部为核心线程；
* workQueue 为LinkedBlockingQueue（**无界阻塞队列**），队列最大值为Integer.MAX_VALUE。如果任务提交速度持续大于任务处理速度，会造成队列大量阻塞。因为队列很大，很有可能在拒绝策略前，内存溢出。是其劣势。
* FixedThreadPool的任务执行是无序的；

适用场景：可用于web服务瞬时晓峰，但需注意长时间持续高峰情况造成的队列阻塞。

##### newCachedThreadPool

```java
public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
```

任务提交之后，将会创建新的线程执行；线程空闲超过60s将会销毁

* corePoolSize=0，maximumPoolSize=Integer.MAX_VALUE，即线程数量几乎无限制
* keepAliveTime=60s，线程空闲60s后自动结束
* workQueue 为 SynchronousQueue 同步队列，这个队列类似于一个接力棒，入队出队必须同时传递，因为CachedThreadPool线程创建无限制，不会有队列等待，所以使用SynchronousQueue；

使用场景：快速处理大量耗时较短的任务，如Netty的NIO接收请求，可使用CachedThreadPool

##### newSingleThreadExecutor

```java
public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }
```

**注意：**这里是用**FinalizableDelegatedExecutorService**包装了一层。**FixedThreadPool**可以向下转型为**ThreadPoolExecutor**，并对其线程池进行配置，而**SingleThreadExecutor**被包装后，无法成功向下转型，成功做到了**Single**

* corePoolSize=maximumPoolSize=1，只支持一个线程
* LinkedBlockingQueue，无界阻塞队列

##### newScheduleThreadPool

```java
public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
        return new ScheduledThreadPoolExecutor(corePoolSize);
    }
public ScheduledThreadPoolExecutor(int corePoolSize) {
        super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
              new DelayedWorkQueue());
    }
```

## shutdown

> 在学习ExecutorService时注意到需要显式关闭线程池，这一点对**ThreadPoolExecutor**同样适用。

在**局部变量**使用**线程池**时，**一定要记得手动关闭**，否则会出现线程泄露问题(无法回收，浪费内存)，或者设置==allowCoreThreadTimeOut(true)==将核心线程也纳入超时判断

```java
public class TestThreadShutdowm {
    public static void main(String[] args) throws InterruptedException {
        RuntimeMXBean runtimeMXBean = ManagementFactory.getRuntimeMXBean();
        String jvmName = runtimeMXBean.getName();
        System.out.println("JVM name=" + jvmName);
        long pid = Long.valueOf(jvmName.split("@")[0]);
        System.out.println("JVM PID=" + pid);
        ThreadMXBean bean = ManagementFactory.getThreadMXBean();
        int n = 1000;
        for (int i = 0; i < n; i++) {
            ThreadPoolExecutor executor=new ThreadPoolExecutor(10,20,
                    1000, TimeUnit.SECONDS,new LinkedBlockingDeque<>());
            for (int j = 0; j < 5; j++) {
                executor.execute(()->{
                    System.out.println("当前线程总数为：" + bean.getThreadCount());
                });
            }
            // executor.shutdown();
        }
        Thread.sleep(10000);
        System.out.println("线程总数为=" + bean.getThreadCount());
    }
}
```

输出结果为：线程总数为=5006，此时线程全部泄露无法回收浪费内存。

==如果==取消==executor.shutdown()==注释，最后结果为：线程总数为=6，而且主线程也会关闭

> ==注意==，还有个区别是若你没有shutdonw，那么最终主线程是不会终止的。而如果你shutdown了，主线程跑完也就终止了。

==newCachedThreadPool==线程池可以不需要手动关闭，因为无核心线程数，线程超过60s后会主动关闭。