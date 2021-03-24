### @EnableJpaAuditing审计功能

##### Spring JPA 中，支持在字段或者方法上进行注解 @CreatedDate、@CreatedBy、@LastModifiedDate、@LastModifiedBy

**@CreateDate**
表示该字段为创建时间时间字段，在这个实体被 insert 的时候，会设置默认值

**@CreatedBy**
表示该字段为创建人，在这个实体被insert的时候，会设置值。

**@LastModifiedDate、@LastModifiedBy同理**。

#### 使用方法

1. 在XXX启动类上添加**@EnableJpaAuditin**

```java
@EnableJpaAuditing
@SpringBootApplication
public class TestMainApplication{
    public static void main(String[] args) {
        SpringApplication.run(RayBoxMainApplication.class, args);
    }
}
```

2. 在实体类上添加**@EntityListeners(AuditingEntityListener.class)**
3. 在需要的字段上加注解

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
public class TestAnnotation implements Serializable{
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    
    @CreatedDate
    @JsonFormat(timezone = "GMT+8", pattern = "yyyy-MM-dd HH:mm:ss")
    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private Date createTime;
    
    @CreatedBy
    private String createBy;
    
    @LastModifiedDate
    @JsonFormat(timezone = "GMT+8", pattern = "yyyy-MM-dd HH:mm:ss")
    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private Date updateTime;
    
    @LastModifiedBy
    private String updateBy;
}
```

4. 实现 AuditorAware 接口来返回你需要插入的值（只供@LastModifiedBy和@CreatedBy这两个接口使用）

```java
@Configuration
@Slf4j
public class UserAuditor implements AuditorAware<String> {
    /**
     * 获取当前创建或修改的用户
     * @return
     */
    @Override
    public Optional<String> getCurrentAuditor() {
        UserDetails user;
        try {
            user = (UserDetails) SecurityContextHolder.getContext().getAuthentication().getPrincipal();
            return Optional.ofNullable(user.getUsername());
        }catch (Exception e){
            return Optional.empty();
        }
    }
}
```



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

