查阅网站：https://mybatis.org/mybatis-3/zh
## 数据持久化
数据持久化就是 内存中的数据模型与存储模型之间的互相转化，操作数据库的过程就是数据持久化
## Mybatis是什么
是一个半ORM（对象关系映射Object Relational Mapping）框架
## mybatis一级缓存与二级缓存
一级缓存：一级缓存是sqlsession级别的，同一个sqlsession执行相同的查询，后面的查询会直接使用缓存中的第一次查询的结果（第一次会去查询数据库并写到缓存中，第二次从一级缓存中取。）

一级缓存何时清空：当sqlsession执行commit，delete，update，insert操作时，就会清除一级缓存避免脏读，同时当sqlsession执行了写操作并且在之后执行了commit（），就会同时清除一级缓存与二级缓存

一级缓存没有过期时间，只有生命周期，SqlSession对象中会有一个Executor对象，Executor对象中持有一个PerpetualCache对象，当会话结束时，SqlSession对象及其内部的Executor对象还有PerpetualCache对象也一并释放掉。

二级缓存：二级缓存是跨sqlsession的，二级缓存是 mapper 级别的缓存，多个 SqlSession 去操作同一个 Mapper 映射的 sql 语句，多个SqlSession 可以共用二级缓存；
也可以理解为它指的是Mybatis中SqlSessionFactory对象的缓存。由同一个SqlSessionFactory对象创建的SqlSession共享其缓存。

二级缓存何时存入：在关闭sqlsession后(close)，才会把该sqlsession一级缓存中的数据添加到namespace的二级缓存中。

二级缓存有过期时间，但是没有专门的线程去检测是否过期，而是每一次查询判断是否过期，过期就清空

mybatis使用缓存查询流程： 二级缓存---> 一级缓存---->数据库

## #与$的区别
### #占位符
mybatis处理#{} 使用jbc对象是PrepareStatement对象

select * from user where id=#{Id}

mybatis创建PrepareStatement对象,执行sql语句,会把#{}变为？

```java
String sql="select * from user where id=?"

PrepareStatement pst=conn.prepareStatement(sql);

pst.setInt(1,1001);传递参数

ResultSet rs=pst.executeQuery();执行sql语句
```
#{}的特点：

1. 使用的PrepareStatement对象，执行sql语句，效率高
2. 使用的PrepareStatement对象，能避免sql语句，sql语句执行更安全，可以避免sql注入
3. #{}常常作为列值使用， 和类型是有关的
### $ 占位符
select * from user where id=${Id}

${}表示字符串连接，把sql语句的其他内容和${}内容使用字符串（+）连接在一起
```java
String sql="select * from user where id="+1001" ";

Statement stm=conn.createStatement(sql);

ResultSet rs=stm.executeQuery();
```
$的特点
1. 使用Statement对象啊，执行sql语句，效率低
2. 使用字符串连接的方式，由sql注入的风险，代码由安全问题
3. ${}数据是原样使用的，不会区分数据类型
4. ${}一般用于传递列名，表名

## 封装mybatis输出结构
封装输出结构：mybatis执行sql语句，得到ResultSet,转为java对象

封装结构由两种 resultType，resultMap
### resultType
resultType：使用java类型的全限定名(也可以使用别名，需要在mybatis配置文件中进行配置，使用typeAlias标签)，表示的意思是mybatis 执行sql， 完了会把ResultSet中的数据转为 改java类型的对象。

具体mybatis有以下操作：

1.调用该java类型的无参构造方法

2.将同名的列赋给同名的属性，（类名不必与表名相同，只需要类中的属性与表中字段名相同）

例：user.setId(rs.getInt("id"))

3.得到java对象，如果返回值是list<>,则会把对象放入容器中

注：如果resultType是Map，那么sql语句最多只能得到一行结果（因为会使用Sqlsession中的selectone（）方法），得到的map对象中，列名是key，相应的值是value

### resultMap（结果映射）
功能性更强，更灵活，可以自定义列名和java对象属性的对应关系，**常用在列名和属性名不同的情况下**

用法:
1. 先定义resultMap标签，指定列名和属性名称对应关系
2. 在select标签中使用resultMap属性，指定上面定义的resulMap标签的id值
```xml
<!--    resultMap与resultTpye不可同时使用，二选一 -->
<!--    使用resultMap定义列与属性的关系-->
<!--    定义resultMap-->
<!--    id：给resultMap的映射关系起个名称，唯一值-->
<!--    type：java类型的全限定名称-->
    <resultMap id="customMap" type="com.example.demo.vo.StudentVo">
        <!--定义列名和属性名的对应-->
        <!--主键类型使用id标签-->
        <id column="id" property="vid"></id>
        <!--非主键类型使用result标签-->
        <result column="name" property="vname"></result>
        <result column="email" property="vemail"></result>
        <result column="age" property="vage"></result>
        <!--列名与属性名相同不用定义-->
    </resultMap>
```
## mybatis模糊查询 like
第一种方式，在Java语句中加%： String name="zhang%";

第二种方式，在sql语句中加%：select * from student where name like "%" #{name} "%"

## 动态sql  dynamic sql（一般在多条件查询时使用）
定义：同一个dao接口的方法，更具不同的条件可以表示不同的sql语句，主要是where部分有变化

实现：使用mybatis提供的标签 ，主要有 if，where，foreach

使用动态sql时，一般用对象传参

### if标签
``` xml
<!--使用if实现动态sql-->
    <select id="selectIf" resultType="com.example.demo.entity.Student">
        select * from student 
        where id=-1
        <if test="name!=null and name!='' ">
            name=#{name}
        </if>
        <if test="age>0">
            or age=#{age}
        </if>
    </select>
```
where id=-1（或者where 1=1）的目的是为了避免出现语法错误，例如当条件一不满足但是条件二满足时sql语句会变为select * from student  or age=#{age} ，有语法错误

注：当mapper的动态sql中出现 >,<,>=，<=时需要将其转换为实体符号，否则可能会出错

常用的符号对应实体
 
<!-- 1.<--- &lt
2.>--- &gt
3.>=--- &gt;=
4.<=--- &lt;= -->

### where标签
使用where时，里面是一个或者多个if标签，只有当其中至少一个if标签为真，where标签会转为where关键字附在sql语句的后面。where标签会删除紧跟其的or与and来保证语法上的正确
```xml
<select id="selectWhere" resultType="com.example.demo.entity.Student">
        select * from student
        <where>
        <if test="name!=null and name!=''">
             or  name=#{name}
        </if>
        <if test="age>0">
             or age=#{age}
         </if>

        </where>
    </select>
```
### foreach标签 循环
可以循环数组，list集合，一般使用在in语句中

语法:

<foreach collection="集合类型" open="开始字符" close="结束字符" separator="集合成员之间的分隔符" item="集合中的成员（类似for循环中的那个x）">
            #{x}
        </foreach>

有两个方式，用简单类型作为参数与对象作为参数
```xml
    <!--使用foreach标签-->
    <select id="selectForeach" resultType="com.example.demo.entity.Student">
        select * from student 
        <if test="list!=null and list.size>0">
            where id in
        <foreach collection="list" open="(" close=")" separator="," item="myId">
            #{myId}
        </foreach>
        </if>
    </select>
    
    <select id="selectForeachObject" resultType="com.example.demo.entity.Student">
        select * from student
        <if test="list!=null and list.size>0">
            where id in
            <foreach collection="list" open="(" close=")" separator="," item="stu">
                #{stu.id}
            </foreach>
        </if>
    </select>
```
### 代码片段，可以实现sql语句的复用
## pagehelper
数据分页，在你的select语句后面加入分页的sql内容

使用步骤
1. 加入依赖

<!--分页 依赖-->
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper-spring-boot-starter</artifactId>
            <version>1.2.10</version>
        </dependency>
2. 在mybatis著配置文件，加入plugin声明
<!--声明分页插件-->
    <plugins>
        <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
    </plugins>

3. 在select语句之前，调用 PageHelper.startPage（页码，每页大小）


