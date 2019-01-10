路线：
1. 入门介绍
    重要的几个接口：  
    > * <code>Configuration</code>: Mybatis的配置信息都在该类中  
    > * <code>SqlSession</code>: 作为MyBatis工作的主要顶层API，表示和数据库交互的会话，完成必要数据库增删改查功能，类似于connection
    > * <code>Executor</code>: 执行器，是MyBatis 调度的核心，负责SQL语句的生成和查询缓存的维护
    > * <code>StatementHandler</code>: 封装了JDBC Statement操作，负责对JDBC statement 的操作，如设置参数、将Statement结果集转换成List集合。  
    > * <code>ParameterHandler</code>: 负责将用户传递的参数转换成JDBC statement锁需要的参数。  
    > * <code>ResultHandler</code>: 负责将JDBC返回的ResultSet结果集装换为List集合
    > * <code>TypeHandler</code>: 负责java数据类型和JDBC数据类型之间的映射和转化
    > * <code>MappedStatement</code>: MappedStatement维护了一条<select|update|delete|insert>节点的封装  
    > * <code>SqlSource</code>: 负责根据用户传递的parameterObject，动态地生成SQL语句，将信息封装到BoundSql对象中，并返回 
    > * <code>BoundSql</code>: 表示动态生成的SQL语句以及相应的参数信息 
    

2. 实例操作，多库连接，动态创建SqlSessionFactory,与spring整合  
    提供Demo

3. 配置文件mybatis-config.xml,内部结点的作用，顺序

4. 执行流程，各个相关类

    MapperProxy.invoke(...) Mapper接口的代理类,  
    MapperMethod.execute(...) 按照sql类型分类执行  
    DefaultSqlSession.selectList(...)  
    Executor.query(...)  
    parameterHandler.parameterize()//设置参数    
    查询缓存是否存在  
    Executor.doQuery(...)  
    statementHandler.query(...)  
    prepareStatement.execute();//jdbc执行  
    resultSetHandler.handleResultSets() 处理结果  
    
5. mapper.xml节点学习，动态sql    

6. 拓展：缓存

6. 插件，分库分表，日志

7. 个人实践：分页，分表，日志，TypeHandler抽象

8. 总结，设计模式
    大量的jdk静态代理使用，Plugin类似于洋葱模型生成代理模式
    Builder模式，例如SqlSessionFactoryBuilder、XMLConfigBuilder、XMLMapperBuilder、XMLStatementBuilder、CacheBuilder；  
    工厂模式，例如SqlSessionFactory、ObjectFactory、MapperProxyFactory；  
    单例模式，例如ErrorContext和LogFactory；  
    代理模式，Mybatis实现的核心，比如MapperProxy、ConnectionLogger，用的jdk的动态代理；还有executor.loader包使用了cglib或者javassist达到延迟加载的效果；  
    组合模式，例如SqlNode和各个子类ChooseSqlNode等；  
    模板方法模式，例如BaseExecutor和SimpleExecutor，还有BaseTypeHandler和所有的子类例如IntegerTypeHandler；  
    适配器模式，例如Log的Mybatis接口和它对jdbc、log4j等各种日志框架的适配实现；  
    装饰者模式，例如Cache包中的cache.decorators子包中等各个装饰者的实现；  
    迭代器模式，例如迭代器模式PropertyTokenizer；  

