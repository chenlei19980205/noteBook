## 2018/11/18    HTTP&Tomcat服务器

目标：能够描述浏览器和服务器交互过程+能过产生HTTP请求、响应协议格式+看懂web项目目录结构

##          **一.****HTTP协议**：

​	超文本传输协议（HyperText Transfer Protocol）,互联网用的最广泛的协议，用于定义web浏览器与web服务器之间交换数据的过程。

特点：基于请求响应，必须成对，先请求后响应。默认端口号：80。1.1版本，只用此版本，一次连接可获得多个web资源，web项目版本一般用2.5而不是3.0.

**HTTP请求：**两种方式的     请求行，头，体： 

1.get请求方式：参数追加在URL后面，不安全，URL长度限制了get方式的数据大小，而且get方式没有请求体。

2.post请求方式：参数放在请求体，安全，能携带更多数据，数据使用键值对的形式。

**HTTP响应（例子）：**

```
HTTP/1.1 200 OK
Server:Apache-coyote/1.1  服务器名称
Accept-Ranges:bytes
ETag:W/"579-78785788"
LastModified:Tur,22 Mar 2016 14:37:35 GTM  最近访问日期
Content-Type:Text/html    响应正文的类型
Content-Length:567   响应正文长度
Date:Tur,22 Mar 2016 14:39:55 GTM
此处有空行
<!DOCTYPE html>
此处为响应的页面的代码。。。省略
```

```
状态码（重要）：例：HTTP/1.1 200 OK
协议/版本 状态码  状态码描述
200  ：  请求成功
302  ：  请求重定向
304  ：  请求资源没有改变，访问本地缓存
404  ：  请求资源未找到，或不存在，通常是路径错误，也可能是服务器资源删除
500  ：  服务器内部出错，通常程序抛异常
```

request中封装着所有的请求信息；response中封装着所有的响应信息

#### getpost提交方式的差别，详解

```
	get提交：请求的数据会附在URL之后（就是把数据放置在HTTP协议头中），以?分割URL和传输数据，多个参数用&连接。传输数据被请求头限制，安全性不高。
　　POST提交：把提交的数据放置在是HTTP请求体中。因此，GET提交的数据会在地址栏中显示出来，而POST提交，地址栏不会改变。理论上传输数据不受限，安全性比较好。
　　
最直观的区别就是GET把参数包含在URL中，POST通过request body传递参数。
GET请求在URL中传送的参数是有长度限制的，而POST么有。
GET在浏览器回退时是无害的，而POST会再次提交请求。
GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。
GET请求只能进行url编码，而POST支持多种编码方式。
GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。

GET和POST是什么？HTTP协议中的两种发送请求的方法。
HTTP是什么？HTTP是基于TCP/IP的关于数据如何在万维网中如何通信的协议。
HTTP的底层是TCP/IP。所以GET和POST的底层也是TCP/IP，也就是说，GET/POST都是TCP链接。GET和POST能做的事情是一样一样的。你要给GET加上request body，给POST带上url参数，技术上是完全行的通的。

GET和POST本质上就是TCP链接，并无差别。但是由于HTTP的规定和浏览器/服务器的限制，导致他们在应用过程中体现出一些不同。 

GET和POST还有一个重大区别，简单的说：
GET产生一个TCP数据包；POST产生两个TCP数据包。
长的说：
对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）；
而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）。
也就是说，GET只需要汽车跑一趟就把货送到了，而POST得跑两趟，第一趟，先去和服务器打个招呼“嗨，我等下要送一批货来，你们打开门迎接我”，然后再回头把货送过去。

1. GET与POST都有自己的语义，不能随便混用。

2. 据研究，在网络环境好的情况下，发一次包的时间和发两次包的时间差别基本可以无视。而在网络环境差的情况下，两次包的TCP在验证数据包完整性上，有非常大的优点。

3. 并不是所有浏览器都会在POST中发送两次包，Firefox就只发送一次。

在我大万维网世界中，TCP就像汽车，我们用TCP来运输数据，它很可靠，从来不会发生丢件少件的现象。但是如果路上跑的全是看起来一模一样的汽车，那这个世界看起来是一团混乱，送急件的汽车可能被前面满载货物的汽车拦堵在路上，整个交通系统一定会瘫痪。为了避免这种情况发生，交通规则HTTP诞生了。HTTP给汽车运输设定了好几个服务类别，有GET, POST, PUT, DELETE等等，HTTP规定，当执行GET请求的时候，要给汽车贴上GET的标签（设置method为GET），而且要求把传送的数据放在车顶上（url中）以方便记录。如果是POST请求，就要在车上贴上POST的标签，并把货物放在车厢里。当然，你也可以在GET的时候往车厢内偷偷藏点货物，但是这是很不光彩；也可以在POST的时候在车顶上也放一些数据，让人觉得傻乎乎的。HTTP只是个行为准则，而TCP才是GET和POST怎么实现的基本。
在我大万维网世界中，还有另一个重要的角色：运输公司。不同的浏览器（发起http请求）和服务器（接受http请求）就是不同的运输公司。 虽然理论上，你可以在车顶上无限的堆货物（url中无限加参数）。但是运输公司可不傻，装货和卸货也是有很大成本的，他们会限制单次运输量来控制风险，数据量太大对浏览器和服务器都是很大负担。业界不成文的规定是，（大多数）浏览器通常都会限制url长度在2K个字节，而（大多数）服务器最多处理64K大小的url。超过的部分，恕不处理。如果你用GET服务，在request body偷偷藏了数据，不同服务器的处理方式也是不同的，有些服务器会帮你卸货，读出数据，有些服务器直接忽略，所以，虽然GET可以带request body，也不能保证一定能被接收到哦。
```



## 二.web开发概述

软件架构：

```
C/S架构：客户端/服务端  要求客户端电脑安装一个客户端程序，例如QQ
   优点：用户体验好，信息安全，服务器负荷轻
   缺点：占用硬盘空间，维护麻烦，依赖用户安装
B/S架构：浏览器/服务器   不安装软件
	优点：维护升级简单，不必安装软件，内置浏览器
	缺点：动画效果受限，相对不安全，服务器压力大，使用按键按下能改善部分用户体验
```

常见web服务器：

1. Tomcat：中小型免费，支持servlet和jsp规范
2. WebLogic：大型收费，支持所有EE规范
3. WebSphere：大型收费，支持所有EE规范

Tomcat目录结构：

```
bin：脚本目录
conf：配置目录
lib：依赖jar库目录。项目中需要使用的jar包
logs：日志目录
temp：临时文件目录
webapps：web应用发布目录
work：Tomcat处理jsp的工作目录
```

web项目目录结构：

```
项目名称（webapps文件夹中的）
		静态资源，html css  js
		WEB-INF （不能直接通过浏览器访问）
			web.xml 当前项目的核心配置，2.5必须有  3.0可省略
			lib  单签项目需要的第三方jar包
			classes  字节码文件
```



































