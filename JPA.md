#### @EntityScan和@EnableJpaRepositories

在==SpringBoot==应用开发中使用==JPA==时，通常主应用程序所在包或者其他子包的某个位置定义我们的==Entity==和==Repository==，这样基于==SpringBoot==的自动配置无需额外设置。而在多模块或者其他情况下，可能需要手动配置扫描发现

**@EntityScan**

用来扫描和发现指定包及其子包中的==Entity==定义（也就是发现@Entity注解），如：

```java
@EntityScan(backPages = {"com.example.entity","com.example.entities"})
```

**@EnableJpaRepositories**

用来扫描和发现指定包及其子包中的==Repository==定义（也就是发现@Repository注解），如：

```java
@EnableJpaRepositories(basePackages = "com.example.repo")
```





关于开启@EnableJpaRepositories扫描后，可以不使用@Repository注解就能实现注入，前提是继承自JpaRepository类的class。

[参考文章](https://blog.csdn.net/andy_zhang2007/article/details/84064862)

