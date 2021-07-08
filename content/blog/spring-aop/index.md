---
title: Spring AOP
date: "2021-03-31T22:12:03.284Z"
description: "Spring AOP"
---

Spring AOP
Spring works on two core concepts: dependency injection and aspect oriented programming (spring AOP).

Enterprise applications have some common crosscutting concerns that will be applicable for different types of objects and modules. Some of common crosscutting concerns are logging, transaction management, data validation etc

Modularity in AOP is done using aspects. This enables us to eliminate direct dependency of cross cutting tasks.

Aspects: are classes which are implementing enterprise level concerns across multiple classes l. An aspect is general classes with @Aspect annotation or XML configuration.

Joinpoint: joinpoint is a normally a specific point in the application like method execution, object variable change, exceptions handling , request processing etc. in spring aop it’s generally method execution.

Advices are certain action taken when a particular joinpoint is reached. An advice method is executed when a particular joinpoints pointcuts are matched. Advices is similar to see msg filters or interceptors

Pointcut are expressions that decide whether. Particular advice is to be executed or not . Spring framework used aspectj expression lAnguage.

Target object where advices are applied. Spring AOP uses runtime proxies so this tamrget object is a proxied object. The subclass is created at runtime and target method is overriden and advices is applied on this based on its configuration.

AOP Proxy: spring AOP uses JDK dynamic proxy for creating proxy classes (AOP Proxy) with targeted objects and advice invocations. (One can add CGLIB proxy dependency for proxy classes)

Weaving: weaving is the process of linking aspects with other objects to result in advised proxy objects. ( weaving happend at compile time , runtime or loadtime )this is done in runtime with Spring AOP.

Advices are of following type based on execution strategy:
@Before : advices executed before joinpoints
@After(finally): advices executed after joinpoints are executed irrespective of the joinpoint method executing successfully or throwing an exception.
@AfterReturning: advices executed only after joinpoint method is executed successfully
@AfterThrowing: advices executed after the joinpoint method throws an exception only
@Around: most powerful advice. This advice controls if the join point has to be executed or not (using proceed). One can add advices that need to be executed before and after the joinpoints l. Around advice actually handles calling of the joinpoint method and returning of values of joinpoint method

```xml

<!-- Enable AspectJ style of Spring AOP -->
<aop:aspectj-autoproxy />

<!-- Configure Employee Bean and initialize it -->
<bean name="employee" class="com.spring.model.Employee">
	<property name="name" value="Dummy Name"></property>
</bean>

<!-- Configure EmployeeService bean -->
<bean name="employeeService" class="com.spring.service.EmployeeService">
	<property name="employee" ref="employee"></property>
</bean>

<!-- Configure Aspect Beans, without this Aspects advices wont execute -->
<bean name="employeeAspect" class="com.spring.aspect.EmployeeAspect" />
<bean name="employeeAspectPointcut" class="com.spring.aspect.EmployeeAspectPointcut" />
<bean name="employeeAspectJoinPoint" class="com.spring.aspect.EmployeeAspectJoinPoint" />
<bean name="employeeAfterAspect" class="com.spring.aspect.EmployeeAfterAspect" />
<bean name="employeeAroundAspect" class="com.spring.aspect.EmployeeAroundAspect" />
<bean name="employeeAnnotationAspect" class="com.spring.aspect.EmployeeAnnotationAspect" />

</beans>
```

```java
@Aspect
public class EmployeeAspectPointcut {

	@Before("getNamePointcut()")
	public void firstAdvice(){
		System.out.println("executing the first advice using the predefined getNamePointcut()");
	}

	@Before("getNamePointcut()")
	public void secondAdvice(){
		System.out.println("executing the second advice using the predefined getNamePointcut()");
	}

	@Before("allMethodsPointcut()")
	public void allmethodsadvice() {
		System.out.println("executing the advice using the predefined allMethodsPointcut()");
	}
	/*
	 * we define pointcuts which can be resued and called by other joinpoints with advice
	 */
	@Pointcut("execution(public String getName())")
	public void getNamePointcut() {}

	@Pointcut("within(com.spring.service.*)")
	public void allMethodsPointcut() {}

}
```

```java
@Aspect
public class EmployeeAspectJoinPoint {

	@Before("execution(public void com.spring.aspects..set*(*)")
	public void loggingwithtragetarg(JoinPoint joinPoint) {
		//method to find the target method
		System.out.println("Before running logging advice on method"+joinPoint.toString());
		System.out.println("method arguments "+Arrays.toString(joinPoint.getArgs()));
	}

	//method to create advice for methods with exactly one argument, the argument name should be same a targeted method
	@Before("args(name)")
	public void logStringargument(String name) {
		System.out.println("String argument passed is"+name);
	}
}
```
