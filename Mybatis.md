## 数据持久化
数据持久化就是 内存中的数据模型与存储模型之间的互相转化，操作数据库的过程就是数据持久化
## Mybatis是什么
是一个半ORM（对象关系映射）框架

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

### resultMap
