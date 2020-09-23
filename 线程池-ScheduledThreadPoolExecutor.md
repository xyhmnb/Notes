## 介绍

在已经拥有**ThreadPoolExecutor**后，场景需要周期性任务线程池或者说定时任务线程池，这样就衍生出了**ScheduledThreadPoolExecutor**，作为可以**定时和周期性执行任务**的线程池。

##### 源码继承图

![image-20200917133932550](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20200917133932550.png)

可以看到，实现了**ScheduledExecutorService**接口，而它又继承了**ExecutorService**接口，所以首先是个线程池，除了线程池的功能，还有定时和周期性执行任务的功能。

## 源码

##### schedule

指定系统时间后，按照间隔执行，与任务毫无关系

##### scheduleAtFixedRate 

以上一个任务开始的时间计时，period时间过去后，检测上一个任务是否执行完毕。如果执行完毕则当前任务立即执行，否则等待上一个任务执行完毕。

##### scheduleWithFixedDelay

以上一个任务结束时开始计时，period时间过去后，立即执行下一个任务。

==注意：==在使用**ScheduledThreadPoolExecutor**执行周期性任务时，如果执行过程中抛出了异常，那么整个线程都会停止执行任务，所以需要catch一切可能的异常。

[csdn参考](https://blog.csdn.net/duoduo18up/article/details/80240972?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.edu_weight&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.edu_weight)