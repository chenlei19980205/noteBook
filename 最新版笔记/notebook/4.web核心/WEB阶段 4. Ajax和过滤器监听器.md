# 2018/11/20   Ajax和过滤器监听器

## 一.Ajax概述

### **1.同步和异步**

```
	同步现象：客户端发送请求到服务器端，当服务器返回响应之前，客户端都处于等待卡死状态。
	异步现象：客户端发送请求到服务器端，无论服务器是否返回响应，客户端都可以随意做其他事情，不会被卡死。
```

### **2.Ajax的运行原理**

```
	页面发起请求，会将请求发送给浏览器内核中的Ajax引擎，Ajax引擎会提交请求到服务器端，在这段时间里，客户端可以任意进行任意操作，直到服务器端将数据返回给Ajax引擎后，会触发你设置的事件，从而执行自定义的js逻辑代码完成某种页面功能。
```

### **3.Json数据格式（重要）**

```
json是一种与语言无关的轻量级的数据交换的格式，作用：
	1.使用ajax进行前后台数据交换
	2.移动端与服务端的数据交换
```

```
1）Json的格式与解析
	1.对象格式：{"key1":obj,"key2":obj,"key3":obj...}
	2.数组/集合格式：[obj,obj,obj...]
	
例如：user对象 用json数据格式表示
{"username":"zhangsan","age":28,"password":"123","addr":"北京"}

List<Product> 用json数据格式表示
[{"pid":"10","pname":"小米4C"},{},{}]

注意：对象格式和数组格式可以互相嵌套

注意：json的key是字符串  jaon的value是Object

json的解析：
json是js的原生内容，也就意味着js可以直接取出json对象中的数据
```

```
2）Json的转换插件

将java的对象或集合转成json形式字符串
json的转换插件是通过java的一些工具，直接将java对象或集合转换成json字符串。
常用的json转换工具有如下几种：
1）jsonlib
2）Gson：google
3）fastjson：阿里巴巴

例：		Gson gson = new Gson();
String json = gson.toJson(Map或List<Map<String,Objct>>);
```

### **4.js原生Ajax（作了解）**

```
	js原生的Ajax其实就是围绕浏览器内内置的Ajax引擎对象进行学习的，要使用js原生的Ajax完成异步操作，有如下几个步骤：
	1）创建Ajax引擎对象
	2）为Ajax引擎对象绑定监听（监听服务器已将数据响应给引擎）
	3）绑定提交地址
	4）发送请求
	5）接受响应数据

注意：如果是post提交
在发送请求之前设置一个头
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");

总结：
所用异步访问都是ajax引擎
```

### **5.Jquery的Ajax技术（重点，重要）**

```
	jquery是一个优秀的js框架，自然对js原生的ajax进行了封装，封装后的ajax的操作方法更简洁，功能更强大，与ajax操作相关的jquery方法有如下几种，但开发中经常使用的有三种。
```

```
1）$.get(url, [data], [callback], [type])
2）$.post(url, [data], [callback], [type])

其中：
url：代表请求的服务器端地址
data：代表请求服务器端的数据（可以是key=value形式也可以是json格式）
callback：表示服务器端成功响应所触发的函数（只有正常成功返回才执行）
type：表示服务器端返回的数据类型（jquery会根据指定的类型自动类型转换）
常用的返回类型：text、json、html等
```

```
3）$.ajax( { option1:value1,option2:value2... } );
常用的option有如下：
async：是否异步，默认是true代表异步
data：发送到服务器的参数，建议使用json格式
dataType：服务器端返回的数据类型，常用text和json
success：成功响应执行的函数，对应的类型是function类型
type：请求方式，POST/GET
url：请求服务器端地址
```

### **6.未完成案例！！**

？？？？？？？异步校验用户名是否存在，站内搜索！！！！！！！

## 二.监听器Listener（面试） 

### **1.Listener简介**

```
servlet规范包括三个技术点：servlet  listener  filter

1．什么是监听器？
	监听器就是监听某个对象的的状态变化的组件
	监听器的相关概念：
	事件源：被监听的对象  ----- 三个域对象 request  session  			servletContext
	监听器：监听事件源对象  事件源对象的状态的变化都会触发监听器
	注册监听器：将监听器与事件源进行绑定
	响应行为：监听器监听到事件源的状态变化时 所涉及的功能代码
```

### **2.  6+2种Listener**

#### **（1）监听域对象的创建与销毁的Listener：**

```
ServletContextListener 
HttpSessionListener  
ServletRequestListener
```

#### **（2）监听域对象内属性的变化的Listener：**

```
ServletContextAtrributeListener 
HttpSessionAtrributeListener  
ServletRequestAtrributeListener
```

#### **（3）与session中的绑定的对象相关的Listener（对象感知监听器）：**

1.**绑定与解绑的监听器HttpSessionBindingListener：**

```
1.绑定状态：就一个对象被放到session域中
2.解绑状态：就是这个对象从session域中移除了

（1）调用 session.invalidate();方法 session销毁
（2）session到了设置或者默认的超时时间，自动销毁（关闭浏览器此session还未销毁，只是不能再用了）；

创建方法：
	新建一个类，继承HttpSessionBindingListener，实现了两个方法，它不需要配置xml文件，
	只是将实例化的HttpSessionBindingListener对象setAttribute到session里面就可以对HttpSessionBindingListener对象对象进行监听了，其实也就是对HttpSessionBindingListener对象所对应的session进行监听，还可以记录该session的具体信息（例如会员的登录信息）。

绑定例子：		会员登录成功的时候，代码中加上：
		UsersOnlineCountListener uocl = 
						new UsersOnlineCountListener();
		uocl.setUid(obj.getUid());
		session.setAttribute("uocl", uocl);//这个时候要触发					valueBound方法了
```

2.**钝化与活化的监听器HttpSessionActivationListener：** 

```
3.钝化状态（序列化）：是将session内存中的对象持久化到磁盘
4.活化状态（反序列化）：就是将磁盘上的对象再次恢复到session内存中

一、序列化与反序列化（本文末尾）
　1.什么是序列化
	把对象转化为字节序列的过程称为序列化（保存到硬盘，持久化）
	把字节序列转化为对象的过程称为反序列化（存放于内存）
 　2.序列化的用途
	把对象的字节序列永久保存到硬盘上，通常放到一个文件中。
	把网络传输的对象通过字节序列化，方便传输
	最常见的是Web服务器中的Session对象，当有10万用户并发访问，就有可能出现10万个Session对象，内存可能吃不消，于是Web容器就会把一些seesion先序列化到硬盘，等要用，再把保存在硬盘中的对象还原到内存中。
```

### **2.Listener的编写步骤**

```
a、编写一个监听器类去实现监听器接口
	例如	implements ServletContextListener
b、覆盖监听器的方法 例如：
	contextInitialized（ServletContextEvent arg0）
	contextDestroyed（ServletContextEvent arg0）
c、需要在web.xml中进行配置---注册
	<listener>
    	<listener-class>
    		com.chenlei.web.filter.Test
    	</listener-class>
 	</listener>
```

### **3.<u>ServletContextListener</u>的主要作用**

```
a、初始化的工作：初始化对象 初始化数据 ---- 加载数据库驱动  连接池的初始化。
b、加载一些初始化的配置文件 --- spring的配置文件
c、任务调度----定时器----Timer/TimerTask （本文最后有定时器例子）
```

**！！！！！！！！邮箱发邮件案例！！！**

## 三.过滤器Filter 

### 1．filter的简介

​	filter是对客户端访问资源的过滤，符合条件放行，不符合条件不放行，并且可以对目  标资源访问前后进行逻辑处理。

### 2．快速入门

步骤：

1）编写一个过滤器的类实现Filter接口

2）实现接口中尚未实现的方法(着重实现doFilter方法)

3）在web.xml中进行配置(主要是配置要对哪些资源进行过滤)

### 3．Filter的API详解

#### (1)filter生命周期及其与生命周期相关的方法

Filter接口有三个方法，并且这个三个都是与Filter的生命相关的方法

init(Filterconfig)：代表filter对象初始化方法 filter对象创建时执行

doFilter(ServletRequest,ServletResponse,FilterCha)：代表filter执行过滤的核心方法，如果某资源在已经被配置到这个filter进行过滤的话，那么每次访问这个资源都会执行doFilter方法

destory()：代表是filter销毁方法 当filter对象销毁时执行该方法

Filter对象的生命周期：

Filter何时创建：服务器启动时就创建该filter对象

Filter何时销毁：服务器关闭时filter销毁

#### (2)Filter的AP详解

1）init(FilterConfig)

​	其中参数config代表 该Filter对象的配置信息的对象，内部封装是该filter的配置信息。

![img](file:///C:\Users\student\AppData\Local\Temp\msohtmlclip1\01\clip_image002.jpg)    

2）destory()方法

filter对象销毁时执行

3）doFilter方法

doFilter(ServletRequest,ServletResponse,FilterChain)

其中的参数：

ServletRequest/ServletResponse：每次在执行doFilter方法时 web容器负责创建一个request和一个response对象作为doFilter的参数传递进来。该request个该response就是在访问目标资源的service方法时的request和response。

FilterChain：过滤器链对象，通过该对象的doFilter方法可以放行该请求



|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](file:///C:\Users\student\AppData\Local\Temp\msohtmlclip1\01\clip_image004.jpg) |

### 4．Filter的配置



|      |                                                              |
| ---- | ------------------------------------------------------------ |
|      | ![img](file:///C:\Users\student\AppData\Local\Temp\msohtmlclip1\01\clip_image005.png) |

url-pattern配置时

1）完全匹配  /sertvle1

2）目录匹配  /aaa/bbb/* ----最多的

/user/*：访问前台的资源进入此过滤器

/admin/*：访问后台的资源时执行此过滤器

3）扩展名匹配  *.abc  *.jsp



Filter的作用？

1）公共代码的提取

2）可以对request和response中的方法进行增强(装饰者模式/动态代理)

3）进行权限控制

**！！！！！day24--过滤器.pdf（自动登录，乱码处理）！！！！**

## 四.末尾 .Java定时器Timer简述

### **1.概述**

​	主要用于Java线程里指定时间或周期运行任务。Timer是线程安全的，但不提供实时性(real-time)保证。 

### **2.构造器**

```
Timer()
默认构造函数。

Timer(boolean)
指定关联线程是否作为daemon线程。

Timer(String)
指定关联线程的名称。

Timer(String, boolean)
指定关联线程的名称，同时指定关联线程的名称和是否作为daemon。
```

### **3.静态的schdule方法（重要）**

```
schedule(TimerTask task, long delay)
以当前时间为基准，延迟指定的毫秒后执行一次TimerTask任务。

schedule(TimerTask task, Date time)
在指定的日期执行一次TimerTask任务，如果日期time早于当前时间，则立刻执行。

schedule(TimerTask task, long delay, long period)
以当前时间为基准，延迟指定的毫秒后，再按指定的时间间隔地无限次数的执行TimerTask任务。

schedule(TimerTask task, Date firstTime, long period)
在指定的日期之后，按指定的时间间隔地无限次数的执行TimerTask任务。如果日期firstTime早于当前时间，则立刻执行，且不执行在时间差内的任务。（以下是例子）
```

```
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
	Date date = null；
    try {
       String dateStr = "2016-12-27 14:36:00";
       Date date = sdf.parse(dateStr);
       Timer timer = new Timer();
       timer.schedule(new TimerTask(){
       	@Override
       	public void run(){
            //执行相关实现代码
            syso.....
       		}
       }, date, 3000);
         } catch (ParseException e) {
           e.printStackTrace();
            }
        }
    }
```

## 五.末尾 .Session 的钝化与活化

### (一)钝化

​	当服务器正常关闭时,还存活着的session(在设置时间内没有销毁) 会随着服务器的关闭被以文件(“SESSIONS.ser”)的形式存储在tomcat 的work 目录下,这个过程叫做Session 的钝化。

### (二)活化

​	当服务器再次正常开启时,服务器会找到之前的“SESSIONS.ser” 文件，从中恢复之前保存起来的Session 对象，这个过程叫做Session的活化。

### (三)注意事项

      1）想要随着Session 被钝化、活化的对象它的类必须实现Serializable 接口，还有要注意的是只有在服务器正常关闭的条件下，还未超时的Session 才会被钝化成文件。当Session 超时、调用invalidate 方法或者服务器在非正常情况下关闭时，Session 都不会被钝化，因此也就不存在活化。 
      2）在被钝化成“SESSIONS.ser” 文件时，不会因为超过Session 过期时间而消失，这个文件会一直存在，等到下一次服务器开启时消失。 
      3）当多个Session 被钝化时，这些被钝化的Session 都被保存在一个文件中，并不会为每个Session 都建立一个文件。

### (四)演示

​	定义两个Servlet ，在一个Servlet 中将“username” 保存在Session 中，另一个Servlet 中获取到该“username”，输出到浏览器。


    public class SessionServlet1 extends HttpServlet {
        private static final long serialVersionUID = 1L;
        
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        //将username = zhangsan 保存在Session 中
        request.getSession().setAttribute("username", "zhangsan");
    }
    
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        doGet(request, response);
    }




    public class SessionServlet2 extends HttpServlet {
        private static final long serialVersionUID = 1L;
        
    protected void doGet(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        //获取到username 输出到浏览器
        String username = (String) request.getSession().getAttribute("username");
        response.getWriter().write(username);
    }
    
    protected void doPost(HttpServletRequest request, HttpServletResponse response) 
            throws ServletException, IOException {
        doGet(request, response);
    }


①：首先访问“session1”，将数据存储到Session 域中，接着再访问“session2”，可以获取到“username”的值，如下图 


②：在Session 时间还未过期的时间内，让服务器正常关闭，在Tomcat 中的work 目录下，会多出一个“SESSIONS.ser” 文件，里面存储着还未过期的Session 信息，这也就说明Session 被钝化了，以文件的形式保存在本地磁盘中。 


③：当服务器再次启动时，该配置文件会消失，当我们再次访问“session2”(不再访问“session1”的前提下)，发现仍然可以获取到“username”的值，这时Session 就从被钝化的文件中活化。 

④：当服务器非正常情况下关闭时(超时、调用invalidate 方法)，Session 不会被钝化，所以在服务器再次启动时，去访问“session2”，会因为没有获取到Session 报空指针异常(下面的演示是服务器非正常情况下关闭)。 

注：由于String 类本身已经实现了序列化接口“java.io.Serializable”，因此会被钝化成文件。如果我们想要自己定义的“POJO”类（也就是普通的java对象，javabean）也可以钝化与活化，那么也必须要实现“java.io.Serializable”接口。这也是为什么我们建议将“POJO”类都实现序列化接口的一个原因。

### （五）session的活化与钝化就是当用户访问时网站异常，不能丢掉session，所有也必须采用文件存储；和之前那个统计网站访问量一样的原理。

 class Person implementsHttpSessionActivationListener,Serializable

HttpSessionActivationListener   实现此接口的JavaBean,可以感知自己被活化(从硬盘到内存)和钝化(从内存到硬盘)的过程。如果需要同时保存Session中的JavaBean则JavaBean也要实现Serializable序列化接口。实现此接口的JavaBean与HttpSessionBindingListener一样，不必配置到web.xml中。




	public class Person implements HttpSessionActivationListener,Serializable{
		private static final long serialVersionUID = 1L;
		private String name;
		public Person(String name) {
			this.name = name;
		}
		@Override
		public void sessionWillPassivate(HttpSessionEvent se) {
			System.out.println("一个Person对象保存到硬盘了...");
		
	@Override
	public void sessionDidActivate(HttpSessionEvent se) {
		System.out.println("一个Person对象从硬盘读取出来了...");
	}
	@Override
	public String toString() {
		return "Person [name=" + name + "]";
	}
​	还需要配置文件。配置文件可以写在tomcat的目录里面conf---》server.xml里面配置，但是里面配置会修改平台，所以不建议使用。

​	**（关键一步）**另一种方法：我们在conf----》Catalina------》localhost------》自定义一个xml文件，里面内容：

```
<Context path="/sessionActivation" docBase="E:/MyEclipse10_workspace/sessionActivation/WebRoot">
  <Manager className="org.apache.catalina.session.PersistentManager"
    saveOnRestart="true" maxActiveSessions="1">
     <Store className="org.apache.catalina.session.FileStore" directory="d:/a">
     </Store>
  </Manager>
</Context>
<!--  以下是对上面的解释    -->
<!-- path为项目的目录   docbase 为myeclipse目录
	className   不能变 
	saveOnRestart ="true"  能够进行存储
	maxActiveSessions="n"  n为最大的session数量
-->
```













