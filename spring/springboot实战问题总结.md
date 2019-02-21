### spring boot 实战问题总结



1、springboot+consul 无法将服务注册到consul

日志

```
2019-02-19 16:39:43.602 [main] INFO  o.s.core.annotation.AnnotationUtils:1966 - Failed to introspect annotations on class org.springframework.cloud.consul.discovery.configclient.ConsulDiscoveryClientConfigServiceBootstrapConfiguration: java.lang.IllegalStateException: Could not obtain annotation attribute value for public abstract java.lang.Class[] org.springframework.boot.autoconfigure.condition.ConditionalOnClass.value()
```

原因可能是spring-cloud的scope不对，把他改成compile或者删除该依赖即可

```
<dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>${spring-cloud.version}</version>
        <type>pom</type>
        <scope>compile</scope>
      </dependency>
```

