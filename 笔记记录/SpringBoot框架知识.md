## 一、SpringBoot知识 ##
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

## 3. SpringBoot的自动装配原理 ##
1. @SpringBootApplication注解可以看作是三个注解的集合
    1. @EnableAutoConfiguration：启用SpringBoot的自动配置机制 @Import ( AutoConfigurationImportSelector.class)
    2. @SpringBootConfiguration：根据Javadoc可知，该注解作用就是将当前的类作为一个JavaConfig，然后触发注解@EnableAutoConfiguration和@ComponentScan的处理，本质上与@Configuration注解没有区别
    3. @ComponentScan：扫描被@Component（@Service ,@Controller）注解的bean，注解默认会扫描启动类所在的包下所有类，可以自定义不扫描某些bean
    4. AutoConfigurationImportSelector加载自动装配类，实现了ImportSelector类，完成了以下工作
        1.  判断自动装配器开关是否打来。
        2.  获取EnableAutoConfiguration注解中的exclude和excludeName。
        3.  获取需要自动配置的所有配置类，读取 META-INFO/spring.factories，读取对应的EnableAutoConfiguration.class类名对应的值
        4.  根据类上的注解判断，若条件满足，则该配置类生效。