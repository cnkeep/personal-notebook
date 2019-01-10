``Mapper.xml``学习
------------
## 前言
> 本节我们针对sql的配置映射文件Mapper.xml进行学习  
> 目录  
>> 基本元素（按照它们应该被定义的顺序）:  
>>>* ``select`` – 映射查询语句  
>>>* ``sql`` – 可被其他语句引用的可重用语句块  
>>>* ``insert`` – 映射插入语句  
>>>* ``update`` – 映射更新语句  
>>>* ``delete`` – 映射删除语句  
>>>* ``parameterMap`` – 已废弃！老式风格的参数映射。内联参数是首选,这个元素可能在将来被移除，这里不会记录。  
>>>* ``resultMap`` – 是最复杂也是最强大的元素，用来描述如何从数据库结果集中来加载对象。  
>>>* ``cache-ref`` – 其他命名空间缓存配置的引用。  
>>>* ``cache`` – 给定命名空间的缓存配置。  

>> 动态sql  
>>>* ``#{}``和``${}``区别    
>>>* 动态表达式  

## 正文 
### 基本元素 

#### select  
> 查询语句是 MyBatis 中最常用的元素之一，光能把数据存到数据库中价值并不大，如果还能重新取出来才有用，多数应用也都是查询比修改要频繁。对每个插入、更新或删除操作，通常对应多个查询操作。这是 MyBatis 的基本原则之一，也是将焦点和努力放到查询和结果映射的原因。简单查询的 select 元素是非常简单的。比如：
```xml
    <select id="selectPerson" parameterType="int" resultType="hashmap">
      SELECT * FROM PERSON WHERE ID = #{id}
    </select>
```
> 这个语句被称作 selectPerson，接受一个 int（或 Integer）类型的参数，并返回一个 HashMap 类型的对象，其中的键是列名，值便是结果行中的对应值。

> 注意参数符号：**#{id}**  

> 这就告诉 MyBatis 创建一个预处理语句参数，通过 JDBC，这样的一个参数在 SQL 中会由一个“?”来标识，并被传递到一个新的预处理语句中，就像这样：
```
    // Similar JDBC code, NOT MyBatis…
    String selectPerson = "SELECT * FROM PERSON WHERE ID=?";
    PreparedStatement ps = conn.prepareStatement(selectPerson);
    ps.setInt(1,id);
```
> select 元素有很多属性允许你配置，来决定每条语句的作用细节。
```xml
    <select
      id="selectPerson"
      parameterType="int"
      parameterMap="deprecated"
      resultType="hashmap"
      resultMap="personResultMap"
      flushCache="false"
      useCache="true"
      timeout="10000"
      fetchSize="256"
      statementType="PREPARED"
      resultSetType="FORWARD_ONLY">
```
| 属性 |	 描述 |
|:--:|:--|
| id 	             | 在命名空间中唯一的标识符，可以被用来引用这条语句。|  
| parameterType 	 | 将会传入这条语句的参数类的完全限定名或别名。这个属性是可选的，因为 MyBatis 可以通过 TypeHandler 推断出具体传入语句的参数，默认值为 unset。|  
| parameterMap  	 | 这是引用外部 parameterMap 的已经被废弃的方法。使用内联参数映射和 parameterType 属性。|  
| resultType 	     | 从这条语句中返回的期望类型的类的完全限定名或别名。注意如果是集合情形，那应该是集合可以包含的类型，而不能是集合本身。使用 resultType 或 resultMap，但不能同时使用。|  
| resultMap 	     | 外部 resultMap 的命名引用。结果集的映射是 MyBatis 最强大的特性，对其有一个很好的理解的话，许多复杂映射的情形都能迎刃而解。使用 resultMap 或 resultType，但不能同时使用。|  
| flushCache 	     | 将其设置为 true，任何时候只要语句被调用，都会导致一级缓存和二级缓存都会被清空，默认值：false。|  
| useCache 	         | 将其设置为 true，将会导致本条语句的结果被二级缓存，默认值：对 select 元素为 true。|  
| timeout 	         | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为 unset（依赖驱动）。|  
| fetchSize 	     | 这是尝试影响驱动程序每次批量返回的结果行数和这个设置值相等。默认值为 unset（依赖驱动）。|  
| statementType 	 | STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。|  
| resultSetType 	 | FORWARD_ONLY，SCROLL_SENSITIVE 或 SCROLL_INSENSITIVE 中的一个，默认值为 unset （依赖驱动）。|  
| databaseId 	     | 如果配置了 databaseIdProvider，MyBatis 会加载所有的不带 databaseId 或匹配当前 databaseId 的语句；如果带或者不带的语句都有，则不带的会被忽略。|  
| resultOrdered 	 | 这个设置仅针对嵌套结果 select 语句适用：如果为 true，就是假设包含了嵌套结果集或是分组了，这样的话当返回一个主结果行的时候，就不会发生有对前面结果集的引用的情况。这就使得在获取嵌套的结果集的时候不至于导致内存不够用。默认值：false。|  
| resultSets  	     | 这个设置仅对多结果集的情况适用，它将列出语句执行后返回的结果集并每个结果集给一个名称，名称是逗号分隔的。|  

#### insert, update 和 delete
> 数据变更语句 insert，update 和 delete 的实现非常接近：
```xml

<insert
  id="insertAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  keyProperty=""
  keyColumn=""
  useGeneratedKeys=""
  timeout="20">

<update
  id="updateAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">

<delete
  id="deleteAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">
```

| 属性 | 描述 |  
| :--: | :-- |  
| id 	            | 命名空间中的唯一标识符，可被用来代表这条语句。 |     
| parameterType 	| 将要传入语句的参数的完全限定类名或别名。这个属性是可选的，因为 MyBatis 可以通过 TypeHandler 推断出具体传入语句的参数，默认值为 unset。 |  
| parameterMap 	    | 这是引用外部 parameterMap 的已经被废弃的方法。使用内联参数映射和 parameterType 属性。 |  
| flushCache 	    | 将其设置为 true，任何时候只要语句被调用，都会导致本地缓存和二级缓存都会被清空，默认值：true（对应插入、更新和删除语句）。 |  
| timeout 	        | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为 unset（依赖驱动）。 |  
| statementType 	| STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用 Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |  
| useGeneratedKeys 	| （仅对 insert 和 update 有用）这会令 MyBatis 使用 JDBC 的 getGeneratedKeys 方法来取出由数据库内部生成的主键（比如：像 MySQL 和 SQL Server 这样的关系数据库管理系统的自动递增字段），默认值：false。 |  
| keyProperty 	    | （仅对 insert 和 update 有用）唯一标记一个属性，MyBatis 会通过 getGeneratedKeys 的返回值或者通过 insert 语句的 selectKey 子元素设置它的键值，默认：unset。如果希望得到多个生成的列，也可以是逗号分隔的属性名称列表。 |  
| keyColumn 	    | （仅对 insert 和 update 有用）通过生成的键值设置表中的列名，这个设置仅在某些数据库（像 PostgreSQL）是必须的，当主键列不是表中的第一列的时候需要设置。如果希望得到多个生成的列，也可以是逗号分隔的属性名称列表。 |  
| databaseId 	    | 如果配置了 databaseIdProvider，MyBatis 会加载所有的不带 databaseId 或匹配当前 databaseId 的语句；如果带或者不带的语句都有，则不带的会被忽略。 |   

#### **sql**  
> 这个元素可以被用来定义可重用的 SQL 代码段，可以包含在其他语句中。它可以被静态地(在加载参数) 参数化. 不同的属性值通过包含的实例变化. 比如：  
```xml
    <sql id="userColumns"> ${alias}.id,${alias}.username,${alias}.password </sql>
```
> 这个 SQL 片段可以被包含在其他语句中，例如：
```xml
    <select id="selectUsers" resultType="map">
      select
        <include refid="userColumns"><property name="alias" value="t1"/></include>,
        <include refid="userColumns"><property name="alias" value="t2"/></include>
      from some_table t1
        cross join some_table t2
    </select>
```
> 属性值也可以被用在 include 元素的 refid 属性里（<include refid="${include_target}"/>）或 include 内部语句中（${prefix}Table），例如：
```xml
    <sql id="sometable">
      ${prefix}Table
    </sql>
    
    <sql id="someinclude">
      from
        <include refid="${include_target}"/>
    </sql>
    
    <select id="select" resultType="map">
      select
        field1, field2, field3
      <include refid="someinclude">
        <property name="prefix" value="Some"/>
        <property name="include_target" value="sometable"/>
      </include>
    </select>
```
#### resultMap  
> 结果集映射是mybatis最复杂的部分，mybatis为我们做了很多的工作,给出一个复杂的例子:
```java
    public class Author{
        private Integer id;
        private String name;
        //getter and setter
    }
    
    public class Comment{
        private Integer id;
        private String content;
        //getter and setter 
    }
    
    public class Blog{
        private Integer id;
        private Author author;
        private List<Comment> commentList;
        private String title;
        //getter and setter
    }
```    
```xml
    <resultMap id="detailedBlogResultMap" type="Blog">
        <id property="id" column="id"/>
        <constructor>
            <idArg column="blog_id" javaType="int"/>
        </constructor>
        <result property="title" column="blog_title"/>
        <association  property="author" javaType="Author">
             <id property="id" column="author_id"/>>
             <result property="name" column="author_name"/>
        </association>
        <collection property="commentList" ofType="Comment">
            <id property="id" column="comment_id"/>
            <result property="content" column="content"/>
        </collection>
        <discriminator javaype="int" column="type" >
            <case value="" resultType=""/>
        </discriminator>
    </resultMap>
```
> resultMap
> * constructor - 用于在实例化类时，注入结果到构造方法中  
>> * idArg - ID 参数;标记出作为 ID 的结果可以帮助提高整体性能  
>> * arg - 将被注入到构造方法的一个普通结果  
> * id – 一个 ID 结果;标记出作为 ID 的结果可以帮助提高整体性能   
> * result – 注入到字段或 JavaBean 属性的普通结果  
> * association – 一个复杂类型的关联;许多结果将包装成这种类型嵌套结果映射 – 关联可以指定为一个 resultMap 元素，或者引用一个   
> * collection – 一个复杂类型的集合嵌套结果映射 – 集合可以指定为一个 resultMap 元素，或者引用一个    
> * discriminator – 使用结果值来决定使用哪个 resultMap  
>> * case – 基于某些值的结果映射嵌套结果映射,一个 case 也是一个映射它本身的结果,因此可以包含很多相 同的元素，或者它可以参照一个外部的 resultMap。  

### 动态sql  
#### ``#{}``和``${}``区别  
> 有时候我们会在xml的sql中使用到这两者但是这两者有什么区别呢？  
> ``#{}``: 属于动态参数，会进行预编译，而且进行类型匹配，用于变量替换  
>　``${}``: 不会进行类型匹配，用于字符串拼接  
> 举个例子：  
```xml
    <select id="findByName1">
        select * from user where name=#{params.name}
    </select>
    <select id="findByName2">
        select * from user where id=${parms.name}
    </select>

```
> 上面2个sql最终的结果都为：``select * from user where name = 'zhangsan' '``,但是findByName1中的参数会进行预编译参数替换为?,防止sql注入；而findByName2只是简单的字符串替换，使用时应当注意sql注入的问题,但是使用order by或者将表名作为参数传递进来是只能使用${}.
#### 动态表达式  
> MyBatis 采用功能强大的基于 OGNL 的表达式来提供了动态sql的功能，主要包含:  
> * if  
> * choose(when, otherwise)  
> * trim (where, set)  
> * foreach  

##### **if**  
```xml
    <select id="findUserList">
        select * from user
        where state=0
        <if test="path != null">
            and path=#{param.path}
        </if>   
    </select>
```
这句话意味着，当传入的参数中path存在则筛选条件追加path字段，反之则忽略。 

##### **choose**  
> 有时我们不想应用到所有的条件语句，而只想从中择其一项。针对这种情况，MyBatis 提供了 choose 元素，它有点像 Java 中的 switch 语句。  
```xml
    <select id="findUserList">
        select * from user
        where name=#{name}
        <choose>
               <when test="state != null and state != 0">
                    state = #{state}
                </when>
                <otherwise>
                    state = 0
                </otherwise>
        </choose>
    </select>
```
> 上面的sql意味着，假如存在state字段，则查询参数拼接该字段，否则拼接为默认的参数值。  

#### trim(where)  
```xml
    <select id="findActiveBlogLike"
         resultType="Blog">
      SELECT * FROM BLOG 
      <where> 
        <if test="state != null">
             state = #{state}
        </if> 
        <if test="title != null">
            AND title like #{title}
        </if>
        <if test="author != null and author.name != null">
            AND author_name like #{author.name}
        </if>
      </where>
    </select>
```
> where 元素只会在至少有一个子元素的条件返回 SQL 子句的情况下才去插入“WHERE”子句。而且，若语句的开头为“AND”或“OR”，where 元素也会将它们去除。
如果 where 元素没有按正常套路出牌，我们可以通过自定义 trim 元素来定制 where 元素的功能。比如，和 where 元素等价的自定义 trim 元素为：  
```xml
    <trim prefix="WHERE" prefixOverrides="AND |OR ">
      ... 
    </trim>
```       
> prefixOverrides 属性会忽略通过管道分隔的文本序列（注意此例中的空格也是必要的）。它的作用是移除所有指定在 prefixOverrides 属性中的内容，并且插入 prefix 属性中指定的内容。  

##### foreach  
> 动态 SQL 的另外一个常用的操作需求是对一个集合进行遍历，通常是在构建 IN 条件语句的时候。比如：
```
  <select id="selectPostIn" resultType="domain.blog.Post">
    SELECT *
    FROM POST P
    WHERE ID in
    <foreach item="item" index="index" collection="list"
        open="(" separator="," close=")">
          #{item}
    </foreach>
  </select>
```
> foreach 中的collection取值可以为(list,array,map)  