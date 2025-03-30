## Spring boot有哪些优点
- 易上手，提升开发效率
- 开箱即用
- 配置，编码，监控变得更简单
## 核心注解
- `@SpringBootApplication`： 组合了`@Configuration`注解，实现了配置文件
- `@EnableAutoConfiguration`：自动装配功能

## Spring boot starter工作原理

Springboot在启动时会根据读取classpath下所有的`spring.factories`文件，该文件中配置了需要被spring容器创建的bean，并且进行自动装配将bean注入到springcontext中

## spring需要独立的容器运行吗
- 可以不需要，springboot内置了 tomcat和/jetty

## springboot热部署
- spring loaded
- spring boot devtools

## springboot使用事务
- 启动类中添加`enabletransactionmanagement`开启事务，就可以使用 transactional 注解了
## springboot中使用异步
- 启动类中添加`@enableasync`注解，公共方法中使用 `@async`注解即可实现异步方法

## 加载配置的方式
- `@PropertySource`：用于显示指定加载配置文件，必须配置@configuration
- `@value`：单个配置注入到字段中
- `@Enviroment`：代码中动态获取配置
- `@ConfigurationProperties`：将一组相关配置绑定到一个类型安全的javabean上

## 什么是java config
- 消除XML配置，利用Java方法进行bean的配置
- `@configuration`：在类型添加注解，表示该类是个配置类
- `@ComponentScan`：配置类添加该注解，默认扫描该类所在包下所有配置类
- `@Bean`：表示该方法返回的对象是bean

## SpringBoot的自动装配原理
- 在启动类上添加注解`@EnableAutoConfiguration`开启自动装配
- 从classpath所依赖的jar包中扫描 `META_INF/Spring.factories`
- 该文件中KV形式映射了需要加载的bean
## Springboot的配置加载顺序
- `properites`文件
- YAML文件
- 系统环境变量
- 命令行参数

## springboot核心配置文件是什么
- bootstrap：由`ApplicationContext`加载，比`application`优先加载，应用启动阶段就开始加载
- application：应用启动后进行加载

## spring profiles
- 可以利用`profiles`根据配置文件中设置 dev\test\prod等参数来注册bean，明确是生产环境还是测试环境