## XML

### XML 介绍与用途

XML（EXtensible Markup Language）可扩展标记语言，与 HTML 类似，单没有预定义标签，重在保存与传输数据。

用途：

- Java 程序的配置描述文件（无需重新编译）；
- 用于保存程序产生的数据；
- 网络间的数据传输（例如 webservice 底层 soap 协议）；

语法规则：

- 第一行必须是 XML 声明`<?xml version="1.0" encoding="UTF-8"?>`；
- 有且只有一个根节点；
- XML 标签的书写规则与 HTML 相同；

### XML 语义约束——DTD、XML Schema

在 xml 上做出改进（类似 JS 上衍生出 TS 类型定义），不允许随意书写节点和属性（都必须经过定义，方便维护）。

- DTD（Document Type Definition，文档类型定义）（文件扩展名为 .dtd），例如：
  - 定义 hr 节点下最少出现一个 employee 子节点：`<!ELEMENT hr (employee+)>`；
  - employee 节点下必须包含以下四个节点，且按顺序出现：`<!ELEMENT employee (name, age, salary, department)>`；
  - 在 xml 文件中使用`<!DOCTYPE>`标签来引用 DTD 文件（写在第二行）：`<!DOCTYPE hr SYSTEM "hr.dtd">`；
  - 注意空格！
- XML Schema，是 W3C 标准，比 DTD 更复杂（提供更多功能，例如：数据累心个、格式限定、数据范围）（文件扩展名为 .xsd）；

### Dom4j：Java 解析 XML

DOM（Document Object Model）定义了访问和操作 XML 文档的标准方法，DOM 把 XML 文档当做”**树结构**“来查看，能够通过 DOM 树来读写所有元素。

Dom4j 将 XML（整个）视为 Document 对象；“XML 标签”被 Dom4j 定义为 Element 对象。

官网地址：https://dom4j.github.io/，底下有各种使用例子。

<img src="http://tva1.sinaimg.cn/large/006KtwDzgy1gz0r453523j30wi0e0ai1.jpg" alt="image.png" style="zoom:50%;" />

### :arrow_down:XPath 路径表达式

稍作了解即可。

XPath 是 XML 文档中查找数据的语言，包含基本表达式和谓语表达式：

- 基本表达式（/、//、.、..、@），例如：
  - bookstore：选取 bookstore 元素的所有子节点；
  - /bookstore：选取根元素 bookstore；
  - bookstore/book：选取属于 bookstore 的子元素的所有 book 元素；
  - //book：选取所有 book 子元素，而不管它们在文档中的位置；
  - //@lang：选取名为 lang 的所有属性；
- 谓语表达式，例如：
  - /bookstore/book[1]：选取属于 bookstore 子元素的第一个 book 元素；
  - /bookstore/book[last()-1]：选取属于 bookstore 子元素的倒数第二个 book 元素；

它的使用就是结合 dom4j：`document.selectNodes(xpathExp);`（要引入一个新的依赖：Jaxen）。

## Servlet

Servlet（Server Applet）服务器小程序，主要功能用于生成动态 Web 内容。

#### 软件结构发展史

- 单机时代——桌面应用；
  - 缺点：数据难以共享、安全性差、更新不及时；
- 联机时代（Client-Server 模式，即”客户端-服务器“模式）；
  - 缺点：必须安装客户端，升级和维护困难；
- 互联网时代（Browser-Server 模式，即”浏览器-服务器“模式）；
  - 相较于 C/S 模式，执行速度与用户体验相对较弱；

#### J2EE 是什么

- J2EE（Java 2 Platform Enterprise Edition）是指”Java 2 企业版“；
- 相较于 J2SE，增加了很多功能；其中开发 BS（Web）应用程序是 J2EE 的核心；
- J2EE 由 13 个功能模块组成（前 3 个常见，后 10 个现在基本不用）：Servlet、JSP、JDBC、XML、EJB、RMI、JNDI、JMS、JTA、JavaMail、JAF、CORBA、JTS；

#### Apache Tomcat 与 J2EE 的关系

- J2EE 是一组技术规范和指南，具体实现由软件产商决定；
- Tomcat 是 J2EE Web（Servlet 与 JSP）标准的实现者；
- J2SE 是 J2EE 运行的基石，运行 Tomcat 离不开 J2SE；

简言之，可以将 Tomcat 视为服务器容器（躯体），Servlet 视为程序逻辑实现（灵魂）。将写好的程序部署在`tomcat/webapps`文件夹底下。

#### 第一个 Servlet

##### 1. 环境初始化

在 IDEA 中新建 Dynamic Web Porject，参考：https://blog.csdn.net/Awt_FuDongLai/article/details/115523552。

##### 2. 继承 HttpServlet，编写代码

```java
public class FirstServlet extends HttpServlet {
  @Override // 重写 service 方法，编写代码
  protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException {
   	// ?name=yqb&spec=English&spec=Program&spec=Swimming
    String name = request.getParameter("name"); // 接收单个参数
    String specs = request.getParameterValues("spec"); // 接收多个同名参数
    
    // 获取请求方法 GET POST
    // String method = request.getMethod();
    
    String html = "<h1>hi, " + name + "!</h1>";
    PrintWriter out = response.getWriter(); // 向浏览器输出的数据流
    out.println(html); // 将 html 发送给浏览器
  }
  
  
}
```

##### 3. 配置 web.xml, 绑定路由

在`WebContent/WEB-INF/web.xml`中：

```xml
<!-- 声明 servlet -->
<servlet>
	<servlet-name>first</servlet-name> <!-- servlet 的别名 -->
  <servlet-class>com.imooc.servlet.FirstServlet</servlet-class>
</servlet>

<!-- 将 servlet 与 url 绑定 -->
<servlet-mapping>
	<servlet-name>first</servlet-name> <!-- 对应 -->
  <url-pattern>/hi</url-pattern>
</servlet-mapping>
```

Tomcat 添加 FistServlet，访问路由：`http://localhost:8080/FirstServlet/hi`。其中 FirstServlet 为 context-path；context-path 是“上下文路径”，默认为工程名。

#### 标准 Java Web 工程结构

- "tomcat 安装目录"/webapps/XXapp/：Java Web 的应用目录；

  - /index.html：默认首页；
  - /META-INF/MANIFEST.MF：包含 web 应用的版本信息；

  - /WEB-INF：WEB 应用的安全目录，用于存放配置文件；
    - /WEB-INF/web.xml：web.xml 是“部署描述符文件”，是该 Web 项目核心配置文件；
    - /WEB-INF/classes：存放编译后的 classes 文件；
    - /WEB-INF/lib：用于存放 web 应用依赖的 jar 文件；

### Get 请求与 Post 请求

```java
public class RequestMethodServlet extends HttpServlet {
  // service 方法是核心，接收所有请求，无差别处理，默认做转发；如果要区分 get、post 请求，需要重写 doGet、do Post 方法
  public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException {
    // ...
  }
  public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException {
    // ...
  }
}
// 配置路由还是安装原来的方法配置；servlet 会根据用户请求的方法，来自动选择 get 和 post；
```

### Servlet 生命周期

1. 装载——web.xml：装载时并没有创建 servlet 对象，只有在 servlet 调用时，才调用对应的构造函数；
2. 创建——构造函数；
3. 初始化——init()；
4. 提供服务——service()；
5. 销毁——destroy()：tomcat 重启；

### 注解简化配置

使用注解，就不需要在 xml 做配置了，优化编码体验。

```java
@WebServlet("/hello")
public class AnnotationServlet extends HttpServlet {
  public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException {
    // ...
  }
  public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException {
    // ...
  }
}
```

### 启动时加载 Servlet

启动时加载，通常用于系统的预处理，例如初始化数据库。

```xml
<servlet>
	<servlet-name>first</servlet-name>
  <servlet-class>com.imooc.servlet.FirstServlet</servlet-class>
  <load-on-startup>0</load-on-startup> <!-- 0-9999，数值越小越先执行 -->
</servlet>
```

也可以使用注释（不需要 xml）：

```java
@WebServlet(urlPatterns="/unused", loadOnStartup=2) // urlPatterns 必传，随意传，不写的话不生效
public class AnnotationServlet extends HttpServlet {
}
```

## JSP

### 介绍

Servlet 的缺点：

- 静态 HTML 与动态 Java 代码混合在一起，难以维护；
- 逐行 out.println("<html>") 是多么痛苦的一件事情，开发效率低下；
- 在 Eclipse 很难在开发过程中发现错误，调试困难；

JSP（Java Server Pages，Java 服务器页面）：

- 是 J2EE 的功能模块，由 Web 服务器（Tomcat）执行，**本质就是 Servlet**；
- 作用就是将 Java 代码和 HTML 分离，降低动态网页开发难度；

```jsp
<!-- salary.jsp -->
<table>
  <%
    for(int i = 0; i <= 50; i ++) {
      out.println("<tr>");
      out.println("<td>" + i + "</td>");
      out.println("<td>" + (1500 + 50 * i) + "</td>");
      out.println("</tr>");
    }
  %>
</table>
<%
	for (int i = 0; i < 10; i ++) {
%>
	<h1>
    <%= i =%> html 嵌入在 for 循环中间，这样可以更方便地在 for 循环中输出复杂的 html（相比较 out.println）
	</h1>
<%
  }
%>
```



### 执行过程

JSP -> （转译成）Servlet 源代码 ->（编译成）Servlet 字节码；

例如上面的 table 例子，转译成 Servlet 代码为：

```java
// salary_jsp.java
public final class salary_jsp extends HttpJspBase implements JspSourceDependent, JspSourceImports {
  // ...
  public void _jspService(final HttpServletRequest request, final HttpServletResponse response) {
    // ...
    out = pageContext.getOut();
    out.write("<table>");
    // ... 中间那段 for 循环原封不动
    out.write("</table>"); 
  }
  // ...
}
```

### 基本语法

- JSP 代码块：`<% java 代码 %>`，例如 `<% System.out.println("hello world!"); %>`
- JSP 声明构造块：`<%! 声明语句 %>`，例如 `<%! public int add(int a, int b) { return a + b; }; %>`
- JSP 输出指令：`<%= java 代码 %>`，例如 `<%="<b>" + name + "</b>" %>`，相当于 `<% out.println("<b>" + name + "</b>"); %>`
- JSP 处理指令：`<%@ jsp 指令 %>`，例如`<%@page import="java.util.*" %>`，常用指令有：
  - `<%@page contetnType="text/html; charset=utf-8" %>` 定义当前 JSP 页面全局设置；
  - `<%@include file="component/header.jsp" %>` 将其他 JSP 页面与当前 JSP 页面合并；
  - `<%@taglib %>` 引入 JSP 标签库；

## Servlet 与 JSP 进阶

略

## JSTL 与 EL 表达式

以下内容更多感觉是“前端如何接收后端数据并展示”部分，先略过

## Java Web 基础入门测试

略

## Maven 项目管理

相当于 npm，多了一个集中式包下载功能（不用一个一个去官网找下载包），统一各种编辑器的项目目录和打包方式。