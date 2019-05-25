- [MVC模式](#mvc模式)
- [SpringMVC重要组件](#springmvc重要组件)
- [SpringMVC运行原理](#springmvc运行原理)
- [SpringMVC使用](#springmvc使用)

# SpringMVC

SpringMVC 框架是以请求为驱动，围绕 Servlet 设计，将请求发给控制器，然后通过模型对象，分派器来展示请求结果视图。其中核心类是 DispatcherServlet，它是一个 Servlet，低层是实现的Servlet接口

## MVC模式

MVC是一种设计模式

**MVC的原理图**

![img](imgs/60679444.jpg)

## SpringMVC重要组件

- **DispatcherServlet**：前端控制器

  Spring MVC 的入口函数。**接收请求，响应结果**，相当于转发器，中央处理器。有了 DispatcherServlet 减少了其它组件之间的耦合度。用户请求到达前端控制器，它就相当于mvc模式中的c，DispatcherServlet是整个流程控制的中心，由它调用其它组件处理用户的请求，DispatcherServlet的存在降低了组件之间的耦合性。

- **HandlerMapping**：处理器映射器

  解析请求格式的，判断希望要执行哪个具体的方法

- **HandlerAdapter**：处理器适配器

  负责调用具体的方法

- **ViewResovler**：视图解析器

  解析结果，准备跳转到具体的物理视图



## SpringMVC运行原理

如果在 web.xml 中设置 DispatcherServlet 的为/时，当用户发起请求，请求一个控制器，首先会执行DispatcherServlet。由DispatcherServlet调用HandlerMapping的DefaultAnnotationHandlerMapping 解 析URL，解析后调用HandlerAdatper组件的AnnotationMethodHandlerAdapter调用Controller中的HandlerMethod。当HandlerMethod执行完成后会返回View，会被 ViewResovler 进行视图解析，解析后调用 jsp对应的.class文件并运行，最终把运行.class 文件的结果响应给客户端。

**如下图所示：**

![img](imgs/49790288.jpg)

**流程说明：**

(1)客户端（浏览器）发送请求，直接请求到 DispatcherServlet。

(2)DispatcherServlet 根据请求信息调用 HandlerMapping，解析请求对应的 Handler。

(3)解析到对应的 Handler(也就是我们平常说的 Controller 控制器)后，开始由HandlerAdapter适配器处理。

(4)HandlerAdapter 会根据 Handler 来调用真正的处理器开处理请求，并处理相应的业务逻辑。

(5)处理器处理完业务后，会返回一个ModelAndView对象，Model是返回的数据对象，View 是个逻辑上的 View。

(6)ViewResolver 会根据逻辑 View 查找实际的 View。

(7)DispaterServlet 把返回的 Model 传给 View（视图渲染）。

(8)把 View 返回给请求者（浏览器）



## SpringMVC使用

需要在 web.xml 中配置 DispatcherServlet ，并且需要配置 Spring 监听器ContextLoaderListener

**web.xml**

````xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd" id="WebApp_ID" version="3.1">
	<!-- 上下文参数 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath*:applicationContext-*.xml</param-value>
	</context-param>
	<!-- 监听器 -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	<!-- SpringMVC前端控制器 -->
	<servlet>
		<servlet-name>springmvc</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:springmvc.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
	<servlet-mapping>
		<servlet-name>springmvc</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
	<!-- 字符编码过滤器 -->
	<filter>
		<filter-name>encoding</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>utf-8</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>encoding</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
</web-app>
````

**springmvc.xml**

````xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
	xmlns:mvc="http://www.springframework.org/schema/mvc"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">
	<!-- 扫描注解 -->
	<context:component-scan base-package="com.ego.*.controller"></context:component-scan>
	<!-- 注解驱动 -->
	<mvc:annotation-driven></mvc:annotation-driven>
	<!-- 静态资源 -->
	<mvc:resources location="/WEB-INF/js/" mapping="/js/**"></mvc:resources>
	<mvc:resources location="/WEB-INF/images/" mapping="/images/**"></mvc:resources>
	<mvc:resources location="/WEB-INF/css/" mapping="/css/**"></mvc:resources>
	<!-- 视图解析器 -->
	<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<property name="prefix" value="/WEB-INF/jsp/"></property>
		<property name="suffix" value=".jsp"></property>
	</bean>
	<!-- Multipart解析器 -->
	<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"></bean>
	<!-- 拦截器 -->
	<mvc:interceptors>
		<bean class="com.ego.cart.interceptor.LoginInterceptor"></bean>
	</mvc:interceptors>
</beans>
````

