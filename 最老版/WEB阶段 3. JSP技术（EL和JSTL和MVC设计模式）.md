# 2018/11/19   JSP技术

## 一.JSP技术

### **1.jsp概述和运行原理**（面试，重要）

```
	JSP全名是Java Server Pages，理解为服务器上的页面，建立在servlet规范上的动态网页开发技术，其中html和java共同存在。
	
	JSP运行原理：客户端发出请求 --> 该jsp第一次被请求：jsp引擎（容器）吧该jsp文件转换成servlet.java文件 -->然后编译成.class文件   --> 执行servlet实例(第二次的话，直接执行servle字节码文件)
被翻译后的servlet在Tomcat的work目录中可以找到。
```

### **2.jsp脚本和注释**

```
jsp脚本：
1）<%java代码%> ----- 内部的java代码翻译到service方法的内部
2）<%=java变量或表达式>----会被翻译成service方法内out.print()
3）<%!java代码%> ---- 会被翻译成servlet的成员的内容

jsp注释：  不同的注释可见范围是不同
1）Html注释：<!--注释内容--> ---可见范围 jsp源码、翻译后的servlet、页面		显示html源码
2）java注释：//单行注释  /*多行注释*/ --可见范围 jsp源码 翻译后的servlet
3）jsp注释：<%--注释内容--%> ----- 可见范围 jsp源码可见
```

### **3.jsp指令（3个）**（重要）

​	jsp的指令是指导jsp翻译和运行的命令，jsp包括三大指令： 

```
1）page指令 --- 属性最多的指令（实际开发中page指令默认）
属性最多的一个指令，根据不同的属性，指导整个页面特性
格式：<%@ page 属性名1= "属性值1" 属性名2= "属性值2" ...%>
常用属性如下：
language：jsp脚本中可以嵌入的语言种类
pageEncoding：当前jsp文件的本身编码---内部可以包含contentType
contentType：response.setContentType(text/html;charset=UTF-8)
session：是否jsp在翻译时自动创建session
import：导入java的包
errorPage：当当前页面出错后跳转到哪个页面
isErrorPage：当前页面是一个处理错误的页面

2）include指令
页面包含（静态包含）指令，可以将一个jsp页面包含到另一个jsp页面中
格式：<%@ include file="被包含的文件地址"%>

3）taglib指令
在jsp页面中引入标签库（jstl标签库、struts2标签库）
格式：<%@ taglib uri="标签库地址" prefix="前缀"%>
```

### **4．jsp内置对象（9个）----- 面试笔试**（重要）

|  **名称**   |                  **类型**                   |
| :---------: | :-----------------------------------------: |
| pageContext | JSP的页面容器   **可以获得其他8大隐式对象** |
|    page     |      指当前页面转换后的Servlet类的实例      |
|   request   |              得到用户请求信息               |
|  response   |          服务器向客户端的回应信息           |
|   session   |             用来保存用户的信息              |
| application |             所有用户的共享信息              |
|     out     |         用于页面输出   out.write()          |
|   config    |       服务器配置，可以取得初始化参数        |
|  exception  | 表示JSP页面所发生的异常，在错误页中才起作用 |

### **5.jsp动作标签**（动态包含和静态包含）

```
jsp中的动作标签：
1）页面包含（动态包含）：<jsp:include page="被包含的页面"/>
   当前页面个被包含的页面，分别产生servlet源码和字节码，运行时，由
   Tomcat进行合并输出。
2）请求转发：<jsp:forward page="要转发的资源" />（这只是jsp标签）
3）静态包含： <%@ include file="被包含页面" %> 
当前页面和被包含的页面，合并后生成一个servlet源码。
```

### **6.servlet与jsp的对比**（面试）

```
	servlet是服务器端的程序,动态生成html页面发到客户端，但是这样程序里有许多out.println(），java和html语言混在一起很乱。所以后来推出了jsp。其实jsp就是servlet，每一个jsp在第一次运行时被转换成servlet文件，再编译成.class来运行。 
	有了jsp，因此在MVC模式中servlet不再负责生成html页面,转而担任控制程序逻辑的作用，控制jsp和javabean之间的流转。 
　　
	Servlet中没有内置对象，对于静态的HTML标签，Servlet都必须使用页面输出流诼行输出。Jsp是Servlet的一种简化，使用Jsp只需要完成程序员需要输出到客户端的内容，至于Jsp中的Java脚本如何镶嵌到一个类中，由Jsp容器完成。而Servlet则是个完整的Java类，这个类的Service方法用于生成对客户端的响应。jsp和servlet的实质是一样的，jsp最终还是编译成servlet
　　
　　一、jsp最终还是编译成servlet，所以jsp比servlet慢。
　　二、jsp负责前台页面显示，servlet负责业务控制。 
　　
　　1、jsp是由servlet发展演变而来的,jsp在运行的时候最终将会被转译成一个servlet。 
　　2、在jsp中可以使用的存值对象在servlet中大多数都能使用。 
　　3、jsp能够实现的功能servlet都能实现。 
　　4、一般情况下，我们在注重页面显示的时候使用jsp，在注重跳转控制的时候使用servlet。
```



## 二.EL技术

### **1.EL 表达式概述**

​	EL（Express Lanuage）表达式可以嵌入在jsp页面内部，减少jsp脚本的编写，EL 出现的目的是要替代jsp页面中脚本的编写。 

### **2.EL从域中取出数据（重要）**

```
EL最主要的作用是获得四大域中的数据，格式  ${EL表达式}
例子：
	EL获得pageContext域中的值：$(pageContextScope.key);
	EL获得request域中的值：$(request.key);
	EL获得session域中的值：$(session.key);
	EL获得application域中的值：$(application.key);
	EL从四个域中获得某个值$(key);

$(pageContext.request.contextPath)   相当于
<%=pageContext.getRequest().getContextPath%>
获得WEB应用的名称
```

### **3.EL 的内置对象**？

```
pageScope,requestScope,sessionScope,applicationScope
 ---- 获取JSP中域中的数据
param,paramValues 	- 接收参数.
header,headerValues	 - 获取请求头信息
initParam				- 获取全局初始化参数
cookie					- WEB开发中cookie
pageContext			- WEB开发中的pageContext.
```

## 三.JSTL技术

### **1.JSTL 概述**

```
	JSTL（JSP Standard Tag Library)，JSP标准标签库，可以嵌入在jsp页面中使用标签的形式完成业务逻辑等功能。jstl出现的目的同el一样也是要提到jsp页面中的脚本代码。JSTL标准标准标签库有5个子库，但随着发展，目前常使用的是他的核心库。（常用就以下一个）
```

| **标签库** | **标签库的URI**                   | **前缀** |
| ---------- | --------------------------------- | -------- |
| Core       | http://java.sun.com/jsp/jstl/core | c        |

### **2.JSTL 下载与导入**

```
JSTL下载：
从Apache的网站下载JSTL的JAR包。进入		“http://archive.apache.org/dist/jakarta/taglibs/standard/binaries/”网址下载	JSTL的安装包。jakarta-taglibs-standard-1.1.2.zip，然后将下载好的JSTL安装包	进行解压，此时，在lib目录下可以看到两个JAR文件，分别为jstl.jar和standard.jar 其中jstl.jar文件包含JSTL规范中定义的接口和相关类，standard.jar文件包含用于实现JSTL的.class文件以及JSTL中5个标签库描述符文件（TLD）。

jstl.jar和standard.jar  两个jar包 导入到工程lib中
```

### **3.JSTL 核心标签库的常用标签**

```
　JSTL的核心标签库标签共13个，使用这些标签能够完成JSP页面的基本功能，减少编码工作。

　　从功能上可以分为4类：表达式控制标签、流程控制标签、循环标签、URL操作标签。
（1）表达式控制标签：out标签、set标签、remove标签、catch标签。
（2）流程控制标签：if标签、choose标签、when标签、otherwise标签。
（3）循环标签：forEach标签、forTokens标签。
（4）URL操作标签：import标签、url标签、redirect标签、param标签。
```

### **4.主要用forEach和if标签：**

```
流程控制标签——if标签使用总结
　　<c:if>标签和程序中的if语句作用相同，用来实现条件控制。

	<c:if>标签的语法
　　【语法1】：没有标签体内容(body)
　　　　<c:if test="testCondition" var="varName" [scope="{page|request|session|application}"]/>
　　【语法2】：有标签体内容
　　　　<c:if test="testCondition" [var="varName"] [scope="{page|request|session|application}"]>
    　　　　　　标签体内容
　　　　</c:if>

　　【参数说明】：
　　　　（1）test是返回boolean的条件，一般使用EL表达式来编写。
　　　　（2）var属性用来存放判断的结果，类型为true或false。
　　　　（3）scopes属性用来指定var属性存放的范围。
　　　　
<%--使用if标签进行判断并把检验后的结果赋给adminchock，存储在默认的page范围中。 --%>
     <c:if test="${param.uname=='admin'}" var="adminchock">
     <%--可以把adminchock的属性范围设置为session，这样就可以在其他的页面中得到adminchock的值，
    使用<c:if text=”${adminchock}”><c:if>判断，实现不同的权限。 --%>
         <c:out value="管理员欢迎您！"/>
     </c:if>
```

```
<c:forEach>标签的语法
　<c:forEach var=”name” items=”Collection” 							varStatus=”StatusName” begin=”begin” 					end=”end” step=”step”>
    本体内容（循环体）
</c:forEach>

【参数解析】：
　　（1）var设定变量名用于存储从集合中取出元素。
　　（2）items指定要遍历的集合。
　　（3）varStatus设定变量名，该变量用于存放集合中元素的信息。   
　　（4）begin、end用于指定遍历的起始位置和终止位置（可选）。
　　（5）step指定循环的步长。
```

## 四.MVC设计模式

```
三组概念：
  架构：简单的说架构就是一个蓝图，是一种设计方案，将客户的不同需求抽象成为抽象组件，并且能够描述这些抽象组件之间的通信和调用。

  框架：软件框架是项目软件开发过程中提取特定领域软件的共性部分形成的体系结构，不同领域的软件项目有着不同的框架类型。框架不是现成可用的应用系统。而是一个半成品，提供了诸多服务，开发人员进行二次开发，实现具体功能的应用系统。

  设计模式：是一套被反复使用、多数人知晓的、经过分类编目的、代码设计经验的总结，它强调的是一个设计问题的解决方法
```

```
MVC设计模型：   （web层）
1.定义：MVC 设计模型是一种使用Model View Controller（模型-视图-控制器）设计创Web应用程序的模式。 由上主谓宾可以很容易看出，mvc模型是一种用来写web应用程序的样式，也就是说只能写web不能写其它。
	最典型的MVC就是javabean + JSP + servlet  的模式

2.既然使用了 Model View Controller（ 模型-视图-控制器），那么就很有必要来介绍一下该（模型-视图-控制器）到底是怎样的一个东西？
	Model（模型）：是应用程序中用于处理应用程序数据逻辑的部分。
　　　通常模型对象负责在数据库中存取数据。

	View（视图）：是应用程序中处理数据显示的部分。
　　　通常视图是依据模型数据创建的。

	Controller（控制器）：是应用程序中处理用户交互的部分。
　　　通常控制器负责从视图读取数据，控制用户输入，并向模型发送数据。
```













