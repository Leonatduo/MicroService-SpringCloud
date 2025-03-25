在上一节，我们利用Nacos实现了服务的治理，利用RestTemplate实现了服务的远程调用。但是远程调用的代码太复杂了：

而且这种调用方式，与原本的本地方法调用差异太大，编程时的体验也不统一，一会儿远程调用，一会儿本地调用。
因此，我们必须想办法改变远程调用的开发模式，让远程调用像本地方法调用一样简单。而这就要用到OpenFeign组件了。
其实远程调用的关键点就在于四个：
- 请求方式
- 请求路径
- 请求参数
- 返回值类型
所以，OpenFeign就利用SpringMVC的相关注解来声明上述4个参数，然后基于动态代理帮我们生成远程调用的代码，而无需我们手动再编写，非常方便。

4.1.快速入门
  4.1.1.引入依赖
  在cart-service服务的pom.xml中引入OpenFeign的依赖和loadBalancer依赖：
    <!--openFeign-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
    <!--负载均衡器-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-loadbalancer</artifactId>
    </dependency>

4.1.2.启用OpenFeign
接下来，我们在service的Application启动类上添加注解@EnableFeignClients，启动OpenFeign功能：

4.1.3.编写OpenFeign客户端
在service中，定义一个新的接口，编写Feign客户端：
这里只需要声明接口，无需实现方法。接口中的几个关键信息：
- @FeignClient("xxx-service") ：声明服务名称
- @GetMapping ：声明请求方式
- @GetMapping("/xxx") ：声明请求路径
- @RequestParam("ids") Collection<Long> ids ：声明请求参数
- List<ItemDTO> ：返回值类型

- Feign替我们完成了服务拉取、负载均衡、发送http请求的所有工作，是不是看起来优雅多了。

而且，这里我们不再需要RestTemplate了，还省去了RestTemplate的注册。

有了上述信息，OpenFeign就可以利用动态代理帮我们实现这个方法，并且发送一个GET请求, 我们只需要直接调用这个方法，即可实现远程调用了。

4.1.4.使用FeignClient
最后，我们在service实现类中改造代码，直接调用Client的方法：
