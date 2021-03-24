> 在SpringBoot启动以及初始化容器的过程中，可以监听各个阶段的事件

下面按照**ApplicationListener**支持事件的顺序列出：

* ==ApplicationStartingEvent==
* ==ApplicationEnvironmentPreparedEvent==
* ==ApplicationPreparedEvent==
* ==ApplicationStartedEvent==
* ==ApplicationReadyEvent==
* ==ApplicationFailedEvent==

事件的监听触发按照如上顺序，其中如果初始化失败，则**ApplicationFailedEvent**事件会在**ApplicationStartedEvent**前执行，**ApplicationReadyEvent**和**ApplicationStartedEvent**不会执行。

#### 测试

添加自定义监听器

```java 
@Slf4j
public class ApplicationEnvironmentPreparedEventListener implements ApplicationListener<ApplicationEnvironmentPreparedEvent> {
    @Override
    public void onApplicationEvent(ApplicationEnvironmentPreparedEvent applicationEnvironmentPreparedEvent) {
        log.info("....ApplicationEnvironmentPreparedEvent...");
    }
}

@Slf4j
public class ApplicationFailedEventListener implements ApplicationListener<ApplicationFailedEvent> {
    @Override
    public void onApplicationEvent(ApplicationFailedEvent applicationFailedEvent) {
        log.info("....ApplicationFailedEvent...");
    }
}

@Slf4j
public class ApplicationPreparedEventListener implements ApplicationListener<ApplicationPreparedEvent> {
    @Override
    public void onApplicationEvent(ApplicationPreparedEvent applicationPreparedEvent) {
        log.info("...ApplicationPreparedEvent...");
    }
}

@Slf4j
public class ApplicationReadyEventListener implements ApplicationListener<ApplicationReadyEvent> {
    @Override
    public void onApplicationEvent(ApplicationReadyEvent applicationReadyEvent) {
        log.info("...ApplicationReadyEventListener");
    }
}

@Slf4j
public class ApplicationStartedEventListener implements ApplicationListener<ApplicationStartedEvent> {
    @Override
    public void onApplicationEvent(ApplicationStartedEvent applicationStartedEvent) {
        log.info("...ApplicationStartedEvent...");
    }
}

public class ApplicationStartingEventListener implements ApplicationListener<ApplicationStartingEvent> {
    @Override
    public void onApplicationEvent(ApplicationStartingEvent applicationStartingEvent) {
        System.out.println("...ApplicationStartingEvent...");
    }
}
```

改造主函数，添加自定义监听器到启动函数

```java 
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication springApplication = new SpringApplication(DemoApplication.class);
        springApplication.addListeners(new ApplicationStartingEventListener());
        springApplication.addListeners(new ApplicationPreparedEventListener());
        springApplication.addListeners(new ApplicationFailedEventListener());
        springApplication.addListeners(new ApplicationStartedEventListener());
        springApplication.addListeners(new ApplicationReadyEventListener());
        springApplication.addListeners(new ApplicationEnvironmentPreparedEventListener());
        springApplication.run(args);
        // SpringApplication.run(DemoApplication.class, args);
    }
    
    //使用Bean进行注入，如果容器初始化失败则不会执行，例如new ApplicationPreparedEventListener不管失败与否都会执行，而使用bean注入失败就不会执行
   @Bean
   @EventListener(ApplicationPreparedEvent.class)
   public void preparedEvent(){
        System.out.println("applicationpreparedevent");
   }
}
```

#### 结果

启动成功

![image-20200925103918097](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20200925103918097.png)

启动失败

![image-20200924195617652](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20200924195617652.png)

**经过测试发现确实按照指定顺序执行**

## 后记

在自定义监听**ApplicationStartingEvent**时不能使用@slf4j注解输出，因为此时刚启动还没加载，所以只能使用sout输出测试。