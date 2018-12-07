[TOC]

# spring cloud+springboot+consul服务发现和注册搭建

spring cloud的服务注册支持Eureka、consul、zookeeper等，使用较多是Eureka和consul，由于Eureka 2.0开始就不维护了，如果要使用，则所有的bug都需要自己负责，这将带来较高的成本和风险。consul作为一款分布式的服务注册产品社区较活跃，同时与spring cloud, docker等主流技术衔接的较好，所以本文采用consul作为服务注册于发现。

>
>
>各类注册中心产品的比对可以参考
>
>https://luyiisme.github.io/2017/04/22/spring-cloud-service-discovery-products/
>
>http://www.ityouknow.com/springcloud/2018/07/20/spring-cloud-consul.html



## 1、安装consul

### 1.1、mac下安装

```bash
官网下载：https://www.consul.io/downloads.html
解压后得到一个consul可执行文件
$ ./consul agent -dev
然后访问http://localhost:8500/ui 看到以下页面则说明启动成功

```

![](images/springcloud/consul_init.png)

consul命令加入到系统命令

```bash
mkdir -p /Users/fox/bin/
cp consul /Users/fox/bin/
sudo vim /etc/paths.d/user_fox
输入/Users/fox/bin/
source /etc/profile
然后在客户端中输入consul测试是否生效
```



consul命令详解

```
-dev 开发者模式 该节点的启动不能用于生产环境，因为该模式下不会持久化任何状态 
该启动模式仅仅是为了快速便捷的启动单节点consul 
该节点处于server模式 
该节点是leader 
该节点是一个健康节点 
-ui 启动自有主机的界面 
-bootstrap-expect 1 集群节点，表示等待多少个节点再启动，这里是1个，一个就启动 
-bind=127.0.0.1 绑定IP ，本机IP地址，内网IP 
-advertise-wan=10.23.123.12 绑定外网ip 
-node=node1 节点名称，如果没有，默认是主机名 
-server 设置为服务端 
-data-dir /tmp/consul 数据存储目录为 /tmp/consul 
-datacenter=dc1 数据中心 
更多请看 
https://www.consul.io/docs/agent/options.html
```



### 1.2、docker安装

拉取consul镜像

```bash
$ docker pull consul
```

启动单节点

```bash
$ docker run -d --name=node1 --restart=always \
             -e 'CONSUL_LOCAL_CONFIG={"skip_leave_on_interrupt": true}' \
             -p 8300:8300 \
             -p 8301:8301 \
             -p 8301:8301/udp \
             -p 8302:8302/udp \
             -p 8302:8302 \
             -p 8400:8400 \
             -p 8500:8500 \
             -p 8600:8600 \
             -h node1 \
             consul agent -server -bind=127.0.0.1 -bootstrap-expect=1 -node=node1 \
             -data-dir=/tmp/data-dir -client 0.0.0.0 -ui
```

同样启动后访问http://localhost:8500 

> 集群搭建参考
>
> https://juejin.im/post/5b2a6b606fb9a00e594c676d
>
> https://blog.csdn.net/fenglailea/article/details/79098246



## 2、注册consul server

### 2.1、创建一个springboot工程

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.3.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example.springcloud</groupId>
	<artifactId>consul.server</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>
	<name>consul.server</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
		<spring-cloud.version>Finchley.RELEASE</spring-cloud.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-consul-discovery</artifactId>
		</dependency>
        <!--用于做监控，可以不加-->
		<dependency>
			<groupId>io.micrometer</groupId>
			<artifactId>micrometer-registry-prometheus</artifactId>
			<version>1.0.6</version>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

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
</project>

```

> 注意：这里的spring-cloud-dependencies 要与对应的spring-boot的版本相匹配，否则会无法启动

### 2.2、配置启动类

启动类需要添加@EnableDiscoveryClient 用于自动注册

```java
package com.example.springcloud.consul.server;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class ConsulProducerApplication {

	public static void main(String[] args) {
		SpringApplication.run(ConsulProducerApplication.class, args);
	}
}

```



### 2.3、配置注册中心

application.yml

```yml
spring:
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: service-producer
        enabled: true
        health-check-path: /actuator/health
  application:
    name: springcloud-consul

server:
  port: 8501

#prometheus监控的配置，如果不需要可以不加
management:
  security:
    enabled: false
  # 仅限于 开发环境可对security进行关闭。
  metrics:
    export:
      prometheus:
        enabled: true
        step: 1m
        descriptions: true
  web:
    server:
      auto-time-requests: true
  endpoints:
    prometheus:
      id: springmetrics
    web:
      exposure:
        include: health,info,env,prometheus,metrics,httptrace,threaddump,heapdump,springmetrics

```



启动后，访问http://localhost:8500/ui，可以看到注册好的服务

![](images/springcloud/consul_server.png)

### 2.4、开发需要对外的Rest服务

此处以hello world为例

```java
package com.example.springcloud.consul.server.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

  @GetMapping("/hello")
  public String sayHello(){
    return "hello consul sever 1";
  }
}

```



## 3、消费consul的服务

### 3.1、创建consumer的springboot工程

pom参考2.1, 额外添加ribbon的依赖，用于服务发现和负载均衡

```xml
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
			<version>2.0.2.RELEASE</version>
		</dependency>
```



### 3.2、配置启动类

启动类添加@EnableDiscoveryClient， @LoadBalanced是用于实现客户端负载均衡，服务发现使用RestTemplate来实现

```java
package com.example.springcloud.consul.client;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
@EnableDiscoveryClient
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}


	@Bean
	@LoadBalanced
	public RestTemplate restTemplate(){
		return new RestTemplate();
	}
}

```

### 3.3、配置注册中心

application.properties, 也可以使用yml的格式

```properties

spring.application.name=spring-cloud-consul-consumer
server.port=8503
spring.cloud.consul.host=127.0.0.1
spring.cloud.consul.port=8500
#为了演示设置不需要注册到 consul 中，
spring.cloud.consul.discovery.register=false
producer.name=service-producer

```

### 3.4、服务消费

```java
package com.example.springcloud.consul.client;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.cloud.client.loadbalancer.LoadBalancerClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
public class ServiceController {

  @Autowired
  private LoadBalancerClient loadBalancer;
  @Autowired
  private DiscoveryClient discoveryClient;

  @Autowired
  private RestTemplate restTemplate;

    //之前注册的服务名，service-producer
  @Value("${producer.name}")
  private String producerName;
  /**
   * 获取所有服务
   */
  @RequestMapping("/services")
  public Object services() {
    return discoveryClient.getInstances("service-producer");
  }

  /**
   * 从所有服务中选择一个服务（轮询）
   */
  @RequestMapping("/discover")
  public Object discover() {
    return loadBalancer.choose("service-producer").getUri().toString();
  }


  @GetMapping("/callHello")
  public String consumeHello(){

    String body = restTemplate
        .getForEntity("http://"+ producerName +"/hello", String.class).getBody();
    return body;
  }
}

```

启动后访问http://localhost:8003/callHello 返回 hello consul sever 1

如果注册了多个服务，可以通过http://localhost:8003/discover 来查看