mybatis-config.xml配置介绍
----
### 介绍 
> mybatis-config.xml作为Mybatis的配置文件，提供了很多配置，而且这些配置存在加载顺序，需要特别注意，这里说明一下:  
> * [properties(属性配置节点)](#properties属性)  
> * [settings (全局配置参数)](#settings全局属性)  
> * [typeAiases(类型别名)](#typeAliases类型别名)  
> * [typeHandlers(类型转换器)](#typeHandlers类型转换器)  
> * [objectFactory (对象工厂)](#objectFactory对象工厂)  
> * [plugins (插件)](#plugins插件)  
> * [environments (环境集合属性对象)](#environments配置环境)  
> * [mappers(xml映射器)](#mappers(映射器))   

### properties属性 
>  将外部配置文件的属性导入，这样就可以在文件内部使用外部配置文件中的属性值了.  
>  * 例如配置jdbc.properties
```
    jdbc.driver=com.mysql.jdbc.Driver
    jdbc.url=jdbc:mysql://localhost:3306/jdbc
    jdbc.username=root
    jdbc.password=123456
``` 
> * 在mybatis-Config.xml中加载db.properties  
```xml
        <properties resource="db.properties">
            <!-- properties中还可以配置一些属性名和属性值,此处的优先加载 -->
            <!-- <property name="driver" value=""/> -->
        </properties>
        <!-- 和spring整合后 environments配置将废除-->
        <environments default="development">
            <environment id="development">
            <!-- 使用jdbc事务管理,事务控制由mybatis管理-->
                <transactionManager type="JDBC" />
            <!-- 数据库连接池,由mybatis管理-->
                <dataSource type="POOLED">
                    <property name="driver" value="${jdbc.driver}" />
                    <property name="url" value="${jdbc.url}" />
                    <property name="username" value="${jdbc.username}" />
                    <property name="password" value="${jdbc.password}" />
                </dataSource>
            </environment>
        </environments>
```
#### 注意：  
> * 在 properties元素内定义的属性优先读取。  
> * 然后读取properties元素中resource或url加载的属性，它会覆盖已读取的同名属性。  
> * 最后读取parameterType传递的属性，它会覆盖已读取的同名属性。  
### settings全局属性  
> mybatis 运行时可以调整一些参数，比如开启二级缓存  
<html>
 <head></head>
 <body>
  <table class="table table-striped" border="0"> 
   <thead> 
    <tr class="a"> 
     <th>设置参数</th> 
     <th>描述</th> 
     <th>有效值</th> 
     <th>默认值</th> 
    </tr> 
   </thead> 
   <tbody> 
    <tr class="b"> 
     <td> cacheEnabled </td> 
     <td> 全局地开启或关闭配置文件中的所有映射器已经配置的任何缓存。即二级缓存 </td> 
     <td> true | false </td> 
     <td> true </td> 
    </tr> 
    <tr class="a"> 
     <td> lazyLoadingEnabled </td> 
     <td> 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置<tt>fetchType</tt>属性来覆盖该项的开关状态。 </td> 
     <td> true | false </td> 
     <td> false </td> 
    </tr> 
    <tr class="b"> 
     <td> aggressiveLazyLoading </td> 
     <td> 当开启时，任何方法的调用都会加载该对象的所有属性。否则，每个属性会按需加载（参考<tt>lazyLoadTriggerMethods</tt>). </td> 
     <td> true | false </td> 
     <td> false (true in ≤3.4.1) </td> 
    </tr> 
    <tr class="a"> 
     <td> multipleResultSetsEnabled </td> 
     <td> 是否允许单一语句返回多结果集（需要兼容驱动）。 </td> 
     <td> true | false </td> 
     <td> true </td> 
    </tr> 
    <tr class="b"> 
     <td> useColumnLabel </td> 
     <td> 使用列标签代替列名。不同的驱动在这方面会有不同的表现， 具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果。 </td> 
     <td> true | false </td> 
     <td> true </td> 
    </tr> 
    <tr class="a"> 
     <td> useGeneratedKeys </td> 
     <td> 允许 JDBC 支持自动生成主键，需要驱动兼容。 如果设置为 true 则这个设置强制使用自动生成主键，尽管一些驱动不能兼容但仍可正常工作（比如 Derby）。 </td> 
     <td> true | false </td> 
     <td> False </td> 
    </tr> 
    <tr class="b"> 
     <td> autoMappingBehavior </td> 
     <td> 指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示取消自动映射；PARTIAL 只会自动映射没有定义嵌套结果集映射的结果集。 FULL 会自动映射任意复杂的结果集（无论是否嵌套）。 </td> 
     <td> NONE, PARTIAL, FULL </td> 
     <td> PARTIAL </td> 
    </tr> 
    <tr class="a"> 
     <td> autoMappingUnknownColumnBehavior </td> 
     <td> 指定发现自动映射目标未知列（或者未知属性类型）的行为。 
      <ul> 
       <li><tt>NONE</tt>: 不做任何反应</li> 
       <li><tt>WARNING</tt>: 输出提醒日志 (<tt>'org.apache.ibatis.session.AutoMappingUnknownColumnBehavior'</tt> 的日志等级必须设置为 <tt>WARN</tt>)</li> 
       <li><tt>FAILING</tt>: 映射失败 (抛出 <tt>SqlSessionException</tt>)</li> 
      </ul> </td> 
     <td> NONE, WARNING, FAILING </td> 
     <td> NONE </td> 
    </tr> 
    <tr class="b"> 
     <td> defaultExecutorType </td> 
     <td> 配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（prepared statements）； BATCH 执行器将重用语句并执行批量更新。 </td> 
     <td> SIMPLE REUSE BATCH </td> 
     <td> SIMPLE </td> 
    </tr> 
    <tr class="a"> 
     <td> defaultStatementTimeout </td> 
     <td> 设置超时时间，它决定驱动等待数据库响应的秒数。 </td> 
     <td> 任意正整数 </td> 
     <td> Not Set (null) </td> 
    </tr> 
    <tr class="b"> 
     <td> defaultFetchSize </td> 
     <td> 为驱动的结果集获取数量（fetchSize）设置一个提示值。此参数只可以在查询设置中被覆盖。 </td> 
     <td> 任意正整数 </td> 
     <td> Not Set (null) </td> 
    </tr> 
    <tr class="a"> 
     <td> safeRowBoundsEnabled </td> 
     <td> 允许在嵌套语句中使用分页（RowBounds）。如果允许使用则设置为false。 </td> 
     <td> true | false </td> 
     <td> False </td> 
    </tr> 
    <tr class="b"> 
     <td> safeResultHandlerEnabled </td> 
     <td> 允许在嵌套语句中使用分页（ResultHandler）。如果允许使用则设置为false。 </td> 
     <td> true | false </td> 
     <td> True </td> 
    </tr> 
    <tr class="a"> 
     <td> mapUnderscoreToCamelCase </td> 
     <td> 是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 A_COLUMN 到经典 Java 属性名 aColumn 的类似映射。 </td> 
     <td> true | false </td> 
     <td> False </td> 
    </tr> 
    <tr class="b"> 
     <td> localCacheScope </td> 
     <td> MyBatis 利用本地缓存机制（Local Cache）防止循环引用（circular references）和加速重复嵌套查询。 默认值为 SESSION，这种情况下会缓存一个会话中执行的所有查询。 若设置值为 STATEMENT，本地会话仅用在语句执行上，对相同 SqlSession 的不同调用将不会共享数据。 </td> 
     <td> SESSION | STATEMENT </td> 
     <td> SESSION </td> 
    </tr> 
    <tr class="a"> 
     <td> jdbcTypeForNull </td> 
     <td> 当没有为参数提供特定的 JDBC 类型时，为空值指定 JDBC 类型。 某些驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER。 </td> 
     <td> JdbcType 常量. 大多都为: NULL, VARCHAR and OTHER </td> 
     <td> OTHER </td> 
    </tr> 
    <tr class="b"> 
     <td> lazyLoadTriggerMethods </td> 
     <td> 指定哪个对象的方法触发一次延迟加载。 </td> 
     <td> 用逗号分隔的方法列表。 </td> 
     <td> equals,clone,hashCode,toString </td> 
    </tr> 
    <tr class="a"> 
     <td> defaultScriptingLanguage </td> 
     <td> 指定动态 SQL 生成的默认语言。 </td> 
     <td> 一个类型别名或完全限定类名。 </td> 
     <td> org.apache.ibatis.scripting.xmltags.XMLLanguageDriver </td> 
    </tr> 
    <tr class="b"> 
     <td> defaultEnumTypeHandler </td> 
     <td> 指定 Enum 使用的默认 <tt>TypeHandler</tt> 。 (从3.4.5开始) </td> 
     <td> 一个类型别名或完全限定类名。 </td> 
     <td> org.apache.ibatis.type.EnumTypeHandler </td> 
    </tr> 
    <tr class="a"> 
     <td> callSettersOnNulls </td> 
     <td> 指定当结果集中值为 null 的时候是否调用映射对象的 setter（map 对象时为 put）方法，这对于有 Map.keySet() 依赖或 null 值初始化的时候是有用的。注意基本类型（int、boolean等）是不能设置成 null 的。 </td> 
     <td> true | false </td> 
     <td> false </td> 
    </tr> 
    <tr class="b"> 
     <td> returnInstanceForEmptyRow </td> 
     <td> 当返回行的所有列都是空时，MyBatis默认返回<tt>null</tt>。 当开启这个设置时，MyBatis会返回一个空实例。 请注意，它也适用于嵌套的结果集 (i.e. collectioin and association)。（从3.4.2开始） </td> 
     <td> true | false </td> 
     <td> false </td> 
    </tr> 
    <tr class="a"> 
     <td> logPrefix </td> 
     <td> 指定 MyBatis 增加到日志名称的前缀。 </td> 
     <td> 任何字符串 </td> 
     <td> Not set </td> 
    </tr> 
    <tr class="b"> 
     <td> logImpl </td> 
     <td> 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。 </td> 
     <td> 查找顺序：SLF4J | LOG4J | LOG4J2 | JDK_LOGGING | COMMONS_LOGGING | STDOUT_LOGGING | NO_LOGGING </td> 
     <td> Not set </td> 
    </tr> 
    <tr class="a"> 
     <td> proxyFactory </td> 
     <td> 指定 Mybatis 创建具有延迟加载能力的对象所用到的代理工具。 </td> 
     <td> CGLIB | JAVASSIST </td> 
     <td> JAVASSIST (MyBatis 3.3 or above) </td> 
    </tr> 
    <tr class="b"> 
     <td> vfsImpl </td> 
     <td> 指定VFS的实现 </td> 
     <td> 自定义VFS的实现的类全限定名，以逗号分隔。 </td> 
     <td> Not set </td> 
    </tr> 
    <tr class="a"> 
     <td> useActualParamName </td> 
     <td> 允许使用方法签名中的名称作为语句参数名称。 为了使用该特性，你的工程必须采用Java 8编译，并且加上<tt>-parameters</tt>选项。（从3.4.1开始） </td> 
     <td> true | false </td> 
     <td> true </td> 
    </tr> 
    <tr class="b"> 
     <td> configurationFactory </td> 
     <td> 指定一个提供<tt>Configuration</tt>实例的类。 这个被返回的Configuration实例用来加载被反序列化对象的懒加载属性值。 这个类必须包含一个签名方法<tt>static Configuration getConfiguration()</tt>. (从 3.2.3 版本开始) </td> 
     <td> 类型别名或者全类名. </td> 
     <td> Not set </td> 
    </tr> 
   </tbody> 
  </table>
 </body>
</html>
  
### typeAliases类型别名 
> 记得我们在***Mapper.xml配置resultMap节点时需要配置type即需要映射的实体类类型，但是项目中一般实体类都在相同的目录下，这样写太过繁杂  
> 于是就有了简化的配置就别名配置，例如：  
```
    <typeAliases>
      <typeAlias alias="Blog" type="domain.blog.Blog"/>
    </typeAliases>
```  
> 也可以配置包名,Mybatis会自动在包名下搜索需要的java bean 对象  
```xml
    <typeAliases>
      <package name="domain.blog"/>
    </typeAliases>
```
### typeHandlers类型转换器   
> mybatis在预处理语句中设置参数和从结果集中取出参数时都会使用类型转换器将获取的值以合适的方式转换为java类型。mybatis内置了大部分的类型转换器。  
> 这些类型转换器实现了TypeHandler接口，如果我们需要自己实现自定义的类型转换器，可以实现该接口，但是这个接口方法太多，我们一般继承BaseTypeHandler抽象类即可。  
> 例如我们实现一个枚举类型的转换器  
```java

    public interface HasIndexEnum {
        int getIndex();
    }
    
    public class EnumTypeHandler<T extends HasIndexEnum> extends BaseTypeHandler<T> {
        private Map<Integer, T> enumCache = new HashMap<>();
    
        public EnumTypeHandler(Class<T> clazz) {
            if (!Enum.class.isAssignableFrom(clazz) && HasIndexEnum.class.isAssignableFrom(clazz)) {
                throw new UnsupportedOperationException("Class shound be enum and implements HasIndexEnum.class!");
            }
            T[] constants = clazz.getEnumConstants();
            for (T e : constants) {
                enumCache.put(e.getIndex(), e);
            }
        }
    
        @Override
        public void setNonNullParameter(PreparedStatement ps, int i, T parameter, JdbcType jdbcType) throws SQLException {
            ps.setInt(i, parameter.getIndex());
        }
    
        @Override
        public T getNullableResult(ResultSet rs, String columnName) throws SQLException {
            Object value = rs.getObject(columnName);
            if (rs.wasNull()) {
                return null;
            }
            return convertOrException(value);
        }
    
    
        @Override
        public T getNullableResult(ResultSet rs, int columnIndex) throws SQLException {
            Object value = rs.getObject(columnIndex);
            if (rs.wasNull()) {
                return null;
            }
            return convertOrException(value);
        }
    
        @Override
        public T getNullableResult(CallableStatement cs, int columnIndex) throws SQLException {
            Object value = cs.getObject(columnIndex);
            if (cs.wasNull()) {
                return null;
            }
            return convertOrException(value);
        }
    
        private T convertOrException(Object value) {
            T e = this.enumCache.get((Integer) value);
            if (null == e) {
                throw new IllegalArgumentException("Cannot convert " + value);
            } else {
                return e;
            }
        }
    }
```  
> 然后在mybatis-config.xml中配置该转换器  
```
   <!-- mybatis-config.xml -->
   <typeHandlers>
     <typeHandler handler="*.*.*.EnumTypeHandler"/>
   </typeHandlers>
```  
### objectFactory对象工厂  
> 不怎么常用，略  

### plugins插件  
> MyBatis 允许你在已映射语句执行过程中的某一点进行拦截调用。默认情况下，MyBatis 允许使用插件来拦截的方法调用包括：  
> * Executor (update, query, flushStatements, commit, rollback, getTransaction, close, isClosed)  
> * ParameterHandler (getParameterObject, setParameters)  
> * ResultSetHandler (handleResultSets, handleOutputParameters)  
> * StatementHandler (prepare, parameterize, batch, update, query)
> 通过 MyBatis 提供的强大机制，使用插件是非常简单的，只需实现 <code>Interceptor</code> 接口，并指定想要拦截的方法签名即可  
```java
    // ExamplePlugin.java
    @Intercepts({@Signature(
      type= Executor.class,
      method = "update",
      args = {MappedStatement.class,Object.class})})
    public class ExamplePlugin implements Interceptor {
      public Object intercept(Invocation invocation) throws Throwable {
        return invocation.proceed();
      }
      public Object plugin(Object target) {
        return Plugin.wrap(target, this);
      }
      public void setProperties(Properties properties) {
      }
    }
```
```xml
    <!-- mybatis-config.xml -->
    <plugins>
      <plugin interceptor="org.mybatis.example.ExamplePlugin">
        <property name="someProperty" value="100"/>
      </plugin>
    </plugins>
```  
> 像一些分页插件，日志插件，分表插件都是基于插件实现的，后面我们会做详细的原理介绍，在此不作过多赘述。  

### environments 配置环境  
> mybatis与spring整合后，该配置就变得十分简单，在此不做介绍了。  

### mappers(映射器)
> 所谓的映射器就是指明我们编写的mapper.xml sql文件的位置的，便于mybatis去做关联。  
> 我们可以配置具体的文件，也可以配置整个包路径，同时还支持表达式符号，例如：  
```xml
    <!-- 使用相对于类路径的资源引用 -->
    <mappers>
      <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
      <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
      <mapper resource="org/mybatis/builder/PostMapper.xml"/>
    </mappers>
    
    <!-- 使用完全限定资源定位符（URL） -->
    <mappers>
      <mapper url="file:///var/mappers/AuthorMapper.xml"/>
      <mapper url="file:///var/mappers/BlogMapper.xml"/>
      <mapper url="file:///var/mappers/PostMapper.xml"/>
    </mappers>
    
    <!-- 使用映射器接口实现类的完全限定类名 -->
    <mappers>
      <mapper class="org.mybatis.builder.AuthorMapper"/>
      <mapper class="org.mybatis.builder.BlogMapper"/>
      <mapper class="org.mybatis.builder.PostMapper"/>
    </mappers>
    
    <!-- 将包内的映射器接口实现全部注册为映射器,要求包名与xml文件的目录名一致 -->
    <mappers>
      <package name="org.mybatis.builder"/>
    </mappers>
```

> 到此Mybatis的配置文件就介绍完了，下一节我们开始讨论学习Mybatis 的执行流程和原理