# tomcat请求处理流程

## 1.1 请求流程

​	设计了这么多层次的容器, Tomcat是怎么确定每一个请求应该由哪个wrapper容 器里的servlet来处理的呢?答案是, Tomcat是用Mapper组件来完成这个任务的。

​	Mapper组件的功能就是将用户请求的URI定位到一个Servlet ,它的工作原理是: Mappe r组件里保存了Web应用的配置信息,其实就是容器组件与访问路径的映射关系,比如Host容器里配置的域名、Context容器里的web应用路径 ,以及wrapper容器里servlet映射的路径,你可以想象这些配置信息就是一一个多层次的Map.

​	当一个请求到来时, Mapper组件通过解析请求URL里的域名和路径,再到自己保存的Map里去查找,就能定位到一个servlet. 请你注意, -个请求URI最后只会定位到一-个wrapper容器,也就是一个servlet。

​	下面的示意图中， 就描述了 当用户请求链接http://www.itcast.cn/bbs/findAll之后，是如何找到最终处理业务逻辑的servlet。

![image-20200318181627140](..\images\tomcat-request-01.jpg)

## tomcat 处理请求流程

![image-20200318181857746](..\images\tomcat-request-02.jpg)

步骤如下：

1. NioEndpoint serverSock.accept(); 接受请求
2. NioEndpoint processKey(SelectionKey sk, NioSocketWrapper attachment) 处理请求
3. 请求交给 AbstractEndpoint processSocket(SocketWrapperBase<S> socketWrapper,SocketEvent event, boolean dispatch) 处理
4. 然后在该方法中执行 executor.execute(sc); 交给线程池处理请求，
5. 执行 SocketProcessorBase.run() 方法，在run方法中调用 NioPoint.doRun() 方法
6. doRun() 调用 ``` state = getHandler().process(socketWrapper, event); ` ``  调用 AbstractProtocol.process(SocketWrapperBase<S> wrapper, SocketEvent status) 方法，在该方法中state = processor.process(wrapper, status); 调用AbstractProcessorLight.process(SocketWrapperBase<?> socketWrapper, SocketEvent status) 方法读取消息报文，解析请求行，请求体，请求头，封装成Request方法。在该方法中执行 state = service(socketWrapper); 调用 Http11Processor 的service 方法，在该方法中调用 getAdapter().service(request, response); 方法，具体调用 CoyoteAdapter.service(org.apache.coyote.Request req, org.apache.coyote.Response res) 方法,然后调用 connector.getService().getContainer().getPipeline().getFirst().invoke(request, response); 方法，最后调用 StandardEngineValve.invoke(Request request, Response response) ,然后调用 StandardHostValve.invoke(Request request, Response response) 方法，然后调用 StandardHostValve.StandardHostValve.invoke(Request request, Response response) 方法,StandardWrapperValve.invoke(Request request, Response response) ,在该方法中调用 filterChain.doFilter(request.getRequest(), response.getResponse()); 方法，ApplicationFilterChain.doFilter(ServletRequest request, ServletResponse response), 在该方法中调用本类方法 internalDoFilter(request,response); 然后调用servlet.service(request, response); ， 随后进入 HttpServlet.service(ServletRequest req, ServletResponse res) 方法中将 ServletRequest, ServletResponse,转为 HttpServletRequest, HttpServletResponse,然后调用本类的 service(HttpServletRequest req, HttpServletResponse resp) 方法，然后判断请求方法(post,get)，然后分别调用该路径servlet 的 doGet,doPost 方法。



![image-20200318204618052](D:\githubWorkSpace\m2\study\笔记\images\tomcat-request-04.jpg)



## 1.2 请求流程源码解析

![image-20200318200356122](D:\githubWorkSpace\m2\study\笔记\images\tomcat-request-03.jpg)

​	在前面所讲解的Tomcat的整体架构中,我们发现T omcat中的各个组件各司其职,组件之间松耦合,确保了整体架构的可伸缩性和可拓展性,那么在组件内部,如何增强组件的灵活性和拓展性呢?在Tomcat中 ,每个container组件采用责任链模式来完成具体的请求处理。

​	在Tomcat中定义了Pipeline和Valve两个接口, Pipeline用于构建责任链，后者代表责任链上的每个处理器。Pipeline 中维护了一个基础的valve ,它始终位于pipeline的末端(最后执行) , 封装了具体的请求处理和输出响应的过程。当然,我们也可以调用addValve ()方法，为Pipeline 添加其他的valve，后添加的valve 位于基础的Valve之前 ,并按照添加顺序执行。Pipiline通过获得首个valve来启动整合链条的执行