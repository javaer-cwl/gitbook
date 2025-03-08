# WebSocket支持

在 Spring Boot 中，实现 WebSocket 功能主要有两种方式：

1. **使用 `@ServerEndpoint` 注解**：将 WebSocket 类直接注册到 Spring 容器中。
2. **使用 `WebSocketConfigurer` 接口**：通过配置类注册 WebSocket 端点。

下面分别介绍这两种实现方式。

***

### **方式 1：使用 `@ServerEndpoint` 注解**

这种方式类似于传统的 Java WebSocket 实现，但需要将 WebSocket 类注册到 Spring 容器中。

#### **实现步骤**

**1. 添加依赖**

在 `pom.xml` 中添加 WebSocket 依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

**2. 创建 WebSocket 类**

使用 `@ServerEndpoint` 注解定义 WebSocket 端点，并通过 `@Component` 将其注册到 Spring 容器中。

```java
import org.springframework.stereotype.Component;
import javax.websocket.*;
import javax.websocket.server.ServerEndpoint;
import java.io.IOException;

@Component
@ServerEndpoint("/websocket") // 定义 WebSocket 端点路径
public class MyWebSocket {

    @OnOpen
    public void onOpen(Session session) {
        System.out.println("WebSocket 连接建立: " + session.getId());
    }

    @OnMessage
    public void onMessage(String message, Session session) throws IOException {
        System.out.println("收到消息: " + message);
        // 回复消息
        session.getBasicRemote().sendText("服务器收到: " + message);
    }

    @OnClose
    public void onClose(Session session) {
        System.out.println("WebSocket 连接关闭: " + session.getId());
    }

    @OnError
    public void onError(Session session, Throwable throwable) {
        System.out.println("WebSocket 发生错误: " + throwable.getMessage());
    }
}
```

**3. 注册 WebSocket 到 Spring 容器**

通过 `ServerEndpointExporter` 将 `@ServerEndpoint` 注解的类注册到 Spring 容器中。

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.server.standard.ServerEndpointExporter;

@Configuration
public class WebSocketConfig {

    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }
}
```

**4. 测试 WebSocket**

使用 WebSocket 客户端工具（如 websocat 或浏览器控制台）测试 WebSocket 连接。

***

### **方式 2：使用 `WebSocketConfigurer` 接口**

这种方式是 Spring 推荐的方式，通过配置类注册 WebSocket 端点，并可以配置拦截器和消息处理器。

#### **实现步骤**

**1. 添加依赖**

在 `pom.xml` 中添加 WebSocket 依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

**2. 创建 WebSocket 处理器**

实现 `WebSocketHandler` 接口，处理 WebSocket 消息。

```java
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;

public class MyWebSocketHandler extends TextWebSocketHandler {

    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        System.out.println("收到消息: " + message.getPayload());
        // 回复消息
        session.sendMessage(new TextMessage("服务器收到: " + message.getPayload()));
    }

    @Override
    public void afterConnectionEstablished(WebSocketSession session) throws Exception {
        System.out.println("WebSocket 连接建立: " + session.getId());
    }

    @Override
    public void afterConnectionClosed(WebSocketSession session, CloseStatus status) throws Exception {
        System.out.println("WebSocket 连接关闭: " + session.getId());
    }
}
```

**3. 配置 WebSocket 端点**

通过 `WebSocketConfigurer` 接口注册 WebSocket 端点，并配置拦截器。

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.config.annotation.EnableWebSocket;
import org.springframework.web.socket.config.annotation.WebSocketConfigurer;
import org.springframework.web.socket.config.annotation.WebSocketHandlerRegistry;

@Configuration
@EnableWebSocket // 启用 WebSocket 支持
public class WebSocketConfig implements WebSocketConfigurer {

    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        registry.addHandler(new MyWebSocketHandler(), "/websocket") // 注册处理器和端点路径
                .addInterceptors(new MyWebSocketInterceptor()); // 添加拦截器
    }
}
```

**4. 创建 WebSocket 拦截器**

实现 `HandshakeInterceptor` 接口，用于在握手前后进行拦截。

```java
import org.springframework.http.server.ServerHttpRequest;
import org.springframework.http.server.ServerHttpResponse;
import org.springframework.web.socket.WebSocketHandler;
import org.springframework.web.socket.server.HandshakeInterceptor;

import java.util.Map;

public class MyWebSocketInterceptor implements HandshakeInterceptor {

    @Override
    public boolean beforeHandshake(ServerHttpRequest request, ServerHttpResponse response,
                                   WebSocketHandler wsHandler, Map<String, Object> attributes) throws Exception {
        System.out.println("WebSocket 握手前");
        return true; // 返回 true 表示允许握手
    }

    @Override
    public void afterHandshake(ServerHttpRequest request, ServerHttpResponse response,
                               WebSocketHandler wsHandler, Exception exception) {
        System.out.println("WebSocket 握手后");
    }
}
```

**5. 测试 WebSocket**

使用 WebSocket 客户端工具测试 WebSocket 连接。

***

### **两种方式的对比**

| 特性                | `@ServerEndpoint` 方式  | `WebSocketConfigurer` 方式 |
| ----------------- | --------------------- | ------------------------ |
| **实现方式**          | 基于 Java WebSocket API | 基于 Spring WebSocket API  |
| **注册到 Spring 容器** | 需要手动注册                | 自动注册                     |
| **拦截器支持**         | 不支持                   | 支持                       |
| **消息处理器支持**       | 不支持                   | 支持                       |
| **推荐场景**          | 简单 WebSocket 需求       | 复杂 WebSocket 需求（如需要拦截器）  |

***

### **总结**

* 如果需要快速实现简单的 WebSocket 功能，可以使用 `@ServerEndpoint` 方式。
* 如果需要更复杂的 WebSocket 功能（如拦截器、消息处理器），推荐使用 `WebSocketConfigurer` 方式。

根据实际需求选择合适的方式即可！
