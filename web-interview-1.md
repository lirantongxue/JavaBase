title: "Java研发复习笔记(五)---web基础"
date: 2015-10-07 07:13:00
author: "郑江龙"
description: 多线程部分知识总结
categories: interview
tags:
    - Java
    - interview
---

## JSP 有哪些内置对象和动作？它们的作用分别是什么？
JSP 共有以下 9 种基本内置组件：
1、**request**对象：客户端的请求信息被封装在request对象中，通过它才能了解到客户的需求，然后做出响应。
2、**response**对象：response对象包含了响应客户请求的有关信息。
3、**session**对象：session对象指的是客户端与服务器的一次会话，从客户连到服务器的一个WebApplication开始，直到客户端与服务器断开连接为止。
4、**out**对象：是向客户端输出内容常用的对象
5、**page**对象：page对象就是指向**当前JSP页面**本身，有点象类中的this指针。
6、**application**对象：application对象实现了用户间数据的共享，可存放**全局变量**。它**开始于服务器的启动，直到服务器的关闭**，在此期间，此对象将一直存在；这样在用户的前后连接或不同用户之间的连接中，可以对此对象的同一属性进行操作；在任何地方对此对象属性的操作，都将影响到其他用户对此的访问。服务器的启动和关闭决定了application对象的生命。
7、**exception**对象：exception对象是一个**例外**对象，当一个页面**在运行过程中发生了例外，就产生这个对象**。如果一个JSP页面要应用此对象，就必须把isErrorPage设为true，否则无法编译。
8、**pageContext**对象：pageContext对象提供了对**JSP页面内所有的对象及名字空间**的访问，也就是说他可以访问到本页所在的**SESSION**，也可以取本页面所在的application的某一属性值，他相当于页面中所有功能的集大成者，它的本 类名也叫pageContext。
9、**config**对象：config对象是在一个Servlet初始化时，JSP引擎向它传递信息用的，此信息包括Servlet初始化时所要用到的参数（通过属性名和属性值构成）以及服务器的有关信息（通过传递一个ServletContext对象）。 

## 描述 JSP 和 Servlet 的区别、共同点、各自应用的范围
答：JSP 在本质上就是 SERVLET,但是两者的创建方式不一样.Servlet 完全是 JAVA 程序代码构成，擅长于流程控制和事务处理，通过 Servlet来生成动态网页很不直观.JSP 由 HTML 代码和 JSP 标签构成，可以方便地编写动态网页.因此在实际应用中采用 Servlet 来控制业务流程，而采用 JSP 来生成动态网页.


## JSP有什么优点
下面列出了使用JSP的优点：
(1)JSP页面是被动态编译成Servlet的，因此，开发者可以很容易的更新展现代码。
(2)JSP页面可以被预编译。
(3)JSP页面可以很容易的和静态模板结合，包括：HTML或者XML，也可以很容易的和产生动态内容的代码结合起来。
(4)开发者可以提供让页面设计者以类XML格式来访问的自定义的JSP标签库。
(5)开发者可以在组件层做逻辑上的改变，而不需要编辑单独使用了应用层逻辑的页面。



## servlet生命周期
servlet的声明周期周期是由servlet的容器来控制,它可以分为3个阶段:初始化,运行和销毁.
**初始化阶段**
初始化阶段主要完成以下任务:
1) servlet容器加载servlet类,把servlet类的.class文件中的数据读到内存中.
2) servlet容器创建一个ServletConfig对象,ServletConfig对象包含了servlet的初始化配置信息.
3) servlet容器创建一个servlet对象
4) servlet容器调用servlet的init方法进行初始化.
**运行阶段**
    当servlet容器收到一个请求时,servlet容器会针对这个请求创建servletRequest和servletResponse对象,然后调用service方法.并把这两个参数传递给service方法.service方法通过servletRequest对象获得请求的信息,并处理该请求. 再通过servletResponse对象生成这个请求的相应结果.然后销毁servletRequest和servletResponse对象. 不管这个请求时post还是get提交的,最终这个请求都会由service方法来处理.
**销毁阶段**
    当web应用被终止时,servlet容器会调用servlet对象的destroy方法,然后销毁servlet对象. 同时也会销毁servlet对象相关联的servletConfig对象. 我们可以通过destroy方法释放servlet占用的资源.

## Servlet链(Servlet Chaining)
Servlet链是把一个Servlet的输出发送给另一个Servlet的方法。第二个Servlet的输出可以发送给第三个Servlet，依次类推。链条上最后一个Servlet负责把响应发送给客户端。

## session和cookie有什么区别
cookie是Web服务器发送给浏览器的一块信息。浏览器会在本地文件中给每一个Web服务器存储cookie。以后浏览器在给特定的Web服务器发请求的时候，同时会发送所有为该服务器存储的cookie。下面列出了session和cookie的区别：
    无论客户端浏览器做怎么样的设置，session都应该能正常工作。客户端可以选择禁用cookie，但是，session仍然是能够工作的，因为客户端无法禁用服务端的session。
    在存储的数据量方面session和cookies也是不一样的。session能够存储任意的Java对象，cookie只能存储String类型的对象。

## 如果cookie禁用了呢
Cookie与 Session，一般认为是两个独立的东西，Session采用的是在服务器端保持状态的方案，而Cookie采用的是在客户端保持状态的方案。但为什么禁用Cookie就不能得到Session呢？因为Session是用Session ID来确定当前对话所对应的服务器Session，而Session ID是通过Cookie来传递的，禁用Cookie相当于失去了Session ID，也就得不到Session了。

## XML 包括哪些解释技术，区别是什么
XML 文档定义有几种形式:XML 文档定义分为 DTD 和 Schema 两种形式；其本质区别在于 Schema 本身也是一个 XML 文件，可以被 XML 解析器解析。
1、Schema本身也是XML文档，DTD定义跟XML没有什么关系，Schema在理解和实际应用有很多的好处。
2、DTD文档的结构是“平铺型”的，如果定义复杂的XML文档，很难把握各元素之间的嵌套关系；Schema文档结构性强，各元素之间的嵌套关系非常直观。
3、DTD只能指定元素含有文本，不能定义元素文本的具体类型，如字符型、整型、日期型、自定义类型等。Schema在这方面比DTD强大。
4、Schema支持元素节点顺序的描述，DTD没有提供无序情况的描述，要定义无序必需穷举排列的所有情况。Schema可以利用xs:all来表示无序的情况。
5、对命名空间的支持。DTD无法利用XML的命名空间，Schema很好满足命名空间。并且，Schema还提供了include和import两种引用命名空间的方法。

对 XML 的解析主要有DOM（文档对象模型）、SAX、StAX（JDK 1.6中引入的新的解析 XML 的方式，Streaming API for XML） 等，
**DOM(文档对象模型)** 处理大型文件时其性能下降的非常厉害，这个问题是由 DOM 的**树结构**所造成的，这种结构占用的**内存较多**，而且 DOM **必须在解析文件之前把整个文档装入内存**,适合对 XML 的随机访问（典型的用空间换取时间的策略）；
**SAX(Simple API for XML)** 是**事件驱动型**的 XML 解析方式，**它顺序读取 XML 文件，不需要一次全部装载整个文件**。当遇到像文件开头，文档结束，或者标签开头与标签结束时，它会触发一个事件，用户通过在其回调事件中写入处理代码来处理 XML 文件，适合对 XML 的顺序访问；
**StAX(Streaming API for XML)**把重点放在**流**上。实际上，StAX与其他方法的区别就在于应用程序能够把XML作为**一个事件流**来处理。将XML作为一组事件来处理的想法并不新颖（事实上 SAX 已经提出来了），但不同之处在于 StAX 允许应用程序代码把这些事件逐个拉出来，而不用提供在解析器方便时从解析器中接收事件的处理程序。

## JSON与XML的区别比较
**XML**扩展标记语言 (Extensible Markup Language, XML) ，用于标记电子文件使其具有结构性的标记语言，可以用来标记数据、定义数据类型，是一种允许用户对自己的标记语言进行定义的源语言。 
(1).XML的优缺点
<1>.XML的优点
　　A.格式统一，符合标准；
　　B.容易与其他系统进行远程交互，数据共享比较方便。
<2>.XML的缺点
　　A.XML文件庞大，文件格式复杂，传输占带宽；
　　B.服务器端和客户端都需要花费大量代码来解析XML，导致服务器端和客户端代码变得异常复杂且不易维护；
　　C.客户端不同浏览器之间解析XML的方式不一致，需要重复编写很多代码；
　　D.服务器端和客户端解析XML花费较多的资源和时间。
**JSON**(JavaScript Object Notation)一种轻量级的数据交换格式，具有良好的可读和便于快速编写的特性。可在不同平台之间进行数据交换。
<1>.JSON的优点：
　　A.数据格式比较简单，易于读写，格式都是压缩的，占用带宽小；
　　B.易于解析，客户端JavaScript可以简单的通过eval()进行JSON数据的读取；
　　C.支持多种语言，包括ActionScript, C, C#, ColdFusion, Java, JavaScript, Perl, PHP, Python, Ruby等服务器端语言，便于服务器端的解析；
　　D.在PHP世界，已经有PHP-JSON和JSON-PHP出现了，偏于PHP序列化后的程序直接调用，PHP服务器端的对象、数组等能直接生成JSON格式，便于客户端的访问提取；
　　E.因为JSON格式能直接为服务器端代码使用，大大简化了服务器端和客户端的代码开发量，且完成任务不变，并且易于维护。
<2>.JSON的缺点
　　A.没有XML格式这么推广的深入人心和喜用广泛，没有XML那么通用性；
　　B.JSON格式目前在Web Service中推广还属于初级阶段。

## XML和JSON的优缺点对比:
相同:两者都具有很强的可读性性,可扩展性,具有丰富编码和解码工具,且都用于描述结构化数据和传输数据.
区别:
1. XML已经被业界广泛的使用，而JSON才刚刚开始，但是在Ajax这个特定的领域，未来的发展一定是XML让位于JSON
2. XML的解析得考虑子节点父节点使得JSON的解析难度小于XML
3. JSON相对于XML来讲，数据的体积小，传递的速度更快些。
4. JSON对数据的描述性比XML较差。

## 在进行数据库编程时，连接池有什么作用？
答：由于创建连接和释放连接都有很大的开销（尤其是数据库服务器不在本地时，每次建立连接都需要进行 TCP 的三次握手，再加上网络延迟，造成的开销是不可忽视的），为了提升系统访问数据库的性能，可以事先创建若干连接置于连接池中，需要时直接从连接池获取，使用结束时归还连接池而不必关闭连接，从而避免频繁创建和释放连接所造成的开销，这是典型的用空间换取时间的策略（浪费了空间存储连接，但节省了创建和释放连接的时间）。池化技术在 Java 开发中是很常见的，在使用线程时创建线程池的道理与此相同。基于 Java 的开源数据库连接池主要有： C3P0、Proxool、DBCP、BoneCP、Druid等。

【补充】在计算机系统中时间和空间是不可调和的矛盾，理解这一点对设计满足性能要求的算法是至关重要的。大型网站性能优化的一个关键就是使用缓存，而缓存跟上面讲的连接池道理非常类似，也是使用空间换时间的策略。可以将热点数据置于缓存中，当用户查询这些数据时可以直接从缓存中得到，这无论如何也快过去数据库中查询。当然，缓存的置换策略等也会对系统性能产生重要影响，对于这个问题的讨论已经超出了这里要阐述的范围。

## Statement和PreparedStatement的区别
Statement 对象用于将 SQL 语句发送到数据库中。实际上有三种 Statement 对象，它们都作为在给定连接上执行 SQL语句的包容器：Statement、PreparedStatement（它从 Statement 继承而来）和CallableStatement（它从 PreparedStatement 继承而来）。它们都专用于发送特定类型的 SQL 语句：Statement 对象用于执行不带参数的简单 SQL 语句；PreparedStatement 对象用于执行带或不带参数的预编译 SQL 语句；CallableStatement 对象用于执行对数据库已存储过程的调用。

**Statement**每次执行sql语句，数据库**都要执行sql语句的编译**，最好用于仅执行一次查询并返回结果的情形，效率高于PreparedStatement.但存在sql注入风险。
**PreparedStatement**: 数据库会对sql语句进行**预编译**，下次执行相同的sql语句时，数据库端不会再进行预编译了，而直接用数据库的缓冲区，提高数据访问的效率（但尽量采用使用？号的方式传递参数），如果sql语句只执行一次，以后不再复用。从安全性上来看，PreparedStatement是通过?来传递参数的，避免了拼sql而出现sql注入的问题，所以安全性较好。在开发中，推荐使用 PreparedStatement.

## 什么是 DAO 模式
答：DAO（DataAccess Object）顾名思义是一个为数据库或其他持久化机制提供了抽象接口的对象，在不暴露数据库实现细节的前提下提供了各种数据操作。为了建立一个健壮的 Java EE 应用，应该将所有对数据源的访问操作进行抽象化后封装在一个公共 API 中。
用程序设计语言来说，就是建立一个接口，接口中定义了此应用程序中将会用到的所有事务方法。在这个应用程序中，当需要和数据源进行交互的时候则使用这个接口，DAO 模式实际上包含了两个模式，一是 Data Accessor（数据访问器），二是 Data Object（数据对象），前者要解决如何访问数据的问题，而后者要解决的是如何用对象封装数据。

## 什么是ORM
答：对象关系映射（Object-Relational Mapping，简称 ORM）是一种为了解决程序的面向对象模型与数据库的关系模型互不匹配问题的技术；简单的说，ORM 是通过使用描述对象和数据库之间映射的元数据（可以用 XML 或者是注解），将 Java 程序中的对象自动持久化到关系数据库中或者将关系数据库表中的行转换成 Java 对象，其本质上就是将数据从一种形式转换到另外一种形式。

## 事务的 ACID 是指什么？
1)原子性(Atomic)：事务中各项操作，要么全做要么全不做，任何一项操作的失败都会导致整个事务的失败；
2)一致性(Consistent)：事务结束后系统状态是一致的；
3)隔离性(Isolated)：并发执行的事务彼此无法看到对方的中间状态；
4)持久性(Durable)：事务完成后所做的改动都会被持久化，即使发生灾难性的失败。通过日志和同步备份可以在故障发生后重建数据。

## 网站访问量巨大、如何提高效率

## servlet 重定向和转发
1、转发：有两种方式获得转发对象(RequestDispatcher)：一种是通过HttpServletRequest的getRequestDispatcher()方法获得，一种是通过ServletContext的getRequestDispatcher()方法获得；

	request.getRequestDispatcher (“demo.jsp"). forward(request, response);//转发到demo.jsp

详解：假设浏览器访问servlet1，而servlet1想让servlet2为客户端服务。此时servlet1调用forward（）方法，将请求转发给servlet2。但是调用forward()方法，对于浏览器来说是透明的，浏览器并不知道为其服务的Servlet已经换成Servlet2，它只知道发出了一个请求，获得了一个响应。浏览器URL的地址栏不变。
2、重定向：HttpServletResponse的sendRedirect()方法。
 服务器根据此请求寻找资源并发送给客户，它可以重定向到任意URL，不能共享request范围内的数据。例如:response.sendRedirect(“demo.jsp");//重定向到demo.jsp
详解：假设浏览器访问servlet1，而servlet1想让servlet2为客户端服务。此时servlet1调用sendRedirect()方法，将客户端的请求重新定向到Servlet2。接着浏览器访问servlet2，servlet2对客户端请求作出反应。浏览器URL的地址栏改变。
3）sendRedirect()方法不但可以在位于同一个主机上的不同Web应用程序之间进行重定向，而且可以将客户端重定向到其他服务器上的Web应用程序资源。而forward（）方法只能将请求转发给同一Web应用的组件。

