# SpringBoot 循环依赖

在 Spring Boot 中，循环依赖（Circular Dependency）是指两个或多个 Bean 相互依赖，导致 Spring 容器无法正确初始化它们。例如，Bean A 依赖 Bean B，而 Bean B 又依赖 Bean A，这就形成了循环依赖。以下是解决循环依赖的常见方法：

***

### **1. 使用构造器注入 + `@Lazy` 注解**

* **构造器注入**：Spring 官方推荐使用构造器注入，因为它可以避免循环依赖问题。
* **`@Lazy` 注解**：延迟加载 Bean，打破循环依赖的初始化顺序。

```java
@Service
public class ServiceA {
    private final ServiceB serviceB;

    @Lazy
    public ServiceA(ServiceB serviceB) {
        this.serviceB = serviceB;
    }
}

@Service
public class ServiceB {
    private final ServiceA serviceA;

    @Lazy
    public ServiceB(ServiceA serviceA) {
        this.serviceA = serviceA;
    }
}
```

***

### **2. 使用字段注入 + `@Lazy` 注解**

如果必须使用字段注入，可以通过 `@Lazy` 注解延迟加载 Bean。

```java
@Service
public class ServiceA {
    @Autowired
    @Lazy
    private ServiceB serviceB;
}

@Service
public class ServiceB {
    @Autowired
    @Lazy
    private ServiceA serviceA;
}
```

***

### **3. 使用 `@PostConstruct` 方法注入**

在 Bean 初始化完成后，通过 `@PostConstruct` 方法手动注入依赖。

```java
@Service
public class ServiceA {
    private ServiceB serviceB;

    @Autowired
    public void setServiceB(ServiceB serviceB) {
        this.serviceB = serviceB;
    }

    @PostConstruct
    public void init() {
        // 初始化逻辑
    }
}

@Service
public class ServiceB {
    private ServiceA serviceA;

    @Autowired
    public void setServiceA(ServiceA serviceA) {
        this.serviceA = serviceA;
    }

    @PostConstruct
    public void init() {
        // 初始化逻辑
    }
}
```

***

### **4. 使用 `ApplicationContext` 手动获取 Bean**

在需要时，通过 `ApplicationContext` 手动获取 Bean，而不是直接注入。

```java
@Service
public class ServiceA {
    @Autowired
    private ApplicationContext applicationContext;

    private ServiceB getServiceB() {
        return applicationContext.getBean(ServiceB.class);
    }
}

@Service
public class ServiceB {
    @Autowired
    private ApplicationContext applicationContext;

    private ServiceA getServiceA() {
        return applicationContext.getBean(ServiceA.class);
    }
}
```

***

### **5. 重构代码，消除循环依赖**

从根本上解决循环依赖问题，通常需要重新设计代码结构：

* **提取公共逻辑**：将相互依赖的部分提取到一个新的 Bean 中。
* **使用接口或抽象类**：通过接口或抽象类解耦具体的实现。
* **引入事件驱动**：使用事件监听机制（如 `ApplicationEvent`）代替直接依赖。

***

### **6. 使用 `@DependsOn` 注解**

通过 `@DependsOn` 明确指定 Bean 的初始化顺序，避免循环依赖。

```java
@Service
@DependsOn("serviceB")
public class ServiceA {
    @Autowired
    private ServiceB serviceB;
}

@Service
public class ServiceB {
    @Autowired
    private ServiceA serviceA;
}
```

***

### **7. 启用 Spring 的循环依赖支持**

Spring 默认支持单例 Bean 的循环依赖，但可以通过配置强制启用或禁用：

```properties
# 启用循环依赖支持（默认值）
spring.main.allow-circular-references=true

# 禁用循环依赖支持
spring.main.allow-circular-references=false
```

***

### **8. 使用代理模式**

通过代理模式（如 JDK 动态代理或 CGLIB）延迟 Bean 的初始化。

```java
@Service
public class ServiceA {
    @Autowired
    private ServiceB serviceB;
}

@Service
public class ServiceB {
    @Autowired
    private ServiceA serviceA;
}
```

***

### **总结**

* **优先使用构造器注入**，避免循环依赖问题。
* 如果必须使用字段注入，可以结合 `@Lazy` 注解。
* 从设计上避免循环依赖是最佳实践，重构代码结构通常是根本解决方案。
* 在特殊情况下，可以通过 `@PostConstruct`、`ApplicationContext` 或 `@DependsOn` 等方式解决。

通过以上方法，可以有效解决 Spring Boot 中的循环依赖问题
