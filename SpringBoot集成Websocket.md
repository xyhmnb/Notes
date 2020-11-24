> 使用Spring自带websocket框架

spring在4.0后将websocket集成进去，要使用spring的websocket的话，spring的版本要在4.0及以上。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

### 开发步骤

#### 添加ws拦截器

```java
@Configuration
public class SemiAutoWebSocketConfig implements WebSocketConfigurer {

    @Resource
    private SemiAutoWebSocketInterceptor semiAutoWebSocketInterceptor;

    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        registry.addHandler(semiAutoWebSocketHandler(), "/ws/semiauto").addInterceptors(semiAutoWebSocketInterceptor).setAllowedOrigins("*");
    }

    @Bean
    public SemiAutoWebSocketHandler semiAutoWebSocketHandler() {
        return new SemiAutoWebSocketHandler();
    }
}
```

实现`WebSocketConfigurer`接口，addHandler()：添加ws连接处理器；addInterceptors()：添加ws消息处理器；setAllowedOrigins()：允许请求来源；

```java
public class SemiAutoWebSocketInterceptor implements HandshakeInterceptor {
    //连接前置权限校验
    @Override
    public boolean beforeHandshake(ServerHttpRequest serverHttpRequest, ServerHttpResponse serverHttpResponse, WebSocketHandler webSocketHandler, Map<String, Object> map) {
        //获取ws连接信息中的参数，如ws://127.0.0.1/ws/semiauto?token=123
        ServletServerHttpRequest servletServerHttpRequest = (ServletServerHttpRequest) serverHttpRequest;
        String semiAutoToken = servletServerHttpRequest.getServletRequest().getParameter("token");
        return true;
    }

    @Override
    public void afterHandshake(ServerHttpRequest serverHttpRequest, ServerHttpResponse serverHttpResponse, WebSocketHandler webSocketHandler, Exception e) {
    }
}
```

```java
public class SemiAutoWebSocketHandler implements WebSocketHandler {

    @Override
    public void afterConnectionEstablished(WebSocketSession webSocketSession) throws Exception {
        //ws连接成功处理
    }

    @Override
    public void handleMessage(WebSocketSession webSocketSession, WebSocketMessage<?> webSocketMessage) throws Exception {
		//ws消息处理
    }

    @Override
    public void handleTransportError(WebSocketSession webSocketSession, Throwable throwable) throws Exception {
        //ws异常处理
    }

    @Override
    public void afterConnectionClosed(WebSocketSession webSocketSession, CloseStatus closeStatus) throws Exception {
        //ws连接断开处理
    }

    @Override
    public boolean supportsPartialMessages() {
        return false;
    }
}

```



