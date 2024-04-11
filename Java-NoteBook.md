## Spring Framework

#### Spring框架概述

Spring是一款开源的轻量级Java开发框架，旨在提高开发人员的开发效率以及系统的可维护性。

其提供的最主要的核心功能就是IoC（Inversion of Control，控制反转）和AOP（Aspect Oriented Programming，面向切面编程）。

它有几个比较重要的模块：spring-core(IoC)、spring-bean、spring-jdbc、spring-aop、spring-webmvc

#### Spring、Spring MVC、Spring Boot的关系

Spring其实包含了很多的功能模块（上述已经提到了几个），而其中的spring-webmvc是其中比较重要的一个模块。

Spring MVC主要提供了快速构建MVC架构的web程序的能力

在Spring开发过程中手动配置过于繁琐，尤其是有时候需要编写复杂的xml文件，为了简化这一过程，Spring Boot诞生了。

> 要注意的是，Spring Boot仅仅是为了简化开发中繁琐的配置。
>
> 比如我们进行MVC开发时候还得使用Spring MVC，但是我们使用了Spring Boot简化了Spring MVC的配置过程。

#### Spring IoC

###### 概述

IoC（Inversion of Control ）即控制反转/反转控制，它是一种思想不是一个技术实现。

比如说：现在有一个类A，依赖于类B，那么就有两种常用的方法

* 自定义A的构造函数，在其中new一个实例B
* 使用IoC，在初始化A的时候注入类B的实例

`我们放弃了自己创建、管理对象的权力，将其交给Spring，但是我们同时也就不需要考虑创建、管理对象这一系列过程。`

**控制反转指的是：**

* 控制：管理、创建对象
* 反转：将权力交给外部框架，如Spring

IoC降低了对象之间的耦合度以及依赖程度，并且使得资源管理变得容易，只要将其加入了容器，你可以随时随地的注入。

###### IoC与DI

IoC（Inversion of Control）是一种设计思想或者说是某种模式，但是这设计思想在其他框架中也是使用了的。

`而IoC的较为普遍的、最合理的实现方式就是DI（Dependency Injection，依赖注入）`

#### Spring Bean

###### 如何声明Bean对象

Bean实际上就是指的被Spring IoC管理的那些对象，最基本的我们可以通过@Component注解来声明一个类为bean对象。

除此之外，在Spring MVC中由于分为了持久层、业务层、控制层，为了提高分辨率衍生了出了三个注解分别为@Repository、@Service、@Controller。

另外，对于通过方法声明的自定义bean，我们可以在方法上面加上@Bean注解来声明。

>@Bean与@Component有什么区别？
>
>1. @Bean作用与方法，而@Component作用与类
>2. 由于@Bean使用的方法，因此自定义性更强，比如定义RedisTemplate的序列化器

###### 如何注入Bean对象

一般来说，使用Spring提供的@Autowired或者JDK内置的@Resource注解

但是@Autowired注解是byType方式注入的，如果某个接口有多个实现类，那么就需要再加上一个@Qualifier注解，使用byName方式注入，而@Resource默认是byName方式。

```java
@Autowired
@Qualifier(value = "smsServiceImpl1")
private SmsService smsService;

@Resource(name = "smsServiceImpl1")
private SmsService smsService;
```

###### Bean是线程安全的吗

在IoC中，有两种常见的作用域，分别为prototype和singleton，这两种作用域下是不同的。

对于prototype来说，由于每次获取都会new一个新对象，因此是线程安全的

但是对于singleton来说，由于使用的是单例模式，因此如果该bean对象是`有状态的`，那么就不是线程安全的。

**有两种解决方法**

* 尽量不要定义有状态的bean对象
* 对于状态字段，可以使用ThreadLocal变量来保存

###### Bean的生命周期

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/image-20231207163100469.png" alt="image-20231207163100469" style="zoom: 50%;" />

* 执行构造函数实例化bean
* bean依赖注入
* 处理一系列Aware结尾的接口
* 执行bean的前置处理器
* 初始化方法（PostConstruct自定义初始化、内置的初始化）
* 执行bean的后置处理器，一般在这做AOP，动态代理
* 销毁bean

#### Spring AOP

###### 概述

AOP（Aspect-Oriented Programming:面向切面编程）能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度

> Spring AOP主要基于动态代理技术，对于有实现接口的类，会使用JDK proxy去创建代理对象，而对于没有实现接口的类会使用Cglib生成一个该类的子类作为代理对象。

###### 几个重要术语

* 横切关注点（cross-cutting concerns） ：多个类或对象中的公共行为（如日志记录、事务管理、权限控制等）。 
* 切面（Aspect）：对横切关注点进行封装的类，一个切面是一个类。切面可以定义多个通知，用来实现具体的功能。 
* 连接点（JoinPoint）：连接点是方法调用或者方法执行时的某个特定时刻（如方法调用、异常抛出等）。 
* 通知（Advice）：通知就是切面在某个连接点要执行的操作。通知有五种类型：Before、After、AfterReturning、AfterThrowing、Around。
* 切点（Pointcut）：一个切点是一个表达式，它用来匹配哪些连接点需要被切面所增强。
* 织入（Weaving）：织入是将切面和目标对象连接起来的过程，也就是将通知应用到切点匹配的连接点上。

###### 常见的实现方式

有两种：`Spring AOP 与 AspectJ AOP`

前者使用的是运行时增强（基于Dynamic Proxy），而后者使用的是编译时增强（基于字节码）。

#### Spring MVC

###### 概述

MVC是模型(Model)、视图(View)、控制器(Controller)的简写，其核心思想是通过将业务、数据、显示三者分离来组织代码

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401181452594.png" alt="image-20240117161209710" style="zoom: 33%;" />

`MVC更多的可以看看作是一种设计模式，而不是软件开发规范`

###### Spring MVC的核心组件

* DispatcherServlet：核心的中央处理器，负责接收请求、分发，并给予客户端响应。 
* HandlerMapping：处理器映射器，根据URL去匹配查找能处理的 Handler ，并会将请求涉及到的拦截器和 Handler 一起封装。 
* HandlerAdapter：处理器适配器，根据HandlerMapping 找到的Handler，调用对应的 Handler
* Handler：请求处理器，处理实际请求的处理器。 
* ViewResolver：视图解析器，根据 Handler 返回的逻辑视图 / 视图，解析并渲染真正的视图，并传递给 DispatcherServlet响应客户端

###### SpringMVC 工作原理

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401181451517.png" alt="image-20240117161537732" style="zoom: 33%;" />

* 用户发起请求，DispatcherServlet拦截到用户的请求
* DispatcherServlet根据URL路径，去HandlerMapping找到响应的Handler，并且会和拦截器一起返回一个调用链
* DispatcherServlet调用HandlerAdapter，让Adapter去调用Handler
* HandlerAdatpter调用Handler，然后返回ModelAndView对象
* DispatcherServlet收到后，会去调用ViewResolver去解析，返回一个View视图对象
* DispatcherServlet返回View视图给用户

#### Spring Transaction

在Spring中，一般使用@Transactional注解来开启事务（侵入性更小），但是要在配置类上加上@EnableTransactionManagement注解

###### Spring事务管理接口

Spring框架中，和事务有关的有三个类：

* PlatformTransactionManager：（平台）事务管理器，Spring 事务策略的核心。
* TransactionDefinition：事务定义信息(事务隔离级别、传播行为、超时、只读、 回滚规则)。
* TransactionStatus：事务运行状态

其中，PlatformTransactionManager是SPI的形式，它定义好了接口，由JDBC、Hibernate等自己去实现自己的事务管理器。在该接口中定义了三个方法：

```java
public interface PlatformTransactionManager {
    //获得事务
    TransactionStatus getTransaction(@Nullable TransactionDefinition var1) throws TransactionException;
    //提交事务
    void commit(TransactionStatus var1) throws TransactionException;
    //回滚事务
    void rollback(TransactionStatus var1) throws TransactionException;
}
```

###### 事务传播行为

当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。比如，到底是新开启一个事务，还是加入当前事务

在Spring中，定义了这么几种形式

1. TransactionDefinition.PROPAGATION_REQUIRED

   这是使用最多的，也是@Transaction注解默认的传播形式

   * 如果外部方法没有开启事务的话，Propagation.REQUIRED修饰的内部方法会新开启自己的事务
   * 如果外部方法开启事务并且被Propagation.REQUIRED的话，所有 Propagation.REQUIRED修饰的内部方法和外部方法均属于同一事务 ，只要一个方法回滚，整个事务均回滚

2. TransactionDefinition.PROPAGATION_REQUIRES_NEW

   创建一个新的事务，如果当前存在事务则把当前事务挂起，当前方法会新开启自己的事务

3. TransactionDefinition.PROPAGATION_NESTED

   与PROPAGATION_REQUIRES_NEW不同，PROPAGATION_NESTED的事务和它的父事务是相依的，它的提交要和它的父事务一起。也就是说，如果父事务最后回滚，它也要回滚。如果子事务回滚或提交，不会导致父事务回滚或提交，但父事务回滚将导致子事务回滚

###### 只读事务

如果你一次执行多条查询语句，为了防止多条语句之间有事务提交，`保证读一致性`，可以开启只读事务。

###### 事务失效场景

1. 异常捕获处理

   在事务中捕获异常没有抛出，而是在catch中自己处理了。由于事务的AOP中就无法捕获到异常，会直接提交事务。

2. 抛出检查异常

   如果在方法后面加上throw 异常，那么就不会抛出RuntimeException了，而spring的事务默认只会捕获运行时异常。

   `解决方法`：在@Transactional注解中指定捕获的异常类

3. 非public方法

   如果使用注解的方法不是public方法，那么也会失效。

4. 在Bean内部调用自己的事务方法

   由于在内部调用自己的方法，因此没有使用代理类，导致AOP失效。

   `解决方法`：获取代理类，比如在方法内部注入自己，用这个类调用方法。

#### Spring Boot

###### 什么是Spring Boot Starters

`Spring Boot Starters是一系列依赖关系的集合`。没有这个之前，在开发web应用的时候，需要自己导入MVC、Tomcat相关依赖，而且还得确定好版本防止冲突。但是有了Spring Boot Starters，只需要引入spring-boot-starter-web就好了，它下面的子依赖已经包含了所有的web需要的依赖，并且配置好了版本。

###### Spring Boot的自动配置原理

在Spring Boot启动类上有这样一个注解：@SpringBootApplication

实际上，这个注解里面包含了三个注解：

* @ComponentScan：扫描Bean，并加入IoC容器
* @Configuration：声明该类是一个配置类，可以在里面声明Bean，导入其他配置类
* @EnableAutoConfiguration：这个就是SpringBoot自动配置的注解，它通过@Import注解导入了一个AutoConfigurationImportSelector类，实现自动配置。

###### AutoConfigurationImportSelector工作流程

* 读取META-INF/spring.factories，获取需要自动装配的所有配置类
* 但并不是加载所有的自动配置类，它会通过@ConditionalOnxxx注解判断是否需要加载，满足条件后才会生效

> 想要相关配置生效必须引入spring-boot-starter-xxx包实现起步依赖

## MySQL

#### MySQL 字段

###### char和varchar

对于varchar来说，磁盘中只会存储实际长度的空间，但是char是声明了多大就会存储多大

对于varchar(10)和varchar(100)来说，虽然磁盘占用一样，但是实际读取到内存中的时候，varchar(100)还是会占用100个字节的空间

###### 不推荐使用TEXT和BLOB

这两种类型，在使用临时表的时候无法在内存中创建，只能在磁盘中创建，并且其检索效率较低

###### NULL和''

在MySQL中，NULL需要占用额外的空间，但是''占用空间是0

此外，在做条件判断的时候NULL只能使用IS NULL来判断，无法使用=、<这种比较运算符。

#### MySQL基础架构

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401181451253.png" alt="image-20240118144412169" style="zoom: 33%;" />

MySQL的基础架构大致可以分为两个部分：**Server层**和**存储引擎**

###### Server层基本组件

* 连接器：接收客户端连接，并校验用户身份和权限。注意的是，用户登录成功后，连接器会从权限系统表中查询出权限数据并保存，即使在该连接中用户修改了系统表，次用户连接的权限仍然是登录时候的权限。
* 查询缓存：会记录select语句以及结果，一旦执行的select语句命中并且权限校验通过，就直接返回结果
* 分析器：对sql语句进行词法分析（提取sql关键字）和语法分析（语句是否正确）
* 优化器：按照优化方案优化语句（可能优化的方式不是最优的），比如索引的选择等
* 执行器：校验权限正确后，调用存储引擎接口，返回数据

> MySQL 8.0 版本后删除了缓存的功能

###### Update语句执行过程

```mysql
update tb_student A set A.age='19' where A.name='张三';
```

* 查询满足条件的数据，如果有缓存会使用缓存
* 查询后，修改相应的记录并调用InnoDB接口
* 在InnoDB中，会先将语句以及修改的相关数据页保存在内存中，并通过两阶段写入日志的方法写入redo log和binlog，并选择合适的时机将脏数据写入磁盘（如果没有写入就宕机了，可以通过redo log恢复）

#### MySQL存储引擎

要注意的是，存储引擎是基于表的而不是数据库，也就是说对于每个表都可以定制自己的存储引擎

MySQL5.5之前，MyISAM是默认引擎，之后就是InnoDB了，二者区别如下：

* InnoDB支持行级锁，而MyISAM只有表级锁
* MyISAM不提供事务支持，InnoDB提供事务支持
* MyISAM不支持MVCC，而InnoDB支持
* InnoDB有redo log和undo log，因此可以支持故障恢复

#### MySQL索引

无论是在InnoDb还是MyISAM中，索引的结构使用的都是B+树的结构

索引虽然可以加快查询的速度，但是增加了更新操作的开销

###### 索引底层的数据结构（B+树）

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202404112111758.png" alt="image-20240118171104728" style="zoom:50%;" />

相比于B树来说：

* B+树只在叶子节点存放数据，每次查询必要搜索到底并且只在最后一次读取全部数据到内存，因此搜索效率更加稳定
* 在叶子节点使用链表的结构，因此更适用于范围查询

> 对于MyISAM，每个索引只存放key以及数据指针，不存放数据；而在InnoDB中，其主键索引存放数据。

###### 聚簇索引与非聚簇索引

聚簇索引指的是key与数据一起存放的索引，非聚簇索引只存放key和对于的指针

在InnoDB中，主键索引就是聚簇索引；InnoDB中的二级索引以及MyISAM中的所有索引都是非聚簇索引

* 聚簇索引查询的效率更高，减少了一次IO，但是需要更大的存储空间
* 非聚簇索引更轻量，但是可能会造成回表查询

###### 覆盖索引

一个索引包含所有要查询的字段值，就称之为覆盖索引。（这其实是一种situation，而不是一种type）

###### 联合索引

使用表中的多个字段创建索引，它遵循最左匹配原则

最左匹配原则的原因是：联合索引简历后，只有左边的值确定了，才会根据排序进一步筛选右边的字段，否则直接筛选右边的字段是无序的

就好比你要从一个排好序的字符串数组中找第三位是'c'的所有字符串，但是你不给前两位，直接找第三位其实是无序的需要全局遍历，如果给定前两位你可以直接二分查找。

###### 对于索引的使用建议

* 为合适的字段创建索引：比如不为NULL、频繁查询、频繁连接的字段
* 被频繁更新的字段应该慎重建立索引，因为添加索引后，会增加更新的开销
* 尽量使用联合索引，而不是单个索引

###### 索引失效场景

* 没有使用索引覆盖，导致查询后导致回表（比如 select *）
* 创建了组合索引，但查询条件未遵守最左匹配原则
* 以%开头的LIKE查询比如LIKE '%abc';

#### MySQL日志

MySQL中有三个较为重要的日志：redo log、undo log、binlog。其中redo log和undo log是InnoDB自己的

###### redo log

`redo log（重做日志）是InnoDB存储引擎独有的，它让MySQL拥有了崩溃恢复能力`

redo log写入磁盘的方式：

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401191956609.png" alt="image-20240119195629553" style="zoom: 33%;" />

redo log buffer -> page cache -> disk

但是刷盘的时机是可以设置的，有三种方式

* 事务提交不立即刷盘，而是每秒将buffer写入page cache，并调用fsync刷盘
* 事务提交立即写入page cache并调用fsync刷盘
* 事务提交立即写入page cache，但每隔一秒调用fsync刷盘

> page cache独立于MySQL，它是属于操作系统的部分

###### 为什么要用redo log，而不是每次更新直接刷数据盘

1. 数据页太大，一般为16KB，不能每次为了更新几十字节的数据就进行16KB的文件IO
2. 每次更新的相关数据不一定位于同一数据页，容易发生页的换进换出，而redo log属于追加方式的顺序IO，很少发生换进换出

###### binlog

redo log它是物理日志，记录内容是“在某个数据页上做了什么修改”，而binlog是逻辑日志，记录内容是语句的原始逻辑

binlog主要用于数据备份，主从同步等操作，保证数据一致性

binlog刷盘流程如下，事务已提交就写入page cache，但是用户可以自定义：每秒fsync还是wrtie完直接fsync

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401192017585.png" alt="image-20240119201717525" style="zoom:33%;" />

###### binlog为什么不能做崩溃恢复

binlog记录了所有的操作，而redo log只记录了数据修改后的结果

因此发生故障时，直接通过redo log覆盖数据就好，但是binlog是逻辑指令，会发生数据不一致

比如binlog记录了加十次1，恢复的时候不能简单基于当前结果加十次1，因为你不知道从哪一次开始发生故障的，而redo log记录了当前记录为8，直接覆盖就行

###### 两阶段提交

用于解决发生故障时，binlog和redo log不一致的问题

具体方法：

* 先写入redo log，并将该记录置为prepare状态
* 事务提交后，会写入binlog
* 写完binlog后，将redo log对应记录置为commit状态

若第二步失败，恢复时会发现redo log存在prepare项且binlog无对应项，`回滚`

若第三步失败，恢复时会发现redo log存在prepare项但binlog有对应项，`提交`

###### undo log

用于事务的回滚操作以及MVCC，所有事务进行的修改都会先记录到这个回滚日志中，然后再执行相关的操作

#### MySQL事务

###### ACID

关系型数据库事务满足ACID特性：Atomicity、Consistency、Isolation、Durability

> AID是手段，C是目的	

###### 并发事务带来的问题

* 脏读：由于其他事务的更新操作，当前事务读到了其他事务修改但还未提交的记录
* 丢失修改：两个事务同时读，并都基于当前值修改（如A=A-1），那么就会导致最终只修改了一次
* 不可重复读：在两次读之间，如果有事务的修改操作提交，会导致两次读的记录可能不一样
* 幻读：在两次读之间，如果有事务进行了插入操作，那么第二次读可能会莫名其妙多了一些数据

> 不可重复读主要针对于update、delete操作，可以直接通过加锁来解决
>
> 但是幻读针对于insert操作，你无法对原本不存在的数据加锁

###### 事务隔离级别

read-uncommitted、read-committed、repeatable-read、serializable

#### MVCC

MVCC的实现主要依靠三个部分实现：

* 隐藏字段：事务id、回滚指针(指向undo log中上一个版本记录的指针)
* 读视图
* undo log

具体实现流程如下：

* 根据当前事务的隔离级别，生成读视图（RC级别下，每次读都会生成；RR级别下，只有第一次读的时候会生成一次）
* 当需要读的时候会根据读视图的内容做以下判断：
  * 当前记录版本的事务id小于min_trx_id，那么说明该版本事务创建于读视图创建之前，直接使用
  * 当前记录版本的事务id大于max_trx_id，那么说明该版本事务创建于读视图创建之后，不能使用
  * 当前记录版本的事务id在二者之间，如果没有在读视图的活跃事务列表中（比如修改的是自己），那么可以使用，否则不能使用
* 一旦发现该版本不能使用，则通过上一个版本的指针判断undo log中的旧版本是否可用，直至遍历到尾指针则返回空

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401201101170.png" alt="image-20240120110139110" style="zoom:50%;" />

#### 一致性非锁定读和锁定读

对于RR下面的一致性非锁定读（普通select语句），可以使用MVCC机制解决不可重复读和幻读问题

但是对于锁定读：

* select ... lock in share mode
* select ... for update
* insert、update、delete

这种操作，不会使用MVCC乐观锁机制，会直接读取最新的版本，可以通过加S锁(共享锁，读锁)和X锁(排他锁，写锁)来保证可重复读，但无法解决幻读问题

解决幻读的方法是添加间隙锁，对于当前查询的范围添加锁，不允许插入满足查询条件的数据

#### Mybatis相关问题

###### Dao接口的工作原理

xml中开头就是namespace，这个是dao接口的全限定名

然后每个MapperStatement都有一个id这个就对应的Dao接口函数名

Dao接口没有实现类，调用的时候会通过接口类的全限定名加函数名构造唯一的key，找到相应的MapperStatment，执行相应语句

## Redis

#### Redis基础

###### Redis为什么这么快

* Redis是基于内存的，IO速度本来就很快
* Redis使用了IO多路复用技术
* Redis是单线程的，不需要考虑上下文切换，而且不用考虑多线程安全问题

###### 常见的缓存更新策略

* Cache Aside Pattern：先更新DB，再删除缓存（反过来不行，因为更新DB的时间更长，容易导致不一致），但是这样也会导致不一致，没办法，redis本来就不是用来保证强一致性的
* Read/Write Through Pattern：写的时候若有cache先写到缓存，然后由cache服务`同步`更新DB
* Write Behind Pattern：写的时候若cache存在，则先写到cache，然后由cache服务`异步`更新DB

#### Redis持久化

###### RDB持久化

会基于当前redis的状态，生成一个快照，对所有数据进行备份

由于这种方式比较耗时，可以通过bgsave指令来另外fork一个线程用于保存快照

###### AOF持久化

和mysql的binlog类似，会记录每次执行的指令，并记录到buffer中，然后buffer -> page cache -> aof file

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401201137280.png" alt="image-20240120113748242" style="zoom:50%;" />

每次写入一个指令到buffer中就会调用一次write，然后会根据不同策略调用fsync：

* 一条指令一个fsync
* 一秒一次fsync
* 由操作系统自己fsync，一般30秒一次

###### 为什么AOF先记录日志再执行指令

> 首先要注意的是，Redis的设计是为了满足系统的高可用性，真的要实现强一致会降低性能

* 避免写入日志前的语法检查
* 避免记录日志对当前指令执行的阻塞，但是会导致对后续指令的阻塞

#### 混合模式

开启混合模式后，会将RDB的文件加到AOF文件开头，大大减小AOF文件的大小

混合模式就是为了综合二者的优点：

* RDB文件只是redis的快照，文件大小更小，并且还原更快
* AOF的持久化速度更快，实时性更高

#### Redis线程模型

Redis的指令执行是单线程的，但是对于网络请求、持久化这方面是可以支持多线程的，为的是提高IO性能

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401201202823.png" alt="image-20240120120209740" style="zoom:50%;" />

#### Redis内存管理

###### 过期key的处理

redis使用了两个处理方式

* 惰性删除：当对该key进行查询操作的时候，检查该key是否过期，如果过期则直接删除
* 定期删除：redis每隔一段时间就会抽取`一部分的key`检查过期状态，**这个线程是主线程，会阻塞指令执行**

###### 内存淘汰机制

当redis内存满了过后再添加数据就会触发内存淘汰机制

1. noeviction：内存满了不允许写入
2. volatile-ttl：对设置了ttl的key，删除最快要过期的数据
3. allkeys-random：对全体key随机删除
4. volatile-random：对设置了ttl的key，随机删除
5. allkeys-lru：对所有的key，使用LRU算法    `这个是最常用的`
6. volatile-lru：对设置里ttl的key，使用LRU算法
7. allkeys-lfu：对所有的key，使用LFU算法
8. volatile-lfu：对设置里ttl的key，使用LFU算法

#### 事务、pipeline、Lua脚本

三者都可以批量执行多个语句，但是还是存在一些区别

* 事务：多个语句要分开做网络传递，但是批量执行期间不会被别的指令打断
* pipeline：多个语句合到一次，只做一次网络交互，但是指令可能会穿插执行
* Lua脚本：只做一次网络交互，并且不会被打断

> 这三个都无法实现**原子性和一致性**，无法支持出错回滚，一般使用Lua脚本来实现事务

#### Redis性能优化

* 使用批量操作减少网络传输：使用mset类似的指令，对于不同指令可以使用Lua脚本、pipeline
* 大量key集中过期问题：定期清理中，若发现大量过期key会导致阻塞时间过长，可以设置随机TTL
* bigkey问题：如果一个key大小过大，会导致网络IO阻塞、过期清理时间长，可以对其分割存储
* hotkey问题：如果一个key访问频率过高，同样也会影响网络IO，可以使用主从库、集群方式减轻压力，也可以使用本地缓存减少redis访问频率
* 慢查询：一般redis中根据key查找都是O(1)复杂度，但是如果使用keys *这种指令，就会导致时间复杂度为O(n)，导致慢查询，我们尽量要避免这种类似指令

#### Redis内存碎片

* 由于Redis为一个key申请空间的时候，一般都要比其占用空间要大一点，这就会导致内部碎片
* 同时当我们频繁的删除key的时候，其占用的内存并不会立刻free，这就会导致外部碎片

#### Redis生产问题

**缓存三兄弟**：缓存穿透、缓存雪崩、缓存击穿

###### 缓存穿透

访问大量DB中不存在的key，由于redis中没有，所有请求会直接转向DB

* 可以在redis中缓存该不存在的key，其value设置为一个约定的特殊字符，代表NULL
* 使用bloom filter，但是会产生误判，认为不存在的key存在，但是使用适当的bitmap长度，性能很高

###### 缓存雪崩

大量key在同一时间失效，或者redis服务直接宕机，那么所有请求都会转向DB

* 使用集群、sentinel
* 随机TTL

###### 缓存击穿

对于某个热点key，如果发生过期或者不在redis中，大量请求也同样会转向DB

* 缓存预热
* 使用互斥锁访问、逻辑过期
* 设置热点数据永不过期或者过期时间比较长

#### Redis集群-哨兵模式

哨兵模式（sentinel）用于实现主从集群的自动故障恢复

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/image-20231130174229378.png" alt="image-20231130174229378" style="zoom: 33%;" />

###### 作用

1. 监控master和slave是否在按照预期正常运作
2. 如果master故障，sentinel会从slave中选取一个作为新的master（故障转移）
3. 通知redis客户端主从发生变化，防止客户端违规的向原本的master写数据

###### 服务状态监控工作原理

基于心跳机制检测，一旦有一个sentinel发现ping不通某个node就会认为该node主观下线。

一旦有超过一定数量（自己配置）的sentinel都认为该node主观下线，则该node客观下线

如果认为客观下线的node是master则会通过以下方式选举新的master：

* 根据设定的优先级选择slave作为master
* 如果优先级一样，选offset最大的slave

> sentinel自己也是一个node，也就是说一个sentinel会ping所有的主从redis，也会ping其他的sentinel

###### 脑裂问题

由于网络原因，sentinel无法连接到master认为其下线从而选出新的master。但是客户端仍可以与原本的master连接，因此在主从切换的过程中，原本的matser仍然在接收写命令。

但是一旦网络恢复，原本的master会变为slave，他会清空原本写入的数据，和新的master做全量同步，这会导致在主从切换过程中写入的数据发生丢失。

可以通过设置两个参数来解决

* `min-replicas-to-write`：表示最少的从节点个数，如果不满足则不允许执行写操作
* `min-replicas-max-lag`：表示数据复制时的 ACK 消息延迟的最大时间。主库做同步时，如果没有在指定时间返回ACK，则拒绝写入数据。

#### Redis集群-分片集群

###### 架构

* 在分片集群中，有很多的master，每个master保存一部分数据

* 每个master必须至少有一个slave，但是并**不会实现读写分离，slave只是做数据备份方便故障转移**

* 各个master之间互相通过心跳机制监控，不需要哨兵机制

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401201351902.png" alt="image-20240120135042566" style="zoom:33%;" />

> 如果有一个master故障并且没有slave，那么整个集群都将不可用

###### 插槽

为了实现访问任何节点都可以得到同一个数据，redis使用了一种插槽的概念

插槽的总数是16384个，对于请求的key，会根据key做hash然后对16485取模

redis给每个master都分配了插槽，如果当前访问的节点发现算出来的结果不是自己的槽就会根据值路由到其他redis节点，否则从自己的内存中查出来返回。

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/image-20231130175653818.png" alt="image-20231130175653818" style="zoom: 33%;" />

###### Redis Cluster扩容缩容期间可以提供服务吗

如果进行重新分片，动态迁移哈希槽，那么可能会发生计算hash后来到指定节点，但是发现数据不存在的情况

为了保证cluster的可用性，有两个重定向包：

* MOVED：说明hash迁移完成，但是客户端没更新分配情况，因此会让客户端更新hash槽信息并返回真正存储该key的node
* ASK：说明还正在迁移，只会发送存储该key的node，但不会让客户端更新hash槽信息

#### Redis的扩容缩容原理

当redis的hash容量达到扩容或者缩容的阈值时，就会触发扩容缩容

此时会使用ht[1]，先申请一个足够的空间，然后将ht[0]的数据迁移过去

为保证可用性，并不是一次性迁移成功，而是每迁移一部分，就会停止，处理客户端请求

当迁移过程中，如果添加数据，就会直接插入ht[1]，查询数据会保留一个字段记录迁移到那个数据，在这个数据之前的访问ht[1]，之后的访问ht[0]

迁移完成后，ht[1]会变成ht[0]，同时释放ht[0]的空间

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401311148188.png" alt="image-20240131114846042" style="zoom: 33%;" />

## JVM

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401201447456.png" alt="image-20240120144703385" style="zoom:33%;" />

JVM的运行时数据区分为两大部分：线程共享和线程私有

其中线程共享的区域为：堆、字符串常量池、运行时常量池

线程私有的区域为：虚拟机栈、本地方法栈、程序计数器

#### 运行时区域

###### 程序计数器

**线程私有**，用于记录当前线程执行到的字节码位置，方便线程的上下文切换

###### 两个栈

**线程私有**，都是用来保存函数调用产生的局部变量，调用一次压一次，返回后自动弹出

但是虚拟机栈保存的Java方法的方法调用，而本地方法栈保存的是native方法的方法调用

> * 方法调用的返回分为两种形式，一种是return正常返回，一种是抛出异常
> * 在局部变量中，对于对象而言，只保存引用变量，真正的对象实例依然是存放在堆中

###### 堆

**线程共享**，几乎所有的对象实例都是在堆中创建的

堆其实又分为多个区域：新生代、老年代，其中新生代又分为Eden区和Survivor区

`要注意的是，虽说在JDK1.7之前还有个永久代，但是实际上永久代是方法区的一种实现，严格意义上说它并不属于堆的一部分`

此外，JDK1.8过后永久代已经被元空间取代，并转移至本地内存中

###### 方法区

方法区会存储已被虚拟机加载的类信息、字段信息、方法信息等数据

方法区可以看作是一个接口，JDK1.8之前其实现是永久代，之后的实现是元空间

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401201511592.png" alt="image-20240120151159547" style="zoom:33%;" />

> 1.7之前是永久代；1.7的时候将字符串常量池和静态方法移至堆中存放；1.7以后，剩下的永久代中的东西移至元空间

移至元空间的原因是永久代存在上限，无法动态调整，而本地内存取决于计算机的配置，不容易OOM

###### 运行时常量池

它是方法区的一部分，用于存放字面量和符号引用的，常量池表会在类加载后存放到运行时常量池中

其中比较重要的是字符串常量池，它在JDK1.7的时候从永久代移到了堆中（静态变量也是），它是为了String类型专门开辟的常量池，为了减少相同字符串的重复创建

###### 直接内存

直接内存比较特殊，它并不属于Java本身，是属于OS层面的，它方便了Java和操作系统之间的IO

一般来说，Java的IO依赖于native方法，这就会导致IO的数据会先从内核拷贝到native内存，再从native内存拷贝到Java内存

但是有了直接内存后，可以直接从内核拷贝到直接内存，然后Java直接读取直接内存，减少了一次数据拷贝

#### JVM对象

###### 创建

* 首先在方法区中查找该对象对应的类有没有加载，若没有加载执行类加载逻辑
* 类加载完成后，就可以在堆中为该对象分配指定空间了，分配空间的方法取决于GC方法。分为空闲列表（未整理）以及指针碰撞（整理）
* 分配完指定空间，就将该空间的所有数据全部置为0，这样可以实现默认初始化，比如int的默认值为0
* 设置对象头，比如类的元数据信息、对象的哈希码、GC年龄
* 最后才是执行构造函数

为了解决对象分配空间的并发问题，有两种方法

* CAS：失败重试
* TLAB：为每个线程分配一点独立Eden空间，不够再CAS

###### 组成

一个对象由三个部分组成：对象头、数据、对齐填充

###### 访问方式

一个引用变量访问对象实例有两种方式

* 句柄：引用变量指向句柄，句柄存放对象地址以及方法区中类信息地址
* 直接指针：引用变量直接指向实例，实例中存放方法区中类信息地址

直接指针的访问速度更快，但是如果对象需要移动位置要修改所有的相关引用变量，而句柄只需要修改自己的对象指针就好

#### 内存分配和回收原则

###### 基本工作流程

* 对象的创建基本都在Eden区，只有大对象的创建才会跳过Eden区，直接进入老年区，创建时年龄初始化为0

* 如果Eden区放不下了，就会触发一次GC，每次GC后所有存活的实例年龄+1，

  > 具体是何种GC方式取决于老年代的最大连续空闲空间是否大于等于所有新生代使用的空间（即就算所有新生代全部进入老年代也能放得下），如果大于等于就Minor GC；否则会Full GC
  >
  > 这其实就是**空间分配担保**

* 如果Eden区有年龄为1的对象，就会进入Survivor区

* Surivior区如果有达到年龄阈值的就进入老年代

  年龄的阈值有两个，一个是设置的固定阈值，一个是50%(50%也是设置的)空间所对应的年龄，二者取较小者为最后的阈值

###### GC分类

* 部分收集 (Partial GC)
  * 新生代收集（Minor GC）：只对新生代进行垃圾收集
  * 老年代收集（Major GC）：只对老年代进行垃圾收集。需要注意的是 Major GC 在有的语境中也用于指代整堆收集
  * 混合收集（Mixed GC）：对整个新生代和部分老年代进行垃圾收集。 
* 整堆收集 (Full GC)：收集整个Java堆和方法区

#### 死亡对象的判断方法

###### 引用计数法

每有一个引用指向该对象，该对象的引用就加1，个数为0的可以被认为是垃圾对象

但是如果出现对象之间的循环引用，那么引用计数法就无法检测出来了

###### 可达性分析算法

构造引用关系图，如果某个对象对任意一个GC root节点都不可达，那么就可以认为是垃圾

GC root可以是：虚拟机栈、本地方法栈中引用的对象、常量引用的对象、静态属性引用的对象

###### 如何回收方法区中的类

回收该类需要满足三个条件

1. 该类所有的实例已经被回收
2. 该类的ClassLoader已经被回收
3. 该类的.Class对象没有地方引用，也没有地方反射调用类的方法

#### 垃圾收集算法

* 标记清除法：标记出所有不需要回收的对象，然后统一回收没有被标记到的对象，

  会产生大量内存碎片

* 标记复制法：将原本的内存区域分为两块，每次清理的时候将存活的对象复制到另一块去

  这会导致可用内存空间减小，并且如果碰到每次GC数量较少的内存区域（如老年代），每次为了GC几个对象而复制一堆对象，有些得不偿失
  
* 标记整理法：每次回收后，会把对象往一端移动，然后直接清理另一端脏数据区域

  移动的效率不高，适合老年代这种回收频率低的区域

> 一般来说，新生代会使用标记复制，而老年代使用标记清除或者标记整理

#### 垃圾收集器

一共有四大类：serial、parallel、CMS、G1。其中serial和parallel都有两个版本分别针对新生代和老年代，称之为serial new + serial old以及parallel new + parallel old

其中new使用的是标记复制，old使用的是标记整理

对于serial和parallel，二者的区别在于回收的时候是单线程还是多线程，但是不管是不是多线程，回收的时候都会暂停程序的运行，即STW("stop the world")

在Java8及以前，JVM使用的就是parallel new + parallel old

而从Java9开始，就使用G1收集器了

###### CMS收集器

它使用的收集方法是标记清除算法

* 初始标记：`STW`，找到GC root以及其直接关联的对象
* 并发标记：和用户线程并发，查找与GC root间接关联的对象，并且记录该过程中引用的变化情况
* 重新标记：`STW`，根据引用的变化情况，重新检查相关对象的可达性
* 并发清理：和用户线程并发，清理垃圾

由于并发清理也可能会出现引用变化产生垃圾，因此会出现浮动垃圾的现象

此外使用标记清除法，会产生大量内存碎片

###### G1收集器

G1收集器不再使用传统的各个区之间物理隔离，而是将整个堆分成了多个region，每个region都可以是Eden、Survivor、old

每次GC的时候其实就是将region中存活的对象拷贝到另一个新的region中，因此G1可以看成是宏观上标记整理，微观上标记复制

当eden区满的时候会触发一次STW young GC

而当老年代达到一定阈值的时候，就会触发一次mix GC

G1无法在堆空间中申请新的分区时，G1便会触发担保机制，执行一次STW式的、单线程的 Full GC

#### 类文件结构

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401211323146.png" alt="image-20240121132314060" style="zoom: 25%;" />

* 前面四字节是固定的，称之为魔数："CAFEBABE"
* minor version和major version分别是Java的大小版本号
* 后面的结构基本都是统一的：xxx的个数 + xxx数组，比如常量池个数+常量池数组
* Constant Pool：常量池
* Access Flag是访问空值，比如public、volatile、final等
* This class和Super Class分别是当前类和父类的索引，这一项没有数组的原因是：Java中是单继承的，因此只会有一个父类
* Interface：接口信息；Field：字段信息；Method：方法信息
* Attribute：在上面三个的数组中其实包含了Access Flag、name、相关Attribute数组。除此之外，用户还可以自定义其他的属性，那么就会保存在这个地方

#### 类加载过程（要区别于对象创建）

<img src="https://raw.githubusercontent.com/ZenithWon/figure/master/202401211339833.png" alt="image-20240121133951788" style="zoom: 33%;" />

一个类的加载过程可以分为：加载->连接->初始化  三大部分

其中，连接又可以拆分为：验证->准备->解析 三个部分

###### 加载

类加载会通过类的全限定名获取二进制字节流，然后将其转换到方法区的运行时数据区中，并且会在内存中生成一个Class对象

类加载可能和连接阶段有部分重叠，比如验证中的字节码文件验证，也就是说类加载还没有完成，连接可能就开始了

###### 连接

连接分为三个步骤

* 验证：验证字节码文件、验证符号引用、验证语法语义等

* 准备：在准备阶段会给类分配内存空间并设置初始值

  这里分配的空间是类的静态变量，而且初始值全都设置为0，除非是static final修饰的字面量，才会在这里赋值

* 解析：将符号引用转化为方法区中的直接引用，比如方法符号引用转化为方法表中的偏移地址

###### 初始化

初始化我理解的就是Class类的初始化，这里就会给类变量真正的初始化了，而不是使用0的默认值

并且只有主动使用该类才会去初始化，否则执行到连接就会停止，直到使用该类

#### 类加载器

Java有三个默认的类加载器：BootstrapClassLoader、ExtensionClassLoader、AppClassLoader

除了BootStrapClassLoader是用C++实现的不用被加载以外，其他只要是类就要被加载（包括这两个类加载器）

加载的时候会使用双亲委派机制，防止重复加载某个类

## 并发编程

#### 进程与线程

进程是程序的一次执行过程，是系统运行程序的基本单位

一个进程可以拥有多个线程，在Java中多个线程共享进程的堆、方法区，但是拥有自己私有给的程序计数器、栈结构

在JDK1.2之前，Java的线程属于用户线程，之后采用的是内核线程，可以实现多核运行

在Java中，进程之间是独立的，不会互相影响，但是线程之间由于共享堆以及方法区，多个线程之间很有可能会互相影响

此外，在单核CPU下，使用多线程并不一定可以提高效率

#### volatile关键字

volatile可以保证变量的可见性，但是无法保证变量更新的过程中的原子性

因为他只会保证每次使用的时候会去主存中获取值，但是更新操作时先读后改再写回，这三步不是原子的

此外volatile可以禁用指令重排序，他不允许在他之前的写操作延迟执行，也不允许在他之后的读操作超前执行

#### 乐观锁和悲观锁

###### 乐观锁

假设没有人来竞争，会直接执行相关操作然后试图更新，发现被抢占了就会自旋

乐观锁的实现方式有版本号机制和CAS操作

对于CAS操作，它是OS提供的一个原子指令，compare和swap两个操作是原子的不会被打断。但是CAS有ABA问题，最终解决方式还是用到了版本号机制

###### 悲观锁

假设有人来竞争，每次需要资源的时候会先获得锁，否则不会执行相关操作

比如synchronized、Reentrantlock、RedissonLock等都是悲观锁

#### synchorized原理

被synchronized修饰的代码块，会借助OS提供的monitorenter、monitorexit两个指令来实现

monitorenter会获取一个monitor对象锁，并且锁的计数器加一

后面monitorexit的时候就会释放锁，计数器减一

但是被synchronized修饰的方法并不是靠这个，而是有一个方法的访问属性表明它是一同步方法，然后执行相应的方法调用

#### ThreadLocal

###### 原理

每个Thread类都有一个ThreadLocalMap类，这是属于线程的私有资源

它是一个hashMap，key是一个ThreadLocal对象，value就是该线程保存的值

当ThreadLocal需要set的时候，会通过当前线程获取当前线程的ThreadLocal，然后用this作为key，来set一个value

此外，他和ArrayList一样也是懒加载的

###### 内存泄漏问题

由于ThreadLocalMap中的key是弱引用，而value是强引用，当他的key没有外部强引用引用的时候就会被GC回收

此时value是强引用不会被回收，这就会导致内存泄漏

#### 线程池

当队列为空且核心线程池不满的时候，会直接使用核心线程池中的线程执行任务

然后，如果核心线程池加满了，但是队列还没有满，那么就放到队列中阻塞

如果队列也满了，那么就会开启非核心线程池，使用非核心线程执行

再满了，就会执行饱和策略了

此外，设置的存活时间是针对于非核心线程的，如果非核心线程在设置的存活时间中一直没有任务，就会被释放

#### AQS

AQS是一个抽象队列同步器，用于同步使用

他将共享资源抽象为state，然后使用CAS操作来更新state，保证原子性并且表达资源的占有情况

并且它使用一个双向队列，来保存阻塞线程

## 计算机网络

#### TCP/IP四层模型

四层模型分别为：应用层、传输层、网络层、网络接口层

* 应用层：主要提供两个终端之间的应用数据传输，这里的应用数据格式是异构的，不同的应用层协议都是不同的

* 传输层：为应用多种多样的协议提供统一的数据传输服务。

  > 从代码角度来说，只需要调用tcp.send(msg)，而不是每个应用层协议都需要写一个send，如http.send(msg)

* 网络层：传输层只负责src send to dst，但是具体怎么送到，走哪几个路由器，是网络层的任务

* 网络接口层：这里其实包含的是数据链路层和物理层，这负责的是每一段链路上数据的正确送达

分层模型可以大大提高系统的灵活度，让每一层的实现都是独立的，分解原本网络传输问题成多个小问题

#### HTTP

###### 从输入URL到页面展示的过程

浏览器会根据输入的URL通过DNS协议解析出IP地址

然后主机会使用自己的随机临时端口和服务主机的80端口连接，这其中会建立一个TCP连接

服务器接收到请求后会处理请求，并返回响应结果

浏览器接收到后会渲染页面

###### HTTP与HTTPS

* HTTP的端口号是80，HTTPS的端口号是443
* HTTP协议中传输的内容都是明文，没有加密，而HTTPS工作与SSL/TLS之上，传输的内容均被加密过

###### HTTPS的加密问题

HTTPS工作在SSL/TLS之上，传输的内容使用的是对称加密，但是对称加密的密钥使用的是数字证书的非对称加密
