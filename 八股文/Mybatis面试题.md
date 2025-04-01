## mybatis是什么
- 是一个ORM框架，开发时不用关注如何加载驱动，创建链接等复杂过程仅用关注编写原生SQL即可

## mybatis的优缺点
**优点**
- 基于SQL语句编写，灵活，SQL写在XML中，与程序解耦便于统一管理，支持动态SQL的编写
- 与直接使用JDBC相比减少了大量的重复代码
- 能很好的兼容各种数据库
- 提供映射标签，可以灵活的将对象和数据库自动映射起来。
**缺点**
- 当SQL字段较多时，工足量较大，需要有一定SQL水准
- SQL语句依赖数据库，导致数据库移植性较差


## hibernate和mybatis区别
**相同点**
- 都是对JDBC的封装，持久层框架
**不同点**
- mybatis是一个半自动映射框架，配置对象和sql语句执行结果对应关系，多表查询配置简单
- hibernate是一个全表映射框架

**SQL优化和移植性**
- hibernate对SQL语句进行了封装，提供响应的方法操作数据库，可以较好的进行数据库的移植，但是SQL优化较为困难
- mybatis 需要手动编写SQL，支持动态SQL编写，列表处理等，因为SQL与数据库强关联，所以移植性相对较差。

## ORM是什么
对象映射框架，为了解决对象与关系型数据库之间映射关系。

## 传统JDBC开发存在的问题
- 频繁创建数据库连接，释放等容易造成资源浪费
- SQL语句和参数设置等存在硬编码，耦合性过高
- 结果处理存在重复代码

## #{}和${}的区别
- #{}是占位符，预编译处理，${}是拼接符，直接字符串替换，容易造成SQL注入
- #{}可以有效防止SQL注入，提高系统安全性
- #{}变量的替换在DBMS中，${}的替换在DBMS外


## 模糊查询like怎么写
- 建议使用CONCAT()拼接函数

## Dao接口方法能重载吗
- 不能，mybatis构建时 通过文件全路径+方法名作为key进行保存


## mybatis的xml映射文件和mybatis内部数据结构间映射关系
-  < parametermap >：标签解析成parametermap对象
- < resultMap>：标签解析成resultmap对象
- < select update delete>：等标签解析成MappedStatement对象
- sql被解析为Boundsql对象
## 除了select insert update等还有哪些标签
- foreach
- where
- include
- sql
- when 
- choose
- otherwise
- if

## 如果A标签引用了B标签的内容，那么是否B标签必须在A标签前面
- 不一定，可以在任意位置，mybatis在解析标签A时，如果B标签还未处理，则当前A标签会被标记成未解析状态，等所有标签解析完成后，再处理未解析的标签。

## Mybatis是否可以映射枚举类
- 自定义TypeHandler可以完成javatype和jdbctype的转换

## mybatis的一级、二级缓存
- 一级缓存：基于hashmap的本地缓存，作用域时session，当session flush或close之后，该session中所有的cache将清空，默认打开一级缓存
- 二级缓存：默认使用hashmap的本地缓存，但是可以使用redis等外部缓存中间件， 生命周期是基于Mapper的， 默认是不打开的。