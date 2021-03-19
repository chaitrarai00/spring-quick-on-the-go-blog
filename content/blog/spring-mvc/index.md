---
title: Spring MVC
date: "2021-03-19T23:46:37.121Z"
---

![Spring MVC Architecture](./SpringMVCarch.png)
Spring MVC works as Model-view-controller architecture.
<init-param>
<param-name>contextConfigLocation</param-name>
<param-value>/WEB-INF/spring-servlet.xml</param-value>
</init-param>
This is used to provide or mentuion the location of the configuration file.
InternalResourceViewResolver is used to mention the location of view opages and suffix used. The controller class returns the name of the view page and the respective location and suffix is picked from here.
<annotation-driven />Tells Dispatcher servlet to look for controllers using annotations.
<context:component-scan base-package="com.spring" />Tells dispatcher servlet where to look for controllers
