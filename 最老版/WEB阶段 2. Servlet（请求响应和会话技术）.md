## 2018/11/**18**   web阶段复习之    Servlet

## 	**一.Servlet**

### **1.简介**

​	运行在服务端的Java小程序，是sun公司提供一套规范（接口），用来处理客户端请求、响应给浏览器的动态资源。但servlet的实质就是java代码，通过java的API   ，动态的向客户端输出内容 。

```
servlet规范：包含三个技术点
1）servlet技术
2）filter技术---过滤器
3）listener技术---监听器
```

```
实际开发中，我们不会直接去实现Servlet接口，因为那样需要覆盖的方法太多，我们一般创建类继承 HttpServlet  ！！！
实现步骤：	
1）创建类继承HttpServlet类
2）覆盖doGet和doPost
3）在web.xml中进行servlet的配置
```

### **2.Servlet的生命周期（面试题）**

```
1）Servlet何时创建
默认第一次访问servlet时创建该对象  innt（ServletConfig config）；
2）Servlet何时销毁
服务器关闭servlet就销毁了  destroy（）；
3）每次访问必然执行的方法
service(ServletRequest req, ServletResponse res)方法
```

### **3.Servlet的配置**

```
1.基本配置  在web.xml中配置
	其中url-pattern的配置方式：   （2和3不能混用）
	1）完全匹配 访问的资源与配置的资源完全相同才能访问到
	2）目录匹配 格式：  /虚拟的目录..   /*   *代表任意
	3）扩展名匹配 格式：   *.扩展名  
2．自定义配置  服务器启动实例化Servlet配置
	Servlet默认第一次访问时创建 为什么是默认？
当在servlet的配置时 加上一个配置 <load-on-startup> servlet对象在服务器启动时就创建
3．缺省Servlet
	可以将url-pattern配置一个/，代表该servlet是缺省的servlet
什么是缺省的servlet？当你访问资源地址所有的servlet都不匹配时 ， 缺省的servlet负责处理其实，web应用中所有的资源的响应都是servlet负责，包括静态资源。
```

### **4.ServletContex对象**

```
ServletContext代表是一个web应用的环境（域）对象，ServletContext对象内部封装的是该web应用的信息。
怎样获得：
1）ServletContext servletContext =config.getServletContext();
2）ServletContext servletContext = this.getServletContext(); 
```

### **5.ServletContex对象的作用**（重要 重要 重要）

```
1. 获得web应用全局的初始化参数   web.xml中配置初始化参数，然后通过对象获得参数。
2. 获得web应用中任何资源的绝对路径（重要 重要 重要）
   方法：String path = context.getRealPath(相对于该web应用的相对地址);
3. ServletContext 是一个域对象，域对象的作用范围：整个web应（所有的web资源都可以随意向servletcontext域中存取数据，数据可以共享）
```

### **6.开发中路径的编写**

```
	相对路径和绝对路径 绝对路径分为：
	
	客户端地址：是客户端去访问服务器的地址，服务器外部的地址，特点：写上web应用名称（工程名称）。
	服务器端地址：服务器内部资源的跳转的地址，特点：不需要写web应用的名称。
```

## **二.Request**

### **1.get和post两种请求方式实例**

```
GET /books/?sex=man&name=Professional HTTP/1.1
Host: www.wrox.com
User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6)
Gecko/20050225 Firefox/1.0.1
Connection: Keep-Alive
```

```
POST / HTTP/1.1
Host: www.wrox.com
User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6)
Gecko/20050225 Firefox/1.0.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 40
Connection: Keep-Alive
（----此处空一行----）
name=Professional%20Ajax&publisher=Wiley
```

### **2.两种提交方式的区别**（面试）

```
	get提交：请求的数据会附在URL之后（就是把数据放置在HTTP协议头中），以?分割URL和传输数据，多个参数用&连接。传输数据被请求头限制，安全性不高。
　　POST提交：把提交的数据放置在是HTTP请求体中。因此，GET提交的数据会在地址栏中显示出来，而POST提交，地址栏不会改变。理论上传输数据不受限，安全性比较好。
```

### **3.通过request获得请求行、头、体**

```
1.通过request获得请求行：
	(1)获得客户端的请求方式：String getMethod();
	(2)获得请求的资源：
	String getRequestURI(); 
	StringBuffer getRequestURL(); 
	String getContextPath() ---web应用的名称(重要)
	String getQueryString() ---get提交url地址后的参数字符串
	   例如：username=zhangsan&password=123
	   
2.通过request获得请求头：
	String getHeader(String name)；  知道一个就行
	
3.通过request获得请求体：
	post提交和get（都一样）：
	例子：username=zhangsan&password=123&hobby=football
	以上面参数为例，通过以下方法获得请求参数：
	String getParameter(String name)；  （常用，重要）

4.解决乱码：
解决post提交的乱码：request.setCharacterEncoding("UTF-8");
get：parameter =   
   new String(parameter.getbytes("iso8859-1"),"utf-8");

```

### **4.request是一个域对象**  （作用范围：一次请求中）

```
setAttribute(Stringname,Objecto)； 
getAttribute(Stringname)；
removeAttribute(Stringname；)
```

### **5.request完成请求转发**

​	request.getRequestDispatcher(String path).forward(req,res);

### **6.转发和重定向的区别**

```
1）重定向两次请求，转发一次请求
2）重定向地址栏的地址变化，转发地址不变
3）重新定向可以访问外部网站 转发只能访问内部资源
4）转发的性能要优于重定向
5）重定向是客户端行为，转发是服务器行为
6）重定向不能访问原request域，转发可以
```

## 三.Response：

### **1.响应：包括响应行，响应头，响应体**

```
HTTP/1.1 200 OK    响应行
Server:Apache-coyote/1.1  服务器名称
Accept-Ranges:bytes
ETag:W/"579-78785788"
LastModified:Tur,22 Mar 2016 14:37:35 GTM  最近访问日期
Content-Type:Text/html    响应正文的类型
Content-Length:567   响应正文长度
Date:Tur,22 Mar 2016 14:39:55 GTM
此处有空行
<!DOCTYPE html>    响应体
此处为响应的页面的代码。。。省略
```

### **2.通过response设置响应行、头、体**

```
1.设置响应行的状态码：
setStatus(int sc)；  设置状态码

2.通过response设置响应头：
addHeader(String name, String value) ；  添加响应头
addIntHeader(String name, int value) ；
addDateHeader(String name, long date) ；
setHeader(String name, String value) ；  设置响应头
setDateHeader(String name, long date) ；
setIntHeader(String name, int value)；

3.通过response设置响应体
	(1)响应体设置文本
	response.getWriter().writ(String s);
	获得字符流，通过字符流的write(String s)方法可以将字符串设置到response缓冲区中，随后Tomcat会将response缓冲区中的内容组装成Http响应返回给浏览器端。
	解决乱码问题：
	设置response的编码：
	response.setCharacterEncoding(String charset) ；   
	（这样只是设置了response缓冲区的编码，页面解析还是按照本地浏览器）
	指定浏览器解析页面的编码：（重要）
	response.setContentType("text/html;charset=UTF-8");
（不仅可以指定解析页面时的编码，同时也含setCharacterEncoding的功能）
所以在实际开发，只需要response.setContentType
	(2)响应体设置字节（了解即可）
	getOutPutSteam().write(byte[] bytes);
```

### **3.未完成目标：   ！！！！！！！！！！！！！？？？？？？？？？？**

***<u>day14--response.pdf  中第六页开始（文件的下载，实现验证码）两个案例</u>***

## **四：Cookie**

### **1.会话技术**

```
	从打开一个浏览器访问某个站点，到关闭这个浏览器的整个过程，成为一次会话。会话技术就是记录这次会话中客户端的状态与数据的。
	会话技术分为Cookie和Session：
Cookie：数据存储在客户端本地，减少服务器端的存储的压力，安全性不好，客户端可以清除cookie。
Session：将数据存储到服务器端，安全性相对好，增加服务器的压力。
```

### **2.服务器端向客户端发送一个Cookie**

```
1.创建Cookie：
Cookie cookie = new Cookie("username"，"zhangsan");

2.设置Cookie在客户端的持久化时间：  （有些重要）
	cookie.setMaxAge(int seconds); ---时间秒
	注意：如果不设置持久化时间，cookie会存储在浏览器的内存中，浏览器关闭时cookie信息销毁（会话级别的cookie），如果设置持久化时间，cookie信息会被持久化到浏览器的磁盘文件里。
	
3.设置Cookie的携带路径：
	cookie.setPath(String path);
	注意：如果不设置携带路径，那么该cookie信息会在访问产生该
    	cookie的web资源所在的路径都携带cookie信息。
    	
4.向客户端发送cookie：
	response.addCookie(Cookie cookie);

5.删除客户端的cookie：
	如果想删除客户端的已经存储的cookie信息，那么就使用同名同路径的持久化时	间为0的cookie进行覆盖即可。
```

### **3.服务器端怎么接受客户端携带的Cookie**

​	cookie信息是以请求头的形式发送到客户端；

```
1.通过request获得所有的Cookie：
Cookie[] cookies = request.getCookies();
2.遍历Cookie数组，通过Cookie的名称获得我们想要的Cookie
for(Cookie cookie : cookies){
	if(cookie.getName().equal(cookieName)){
		String cookieValue = cookie.getValue();
	}
}
```

## **五：Session**

### **1.Session简介**

```
	Session技术是将数据存储在服务器端的技术，会为每个客户端都创建一块内存空间存储客户的数据，但客户端需要每次都携带一个标识ID去服务器中寻找属于自己的内存空间。所以说Session的实现是基于Cookie，Session需要借助于Cookie存储客户的唯一性标识JSESSIONID。
```

### **2.获得Session对象**

```
	HttpSession session = request.getSession();
	此方法会获得专属于当前会话的Session对象，如果服务器端没有该会话的Session，对象会创建一个新的Session返回。
	如果已经有了属于该会话Session直接将已有的Session返回（实质就是根据JSESSIONID判断该客户端是否在服务器上已经存在	ession了）
```

### **3.怎样向session中存取数据**（session也是一个域对象）

```
Session也是存储数据的区域对象，所以session对象也具有如下三个方法：
	session.setAttribute(String name,Object obj);
	session.getAttribute(String name);
	session.removeAttribute(String name);
```

### **4.Session对象的生命周期**（面试题/笔试题）

```
1.创建：第一次执行request.getSession()时创建

2.销毁：
	(1)服务器（非正常）关闭时
	(2)session过期/失效（默认30分钟,从不操作服务器端的资源开始计时）
		可以在工程的web.xml中进行配置
		<session-config>
       		<session-timeout>30</session-timeout>
		</session-config>
	(3)手动销毁session :  session.invalidate();

3.作用范围：
   默认在一次会话中，也就是说在，一次会话中任何资源公用一个session对象
```

## 5**.未完成目标：   ！！！！！！！！！！！！！？？？？？？？？？？**

***<u>day16--Session和Cookie.pdf  中（用户上次访问时间，一次性验证码校验）两个案例</u>***    JSESSIONID的持久化？？  验证码校验功能实现和总结

