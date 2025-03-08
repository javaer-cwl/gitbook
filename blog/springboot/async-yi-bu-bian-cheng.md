# @Async 异步线程

&#x20;       在 Spring Boot 中，**异步编程**可以通过 `@Async` 注解来实现。`@Async` 注解可以将一个方法标记为异步执行，即该方法会在单独的线程中运行，而不会阻塞主线程。这对于处理耗时任务（如 I/O 操作、远程调用等）非常有用。

***

### **1. 启用异步支持**

&#x20;       在使用 `@Async` 注解之前，需要在 Spring Boot 应用中启用异步支持。可以通过以下两种方式实现：

#### **方式 1：在启动类上添加 `@EnableAsync` 注解**

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.scheduling.annotation.EnableAsync;

@SpringBootApplication
@EnableAsync // 启用异步支持
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
    }
}
```

#### **方式 2：在配置类上添加 `@EnableAsync` 注解**

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableAsync;

@Configuration
@EnableAsync // 启用异步支持
public class AsyncConfig {
}
```

***

### **2. 使用 `@Async` 注解**

&#x20;       在需要异步执行的方法上添加 `@Async` 注解即可。

#### **示例代码**

```java
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;

@Service
public class MyService {

    @Async // 标记为异步方法
    public void asyncTask() {
        try {
            // 模拟耗时任务
            Thread.sleep(5000);
            System.out.println("异步任务执行完成，线程：" + Thread.currentThread().getName());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

#### **调用异步方法**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class MyRunner {

    @Autowired
    private MyService myService;

    public void runAsyncTask() {
        System.out.println("调用异步方法前，线程：" + Thread.currentThread().getName());
        myService.asyncTask(); // 调用异步方法
        System.out.println("调用异步方法后，线程：" + Thread.currentThread().getName());
    }
}
```

#### **输出结果**

```markdown
调用异步方法前，线程：main
调用异步方法后，线程：main
异步任务执行完成，线程：task-1
```

&#x20;       可以看到，`asyncTask` 方法在单独的线程中执行，而主线程不会被阻塞。

***

### **3. 配置线程池**

&#x20;       默认情况下，Spring Boot 使用 `SimpleAsyncTaskExecutor` 来执行异步任务，每次都会创建一个新线程。为了更高效地管理线程，可以自定义线程池。

#### **自定义线程池配置**

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

import java.util.concurrent.Executor;

@Configuration
public class AsyncConfig {

    @Bean(name = "taskExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(10); // 核心线程数
        executor.setMaxPoolSize(20); // 最大线程数
        executor.setQueueCapacity(50); // 队列容量
        executor.setThreadNamePrefix("AsyncThread-"); // 线程名前缀
        executor.initialize();
        return executor;
    }
}
```

#### **指定线程池**

&#x20;      在 `@Async` 注解中指定线程池的名称。

```java
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;

@Service
public class MyService {

    @Async("taskExecutor") // 指定线程池
    public void asyncTask() {
        try {
            Thread.sleep(5000);
            System.out.println("异步任务执行完成，线程：" + Thread.currentThread().getName());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

***

### **4. 处理异步方法的返回值**

&#x20;       如果异步方法有返回值，可以使用 `Future` 或 `CompletableFuture` 来接收结果。

#### **示例代码**

```java
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;

import java.util.concurrent.CompletableFuture;

@Service
public class MyService {

    @Async
    public CompletableFuture<String> asyncTaskWithResult() {
        try {
            Thread.sleep(5000);
            return CompletableFuture.completedFuture("异步任务执行完成");
        } catch (InterruptedException e) {
            return CompletableFuture.completedFuture("任务被中断");
        }
    }
}
```

#### **调用异步方法并获取结果**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.util.concurrent.CompletableFuture;

@Component
public class MyRunner {

    @Autowired
    private MyService myService;

    public void runAsyncTaskWithResult() throws Exception {
        System.out.println("调用异步方法前，线程：" + Thread.currentThread().getName());
        CompletableFuture<String> future = myService.asyncTaskWithResult(); // 调用异步方法
        System.out.println("调用异步方法后，线程：" + Thread.currentThread().getName());
        System.out.println("异步任务结果：" + future.get()); // 获取结果
    }
}
```

***

### **5. 注意事项**

1. **`@Async` 注解的限制**
   * 只能用于 `public` 方法。
   * 不能在同一类中调用异步方法（因为 Spring 的代理机制无法生效）。
2. **异常处理**\
   异步方法中的异常不会被传播到调用线程。可以通过实现 `AsyncUncaughtExceptionHandler` 来处理异常。
3. **线程池配置**\
   建议始终配置线程池，避免无限制地创建新线程。

***

### **总结**

* 使用 `@Async` 注解可以轻松实现异步编程。
* 通过 `@EnableAsync` 启用异步支持。
* 配置线程池以提高性能。
* 使用 `Future` 或 `CompletableFuture` 处理异步方法的返回值。
