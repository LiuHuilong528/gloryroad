# Spring Cloud

## 第一章 基础知识
**微服务** ：系统架构上的一种设计风格，将原本独立的系统拆分成多个小型服务，这些小型服务可以在各自独立的进程中运行，通过HTTP的RESTful API 进行通信。

为服务框架中，常用两种服务调用方式：
- HTTP 的 RESTfule API 或轻量级的消息发送协议
- 在轻量级消息总线上传递消息，类似RabbitMQ 等可提供可靠异步交换的中间件

Spring Cloud 以Spring Boot 为基础，为微服务中的配置管理、服务治理、断路器、智能路由、微代理、控制总线、全局锁、决策竞选、分布式会话和集群状态管理等操作提供简单的咖啡方式。

Spring Cloud 包含的子项目：
- spring Cloud Config : 配置管理工具
- Spring Cloud Netflix : 核心组件，对 Netflix OSS 开源套件进行组合        
  - Eureka : 服务治理组件——服务注册中心、服务注册与发现机制的实现。
  - Hystrix : 容错管理组件，实现断路器模式，为服务依赖中出现的延迟和为故障提供强大的容错能力
  - Ribbon : 客户端负载均衡的服务调用组件
  - Feign : 基于Ribbon和Hystrix 的声明式服务调用组件
  - Zuul : 网关组件，提供智能路由、访问过滤等功能
  - Archaius : 外部部署组件
- Spring Cloud Bus : 事件、消息总线，用于传播集群中的状态变化或事件，以触发后续的处理；如动态刷新配置信息
- Spring Cloud Cluster : 对 Zookeeper , Redis , Hazelcast , Consul 的选举算法和通用状态模式的实现
- Spring Cloud Cloudfoundry : 与Pivotal Cloudfoundry 的整合支持
- Spring Cloud Consul : 服务发现和配置管理工具
- Spring Cloud Stream : Redis , Rabbit 或者 Kafka 实现消费者微服务，通过简单的声明式模型来发送和接收消息
- Spring Cloud AWS : 简化整合 Amazon Web Service 组件
- Spring Cloud Security : 安全工具包，提供Zuul 代理中对 OAuth2 客户端请求的中继器
- Spring Cloud Sleuth : Spring Cloud 应用的分布式跟踪实现，可完整整合Zipkin
- Spring Cloud Zookeeper : 基于Zookeeper 的服务发现与配置管理组件
- Spring Cloud Starters : Spring Cloud 的基础组件，基于Spring Boot 风格项目的基础依赖模块
- Spring Cloud CLI : Spring Cloud 应用的 Spring Boot CLI插件


## 第二章 微服务构建： Spring Boot

Spring Boot 加载机制:
1. 命令行参数
2. SPRING_APPLICATION_JSON 中的属性。以JSON格式配置在系统环境变量中的内容
3. java:comp/env 中的JNDI属性
4. Java系统属性，通过 ` System.getProperties() ` 获得内容
5. 操作系统环境变量
6. random.* 配置的随机属性
7. 应用外的 {profile} 环境配置文件内容
8. 应用内的 {profile} 环境配置文件内容
9. 应用外的 ` application.properties ` 和 ` *.yml `
10. 应用内的 ` application.properties ` 和 ` *.yml `
11. ` @Configuration ` 注解修改的类中，通过 ` @PropertySource ` 属性
12. 默认属性 SpringApplication.setDafaultProperties 定义的内容

优先级由高到低。

原生端点，分三大类：
- 应用配置类，端点有 :    
  ```json
    /autoconfig
    /beans
    /configprops
    /env
    /mapping
    /info
  ```

- 度量指标类,端点：     
  ```json
    /metrics
    /health
    /dump
    /trace
  ```
- 操作控制类,端点：      
  ```json
    /shutdown
  ```

## 第三章 服务治理：Spring Cloud Eureka

核心内容:
- 构建服务中心
- 服务注册与服务发现
- Eureka 的基础架构
- Eureka 的服务治理机制
- Eureka 的配置

服务治理——实现各个服务实例的自动化注册与发现。

#### 服务注册中心
1. ` pom.xml `           
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    	<modelVersion>4.0.0</modelVersion>

    	<groupId>com.lhl</groupId>
    	<artifactId>eureka-server</artifactId>
    	<version>0.0.1-SNAPSHOT</version>
    	<packaging>jar</packaging>

    	<name>eureka-server</name>
    	<description>eureka-server</description>

    	<parent>
    		<groupId>org.springframework.boot</groupId>
    		<artifactId>spring-boot-starter-parent</artifactId>
    		<version>1.5.7.RELEASE</version>
    		<relativePath/> <!-- lookup parent from repository -->
    	</parent>

    	<properties>
    		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    		<java.version>1.8</java.version>
    		<spring-cloud.version>Dalston.SR4</spring-cloud.version>
    	</properties>

    	<dependencies>
    		<dependency>
    			<groupId>org.springframework.cloud</groupId>
    			<artifactId>spring-cloud-starter-eureka-server</artifactId>
    			<version>1.1.5.RELEASE</version>
    		</dependency>

    		<dependency>
    			<groupId>org.springframework.boot</groupId>
    			<artifactId>spring-boot-starter-test</artifactId>
    			<scope>test</scope>
    		</dependency>
    	</dependencies>

    	<dependencyManagement>
    		<dependencies>
    			<dependency>
    				<groupId>org.springframework.cloud</groupId>
    				<artifactId>spring-cloud-dependencies</artifactId>
    				<version>${spring-cloud.version}</version>
    				<type>pom</type>
    				<scope>import</scope>
    			</dependency>
    		</dependencies>
    	</dependencyManagement>

    	<build>
    		<plugins>
    			<plugin>
    				<groupId>org.springframework.boot</groupId>
    				<artifactId>spring-boot-maven-plugin</artifactId>
    			</plugin>
    		</plugins>
    	</build>
    </project>
    ```

2. 通过 ` @EnableEurekaServer ` 注解启动服务注册中心：         
    ```java
      package com.lhl.eurekaserver;

      import org.springframework.boot.SpringApplication;
      import org.springframework.boot.autoconfigure.SpringBootApplication;
      import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
      import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

      @EnableEurekaServer// Start Service Registry Center
      @SpringBootApplication
      public class Application {

      	public static void main(String[] args) {
      		SpringApplication.run(Application.class, args);
      	}
      }
    ```

3. 额外自定义配置：            
    ```
      server.port=8888
      eureka.instance.hostname=localhost
      # Don't register itself
      eureka.client.register-with-eureka=false
      # Don't fectch Service
      eureka.client.fetch-registry=false
      eureka.client.service-url.default-zone=http://localhost:8888/eureka/
    ```

#### 服务提供者

1. pom.xml      
    ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      		 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      	<modelVersion>4.0.0</modelVersion>

      	<groupId>com.lhl</groupId>
      	<artifactId>eureka-provider</artifactId>
      	<version>0.0.1-SNAPSHOT</version>
      	<packaging>jar</packaging>

      	<name>spring-cloud-producer</name>
      	<description>Demo project for Spring cloud producer</description>

      	<parent>
      		<groupId>org.springframework.boot</groupId>
      		<artifactId>spring-boot-starter-parent</artifactId>
      		<version>1.5.7.RELEASE</version>
      		<relativePath/> <!-- lookup parent from repository -->
      	</parent>

      	<properties>
      		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
      		<java.version>1.8</java.version>
      		<spring-cloud.version>Dalston.SR4</spring-cloud.version>
      	</properties>

      	<dependencies>
      		<dependency>
      			<groupId>org.springframework.cloud</groupId>
      			<artifactId>spring-cloud-starter-eureka</artifactId>
      			<version>1.1.5.RELEASE</version>
      		</dependency>
      		<dependency>
      			<groupId>org.springframework.boot</groupId>
      			<artifactId>spring-boot-starter-test</artifactId>
      			<scope>test</scope>
      		</dependency>
      	</dependencies>

      	<dependencyManagement>
      		<dependencies>
      			<dependency>
      				<groupId>org.springframework.cloud</groupId>
      				<artifactId>spring-cloud-dependencies</artifactId>
      				<version>${spring-cloud.version}</version>
      				<type>pom</type>
      				<scope>import</scope>
      			</dependency>
      		</dependencies>
      	</dependencyManagement>

      	<build>
      		<plugins>
      			<plugin>
      				<groupId>org.springframework.boot</groupId>
      				<artifactId>spring-boot-maven-plugin</artifactId>
      			</plugin>
      		</plugins>
      	</build>
      </project>
    ```

2. ` @EnableDiscoveryClient ` 注解，激活Eureka 中的 DiscoveryClient 实现（自动创建EurekaDiscoveryClient 实例）         
    ```java
        package com.lhl.eurekaprovider;

        import org.springframework.boot.SpringApplication;
        import org.springframework.boot.autoconfigure.SpringBootApplication;
        import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

        @SpringBootApplication
        @EnableDiscoveryClient
        public class ProviderApplication {

        	public static void main(String[] args) {
        		SpringApplication.run(ProviderApplication.class, args);
        	}
        }

    ```     
    供他人调用的服务： ` /hello `          
    ```java
        package com.lhl.eurekaprovider.web;

        import org.slf4j.Logger;
        import org.slf4j.LoggerFactory;
        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.cloud.client.ServiceInstance;
        import org.springframework.cloud.client.discovery.DiscoveryClient;
        import org.springframework.web.bind.annotation.RequestMapping;
        import org.springframework.web.bind.annotation.RestController;

        @RestController
        public class HelloController {

            private final Logger logger = LoggerFactory.getLogger(getClass());

            //EurekaClientAutoConfiguration.discoveryClient
            @Qualifier("discoveryClient")
            @Autowired
            private DiscoveryClient discoveryClient;

            @RequestMapping("/hello")
            public String index(){
                ServiceInstance instance = discoveryClient.getLocalServiceInstance();
                logger.info("Host: " +instance.getHost()+" ; Service_Id: "+instance.getServiceId());
                return "Hello World";
            }
        }
    ```

3. 配置       
    ```
        spring.application.name=hello-provider
        eureka.client.serviceUrl.defaultZone=http://localhost:8888/eureka/
    ```

#### 集群注册中心

在Eureka的服务治理设计中，所有节点即是服务提供方，也是服务消费方， **服务中心也是一样的** ！

Eureka Server 高可用就将自己作为服务向其他服务注册中心注册自己，形成一组相互注册的服务注册中心，实现服务清单的互相同步，达到高可以的效果。

#### 服务消费者

服务消费者任务——发现服务和消费服务；服务发现由Eureka客户端完成，消费服务由Ribbon完成。

Ribbon是基于HTTP和TCP的客户端负载均衡器，可在客户端配置 ` ribbonServerList ` 服务端列表去轮询访问已达到负载均衡器

当两者联合使用时， Ribbon的服务实例清单 `RibbonServerList` 会被 ` DiscoveryEnabledNIWSServerList ` 重写，扩展位冲 Eureka 注册中心获取服务端列表。使用 `NIWSDiscoveryPing` 取代 `IPing` , 职责委托给 Eureka 确定服务端是否启动。

创建服务消费者

1. ` pom.xml `     
    ```xml
        <?xml version="1.0" encoding="UTF-8"?>
        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        	<modelVersion>4.0.0</modelVersion>

        	<groupId>com.lhl</groupId>
        	<artifactId>eureka-consumer</artifactId>
        	<version>0.0.1-SNAPSHOT</version>
        	<packaging>jar</packaging>

        	<name>eureka-consumer</name>
        	<description>eureka-consumer</description>

        	<parent>
        		<groupId>org.springframework.boot</groupId>
        		<artifactId>spring-boot-starter-parent</artifactId>
        		<version>1.5.7.RELEASE</version>
        		<relativePath/> <!-- lookup parent from repository -->
        	</parent>

        	<properties>
        		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        		<java.version>1.8</java.version>
        		<spring-cloud.version>Dalston.SR4</spring-cloud.version>
        	</properties>

        	<dependencies>
        		<dependency>
        			<groupId>org.springframework.cloud</groupId>
        			<artifactId>spring-cloud-starter-ribbon</artifactId>
        		</dependency>
        		<dependency>
        			<groupId>org.springframework.cloud</groupId>
        			<artifactId>spring-cloud-starter-eureka</artifactId>
        			<version>1.1.5.RELEASE</version>
        		</dependency>
        		<dependency>
        			<groupId>org.springframework.boot</groupId>
        			<artifactId>spring-boot-starter-web</artifactId>
        		</dependency>

        		<dependency>
        			<groupId>org.springframework.boot</groupId>
        			<artifactId>spring-boot-starter-test</artifactId>
        			<scope>test</scope>
        		</dependency>
        	</dependencies>

        	<dependencyManagement>
        		<dependencies>
        			<dependency>
        				<groupId>org.springframework.cloud</groupId>
        				<artifactId>spring-cloud-dependencies</artifactId>
        				<version>${spring-cloud.version}</version>
        				<type>pom</type>
        				<scope>import</scope>
        			</dependency>
        		</dependencies>
        	</dependencyManagement>

        	<build>
        		<plugins>
        			<plugin>
        				<groupId>org.springframework.boot</groupId>
        				<artifactId>spring-boot-maven-plugin</artifactId>
        			</plugin>
        		</plugins>
        	</build>
        </project>

    ```

2. java 类-配置类      
    ```java
        package com.lhl.eurekaconsumer;

        import org.springframework.boot.SpringApplication;
        import org.springframework.boot.autoconfigure.SpringBootApplication;
        import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
        import org.springframework.cloud.client.loadbalancer.LoadBalanced;
        import org.springframework.context.annotation.Bean;
        import org.springframework.web.client.RestTemplate;

        @EnableDiscoveryClient
        @SpringBootApplication
        public class ConsumerApplication {

        	@Bean
        	@LoadBalanced
        	RestTemplate restTemplate(){
        		return new RestTemplate();
        	}

        	public static void main(String[] args) {
        		SpringApplication.run(ConsumerApplication.class, args);
        	}
        }
    ```
    调用服务类：        
    ```java
        package com.lhl.eurekaconsumer.web;

        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.web.bind.annotation.RequestMapping;
        import org.springframework.web.bind.annotation.RestController;
        import org.springframework.web.client.RestTemplate;

        @RestController
        public class ConsumerController {

            @Autowired
            RestTemplate restTemplate;

            @RequestMapping("/ribbon-consumer")
            public String helloConsumer(){
                return restTemplate.getForEntity("http://HELLO-SERVICE/hello",String.class).getBody();
            }
        }
    ```

3. 调用配置       
    ```
        spring.application.name=ribbon-consumer
        server.port = 9000

        eureka.client.serviceUrl.defaultZone=http://localhost:8888/eureka/
    ```

#### Eureka 详解

##### 基础架构：
- 服务注册中心 - eureka-server
- 服务提供者   - eureka-provider
- 服务消费者   - eureka-consumer 基于Ribbon实现的消费者

客户端——Eureka-Client 即使服务提供者也是服务消费者。

##### 服务治理机制

![服务架构图](https://github.com/LiuHuilong528/gloryroad/img/eureka-archture.png)

###### 服务提供者
1. 服务注册——Eureka Server 将服务的信息存储在双层Map中，第一层是key是服务名，第二层key是具体的服务实例名。
` eureka.client.register-with-eureka=true ` 自动注册服务配置项

2. 服务同步—— 服务注册中心之间互相注册为服务，接收到请求时会将请求转发给集群中其他的注册中心，这样实现注册中心之间的服务同步。

3. 服务续约——注册完成后，服务提供者会维护一个心跳持续通知Eureka Server服务还活着，以防被注册中心删除服务.
    ```
      eureka.instance.lesse-renewal-interval-in-seconds = 30 # 服务续约任务调用间隔时间
      eureka.instance.lesse-expiration-duration-in-seconds=90 # 服务失效时间
    ```

###### 服务消费者

1. 获取服务——获取注册中心的服务清单，每隔30秒更新一次；
相关配置：    
    ```
      eureka.client.fetch-registry=true
      # 修改缓存清单更新时间
      eureka.client.registry-fetch-interval-seconds=30
    ```

2. 服务调用——通过服务名获得具体的实例名和实例的元数据信息，根据需要决定调用的实例。Ribbon中默认采用轮询方式进行调用，实现负载均衡。     
Eureka 中有 Region 和 Zone 的概念，一个Region 中可以包含多个Zone ， 每个服务客户端被注册得到一个Zone中，所以每个客户端对应一个Region和一个Zone。服务调用的优先在同一个Zone ，没有在访问其他Zone。

3. 服务下线——通知Eureka Server 服务下线；

###### 服务注册中心

1. 失效剔除——默认每隔60秒将服务清单中超时（默认90秒）的服务剔除。

2. 自我保护——统计心跳失败比例并保护当前实例注册信息——注册的服务不会过期。
    ```
      # 关闭保护机制
      eureka.server.enable-self-preservation=false
    ```

###### 源码分析
` org.springframework.cloud.client.discovery.DiscoveryClient ` 是Spring Cloud 的接口，定义了发现服务的常用抽象方法; ` org.springframework.cloud.netflix.eureka.EurekaDiscoveryClient ` 是该接口的实现类，此类依赖了  `com.netflix.discovery.EurekaClient` 接口，定义针对 Eureka 的服务发现的抽象方法，真正实现发现服务的是Netflix 包中的 `com.netflix.discovery.DiscoveryClient` 类。

###### 配置详解

Eureka 客户端配置分2个方面：        
- 服务注册相关的配置信息，包括服务注册中心的地址、服务获取的间隔时间、可用区域等
- 服务实例相关配置信息，服务实例的名称、IP地址、端口号、健康检查路径等。
` org.springframework.cloud.netflix.eureka.servcer.EurekaServerConfigBean ` 中关于服务的配置

`enabaleSelefPreservation` 关闭注册中心的“自我保护功能”；

###### 服务注册类配置
可以查看 ` org.springframework.cloud.netflix.eureka.EurekaClientConfigBean ` 以 ` eureka.client ` 为前缀。

##### 服务实例配置
* 元数据     
` org.springframework.cloud.netflix.eureka.EurekaInstanceConfigBean ` 中以 ` eureka.instance ` 为前缀，此类中的 `metadata` 是 **自定义的元数据信息** ;而其他的则为标准的元数据信息;
`eureka.instance.<properties> = <value>` 格式对标准化元数据直接配置；     
`eureka.instance.metadataMap.<key>=<value>` 对自定义个元数据配置；

* 实力名配置     
命名规则：`${spring.cloud.client.hostname}:${spring.application.name}:${spring.application.instance_id}:${server.port}`  

* 端点配置
homePageUrl 、 statusPageUrl、healthCheckUrl 代表主页URL、状态页的URL、健康检查的URL。    
特殊情况才需要修改这些URL配置:        
  - 应用设置了 ` context-path ` 为/info 和 /health 端点做以下配置       
    ```
      management.context-path = /hello

      eureka.instance.statusPageUrlPath = ${management.context-path}/info
      eureka.instance.healthCheckUrlPath = ${management.context-path}/health
    ```
    安全考虑，也会修改 /info 和 /health 断电的原始路径特殊配置：      
    ```
      endpoints.info.path = /appInfo
      endpoints.health.path = /checkHealth

      eureka.instance.statusPageUrlPath = /${endpoints.info.path}
      eureka.instance.healthCheckUrl=/${endpoints.info.path}
    ```
    以上使用的是相对路径，spring cloud eureka 提供了绝对路径的配置

* 健康监测      
  spring cloud eureka中可以将Eureka客户端的健康检测交给spring-boot-actuator 模块的 /health 端点，步骤：    
    * `pom.xml` 添加 spring-boot-actuator 模块
    * application.properties 增加配置： eureka.client.healthcheck.enabel=true
    * 对客户端的 /health 端点的特殊处理

### 跨平台支持
通信机制使用了 HTTP 的REST接口实现，利用HTTP的平台无关性，是的Eureka的客户端不限于用java进行开发。

#### 通信协议
默认使用 Jersey 和 XStream 配额 JSON 作为 Server 与 Client 的通信协议；       
1. Jersey 是 JAX-RS 的参考实现，三个主要部分：      
  * **核心服务器** ：使用 JSR311 的标准化注解和API标准化，直观的开发 RESTful Web 服务。
  * **核心客户端** : Jersey客户端API与REST服务通信简单
  * **集成** ：提供轻松集成 Spring、Guice、Apache Abdera的库。
2. XStream 是将对象序列化为XML（JSON）或反序列化为对象的JAVA类库

## 第四章 客户端负载均衡： SpringCloudRibbon

Spring Cloud Ribbon 基于 HTTP 和 TCP 的客户端负载均衡工具基于Ribbon实现。

使用Ribbon实现客户端的负载均衡及原理。

### 客户端负载均衡

所有客户端节点维护自己要访问的来自服务注册中心的服务端清单.

Spring Cloud 实现的服务治理框架中，默认会创建各个服务治理框架的 Ribbon的自动化整合配置：    
  * Eureka —— org.springframework.cloud.netflix.eureka.RibbonEurekaAutoConfiguration
  * Consul —— org.springframework.cloud.consul.discovery.RibbonConsulAutoConfiguration

Ribbon 使用客户端负载均衡调用要二步：
1. 服务提供者启动多个服务实例并注册到同一个注册中心或多个相关的服务注册中心- `@EnableDiscoveryClient`。
2. 服务消费者直接通过调用被 ` @LoadBalanced ` 注解修饰过的 RestTemplate 实现服务接口的调用。

RestTemplate 实现GET、POST、PUT、DELETE四种请求的服务调用实现; Ribbon的客户端负载均衡也是基于RestTemplate实现的。

`@LoadBalanced` 修饰 RestTemplate ，以使用 `LoadBalancerClient` 配置它;       
`LoadBalancerClient` 定义的方法有：    
  1. ServiceInstance choose(String serviceId) - 从负载均衡器中挑选一个服务实例
  2. T excute - 使用服务实例执行请求（服务调用）
  3. URI reconstructURI - 将 `http://myservice/path/to/serviceId` 形式请求转换为 `host:port` 形式。


- [ ] Spring Cloud Ribbon 实现客户端负载均衡的脉络，了解 LoadBalancerInterceptor拦截器对RestTemplate请求进行拦截，用Spring Cloud负载均衡器LoadBalancerClient将逻辑服务名为host的URI转换成具体服务实例地址的过程。同时分析了LaodBalancerClient的Ribbon实现RibbonLoadBalancerClient,可以知道在使用Ribbon实现负载均衡器时，实际使用的时Ribbon中ILoadBalancer接口的实现，自动化配置会采用ZoneAwareLoadBalancer实例来实现客户端的负载均衡。

#### 负载均衡器
Spring Cloud中定义LoadBalancerClient 作为负载均衡器的同哟那条接口，针对 Ribbon 实现了 RibbonLoadBalancerClient;但是在具体实现时将负载均衡委托给 Ribbon 的 ILoadBalancer 接口的实现;


ILoadBalancer 接口实现类实现客户端负载均衡：       
  * **AbstractLoadBalancer** 抽象类
  * **BaseLoadBalancer** 基础实现类，其中负载均衡的处理规则 IRule 对象实现了 **服务实例选择** ;
  * **DynamicServerListLoadBalancer** 基于 BaseLoadBalancer 的扩展，服务实例清单的 **运行期** 动态更新，和对服务实例清单的过滤;



### 配置详解

#### 自动化配置

Spring Cloud Ribbon 帮助简化了 Ribbon 的配置，自动化构建了以下接口实现：    
  1. IClientConfig： **Ribbon** 的客户端配置，默认采用 `com.netflix.client.config.DefaultClientConfigImpl` 实现
  2. IRule: **Ribbon** 负载均衡策略，默认采用 `com.netflix.loadbalancer.ZoneAvoidanceRule` 实现——在多区域环境下选出最件区域的实例进行访问。
  3. IPing: **Ribbon** 的实例检查策略，默认采用 `com.netflix.loadbalancer.NoOpPing` 实现，默认所有服务实例可用
  4. ServerList<Server>: 服务实例清单的维护机制，默认采用 `com.netflix.loadbalancer.ConfigurationBasedServerList` 实现
  5. ServerListFilter<Server>:服务清单过滤机制，默认 `org.springframework.cloud.netflix.ribbon.ZonePreferenceServerListFilter` 实现，优先过滤出与请求调用方处于同区域的服务实例
  6. ILoadBalancer:负载均衡器，默认采用 `com.netflix.loadbalancer.ZoneAwareLoadBalancer` 实现，具备区域感知能力

**以上配置，只在没有引入Spring Cloud Eureka 服务治理框架时如此**

**`org.springframework.cloud.netflix.ribbon.PropertiesFactory`** 动态为 RibbonClient 创建接口实现：       
  1. NFLoadBalancerPingClassName ——IPing接口实现类
  2. NFLoadBalancerClassName -- ILoadBalancer 接口实现
  3. NFLOadBalancerRuleClassName -- IRule 接口实现
  4. NIWSServerListClassName -- ServerList 接口实现
  5. NIWSServerListFilterClassName -- ServerListFilter 接口实现

#### 参数配置
**Ribbon** 参数配置两种方式：全局配置以及指定客户端配置     
  * 全局配置简单使用 ribbon.<key>=<value>，<key>代表Ribbon客户端配置的参数名
  * 客户端配置采用<client>.ribbon.<key>=<value>

Ribbon 参数的 key 和 value 可以参看 `com.netflix.client.config.CommonClientConfigKey` 获取更详细的配置内容

#### 与Eureka 结合
Eureka中对Ribbon的自动化配置会被触发：     
  1. ServerList的维护机制——`com.netflix.niws.loadbalancer.DiscoveryEnabledNIWWServerList` ,服务清单列表由 Eureka 服务治理机制维护
  2. `com.netflix.niws.loadbalancer.NIWSDiscoveryPing`——IPing的实现， 负责实例检查任务
  3. `org.springframework.cloud.netflix.ribbon.eureka.DomainExtractingServerList` 负责实例维护策略

Spring Cloud Ribbon 与 Spring Cloud Eureka 结合的工程中，可以禁用Eureka对Ribbon服务实例的维护实现：      
  ```
    ribbon.eureka.enabled = false
  ```


## 第五章 服务容错保护：Spring Cloud Hystrix


































---
