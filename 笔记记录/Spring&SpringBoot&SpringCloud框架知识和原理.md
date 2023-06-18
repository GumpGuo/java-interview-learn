# 一、Spring的生命周期 #
1. bean实例化
2. benn对象的属性注入 
3. bean的初始化过程 
	1. 查看Aware接口。实现
	2. 查看是否有PreBeanPostProcessor
	3. 检查InitializingBean以决定屌用afterProperties方法
	4. 检查是否配置有自定以的init-method方法。
	5. BeanPostProcessor方法。
4. bean的销毁
	1. 是实现了DisableBean接口。执行destory方法
	2. 是否配置了destory-method属性。执行指定的方法。


## 二、SpringBean对象的三级缓存 ##
1. 一级缓存：实例对象的Map。
2. 二级缓存：实例化未初始化完成的实力对象。
3. 三级缓存：对象工厂的缓存。

## 三、SpringAOP  ##
1. 概念
	1. 目标：被通知的对象
	2. 代理：向目标对象应用通知之后创建的对象
	3. 连接点：目标对象的所属类中，所有的方法都是连接点
	4. 切入点：被切面拦截/增加的连接点
	5. 通知：增加的逻辑/代码。
	6. 切面：切入点+通知。
	7. 织入：将通知应用到目标对象，生成代理对象的过程动作。
2. 通知的类型有拿下
	1. Before
	2. After
	3. AfterReturning
	4. AfterThrowing
	5. Around

## SpringIOC和AOP概念 ##
1. IOC：控制反转。 
	1. 控制对象的工作交由容器管理。
	2. 只需要配置对象信息即可。 @Auto  @Resource @Respos  @Beab @Service @Controller
2. AOP：面向切面的编程思想。  通过代理实现


## 三、SpringMVC ##
1. 什么是SpringMVC
	1. 什么是MVC Model View Controller 模型-视图-控制器。是一种将业务逻辑处理、数据和显示分离的方法。
		- Model是用来处理应用数据逻辑的部分。通常模型对象负责在数据库中读取数据。
		- View（视图）：是应用程序中处理数据显示的部分。通常视图是一句模型数据创建的。
		- Controller ：处理用户交互的部。通常控制器负责从视图读取数据，控制用户输入，并向模型发送数据
	2. SpringMVC：就是在Spring Container Core和AOP 等技术的基础上，遵循MVC规范推出的Web框架。目的是简化Web开发。
		
1. 核心组件：DispatcherServlet 、HandMapping、HandlerAdapter、ModelAndView
2. 请求的流程
	1. 浏览器发送请求。DispatchServlet接受到请求。
	2. DispatchServlet->HandlerMapping .HandlerMapping会把请求映射为HandlerExecutionChain对象（包含一个Handler处理器、多个HandlerInterceptor拦截器。
	3. DispatchServlet-> HandlerAdapter 会把处理器包装成适配器。从而支持多种类型的处理器，即适配器设计模式的应用。
	4. HandlerAdapter->处理器功能处理方法的调用，HandlerAdapter将会根据适配的结果调用真正的处理器的功能处 理方法，完成功能处理；并返回一个ModelAndView 对象（包含模型数据、逻辑视图名）；
	5. ModelAndView的逻辑视图名 -> ViewResolver, ViewResolver将把逻辑视图名解析为具体的View。
	6. View -> 渲染， View根据传来的Model模型数据进行渲染，
	7. 返回控制权限给DispatchServlet。由DispatchServlet返回响应给用户。


## 四、Spring事务管理 ##
1. **Spring事务有哪几种传播行为(7中。)**
	1. TransactionDefinition.PROPAGATION_REQUIRED：如果当前存在事务则加入该事务，如果没有事务则创建。
	2. TransactionDefinition.PROPAGATION_ REQUIRES_NEW：创建一个新的事务。如果当前存在事务，则把当前事务挂起。
	3. TransactionDefinition.PROPAGATION_NESTED：创建一个新的事物。如果当前存在事务，则嵌套到当前事务来运行。（父子任务回滚）
	4. TransactionDefinition.PROPAGATION_MANDATORY：如果当前有事务的，则加入打当前事务，如果没有的，则抛出异常。
	5. TransactionDefinition.PROPAGATION_SUPPORTS：如果当前有事务的，则加入该事务，如果没有的则非事务方式运行。
	6. TransactionDefinition.PROPAGATION_ NOT_SUPPORTED：非事务方式运行。当前存在事务的就挂起。
	7. TransactionDefinition.PROPAGATION_NEVER：非事务方式运行。如果当前存在事务的，则抛出异常。

2. 事务隔离级别
	1.  Default:使用数据库默认的隔离级别
	2.  读未提交
	3.  读已提交
	4.  可重复度
	5.  线性化
# Spring中的事件驱动（观察者模式）是什么？？ #
1. 首先理解观察者模式是什么？
	1. 观察者模式：是一种对象行为模式。表示一种对象和另一种对象之间具有依赖关系，当一个对象发生改变时，依赖这个对象的所有对象也会做出反应。
	2. Spring的事件驱动是观察者模式的经典应用。
	3. Spring事件驱动的三种角色：
		1. 事件角色：ApplicationEvent充当事件角色，集成了EventObject并实现了Seriablizable接口。	
			1. Spring默认存在以下事件，都是ApplicationEvent的实现（集成自ApplicationEvent类）
				1. ContextStartedEvent:ApplicationContext启动后触发的事件
				2. ContextStoppedEvent：ApplicationContest停止后触发的事件
				3. ContentRefreshedEvent：ApplicationContext初始化或者刷新完后触发的事件
				4. ContentClosedEvent：ApplicationContext关闭后触发的事件。
		2. 事件监听者角色：ApplicationListener充当了事件监听者角色。定义了一个onApplicationEvent()方法了处理ApplicationEvent。    @EventListener + @Component\@Service  或者实现ApplicationListener方法。
		3. 事件监发布者角色：ApplicationEventPublisher充当了事件发布者，他也是一个接口。定义了publishEvent方法。
	4. 事件驱动的好处
		1. 没有耦合的关联。事件发布者不需要预先知道订阅这的存在。
		2. 异步消息传递，业务逻辑可以同时发生。
		3. 多对多的交互，发布订阅模式。
    5. 

## Spring中拦截器、过滤器、监听器的区别 ##
1. 拦截器Interceptor：拦截器是依赖于IOC容器的，在实现上面基于Java动态代理实现。是AOP的一种表现。
	1. 实现HandlerInterceptor接口
	2. 通过WebMvcConfig配置，注册过滤器。
2. 过滤器：过滤器是Servert容器层面的，在实现上基于函数回调，可以对几乎所有的请求进行过滤。过滤器是对数据进行过滤，预处理的过程。
	1. Spring实现过滤器的方式
		1. 无路径、无顺序@Component。直接实现Filter。并使用Component。
		2. 有路径无顺序 @WebFilter+@ServletComponentScan:通过实现Filter接口，并增加@WebFilter注解。需要在启动类中家@ServletComponentScan注解（扫描@WebFilter,@WebServlet，@WebListener并注入bean）。
		3. 有顺序有路径
			1. @Configuration+@Bean+ FilterRegisterationBean实现。
3. 监听器：监听器也是Servlet层面的，可以用于监听Web应用中的某些对象，信息的创建、销毁和修改等动作发生，然后做出相应的响应处理。
	# 1. 监听器分为三类 #
	1.  ServletContext，实现接口ServletContextListener。
	2.  HttpSession，实现接口HttpSessionListener。
	3.  ServletRequest，实现接口ServletRequestListener。
	4.  实现方式和Filter一样。一种是只加@Compoonent 。另一种是@WebListener和@ServletComponentScan一起使用。
### 拦截器、监听器、过滤器的不同点 ###
1. 实现方式不同。
	1. 拦截器：代理模式。
	2. 过滤器：回调函数。
	3. 监听器：事件。
2. 依赖不同：过滤器和监听器都是在WebServlet容器中。 过滤器是在SpringIOC容器中。
3. 使用场景不同<br/>
   （1）过滤器：设置字符编码、URL级别的权限访问控制、过滤敏感词汇、压缩响应信息等、身份验证、资源过滤<br/>
   （2）拦截器：拦截未登入、审计日志等<br/>
   （3）监听器：统计在线人数、清除过期Session<br/>
4. 触发的时机不一样：过滤器实在请求进入容器之后，但是在servlet之前进行预处理的，请求结束返回也是，实在servlet处理完后，返回给前端之前。 而拦截器实在



## SpringBoot知识 ##
## 1. SpringBoot的启动流程 ## 
[https://blog.csdn.net/BASK2311/article/details/129703129#:~:text=SpringBoot%E7%9A%84%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B%201%201.%20%E5%8A%A0%E8%BD%BD%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E4%B8%8E%E5%90%AF%E5%8A%A8%E7%B1%BB%202%202.%20%E5%88%9D%E5%A7%8B%E5%8C%96Spring%E5%AE%B9%E5%99%A8%203,3.%20%E5%BC%80%E5%90%AF%E8%87%AA%E5%8A%A8%E9%85%8D%E7%BD%AE%E5%8A%9F%E8%83%BD%204%204.%20%E5%90%AF%E5%8A%A8%E5%86%85%E5%B5%8C%E7%9A%84Web%E6%9C%8D%E5%8A%A1%E5%99%A8%205%205.%20%E5%90%AF%E5%8A%A8SpringBoot%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F](https://blog.csdn.net/BASK2311/article/details/129703129#:~:text=SpringBoot%E7%9A%84%E5%90%AF%E5%8A%A8%E6%B5%81%E7%A8%8B%201%201.%20%E5%8A%A0%E8%BD%BD%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E4%B8%8E%E5%90%AF%E5%8A%A8%E7%B1%BB%202%202.%20%E5%88%9D%E5%A7%8B%E5%8C%96Spring%E5%AE%B9%E5%99%A8%203,3.%20%E5%BC%80%E5%90%AF%E8%87%AA%E5%8A%A8%E9%85%8D%E7%BD%AE%E5%8A%9F%E8%83%BD%204%204.%20%E5%90%AF%E5%8A%A8%E5%86%85%E5%B5%8C%E7%9A%84Web%E6%9C%8D%E5%8A%A1%E5%99%A8%205%205.%20%E5%90%AF%E5%8A%A8SpringBoot%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F "参考地址")
	1. 执行构造函数。 
	2. 加载配置文件与启动类：当Springboot项目启动时，会首先读取项目中的配置文件，主要是application.yaml和application.properties文件。这些文件会指定启动端口号、数据库连接等一系列配置信息。同时，SpringBoot也会加载启动类，这个类中有@SpringBootApplication注解。
	2. 初始化Spring容器：包括初始化bean实例，依赖注入等
	3. 开启自动配置功能：扫描项目中的类，自动注册bean。
	4. 启动内嵌的Web服务器，启动内嵌的Web服务器，比如Tomcat或者Jetty，在Web服务器上部署应用
	5. 启动Springboot应用程序

1. 执行构造函数
	1. 构造函数完成的四件事情
		1. 推断当前项目是否是web项目
		2. 加载所有的初始化器
		3. 加载所有的监视器
		4. 设置程序运行的主类
2. 运行Run方法
	1. 计时器实例并启动
	2. 初始化监听器
	3. 启动监听器
	4. 装配环境参数
	5. 打印banner图案
	6. 上下文区域
	7. 准备上下文异常报告
	8. 上下文前置处理器
		1. 该方法做了三件之。environment环境设置、initialize初始化设置、资源获取并加载以及配置监听
	9. 上下文刷新。
	10. 上下文后置处理器
	11. 发布应用上下文启动
	12. 执行Runner执行器
	13. 发布应用上下文就绪并返回
3. 

## 2. SpringBoot集成mybatis和数据库的流程 ##
1. 首先引入依赖jar包。
2. 配置数据库连接信息
3. 新建Dao类
4. 新建Mapper接口
5. 新建Mapper.xml文件
6. 新建Service接口和实现类
7. 新建Controller类。测试数据

## SpringBoot的自动装配原理 ##
1. @SpringBootApplication注解可以看作是三个注解的集合
	1. @EnableAutoConfiguration：启用SpringBoot的自动配置机制 @Import ( AutoConfigurationImportSelector.class)
	2. @SpringBootConfiguration：根据Javadoc可知，该注解作用就是将当前的类作为一个JavaConfig，然后触发注解@EnableAutoConfiguration和@ComponentScan的处理，本质上与@Configuration注解没有区别
	3. @ComponentScan：扫描被@Component（@Service ,@Controller）注解的bean，注解默认会扫描启动类所在的包下所有类，可以自定义不扫描某些bean
	4. AutoConfigurationImportSelector加载自动装配类，实现了ImportSelector类，完成了以下工作
		1.  判断自动装配器开关是否打来。
		2.  获取EnableAutoConfiguration注解中的exclude和excludeName。
		3.  获取需要自动配置的所有配置类，读取 META-INFO/spring.factories，读取对应的EnableAutoConfiguration.class类名对应的值
		4.  根据类上的注解判断，若条件满足，则该配置类生效。