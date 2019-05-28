- [HTTP协议](#http协议)
  - [交互流程](#交互流程)
  - [请求](#请求)
  - [响应](#响应)
- [Tomcat服务器](#tomcat服务器)
- [Servlet技术](#servlet技术)
  - [request对象](#response对象)
  - [转发和重定向](#转发和重定向)
  - [Cookie](#cookie)
  - [Session技术](#session技术)
  - [ServletContext对象](#servletcontext对象)
  - [ServletConfig对象](#servletconfig对象)
- [xml文件](#xml文件)
  - [web.xml](#web.xml)
- [JSP](#jsp)
  - [语法和指令](#语法和指令)
  - [九大内置对象](#九大内置对象)
  - [资源路径](#资源路径)
- [Ajax](#ajax)
- [jQuery中ajax分类](#jquery中ajax分类)
- [EL、JSTL](#eljstl)
- [#过滤、监听器](#过滤监听器)

## HTTP协议

**http**：超文本传输协议

**作用**：规范了浏览器和服务器的数据交互

**特点**：

- 简单快速、灵活、无连接、无状态、支持B/S及C/S模式
- HTTP1.1版本后支持可持续连接

### 交互流程

1. 客户端和服务器端建立连接

2. 客户端发送请求到服务器端(HTTP协议)

3. 服务端接收到请求后进行处理，将处理结果响应客户端

4. 关闭客户端和服务端的连接(HTTP1.1后不会立即关闭)

### 请求

**请求格式**

- 请求头：请求方式、请求的地址和HTTP协议版本
- 请求行：消息报头，一般用来说明客户端要使用的一些附加信息
- 空行：位于请求行和请求数据之间，空行是必须的
- 请求数据：非必须

**请求方式**

- GET：请求指定的页面信息，并返回实体主体

- POST：想指定资源提交数据进行处理请求

- HEAD：类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头

- HTTP1.1 新增了五种请求方法：OPTIONS, PUT, DELETE, TRACE 和CONNECT 方法

- **get与post区别**

  - get

    请求数据会以？的形式隔开拼接在请求头中，不安全，没有请求实体部分

    HTTP 协议虽然没有规定请求数据的大小，但是浏览器对 URL 的长度是有限制的，所以 get 请求不能携带大量的数据

  - post

    请求数据在请求实体中进行发送，在 URL 中看不到具体的请求数据，安全。适合数据量大的数据发送

### 响应

**响应格式**

- 响应行(状态行)：HTTP 版本、状态码、状态消息
- 响应头：消息报头，客户端使用的附加信息
- 空行：响应头和响应实体之间的，必须的
- 响应实体：正文，服务器返回给浏览器的信息

**响应状态码**

- 200 OK：客户端请求成功
- 400 Bad Request：客户端请求有语法错误，不能被服务器所理解
- 401 Unauthorized：请求未经授权，这个状态代码必须和WWW-Authenticate 报头域一起使用
- 403 Forbidden：服务器收到请求，但是拒绝提供服务
- 404 Not Found：请求资源不存在，eg：输入了错误的 URL
- 500 Internal Server Error：服务器发生不可预期的错误
- 503 Server Unavailable：服务器当前不能处理客户端的请求，一段时间后可能恢复正常



## Tomcat服务器

**服务器**

使用代码编写的一个可以根据用户请求实时的调用执行对应的逻辑代码的一个容器

**下载安装**

- 在安装目录\bin找到startup.bat启动
- localhost:8080 能访问说明安装成功
- tomcat 的运行依赖 JDK，必须配置 JDK 环境

**目录结构介绍**

- \bin 存放启动和关闭 Tomcat 的可执行文件
- \conf 存放 Tomcat 的配置文件
- \lib 存放库文件
- \logs 存放日志文件
- \temp 存放临时文件
- \webapps 存放 web 应用
- \work 存放 JSP 转换后的 Servlet 文件



## Servlet技术

**概念**

狭义的 Servlet 是指 Java 语言实现的一个接口，广义的 Servlet 是指任何实现了这个 Servlet 接口的类。

**特点**

- 运行在支持 java 的应用服务器上
- Servlet 的实现遵循了服务器能够识别的规则，也就是服务器会自动的根据请求调用对应的 servlet 进行请求处理
- 简单方便，可移植性强

**使用**

1. 创建普通的 java 类并继承 HttpServlet

2. 覆写 service 方法

3. 在 service 方法中书写逻辑代码即可

4. 在 webRoot 下的 WEB-INF 文件夹下的 web.xml文件中配置 servlet

**生命周期**

- 从第一次调用到服务器关闭
- 如果Servlet在web.xml中配置了load-on-startup,周期是从服务器启动到关闭

**方法**

- Service：可以处理get/post请求方式，此方法会被优先调用
- doGet：处理get请求的方式
- doPost：处理post请求方式

### request对象

- 作用：request对象中封存了当前请求的所有请求信息
- request对象由Tomcat服务器创建，并作为实参传递给处理请求的servlet的service方法。
- 注意：如果要获取的请求数据不存在，不会报错，返回null。
- 获取请求头数据
  - req.getMethod() //获取请求方式
  - req.getRequestURL() //获取请求URL信息
  - req.getRequestURI() //获取请求URI信息
  - req.getScheme() //获取协议
- 获取请求行数据
  - req.getHeader("键名") //返回指定的请求头信息
  - req.getHeaderNames() //返回请求头的键名的枚举集合
- 获取用户数据
  - req.getParameter("键名") //返回指定的用户数据
  - req.getParameterValues("键名") //返回同键不同值的请求数据(多选)，返回的数组
  - req.getParameterNames() //返回所有用户请求数据的枚举集合

### response对象

- 用来响应数据到浏览器的一个对象
- servlet请求处理代码流程
  - 设置请求编码格式
  - 设置响应编码格式
  - 获取请求数据
  - 处理请求数据
  - 数据库操作（MVC思想）
  - 响应处理结果
- 设置响应头
  - setHeader(String name,String value);//在响应头中添加响应信息，同键会覆盖
  - addHeader(String name,String value);//不会被覆盖
- 设置响应编码格式
  - resp.setContentType("text/html;charset=utf-8")
- 设置响应状态码
  - sendError(int num,String msg);//自定义响应状态码
- 设置响应实体
  - resp.getWrite().write(String str);//响应具体的数据给浏览器

### 转发和重定向

**转发(Forward)是服务器行为，重定向(Redirect)是客户端行为。**

**转发（Forward）** 通过RequestDispatcher对象的forward（HttpServletRequest request,HttpServletResponse response）方法实现的。RequestDispatcher可以通过HttpServletRequest 的getRequestDispatcher()方法获得。

**重定向（Redirect）** 是利用服务器返回的状态码来实现的。客户端浏览器请求服务器的时候，服务器会返回一个状态码。服务器通过 `HttpServletResponse` 的 `setStatus(int status)` 方法设置状态码。如果服务器返回301或者302，则浏览器会到新的网址重新请求该资源。

转发地址栏不变，重定向地址栏改变。转发是一次请求，重定向是两次请求。

### Cookie

客户端存值技术

**运行原理**

- 当浏览器输入 URL 访问服务器时会自动携带所有有效Cookie(时间内,指定路径内,指定域名内)，Tomcat 接收请求后会把Cookie 放入到 HttpServletRequest 中，在代码中通过 request 对象获取到 Cookie 的内容。
- 服务器内容可以产生 Cookie 内容，需要放入到响应对象中响应给客户端浏览器.(要求跳转类型为重定向)，客户端浏览器接收响应内容后会把 Cookie 内容存储到指定文件夹内容。

创建cookie对象：Cookie c=new Cookie(String name,String value)

响应cookie信息给客户端：resp.addCookie(c)

设置cookie有效期：setMaxAge();

设置有效路径：setPath()

### Session技术

- **特点**：存储在服务器端、服务器进行创建、依赖cookie技术
- 创建session对象：HttpSession hs=req.Session();
- 设置存储时间：hs.setMaxInactiveInterval()；默认存储时间30分钟
- 设置session强制失效：hs.invalidate();
- 存储和获取数据：
  - hs.setAttribute(String name,Object value)
  - hs.getAttribute(String name)

### ServletContext对象

- 不同用户的数据共享
- 获取方式
  - 一、ServletContext sc=this.getServletContext()
  - 二、ServletContext sc=this.getServletConfig().getServletContext()
  - 三、ServletContext sc=req.getSession().getServletContext()
- 数据存储：sc.setAttribute(String name,Object value)
- 获取web.xml的全局配置数据
  - String src=sc.getInitParameter(String name)
  - 作用：将静态数据和代码进行解耦
- 获取webroot下资源的绝对路径
  - String path=sc.getRealPath(String path)
  - 获取的目录为项目的根目录
- 获取webroot下资源的流对象
  - InputStream is=sc.getResourceAsStream(String path)

### ServletConfig对象

- 获取在web.xml中的每个servlet单独配置的数据
- 获取对象：ServletConfig sc=this.getServletConfig();
- 获取web.xml的配置数据：String code=sc.getInitParameter(String name)



## xml文件

### web.xml

**Web项目下**：局部配置，针对本项目的位置

**Tomcat下**：全局配置，配置公共信息

内容（**核心组件**）

- 全局上下文配置
- servlet配置
- 过滤器配置
- 监听器配置

**加载顺序**：ServletContext->context-param->listener->filter->servlet

**加载时机**：服务器启动



## JSP

**概念**

Java Server Pages，java服务器页面

特点

- 本质上还是servlet
- 跨平台，一次编写处处运行
- 组件跨平台
- 健壮性和安全性

**访问原理**

- 浏览器发起请求，请求 JSP，请求被 Tomcat 服务器接收，执行JspServlet 将请求的 JSP 文件转义成为对应的 java 文件(也是Servlet)，然后执行转义好的 java 文件

### 语法和指令

- JSP三种注释

  前端语言注释：会被转译，也会被发送，但不会被浏览器执行

  - Java语言注释：会被转译，但不会被servlet执行
  - JSP注释：不会被转译<%-- --%>

- page指令(配置JSP文件的转译相关的参数)

  - <%@ page 属性名="属性值" 属性名="属性值"... %>
  - language：声明JSP要转译的语言
  - import：声明转译的java文件要导入的包，不同的包使用逗号隔开
  - pageEncoding：设置JSP文件的数据编码格式
  - contentType="text/html; charset=UTF-8"
    - 设置JSP数据响应给浏览器的解析和编码格式
  - session：设置转译的servlet中是否开启session支持，默认开启。false关闭
  - errorPage：设置JSP运行错误跳转的页面
  - extends：设置jsp转译的Java文件要继承的父类（包名+类名）

- 局部代码块

  - <% java代码 %>
  - 局部代码块中声明的java代码会被原样转译到jsp对应的servlet文件的_JspService方法中
  - 代码块中声明的变量都是局部变量

- 全局代码块

  - <%! java代码 %>
  - 声明的java代码作为全局代码转译到对应的servlet类中

- 脚本段语句

  - <% =变量名/方法 %>

- 静态引入

  - <%@ include file="要引入的jsp文件的相对路径" %>
  - 会将引入的jsp文件和当前jsp文件转译成一个java文件使用

- 动态引入

  - 会将引入的jsp文件单独转译，在当前文件转译好的java文件中调用引入的jsp文件的转译文件

- 转发标签

  - 特点：一次请求，地址栏信息不改变

### 九大内置对象

jsp文件在转译成对应的servlet文件时自动生成并声明的对象，在jsp页面可以直接使用

**pageContext**

- 页面上下文对象，封存了其他内置对象
- 每个jsp文件单独拥有一个pageContext对象,作用域：当前页面

**request**

- 封存当前请求数据的对象，由Tomcat创建，一次请求

**session**

- 用来存储用户不同请求的共享数据。一次会话

**application**

- 就是ServletContext对象，一个项目只有一个。存储用户共享数据的对象，以及完成其他操作。项目内

**response**

- 响应对象，用来响应请求处理结果给浏览器。设置响应头，重定向

**out**

- 响应对象，jsp内部使用。带有缓冲区的响应对象，效率高于response

**page**

- 代表当前jsp 的对象。相当于java中的this

**exception**

- 异常对象。存储当前运行异常的信息
- 使用此对象需要在page指定中使用属性isErrorPage="true"开启

**config**

- 也就是ServletConfig，主要用来获取web.xml中配置数据，完成一些初始化数据的读取

### 资源路径

- **相对路径**
- 使用**绝对路径**
  - /虚拟项目名/项目资源路径
  - 在jsp中资源的第一个"/"表示服务器根目录，相当于：localhost:8080
- 使用jsp中自带的**全局路径声明**
  - `<% String path = request.getContextPath(); String basePath = request.getScheme() + "://" + request.getServerName() + ":"+request.getServerPort() + path + "/"; %>`
  - `<base href="<%=basePath %>">`
  - 上面这段代码实现打印的是： http://localhost:8080/项目名
  - 作用：资源前面添加项目路径。 例如：http://127.0.0.1:8080/项目名



## Ajax

**概念**

异步JavaScript和XML，异步刷新技术，用来在当前的页面内响应不同的请求内容

**状态码**

- readyState:0,1,2,3,4
- 4:表示响应内容被成功接收

**响应状态码**

- status
- 200：一切ok
- 404：资源未找到
- 500：内部服务器错误

### jQuery中ajax分类

- $.ajax({ 属性名:值,属性名:值})

  ```js
  /*
  url: 请求服务器地址
  data:请求参数
  dataType:服务器返回数据类型
  error 请求出错执行的功能
  success 请求成功执行的功能,function(data) data 服务器返
  回的数据.
  type:请求方式
  */
  $("a").click(function(){
  	$.ajax({
  		url:'demo',
  		data:{"name":"张三"},
  		dataType:'html',
  		error:function(){
  		alert("请求出错.")
  		},
  		success:function(data){
  		alert("请求成功"+data)
  		},
  		type:'POST'
  	});
  	return false;
  })
  ```

- 简化$.ajax

  `$.get(url,data,success,dataType))`

  `$.post(url,data,success,dataType)`

- 简化$.get()

  `$.getJSON(url,data,success)`，相 当 于 设 置 $.get 中 `dataType=”json”`

  `$.getScript(url,data,success) `，相 当 于 设 置 $.get 中 `dataType=”script”`

如果服务器返回数据是从表中取出，为了方便客户端操作返回的数据，服务器端返回的数据设置成 json，客户端把 json 当作对象或数组操作。

**json数据格式**

- JsonObject：json 对象，理解成 java 中对象 `{“key”:value,”key”:value}`
- JsonArray：json 数组 `[{“key”:value,”key”:value},{}]`



## EL、JSTL

**EL表达式**

- `${ }`
- 作用域查找顺序
  - pageConext->request->session->application
  - 从小到大查找，获取到则不继续找；
- 空值判断：`${empty 键名}`
- 请求头数据
  - `${header}`：所有请求头数据
  - `${header["键名"]}`：指定键名的请求头数据
  - `${headerValues["键名"]}`：返回指定键名(同键不同值)的值数组

**JSTL标签库**

- 需要jstl.jar包
- 核心标签：`<%@ taglib prefix="c" uri="<http://java.sun.com/jsp/jstl/core>" %>`



## 过滤、监听器

**过滤器**

- 创建一个实现Filter接口的普通java类

- 覆写接口的方法

  init方法：服务器启动即执行，资源初始化

  doFilter方法

  - 拦截请求的方法，在此方法中可以对资源实现管理
  - 需要手动对请求进行放行。chain.doFilter(request,response)

  dostroy方法：服务器关闭执行

- 在web.xml中配置过滤器

- 生命周期：服务器启动到服务器关闭

- 案例

  - 统一编码格式设置
  - session管理
  - 资源管理（加水印、和谐词汇等）

**监听器**

- 监听request
  - 实现ServletRequestListener接口：监听request对象的创建和销毁
  - 实现ServletRequestAttributeListener：监听request作用域数据的变更
- 监听session
  - HttpSessionListener：监听session的创建和销毁
  - HttpSessionAttributeListener：监听session数据的变更
- 监听application
  - ServletContextListener：监听application对象的初始化和销毁
  - ServletContextAttributeListener：监听数据的变更
- 案例
  - 统计当前在线人数