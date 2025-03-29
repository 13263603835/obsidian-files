## 什么是Spring框架
> Spring是一款轻量级Java开发框架，可以提高开发人员效率及系统的可维护性

Spring支持IOC控制反转和AOP面向切面编程。

## 包含那些组件
Spring-core
Spring-context
Spring-Beans
Spring-jdbc

## SpringIOC

控制反转，是一种设计思想，期望将原本在程序中手动创建的的对象交给spring框架来管理。
- 控制：指对象的创建的权力
- 反转：控制权交给Spring框架

## @Component和@Bean的区别是什么
- @Component 作用于类，@Bean作用于方法
- @Component 通过类路径扫描来自动装配到容器中，@Bean通常是我们标有该注解的方法，产生这个bean，@Bean告诉spring这个是某个类的实例。
## @Autowired和@Resource的区别
- @Autowired 是spring内置注解，默认使用`byType`classtype进行匹配，有限根据接口类型取匹配实现类，当一个接口有多个实现类时，注入方式会编程 `byName`，
	- 多个实现类时，可以通过`@Qualifier`这个注解来显示指定使用哪个
- @Resource 默认注入方式`byName`，如果无法匹配到对应的bean则会改为`byType`
	- 通过@Resource(name='className')来进行匹配，并不需要搭配其他注解进行匹配
- @Autowired 支持在构造函数，方法，字段和参数上使用，@Resource仅支持用于字段和方法上注入。
## 依赖注入的方式
- 构造函数
- setter
- field
## 使用构造函数注入，还是setter注入
>官方推荐使用构造函数注入
 
- 依赖完整性：确保所有对象在创建时就被注入，避免了空指针异常
- 不可变性：有助于创建不可变对象
- 初始化保证
## Bean的作用域
- singleton：默认单例
- prototype：每次获取时都会创建一个新的实例
- request：仅web应用可用，每个http请求产生一个新的bean，生命周期是这个http请求
- session：仅web应用可用，每个新session的http请求会产生一个新bean，生命周期是这个session
- application：仅web可用，每个应用启动时创建一个bean，生命周期是当前应用启动的时间
- websocket：仅web可用，每个websocket绘画产生一个新的bean

## Bean是线程安全的吗
- singleton作用域下，单例模式的bean对象应该是无状态的，否则多线程情况下会存在资源竞争问题
- prototype作用域下，每次获取都会创建新的，不存在线程安全问题
**确保单例情况下bean的线程安全的方法**
- 避免使用可变成员变量
- 使用threadlocal
- 使用同步机制

## bean的生命周期
- **创建bean的实例**：bean容器根据配置信息，利用反射来创建bean的实例，此时bean中的属性都是空值
- **bean属性的赋值**：根据@autowired、@value等注解给相关属性赋值
- **bean的初始化**：
	- 如果bean实现了一些以`Aware`结尾的接口，会调用相关的方法进行操作
	- 调用`beanpostprocessor`的`postProcessBeforeInitialization()`方法进行处理
	- 调用`beanpostprocessor`的`postProcessAfterInitialization()`方法进行处理
- **销毁bean**
	- 如果实现了`disposableBean`接口执行`destory()` 
## Spring AOP
面向切面编程，将与业务无关的，缺为所有业务模块共同调用的逻辑封装起来，减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

基于动态代理来实现的
- 接口的代理，使用JDK Proxy
- class的代理使用CGLIB

**名词**
- 目标target：需要被代理的类
- 代理 proxy：需要被代理的类生成的类
- 连接点 joinpoint：目标对象所属类中，定义的所有方法均为连接点
- 切入点 pointcut：被拦截的连接点
- 通知 adivce：增强的代码逻辑，就是拦截后要进行的动作
- 切面：切入点+通知
- 织入：将通知应用到目标对象，生成代理类的过程


## Spring AOP 和AspectJ AOP的区别

| 特性   | Spring AOP | AspectJ AOP |
| ---- | ---------- | ----------- |
| 增强方式 | 运行时增强      | 编译时、类加载时    |
| 切入方式 | 方法         | 方法、字段、构造器   |
| 性能   | 切面较多时，性能低  | 性能搞         |
| 复杂性  | 简单易用       | 功能强大，相对复杂   |

## Spring AOP的通知类型
- before：方法执行前
- after：方法执行后
- after returning：返回之前
- after throwing：抛异常之后
- around：所有场景都会调用
## 多个切面如何控制顺序
- 使用`@Order`注解，值越小，优先级越高
- 实现`Ordered`接口，重写`getOrder`方法

## Spring MVC
将业务逻辑、数据、显示进行分离

## Spring MVC核心组件
- `DispatcherServlet`：核心的中央处理器，负责接收请求、分发、并给予客户端响应
- `HandlerMapping`：处理器映射，根据URL匹配相关处理的`Handler`，
- `HandlerAdapter`：处理器适配，根据`HandlerMapping`找到的`Handler`适配 执行 `Handler` 
- `Handler`：请求的处理器，处理实际请求
- `ViewResolver`：视图解析器，根据返回的视图数据，解析并渲染真正的视图。
## Spring的循环依赖
循环依赖是指bean的对象互相持有对方的引用。
Srping使用三级缓存来解决该问题
- 一级缓存：存放已经完成初始化的bean
- 二级缓存：存放过度的bean，没有初始化完全的bean放在该缓存中，完成后从这里删除
- 三级缓存：存放ObjectFactory，可以生成原始bean的对象或者代理对象。三级缓存只对单例生效
**创建bean的流程**
- 先从一级缓存中获取，存在就直接返回
- 不存在从二级缓存中获取
- 如果还没有获取到，从三级缓存中获取，如果获取成功放入二级缓存，并删除三级缓存中数据


## Spring事务
### 事务的管理方式
- 编程式事务：在代码中手动管理事务，
- 声明式事务：通过AOP实现的，使用@Transactional注解来进行事务的管理
### 事务的传播机制
>为了解决业务层方法之间的互相调用问题

**类型**
- `TransactionDefinition.PROPAGATION_REQUIRED`：如果存在事务就加入，如果不存在，创建一个新事物
- `TransactionDefinition.PROPAGATION_REQUIRES_NEW`：如果当前存在一个事务，将事务挂起，重新创建一个新的事务
- `TransactionDefinition.PROPAGATION_NESTED`：如果当前存在事务，则创建一个事务当作当前事务嵌套事务，如果没有事务，创建一个新事物
- `TransactionDefinition.PROPAGATION_MANDATORY`：如果存在事务jia'ru