## 1、什么是Mybatis ##
### 优点 ###
1.	mybatis是一个半ORM（对象关系映射）框架，底层封装了JDBC，开发时只需要关心SQL本身，而不需要进行驱动加载、数据库连接、创建statement等操作。
2.	可以通过XML文件配置或者注解方式配置映射原生关系，可以将POJO配置成数据库中的记录，避免了JDBC代码，手动配置参数，获取结果集等。

### 缺点 ###
1. SQL语句的编写量大
2. SQL以来于数据库。导致代码移植性差。不能随意切换数据库。


## 2、ORM是什么 ##
1. ORM（Object Relation Mapping）:对象关系映射，是一种为了解决关系型数据库数据与简单Java对象（POJO）的映射关系的技术。简单来说，ORM是通过使用描述对象和数据库之间映射的元数据，将程序中的对象自动持久化到关系型数据库中。


## 3、为什么说Mybatis是半自动ORM映射工具？它与全自动的区别在哪里？ ##
 - Hibernate属于全自动ORM映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。
 - 而Mybatis在查询关联对象或关联集合对象时，需要手动编写SQL来完成，所以，被称之为半自动ORM映射工具。

## 4、Hibernate和Mybatis有什么区别 ##
### 相同点 ###
 - 都是对JDBC的封装，都是持久层的框架
### 不同点
1. 映射关系不同
2. SQL优化和移植性
	1. Hibernate。对SQL语句进行了封装，还提供了日志、缓存、关联等功能。提供了HQL语句操作数据库。数据库无关性好，但是会消耗性能。 SQL语句优化较难
	2. MyBatis 需要手动编写 SQL，支持动态 SQL、处理列表、动态生成表名、支持存储过程。开发工作量相对大些。直接使用SQL语句操作数据库，不支持数据库无关性，但sql语句优化容易。
3. 使用场景不同
	1.	Hibernate 是标准的ORM框架，SQL编写量较少，但不够灵活，适合于需求相对稳定，中小型的软件项目，比如：办公自动化系统
	2.	MyBatis 是半ORM框架，需要编写较多SQL，但是比较灵活，适合于需求变化频繁，快速迭代的项目，比如：电商网站


## 5、Mybatis的使用过程、生命周期 ##
1. 建SQLSessionFactory
2. 创建SQLSession
3. 查找Mapper
4. 执行SQL语句 /提交事务
5. 关闭Session

## 6、在Mapper如何传入多个参数 ##
 - 顺序传参法
 - @Param注解传参法
 - Map传参法
 - javabean传参法 

## 7、实体类属性明和表字段名不一样怎么办 ##
 - SQL语句定义字段的别名和实体类一致
 - ResultMap来映射字段名和实体类属性名一一对应。

## 8、Mybatis是否可以映射Enum类 ##
 - 可以，不但可以映射枚举类。可以映射任何对象到。通过自定义一个TypeHandler。 setParamter()方法和 getResult()方法
 - TypeHandler有两个作用。一个是完成JDBCType到JavaType的转换，另外一个是JavaType到JdbcType的转换。体现为setParameter()和getResult()两个方法，分别代表设置sql问号占位符参数和获取列查询结果。

## 9、\#{} 和${} 有什么区别##
- \#{} 是占位符，会进行预编译处理，${}是拼接符，字符串替换。没有预编译处理。
- \#{}传入参数是以字符串传入，会将 SQL中的#{}替换为？号，调用PreparedStatement的set方法来赋值。
- \#{}可以有效的防止SQL注入，提高系统安全行，${}不能防止SQL注入。
- \#{}变量替换实在DBMS中。${}变量替换实在DBMS外。

## 10、Mybatis能执行一对一和一对多的关联查询吗？ ##
- 支持。不止支持一对一、一对多的关联查询。还支持多对多、多对一的关联插叙。
- 一对一 association 
- 一对多 collection

## 11、Mybatis是否支持延迟加载？原理？ ##
- 支持。Mybatis支持association关联对象和collection关联集合对象的延迟加载。在mybatis配置中，可以配置是否延迟加载啊。lazyLoadingEnabled=true|false.
- 他的原理是，使用Cglib创建目标对象的代理对象。当调用目标方时。进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发下a.getB()是null值，那么就会单独发送事先保存号的查询关联b对象的SQL。把b查询上来。然后调用a.setB(b)，那么a的对象属性b就优质。

## 12、如何获取生成的主键？ ##
- 新增标签中添加keyProperty="ID"即可。

## 13、Mybatis支持动态SQL吗？ ##
 - Mybatis有一些支持动态语言的标签。通过OGNL从SQL参数对象中计算表达式的值，根据表达式的值动态拼接SQL，以此来完成动态SQL语句的功能。
 - if where foreach set  
 - choose(when otherwise)

## 14、Mybatis如何执行批量操作 ##
1. foreach标签。 可以在SQL语句中迭代一个集合。
 - foreach标签的属性 
	 - item
	 - index
	 - open
	 - close
	 - separator
	 - collection
 - collection。该属性值是必须指定的。但是不同情况下。该属性的值是不一样的。主要有以下三种情况
	 - 1、如果传入的是单个参数且参数类型是一个List的时候，collection属性值为list
	 - 2、如果传入的是单个参数且参数类型是一个array数组的时候，collection的属性值为array。
	 - 3、如果传入的十多个参数的时候。 我们就需要把他封装成一个map了。map的key是参数名。这个时候collection属性值就是传入的list或者array对象在自己封装的map里面的key
   
2. 使用ExecutorType.BATCH

## 15、说说Mybatis的一级缓存和二级缓存 ##
1. 一级缓存：基于PerpetualCache的HashMap本地缓存，其存储作用域为SQLSession。各个SqlSession之间的缓存相互隔离，当Session flush或close后，该SqlSession中的所有cache就将清空。mybatis默认打开一级缓存。
2. 二级缓存：二级缓存与一级缓存的机制相同。默认也是采用PerpetualCache。不同之处在于其存储的作用域为Mapper。可以在多个SQLSession之间共享。并且可以自定义存储源。默认不开启二级缓存。使用二级缓存属性需要实现Serializable序列化接口，可以在映射文件中配置。 

## 16、Mybatis工作原理  ##
 - 构建会话工厂
1. 读取Mybatis配置文件 --- mybatis-config.xml文件、加载映射问价按---映射文件机SQL映射文件。文件配置了操作数据库的SQL语句。最后生成一个配置对象。
2. 构造会话工厂：通过Mybatis的环境变量等配置信息构建会话工厂SqlSessionFactory。
3. 创建会话对象：有会话工程创建SqlSession对象，该对象中包含了执行SQL语句的所有方法。   	
 - 会话运行
4. Executor执行器：Mybatis底层定义了一个Executor接口来操作数据库，它将根据SqlSession传递的参数动态生成需要执行的SQL语句。同时负责查询缓存的维护。
5. StatementHandler：数据库会话器，串联起参数映射的处理和运行结果映射的处理
6. 参数处理（ParameterHandler(参数处理器)）：对输入参数的类型进行处理，并预编译
7. 结果处理（ResultSetHandler(结果处理器)）：对返回结果类型进行处理。根据对象映射规则。返回相应的对象。

## 17、Mybatis的功能架构是怎么样的？
 - 我们一般把mybatis的功能架构分为三层：API接口层、数据处理层、基础支撑层
   - API接口层：提供给外部使用的API接口，开发人员通过这些本地API来操作数据库
   - 数据处理层：负责具体的SQL查找、SQL解析、SQL执行和执行结果映射处理等。
   - 基础支撑层：负责最基础的功能支撑，包括连接管理、事务管理、配置加载和缓存处理。

## 18、为什么Mybatis的接口不需要实现？
 - Mybatis通过动态代理的方式生成具体的代理类。因此开发者本身不需要实现接口。
   - 获取Mapper的过程
     - Mapper =》MapperProxyFactory=》MapperProxy=》MapperMethod=》execute
     - 获取Mapper，首选需要获取MapperProxyFactory对象，通过该对象创建MapperProxy对象。通过执行MapperMethod执行SQL语句。

## 18、Mybatis都有哪些执行器？
 - 有三种基本执行器SimpleExecutor、ReuseExecutor、BatchExecutor
   - SimpleExecutor:每执行一次update或者select，就开启一个是Statement对象，用完立刻关闭
   - ReuseExecutor:执行Select或select，以SQL为key创建Statement对象，存在则使用，不存在则创建。用完后，不关闭Statement对象，而是放在Map<String,Statement>中，供下一次使用。
   - BatchExecutor:执行Update(没有Select)，将所有sql都添加到批处理中（addBatch），等待统一执行（executeBatch()），它缓存了多个statement对象，每个对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理。
 - 作用范围：Executor的这些特点，都严格限制在SqlSession生命周期范围内。
### 1、Mybatis如何指定使用哪种Executor？
 - mybatis配置文件中配置。也可以手动给DefaultSqlSessionFactory的创建SqlSession方法传递ExecutorType。

## 19、除了常用的 select、insert、update、delete标签。还有什么标签。
 - resultMap include sql if where choose set foreach collection association等。

## 20、Mybatis是如何进行分页的
(1) 使用RowBounds对象进行分页，他是针对ResultSet结果集进行的内存分页，而非物理分页。
(2) 使用物理分页的SQL语句实现物理分页功能。
(3) 使用分页插件进行分页。

## 21、Mybatis插件的原理是什么？
1. Mybatis通过 Executor【SQL执行器】、StatementHandler【Sql语法构建对象】、ParameterHandler【参数处理器】、ResultSetHandler【结果集处理器】四大扩展机制完成自定义插件实现。
 - Mybatis回话的运行需要 Executor 、StatementHandler、ParameterHandler、ResultHandler四大对象配合。插件的实现原理就是在这个四大想调用的时候，插入我们自己的代码。
 - Mybatis提供了Plugin类，实现了InvocationHandler接口，使用Plugin生成对象，代理对象在调用方法的时候就会进入invoke方法。在invoke方法中，如果存在签名的拦截方法，插件的intercept方法就会在这里被我们调用，然后就返回结果。如果不存在签名方法，那么将直接反射调用我们要执行的方法。
	- 所以自定义拦截器的过程
	 - 1、实现Interceptor接口
	 - 2、然后再给插件添加注解，确定要拦截的对象。要拦截的方法。
2. 分页插件的实现原理：
   - 根据mybatis提供的插件接口，实现自定义插件。拦截了Executor的query方法，在执行sql的时候，拦截执行的SQL，根据dialect方言，重写SQL。
	 - 分页插件过程
		- 1、首先判断是否需要分页
		- 2、判断是否需要count
		- 3、判断是否需要分页查询
		- 4、

## 22、Mapper接口为什么不需要实现？
 - Mapper都是通过动态代理方式生成的。不需要实现。
	- 获取Mapper的过程
 	

