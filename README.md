# SpringBoot整合dubbo示例
## 一、定义API（API模块）
### 1. 定义api
```java
package com.imooc.springboot.dubbo.demo;

public interface DemoService {
    String sayHello(String name);
}
```
### 2. install到本地
对外提供的maven坐标如下：
```xml
<dependency>
    <groupId>com.imooc</groupId>
    <artifactId>dubbo-demo-api</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```
## 二、服务提供者（provider模块）
### 1. 增加maven依赖
```xml
<!-- springboot parent -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.3.RELEASE</version>
</parent>
<!-- springboot dubbo starter -->
<dependency>
    <groupId>io.dubbo.springboot</groupId>
    <artifactId>spring-boot-starter-dubbo</artifactId>
    <version>1.0.0</version>
</dependency>
<!-- api dependence -->
<dependency>
    <groupId>com.imooc</groupId>
    <artifactId>dubbo-demo-api</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```
### 2. 实现API接口
```java
package com.imooc.springboot.dubbo.demo.provider;

import com.alibaba.dubbo.config.annotation.Service;
import com.imooc.springboot.dubbo.demo.DemoService;

@Service
public class DemoServiceImpl implements DemoService {

    public String sayHello(String name) {
        return "Hello, " + name + " (from Spring Boot)";
    }

}
```
### 3. springboot配置文件 - application.properties
```bash
spring.dubbo.application.name=demo-provider
#这里使用广播的注册方式，
#如果有Can't assign address异常需要加vm参数：
#-Djava.net.preferIPv4Stack=true
spring.dubbo.registry.address=multicast://224.5.6.7:1234
spring.dubbo.protocol.name=dubbo
spring.dubbo.protocol.port=20880
spring.dubbo.scan=com.imooc.springboot.dubbo.demo.provider
```

### 4. 启动类
```java
package com.imooc.springboot.dubbo.demo.provider;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class MainProvider {
    public static void main(String[] args) {
        SpringApplication.run(MainProvider.class,args);
    }
}
```
## 三、服务消费者（consumer模块）
### 1. 增加maven依赖
```xml
<!-- springboot parent -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.3.RELEASE</version>
</parent>
<!-- springboot web starter -->
<dependency>
   <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!-- springboot dubbo starter -->
<dependency>
    <groupId>io.dubbo.springboot</groupId>
    <artifactId>spring-boot-starter-dubbo</artifactId>
    <version>1.0.0</version>
</dependency>
<!-- api dependency -->
<dependency>
    <groupId>com.imooc</groupId>
    <artifactId>dubbo-demo-api</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```
### 2. 实现controller
```java
package com.imooc.springboot.dubbo.demo.consumer;

import com.alibaba.dubbo.config.annotation.Reference;
import com.imooc.springboot.dubbo.demo.DemoService;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class DemoConsumerController {

    @Reference
    private DemoService demoService;

    @RequestMapping("/sayHello")
    public String sayHello(@RequestParam String name) {
        return demoService.sayHello(name);
    }

}
```
### 3. springboot配置文件 - application.properties
```bash
server.port=8080
#dubbo config
spring.dubbo.application.name=demo-consumer
#这里使用广播的注册方式，
#如果有Can't assign address异常需要加vm参数：
#-Djava.net.preferIPv4Stack=true
spring.dubbo.registry.address=multicast://224.5.6.7:1234
spring.dubbo.scan=com.imooc.springboot.dubbo.demo.consumer
```

### 4. 启动类
```java
package com.imooc.springboot.dubbo.demo.consumer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Main {

    public static void main(String[] args) {
        SpringApplication.run(Main.class,args);
    }

}
```
