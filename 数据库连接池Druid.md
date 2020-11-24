> 数据库连接池类似线程池，都是为了减少创建和关闭数据库连接的开销，使资源最大化使用

`Druid`是阿里巴巴的开源项目，它除了是一个**高性能数据库连接池**外，更是一个**自带监控**的数据库数据库连接池，目前使用它的人还挺多的，值得学习使用。

监控信息是对于这个应用实例的数据源而言，并不是数据库全局层面，可以视为应用层的监控，不能作为中间件层的监控。

## 配置Druid数据源

**第一步：**在==porm.xml==中引入druid官方提供的包

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.1.21</version>
</dependency>
```

**第二步：**在==application.properties==或==application.yml==中配置数据库连接信息，**Druid**的配置都以==spring.datasource.druid==作为前缀，下面以properties为示例

```properties
spring.datasource.druid.url=jdbc:mysql://localhost:3306/test
spring.datasource.druid.username=root
spring.datasource.druid.password=root
spring.datasource.druid.driver-class-name=com.mysql.cj.jdbc.Driver
```

**第三步：**配置**Druid**连接池（可选），默认会有部分配置

```properties
spring.datasource.druid.initialSize=10  //初始化建立连接数
spring.datasource.druid.maxActive=20	//最大连接池数量
spring.datasource.druid.maxWait=60000	//	获取连接时最大等待时间
spring.datasource.druid.minIdle=1	//最小连接池数量
spring.datasource.druid.timeBetweenEvictionRunsMillis=60000	//检测连接使用的空闲时间，大于则关闭
spring.datasource.druid.minEvictableIdleTimeMillis=300000	//连接保持空闲的最小时间
spring.datasource.druid.testWhileIdle=true	//建议配置为true，不影响性能，并且保证安全性。申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。
spring.datasource.druid.testOnBorrow=true	//申请连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。默认值
spring.datasource.druid.testOnReturn=false	//归还连接时执行validationQuery检测连接是否有效，做了这个配置会降低性能。默认值
spring.datasource.druid.poolPreparedStatements=true	//是否缓存preparedStatement，也就是PSCache。PSCache对支持游标的数据库性能提升巨大，比如说oracle。在mysql下建议关闭。
spring.datasource.druid.maxOpenPreparedStatements=20	//要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100
spring.datasource.druid.validationQuery=SELECT 1	//用来检测连接是否有效的sql，要求是一个查询语句，常用select ‘x’。如果validationQuery为null，testOnBorrow、testOnReturn、testWhileIdle都不会起作用。
spring.datasource.druid.validation-query-timeout=500	//	单位：秒，检测连接是否有效的超时时间。底层调用jdbc Statement对象的void setQueryTimeout(int seconds)方法
spring.datasource.druid.filters=stat	//属性类型是字符串，通过别名的方式配置扩展插件，常用的插件有： 监控统计用的filter:stat 日志用的filter:log4j 防御sql注入的filter:wall
```

## 配置Druid监控

上面是配置**Druid**的数据源使用，配好后就可以操作。而既然用了**Druid**可以顺便配置一下监控看看。

```properties
spring.datasource.druid.stat-view-servlet.enabled=true	//开启stat
spring.datasource.druid.stat-view-servlet.url-pattern=/druid/*	//访问地址规则
spring.datasource.druid.stat-view-servlet.reset-enable=true	//是否允许清空数据统计
spring.datasource.druid.stat-view-servlet.login-username=admin	//监控页面登录账户
spring.datasource.druid.stat-view-servlet.login-password=admin	//监控页面登录密码
```

## 访问监控

访问监控页面http://localhost:8080/druid/

![image-20200925094856654](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20200925094856654.png)

常用监控页面：

* 数据源：查看配置数据库连接池信息，以及各种使用指标
* SQL监控：对SQL语句的统计，执行频率，执行效率等等
* SQL防火墙：记录与SQL监控不同维度的监控数据，对表访问维度，SQL防御维度的统计，需要配置==filters=stat,wall==

![image-20200925095643428](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20200925095643428.png)

![image-20200925094949291](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20200925094949291.png)

![image-20200925095712831](https://xie-cloud-oss.oss-cn-shanghai.aliyuncs.com/images/image-20200925095712831.png)

## 后记

spring官方也有监控工具，==spring-boot-starter-actuator==是监控系统健康情况的工具，如实例化Bean信息、JVM环境信息、application.properties信息和控制器映射信息等等，有需要的可以自行去了解

[参考资料](http://blog.didispace.com/spring-boot-learning-21-3-3/)