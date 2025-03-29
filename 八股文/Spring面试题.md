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
	- 调用`beanpo