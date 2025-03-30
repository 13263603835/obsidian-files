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
- ``