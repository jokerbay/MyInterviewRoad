1.mybatis是什么
MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的XML 或注解来配置和映射原生信息，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录。

2.传统jdbc的问题
数据库链接创建、释放频繁造成系统资源浪费从而影响系统性能，如果使用数据库链接池可解决此问题。
Sql 语句在代码中硬编码，造成代码不易维护，实际应用 sql 变化的可能较大，sql 变动需要改变 java代码。
使用 preparedStatement 向占有位符号传参数存在硬编码，因为 sql 语句的 where 条件不一定，可能多也可能少，修改 sql 还要修改代码，系统不易维护。
对结果集解析存在硬编码（查询列名），sql 变化导致解析代码变化，系统不易维护，如果能将数据库记录封装成 pojo 对象解析比较方便。
3.mybatis的几张图
mvc三层架构
在这里插入图片描述

mybatis与jdbc的联系
在这里插入图片描述
mybatis流程图
mybatis流程图
4.mybatis全局配置文件
一个简单的配置实例
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
  <environments default="development">
    <environment id="development">
      <transactionManager type="JDBC"/>
      <dataSource type="POOLED">
        <property name="driver" value="${driver}"/>
        <property name="url" value="${url}"/>
        <property name="username" value="${username}"/>
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
我们可以根据这个xml文件来获取可以执行sql语句的sqlSession对象
String resource = "SqlMapConfig.xml";
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
4.1文档中的标签
configuration（配置）
properties（属性）
settings（设置）
typeAliases（类型别名）
typeHandlers（类型处理器）
objectFactory（对象工厂）
plugins（插件）
environments（环境配置）
environment（环境变量）
transactionManager（事务管理器）
dataSource（数据源）
databaseIdProvider（数据库厂商标识）
mappers（映射器）
4.2各标签详解
4.2.1 properties
properties标签用于引入外部properties文件或这自定义properties键值，一般用来定义数据库连接信息

<properties resource="org/mybatis/example/config.properties">
  <property name="username" value="dev_user"/>
  <property name="password" value="F2Fa3!33TYyg"/>
</properties>
4.2.2 settings
settings 是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。以下是常见的几个配置

设置名	描述	有效值	默认值
cacheEnabled	全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。	true或 false	true
lazyLoadingEnabled	延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 fetchType 属性来覆盖该项的开关状态。	true或 false	false
aggressiveLazyLoading	开启时，任一方法的调用都会加载该对象的所有延迟加载属性。 否则，每个延迟加载属性会按需加载。	true或false	false （在 3.4.1 及之前的版本中默认为 true
<settings>
  <setting name="cacheEnabled" value="true"/>
  <setting name="lazyLoadingEnabled" value="true"/>
  <setting name="aggressionLazyLoading" value="false"/>
</settings>
4.2.3 类型别名（typeAliases）
类型别名可为 Java 类型设置一个缩写名字。 它仅用于 XML 配置，作用是降低冗余的全限定类名书写。 有两种配置方法，第一种直接对应java类，第二种是对应相应的包, 在没有注解的情况下，会使用 Bean 的首字母小写的非限定类名来作为它的别名

<typeAliases>
  <typeAlias alias="author" type="com.scau.Author"/>
  <package name="com.scau.bean"/>
</typeAliases>
另外补充一下，常见的 Java 类型内建的类型别名。它们都是不区分大小写的，注意，为了应对原始类型的命名重复，采取了特殊的命名风格。

别名	映射的类型
_byte	byte
_long	long
_short	short
_int	int
_double	double
_float	float
_boolean	boolean
string	String
byte	Byte
这个以下都是是一些常见的包装类，都是首字母变小写取别名，这里我们可以发现如果是基本数据类型，都是在前面加下划线，其他的引用类型都是单词中的大写变小写(注：是全部大写都变小写)
4.2.4 映射器（mappers)
我们需要告诉 MyBatis 到哪里去找我们定义的sql语句。 在自动查找资源方面，Java 并没有提供一个很好的解决方案，所以最好的办法是直接告诉 MyBatis 到哪里去找映射文件。 你可以使用相对于类路径的资源引用，或完全限定资源定位符（包括 file:/// 形式的 URL），或类名和包名等。以下是常见的四种配置类型

<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="com/edu/scau/dao/UserMapper.xml"/>
  <mapper resource="com/edu/scau/dao/BlogMapper.xml"/>
  <mapper resource="com/edu/scau/dao/PostMapper.xml"/>
</mappers>
<!-- 使用完全限定资源定位符（URL） -->
<mappers>
  <mapper url="file:///var/mappers/UserMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
<!-- 使用映射器接口实现类的完全限定类名 -->
<mappers>
  <mapper class="com.edu.scau.dao.UserMapper"/>
  <mapper class="com.edu.scau.dao.BlogMapper"/>
  <mapper class="com.edu.scau.dao.PostMapper"/>
</mappers>
<!-- 将包内的映射器接口实现全部注册为映射器 -->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
5. mybatis映射文件
5.1 输入映射parameterType
#{}对应jdbc中的占位符?，parameterType接收简单类型的参数时，里面的名称可以是任意值
${}表示拼接符，parameterType接收简单类型的参数时，里面的名称必须是value

<select id="findUserById" parameterType="int" resultType="user">
		SELECT * FROM USER WHERE id = #{id}
	</select>
	
<select id="findUsersByName" parameterType="java.lang.String" resultType="user">
		SELECT * FROM USER WHERE username LIKE "%${value}%"
	</select>
#{}表示一个占位符号通过#{}可以实现 preparedStatement 向占位符中设置值，自动进行 java 类型和 jdbc 类型转换，#{}可以有效防止 sql 注入。 #{}可以接收简单类型值或 pojo 属性值。 如果 parameterType 传输单个简单类型值，#{}括号中可以是 value 或其它名称。

$ {}表示拼接 sql 串通过$ {}可以将 parameterType 传入的内容拼接在 sql 中且不进行 jdbc 类型转换， $ { }可以接收简单类型值或 pojo 属性值，如果 parameterType 传输单个简单类型值，$ {}括号中只能是 value。

5.2输出结果封装resultType
resultType 属性可以指定结果集的类型，它支持基本类型和实体类类型。需要注意的是，它和 parameterType 一样，如果注册过类型别名的，可以直接使用别名。没有注册过的必须使用全限定类名。同时，当是实体类名称是，还有一个要求，实体类中的属性名称必须和查询语句中的列名保持一致，否则无法实现封装.

<!-- 配置查询所有操作 -->
<select id="findAll" resultType="com.edu.scau.bean.User">
select * from user
</select>
5.3输出结果封装resultMap
resultMap 标签可以建立查询的列名和实体类的属性名称不一致时建立对应关系。从而实现封装。在 select 标签中使用 resultMap 属性指定引用可。

<resultMap type="com.edu.scau.bean.User" id="userMap">
<id column="id" property="userId"/>
<result column="username" property="userName"/>
<result column="sex" property="userSex"/>
<result column="address" property="userAddress"/>
<result column="birthday" property="userBirthday"/>
</resultMap>
<!--可以在这里引用之前定义好的reusltMap-->
<select id="findAll" resultMap="userMap">
select * from user
</select>
6. 延迟加载
在需要用到数据时才进行加载，不需要用到数据时就不加载数据。延迟加载也称懒加载.

好处：先从单表查询，需要时再从关联表去关联查询，大大提高数据库性能，因为查询单表要比关联查询多张表速度要快
坏处：因为只有当需要用到数据时，才会进行数据库查询，这样在大批量数据查询时，因为查询工作也要消耗时间，所以可能造成用户等待时间变长，造成用户体验下降
基本做法：再全局配置文件中设置setting的值为懒加载，前面有提过，然后先做单表查询，再根据这张表的id值或者与其他表产生关系的列去作为resultMap中association或collection中一个column值，properties对应实体类中的一个集合，select 引用所要关联的表中的查询方法

7.缓存
像大多数的持久化框架一样，Mybatis 也提供了缓存策略，通过缓存策略来减少数据库的查询次数，从而提高性能。
Mybatis 中缓存分为一级缓存，二级缓存。
在这里插入图片描述

7.1 一级缓存
一级缓存是 SqlSession 级别的缓存，只要 SqlSession 没有 flush 或 close，它就存在。一级缓存是 SqlSession 范围的缓存，当调用 SqlSession 的修改，添加，删除，commit()，close()，clearCache()等方法时，就会清空一级缓存。缓存中是map结构

在这里插入图片描述

7.2 二级缓存
二级缓存是 mapper 映射级别的缓存，或者说是SqlSessionFactory级别的，多个 SqlSession 去操作同一Mapper 映射的 sql 语句，多个SqlSession 可以共用二级缓存，二级缓存是跨SqlSession 的。
在这里插入图片描述
首先开启 mybatis 的二级缓存。

sqlSession1 去查询用户信息，查询到用户信息会将查询数据存储到二级缓存中。 这里说明以下存入二级缓存中的不是对象，而是对象的数据，下一个session来查的时候是取出数据再去新建一个对象，所以如果将返回的对象打印的话，不会同一个
如果 SqlSession3 去执行相同 mapper 映射下 sql，执行 commit 提交，会清空该 mapper 映射下的二级缓存区域的数据。
sqlSession2 去查询与 sqlSession1 相同的用户信息，首先会去缓存中找是否存在数据，如果存在直接从缓存中取出数据。
二级缓存的开启步骤

在 SqlMapConfig.xml 文件开启二级缓存(因为 cacheEnabled 的取值默认就为 true，所以这一步可以省略不配置。为 true 代表开启二级缓存；为
false 代表不开启二级缓存。)
<settings>
<!-- 开启二级缓存的支持 -->
<setting name="cacheEnabled" value="true"/>
</settings>

配置相关的 Mapper 映射文件
<cache>标签表示当前这个 mapper 映射将使用二级缓存，区分的标准就看 mapper 的 namespace 值。
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper 
 PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" 
 "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.itheima.dao.IUserDao">
<!-- 开启二级缓存的支持 -->
<cache></cache>
</mapper>
配置 statement 上面的 useCache 属性
<!-- 根据 id 查询 -->
<select id="findById" resultType="user" parameterType="int" useCache="true">
select * from user where id = #{uid}
</select>
