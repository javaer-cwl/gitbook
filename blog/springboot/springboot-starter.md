# SpringBoot Starter

&#x20;       在 Spring Boot 中，自定义 Starter 是一种将常用功能封装为独立模块的方式，方便在其他项目中复用。以下是实现自定义 Starter 的详细步骤：

***

## 一、**创建自定义 Starter 项目**

## **1. 项目结构**

创建一个 Maven 或 Gradle 项目，结构如下：

```markdown
my-starter/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/mystarter/
│   │   │       ├── MyStarterAutoConfiguration.java
│   │   │       └── MyService.java
│   │   └── resources/
│   │       └── META-INF/
│   │           └── spring.factories
│   └── test/
│       └── java/
└── pom.xml 或 build.gradle
```

## **2. 添加依赖**

在 `pom.xml` 或 `build.gradle` 中添加 Spring Boot 依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-autoconfigure</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

***

## 二、**实现自定义功能**

### **1. 定义配置类**

创建一个配置类，用于启用自定义功能：

```java
package com.example.mystarter;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MyStarterAutoConfiguration {

    @Bean
    public MyService myService() {
        return new MyService();
    }
}
```

### **2. 定义服务类**

实现自定义功能：

```java
package com.example.mystarter;

public class MyService {
    public String sayHello(String name) {
        return "Hello, " + name + "!";
    }
}
```

***

## 三、**配置自动装配**

### **1. 注册自动配置类**

在 `src/main/resources/META-INF/spring.factories` 中注册自动配置类：

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.example.mystarter.MyStarterAutoConfiguration
```

### **2. 打包发布**

使用 Maven 或 Gradle 打包项目：

```bash
mvn clean install
```

***

## 四、**在其他项目中使用自定义 Starter**

### **1. 添加依赖**

在目标项目的 `pom.xml` 或 `build.gradle` 中添加自定义 Starter 依赖：

```xml
<dependency>
    <groupId>com.example</groupId>
    <artifactId>my-starter</artifactId>
    <version>1.0.0</version>
</dependency>
```

### **2. 使用自定义功能**

在 Spring Boot 项目中直接注入并使用自定义服务：

```java
import com.example.mystarter.MyService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class MyController {

    @Autowired
    private MyService myService;

    @GetMapping("/hello")
    public String hello(@RequestParam String name) {
        return myService.sayHello(name);
    }
}
```

***

## 五、**可选：支持自定义配置**

### **1. 定义配置属性类**

创建一个配置属性类，用于读取配置文件中的自定义属性：

```java
package com.example.mystarter;

import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties(prefix = "my.starter")
public class MyStarterProperties {
    private String prefix = "Hello";
    private String suffix = "!";

    // Getter and Setter
}
```

### **2. 修改配置类**

在配置类中使用自定义属性：

```java
@Configuration
@EnableConfigurationProperties(MyStarterProperties.class)
public class MyStarterAutoConfiguration {

    @Bean
    public MyService myService(MyStarterProperties properties) {
        return new MyService(properties.getPrefix(), properties.getSuffix());
    }
}
```

### **3. 修改服务类**

在服务类中使用自定义属性：

```java
public class MyService {
    private final String prefix;
    private final String suffix;

    public MyService(String prefix, String suffix) {
        this.prefix = prefix;
        this.suffix = suffix;
    }

    public String sayHello(String name) {
        return prefix + ", " + name + suffix;
    }
}
```

### **4. 在配置文件中定义属性**

在 `application.properties` 或 `application.yml` 中定义自定义属性：

```properties
my.starter.prefix=Hi
my.starter.suffix=!!!
```

***

## 六、**Spring Boot 2.x 和 3.x 的区别**

### **1. 自动配置注册方式**

*   **Spring Boot 2.x**：\
    使用 `META-INF/spring.factories` 文件注册自动配置类。\
    示例：

    ```properties
    org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
    com.example.mystarter.MyStarterAutoConfiguration
    ```
*   **Spring Boot 3.x**：\
    推荐使用 `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` 文件注册自动配置类。\
    示例：

    ```
    com.example.mystarter.MyStarterAutoConfiguration
    ```

### **2. 依赖管理**

* **Spring Boot 3.x**：\
  升级了部分依赖的版本，例如：
  * **Java 基线版本**：Spring Boot 3.x 要求至少 Java 17。
  * **Jakarta EE 命名空间**：从 `javax.*` 迁移到 `jakarta.*`。
  * **依赖包的变化**：例如 `spring-boot-starter-web` 可能被替换为 `spring-boot-starter-webflux`。

### **3. 配置属性处理**

* **Spring Boot 3.x**：\
  引入了新的配置属性处理机制，支持更严格的类型检查和验证。

## 七、**总结**

&#x20;       通过以上步骤，您可以创建一个自定义 Spring Boot Starter，并将其发布到 Maven 仓库或其他项目中复用。自定义 Starter 的核心步骤包括：

1. 创建项目并添加依赖；
2. 实现自定义功能；
3. 配置自动装配；
4. 在其他项目中引入并使用。

支持自定义配置可以让 Starter 更加灵活，适用于不同场景。
