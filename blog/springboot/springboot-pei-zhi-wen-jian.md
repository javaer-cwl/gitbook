# SpringBoot 配置文件

在 Spring Boot 中，可以通过多种方式获取配置文件（如 `application.properties` 或 `application.yml`）中的配置值。以下是常用的方法：

***

### **1. 使用 `@Value` 注解**

通过 `@Value` 注解可以直接将配置文件中的值注入到字段或方法参数中。

#### **示例代码**

{% code fullWidth="false" %}
```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class MyConfig {

    @Value("${app.name}") // 获取配置文件中的 app.name 值
    private String appName;

    @Value("${app.version:1.0.0}") // 如果 app.version 不存在，默认值为 1.0.0
    private String appVersion;

    public void printConfig() {
        System.out.println("App Name: " + appName);
        System.out.println("App Version: " + appVersion);
    }
}
```
{% endcode %}

#### **配置文件 (`application.properties`)**

```properties
propertiesapp.name=MyApp
```

#### **配置文件 (`application.yml`)**

```yaml
app:
  name: MyApp
```

***

### **2. 使用 `@ConfigurationProperties` 注解**

通过 `@ConfigurationProperties` 注解可以将配置文件中的值绑定到一个 Java 对象中，适合需要获取多个相关配置的场景。

#### **示例代码**

```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "app") // 绑定以 app 为前缀的配置
public class AppConfig {

    private String name;
    private String version;

    // Getter 和 Setter 方法
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getVersion() {
        return version;
    }

    public void setVersion(String version) {
        this.version = version;
    }

    public void printConfig() {
        System.out.println("App Name: " + name);
        System.out.println("App Version: " + version);
    }
}
```

#### **配置文件 (`application.properties`)**

```yaml
app.name=MyApp
app.version=2.0.0
```

#### **配置文件 (`application.yml`)**

```yaml
app:
  name: MyApp
  version: 2.0.0
```

***

### **3. 使用 `Environment` 对象**

通过 `Environment` 对象可以动态获取配置文件中的值。

#### **示例代码**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;

@Component
public class MyConfig {

    @Autowired
    private Environment env;

    public void printConfig() {
        String appName = env.getProperty("app.name");
        String appVersion = env.getProperty("app.version", "1.0.0"); // 默认值
        System.out.println("App Name: " + appName);
        System.out.println("App Version: " + appVersion);
    }
}
```

***

### **4. 使用 `@PropertySource` 注解**

如果需要加载自定义的配置文件（非 `application.properties` 或 `application.yml`），可以使用 `@PropertySource` 注解。

#### **示例代码**

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;

@Component
@PropertySource("classpath:custom.properties") // 加载自定义配置文件
public class CustomConfig {

    @Value("${custom.name}")
    private String customName;

    public void printConfig() {
        System.out.println("Custom Name: " + customName);
    }
}
```

#### **自定义配置文件 (`custom.properties`)**

```yaml
custom.name=MyCustomApp
```

***

### **5. 使用 `@ConfigurationProperties` 和 `@EnableConfigurationProperties`**

如果需要将配置绑定到一个非组件的类中，可以使用 `@ConfigurationProperties` 和 `@EnableConfigurationProperties`。

#### **示例代码**

```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Configuration;

@Configuration
@ConfigurationProperties(prefix = "app")
public class AppConfig {

    private String name;
    private String version;

    // Getter 和 Setter 方法
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getVersion() {
        return version;
    }

    public void setVersion(String version) {
        this.version = version;
    }
}
```

#### **启用配置绑定**

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.properties.EnableConfigurationProperties;

@SpringBootApplication
@EnableConfigurationProperties(AppConfig.class) // 启用配置绑定
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
    }
}
```

***

### **6. 动态获取配置文件的值**

如果需要根据条件动态获取配置文件的值，可以使用 `Environment` 或 `@Value` 结合 SpEL（Spring Expression Language）。

#### **示例代码**

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class DynamicConfig {

    @Value("#{environment['app.name'] ?: 'DefaultApp'}") // 使用 SpEL
    private String appName;

    public void printConfig() {
        System.out.println("App Name: " + appName);
    }
}
```

***

### **总结**

* 使用 `@Value` 注解：适合获取单个配置值。
* 使用 `@ConfigurationProperties`：适合获取多个相关配置值。
* 使用 `Environment`：适合动态获取配置值。
* 使用 `@PropertySource`：适合加载自定义配置文件。
