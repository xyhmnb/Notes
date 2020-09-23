> 在日常使用过程中，经常遇到计算时间量或日期之间的差

* Period，基于日期值，对应使用LocalDate
* Duration，基于时间值，对用使用Instant

==注意：==是分块计算，比如年与年计算，分钟与分钟计算，天与天计算。比如，2020.2.2-2020.2.5和2019.2.3-2020.2.5直接计算差值period.getdays都是3天，这个时候应该先转化再计算。 **toEpochDay**方法转化天数计算

## Period

主要计算差值的日期表示，如年、月、日。

```java
LocalDate now = LocalDate.now();
LocalDate startDate = LocalDate.of(2019, 1, 1);
Period period = Period.between(startDate, now);
System.out.println(period.getYears());
System.out.println(period.getDays());
System.out.println(period.getMonths());
//输出 1,21,8
```

## Duration

主要计算差值的时间表示，如秒，毫秒，纳秒。

between()，不支持LocalDate，应该是失少是秒级或分钟级单位

```java
LocalTime of = LocalTime.of(3, 5,30);
LocalTime of1 = LocalTime.of(3,10,45);
Duration duration1 = Duration.between(of, of1);
System.out.println(duration1.getSeconds());
System.out.println(duration1.toMinutes());
//输出 315,5
```

## 方法

==Duration.isNegative()==，计算差值是否为负数，不包括0，可以用来比较时间前后。