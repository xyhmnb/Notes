JavaBean

```java
public class SemiAutoMessage {
    private Origin origin;
    private String action;
    private T data;
}
class Origin{
    private String name;
    private String ip;
    @Value("${raybox.version}")
    private String version;
}
```

json数据

```json
{
    "origin": {
        "name": "Raybox",
        "ip": "10.1.1.1",
        "version": "2.0.1"
    },
    "action": 2,
    "data":{
    }
}
```

JsonUtil.toBean