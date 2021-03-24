## @Conditional

> 在项目中第一次看到该注解，所以学一下

@Conditional是Spring4新提供的注解，作用是按照一定的条件判断，如果满足条件则给容器注册bean

##### 注解定义

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Conditional {
    Class<? extends Condition>[] value();
}
//从代码中可以看到，需要传入一个Class数组，并且需要继承Condition接口
```

##### 接口定义

```java
@FunctionalInterface
public interface Condition {
    boolean matches(ConditionContext var1, AnnotatedTypeMetadata var2);
}
```

Condition是个接口，需要实现matches方法，返回true则注入bean，false则不注入

#### 使用方法

定义接口实现类判断方法

```java
public class WindowsCondition implements Condition {
    @Override
    public boolean matches(ConditionContext conditionContext, AnnotatedTypeMetadata annotatedTypeMetadata) {
        Environment environment = conditionContext.getEnvironment();
        String osName = environment.getProperty(OS_KEY);
        return !StringUtils.isEmpty(osName) && osName.toLowerCase().contains(WIN_OS_NAME);
    }
}
// mactches判断是否为Windows系统运行
```

配置注入

```java
@Service
@Conditional(WindowsCondition.class)
public class IpConfigService {
    //如果判断为windows系统，则注入该Service
}
```

可以配置多个

```java
@Conditional({WindowsCondition.class,OsSystemCondition.class})
//同为true则true，反之false
```

