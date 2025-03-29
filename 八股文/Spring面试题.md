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