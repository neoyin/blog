---
title: SpringBoot添加WebFilter
date: 2017-05-15 17:34
Author: neoyin
categories: 技术流
tags: spring,spring-boot
permalink: spring-boot-webfilter
---

---

想在SpringBoot中添加一个自定义的filter 发现sevlet3支持 `@WebFilter` 于是添加之如下

```
@Configure
@WebFilter(         urlPatterns = "/admin/*",         filterName = "AdminFilter", ) public class ManageFilter implements Filter {     @Override     public void init(FilterConfig filterConfig) throws ServletException {         System.out.println("init manage filter ....");     }      @Override     public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {          filterChain.doFilter(servletRequest,servletResponse);     }      @Override     public void destroy() {      } }
```

启动时已初始化此filter 但是urlpatterns 怎么也对不上
`o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'manageFilter' to: [/*]`

百思不得其解,试了好几种方法都不work. 一直以为是urlpattern的问题

google后去掉`@Configure`  在`Application`添加 `@ServletComponentScan` 解决问题

```
@ImportResource("classpath:applicationContext.xml")
@ServletComponentScan
@SpringBootApplication
public class SpringBootAnnotatedApp {
 
    public static void main(String[] args) {
        SpringApplication.run(SpringBootAnnotatedApp.class, args);
    }
}
```





