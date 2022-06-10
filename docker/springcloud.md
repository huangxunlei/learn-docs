# SpringCloud Gateway 

使用的时webflux中的reactor-netty响应式编程组件，底层使用了Netty通讯框架。



## 能干什么

- 反向代理
- 鉴权
- 流量熔断
- 日志监控
- 。。。。。



## 特点

基于Spring Framework5 ，Project  Reactor和Spring boot 2之上，使用非租塞线程

## 概念

### router(路由)

路由是构建网关的基本模块，它由ID,目标URI,一系列的断言和过滤器组成，如果断言为true则匹配该路由。

### predicate(断言)

开发人员可以匹配hTTP请求中的所有内容（例如请求头或请求参数），如果请求与断言相匹配则进行路由。

### Filter(过滤)

指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。





```java
@Configuration
class GateWayConfig {

@Bean 
public RouteLocator routes(RouteLocatorBuilder builder) {    
    return builder.routes()
        .route("circuitbreaker_route", r -> r.path("/consumingServiceEndpoint").filters(f -> f.circuitBreaker(c -> c.name("myCircuitBreaker")
                                                                                                              .fallbackUri("forward:/inCaseOfFailureUseThis"))                .rewritePath("/consumingServiceEndpoint", "/backingServiceEndpoint"))
               .uri("lb://backing-service:8088")      
               .build();
     }}
```

# 服务配置

## config

配置问题？

- 多个项目的配置问题

- 上线测试对个版本问题
- 微服务意味着要将单体应用中的业务拆分成一个个子服务，每个服务的力度相对粒度比较小，因此系统中会出现大量的服务。由于每个服务都需要必要的配置信息才能运行，所以一套集中式的、动态的配置管理设施是必不可少的。
- SpringCloud 提供了ConfigServer来解决这个问题，我们每个微服务带着一个application.yml,上百个配置文件的管理。。。

SpringCloud Config 为微服务架构找中的微服务提供集中化的外部配置支持，配置服务器为各个不同微服务应用的所有环境提供了一个中心化的外部配置



能干什么

- 集中管理配置文件
- 不同环境不同配置，动态化的配置更新，分环境部署/dev /test/prod/beta/release
- 运行期间动态调整配置，不再需要每个服务部署的机器上编写配置文件，服务会向配置中心统一拉去自己的配置文件
- 当配置发生改变时，服务不需要重启即可感知到配置的变化并应用新的配置
- 将配置信息以REST接口的形式暴露





# AP CP

C 是所有节点在同一时间看到的数据是一致的；而A定义的时所有请求都会收到相应