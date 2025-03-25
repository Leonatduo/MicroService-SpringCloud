# OpenFeign 远程调用简化方案

## 背景问题
在上一节中，我们使用Nacos实现了服务治理，利用RestTemplate实现了服务远程调用。但存在以下问题：
- 远程调用代码过于复杂
- 与本地方法调用差异大，编程体验不统一

## OpenFeign解决方案
OpenFeign通过声明式HTTP客户端，让远程调用像本地方法调用一样简单。其核心是处理四个关键点：
1. 请求方式
2. 请求路径
3. 请求参数
4. 返回值类型

OpenFeign利用SpringMVC注解声明这些参数，基于动态代理生成远程调用代码。

## 4.1 快速入门

### 4.1.1 引入依赖
在`cart-service`的`pom.xml`中添加：

```xml
<!-- openFeign -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>

<!-- 负载均衡器 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```
## 4.1.2 启用OpenFeign

在Spring Boot应用的启动类上添加`@EnableFeignClients`注解：

```java
@SpringBootApplication
@EnableFeignClients  // 启用OpenFeign功能
public class CartServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(CartServiceApplication.class, args);
    }
}
```

## 4.1.3 编写OpenFeign客户端

### 基本使用方式
在服务中定义一个接口作为Feign客户端，**无需实现方法**，只需声明接口即可：

```java
@FeignClient("item-service")  // 声明要调用的服务名称
public interface ItemClient {
    
    @GetMapping("/items")  // 声明GET请求方式和路径
    List<ItemDTO> queryItemsByIds(@RequestParam("ids") Collection<Long> ids);
}
```
这里只需要声明接口，无需实现方法。接口中的几个关键信息：
- @FeignClient("item-service") ：声明服务名称
- @GetMapping ：声明请求方式
- @GetMapping("/items") ：声明请求路径
- @RequestParam("ids") Collection<Long> ids ：声明请求参数
- List<ItemDTO> ：返回值类型

有了上述信息，OpenFeign就可以利用动态代理帮我们实现这个方法，并且向http://item-service/items发送一个GET请求，携带ids为请求参数，并自动将返回值处理为List<ItemDTO>。
我们只需要直接调用这个方法，即可实现远程调用了。
