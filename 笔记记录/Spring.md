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

### 三、SpringAOP  ###
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
1. 核心组件



## 四、Spring事务管理 ##
1. **Spring事务有哪几种传播行为(7中。)**
	1. TransactionDefinition.PROPAGATION_REQUIRED：如果当前存在事务则加入该事务，如果没有事务则创建。
	2. TransactionDefinition.PROPAGATION_REQUIRES_NEW：创建一个新的事务。如果当前存在事务，则把当前事务挂起。
	3. TransactionDefinition.PROPAGATION_NESTED：创建一个新的事物。如果当前存在事务，则嵌套到当前事务来运行。（父子任务回滚）
	4. TransactionDefinition.PROPAGATION_MANDATORY：如果当前有事务的，则加入打当前事务，如果没有的，则抛出异常。
	
 
	5. TransactionDefinition.PROPAGATION_SUPPORTS：如果当前有事务的，则加入该事务，如果没有的则非事务方式运行。
	6. TransactionDefinition.PROPAGATION_NOT_SUPPORTED：非事务方式运行。当前存在事务的就挂起。
	7. TransactionDefinition.PROPAGATION_NEVER：非事务方式运行。如果当前存在事务的，则抛出异常。
2. 事务隔离级别
	1.  Default:使用数据库默认的隔离几倍
	2.  读未提交
	3.  读已提交
	4.  可重复度
	5.  线性化
# Spring中的事件驱动是什么？？ #
1. 

## Spring中拦截器、过滤器、监听器的区别 ##
1. 拦截器
2. 过滤器
3. 监听器



## SpringBoot知识 ##
1. SpringBoot的启动流程
	1. 
2. Spring集成mybatis和