---
title: Spring MVC
date: "2021-03-19T23:46:37.121Z"
---

![Spring MVC Architecture](./SpringMVCarch.png)
Built on top of Servlet API with all Spring Core features is a sub framework of Spring used to build web applications. Spring MVC works implementing Model-view-controller design pattern.
##Advantages:

> separate roles Model, View and controller
> configuration using spring is easier and powerful
> once configuration is done application development is fast
> easy to test and flexible
> process form data and help manage application states for web requests

Spring MVC app steps:
=> Configure Dispatcher Servlet in web.xml
=> Create Spring Configuration file (spring-servlet.xml for bean configuration, spring is the name of DispatcherServlet in the example)
=> Create View Resolver
=> Create Controller
=> Create a view to show page
![Spring MVC Architecture Working](./springmvcwork.png)
InternalResourceViewResolver is used to mention the location of view pages and suffix used. The controller class returns the name of the view page and the respective location and suffix is picked from here.

<annotation-driven />Tells Dispatcher servlet to look for controllers using annotations.

<context:component-scan base-package="com.spring" />Tells dispatcher servlet where to look for controllers

```xml
<init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>/WEB-INF/spring-servlet.xml</param-value>
</init-param>
```

This is used to provide or mention the location of the configuration file.

Dispatcher Servlet in web.xml:

```xml
<servlet>
      <servlet-name>spring</param-name>
      <servlet-class>org.springframework.web.servlet.DispatcherServlet
</servlet-class>
</servlet>

<servlet-mapping>
      <servlet-name>spring</param-name>
      <url-pattern>/</url-pattern>
<!—Which url should Dispacher handle, here / means all urls are to be handled-->
</servlet-mapping>

```

Sending data from controller to view use model or ModelAndView
