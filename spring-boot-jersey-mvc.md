---
title: SpringBoot同时配置Jersey和Mvc
date: 2017-12-11 17:34
Author: neoyin
categories: 技术流
tags: spring,spring-boot
permalink: spring-boot-jersey-mvc
---

---

最近自己的`Spring-boot`小项目想用到jersey ，但是也需要jsp做展示，这就需要注册两个servlet 

先配置好mvc相关配置

`pom.xml`中添加jsp相关依赖

```
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
	<groupId>org.apache.tomcat.embed</groupId>
	<artifactId>tomcat-embed-jasper</artifactId>
	<scope>provided</scope>
</dependency>
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>jstl</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-jersey</artifactId>
</dependency>
<dependency>
	<groupId>org.glassfish.jersey.media</groupId>
	<artifactId>jersey-media-multipart</artifactId>
	<version>2.19</version>
</dependency>
```

`application.properties`中添加 

```
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
```


添加jersey配置`jerseyConfig`

```
import org.glassfish.jersey.media.multipart.MultiPartFeature;
import org.glassfish.jersey.server.ResourceConfig;
import org.glassfish.jersey.server.spring.scope.RequestContextFilter;
import org.springframework.context.annotation.Configuration;

public class JerseyConfig extends ResourceConfig {

    public JerseyConfig(){
        register(RequestContextFilter.class);
        register(MultiPartFeature.class);
        packages("...controller");
    }
}
```

添加两个相关servlet

```
@Configuration
public class ServletConfig {

    @Bean
    public ServletRegistrationBean dispatcherRegistration(DispatcherServlet dispatcherServlet) {
        ServletRegistrationBean registration = new ServletRegistrationBean(
                dispatcherServlet);
        registration.addUrlMappings("/jsp/*");
        return registration;
    }
    @Bean
    public ServletRegistrationBean registrationJerseyBean(){
        ServletRegistrationBean registration = new ServletRegistrationBean(new ServletContainer(new JerseyConfig()));
        registration.setEnabled(true);
        registration.setName("jersey-servlet");
        registration.addUrlMappings("/rest/*");
        return registration;
    }
}
```

---

参考

- <https://www.leveluplunch.com/blog/2014/04/01/spring-boot-configure-servlet-mapping-filters/>
- <https://stackoverflow.com/questions/35208439/use-different-paths-for-public-and-private-resources-jersey-spring-boot>
- <https://www.programcreek.com/java-api-examples/index.php?class=org.springframework.boot.context.embedded.ServletRegistrationBean&method=setName>