---
title: "JDBC的使用"
date: 2021-04-02T19:44:50+08:00
categories: "MySQL"
description: "使用JDBC连接MySQL数据库，创建SQL以及执行，批处理还有开启事务，入门c3p0和druid连接池"
tags : ["MySQL"]
---

## JDBC的使用

### 什么是JDBC

JDBC是执行SQL的java API，为关系型数据库提供访问。JDBC可以在更大平台使用像Windows、Mac、Unix等等

JDBC体系由JDBC驱动和JDBC API构成。通过驱动管理器可以连接不同结构的数据库管理系统。

### JDBC可以做什么

1.  创建数据库连接
2.  创建SQL语句
3.  执行SQL查询
4.  查看或修改记录

### JDBC的核心组件

1.  DriverManager:通过适合的通讯子协议，Java应用程序和相应数据库驱动程序匹配连接
2.  Driver:处理与数据库服务器的连接，客户端程序很少使用
3.  Connection:通讯的上下文，可以获取连接信息，可以设置MySQL非自动提交开启事务
4.  Statement:将SQL语句提交到数据库
5.  ResultSet:执行查询语句得到的结果集，通过它可以检索数据
6.  SQLException:处理数据相关的异常

#### JDBC基本使用

使用前需要，导包

1.  注册JDBC驱动

```java
Class.forName("com.mysql.jdbc.cj.Driver");
```

2.  数据库配置
    
```java
String url = "jdbc:mysql://localhost:3306/demodb?ServerTimezone=UTC";
String username = "mysql";
String password =  "password";
```

假定mysql服务器监听3306端口，数据库为demodb，服务器时区为UTC。用户名为username，密码为password。

3.  打开连接
```java
Connection conn = DriverManager.getConnection(url, username, password);
```

打开连接用到上面的三个配置信息

4.  创建状态通道
   
```java
tatement stmt = conn.createStatement();
```

5.  执行语句
```java
String sql = "select id,name from user";

ResultSet rs = stmt.executeQuery(sql);
// 或
// String name = "xiaoming";
// String sql2 = "insert into user(name) values(" + name + ")";
// int count = stmt.executeUpdate(sql2);
```

select语句使用executeQuery，update、insert、delete语句使用executeUpdate

6.  得到结果
```java
while (rs.next()) {
    int id = rs.getInt("id");
    String name = rs.getString("name");
    System.out.println(id + " - " + name);
}
// 或
// System.out.println("改变的行数："+count);
```

select语句得到使ResultSet的结果，其他语句得到是int类型表示，影响数据库的行数

7.  关闭连接
    
```java
try {
    ...
} catch(SQLException e) {
    ...
} finally {
    conn.close();
}
```

#### 使用预状态通道避免SQL注入

PreparedStatement是Statement的子类，支持参数化查询，来防止SQL注入攻击。PreparedStatement还可以预编译SQL语句，使执行SQL的效率更高点

```java
int id = 2;
String sql = "select id, name from user where id=?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setInt(1, id);
rs = pstmt.executeQuery();
```

#### JDBC的事务

事务常常见于转账

1.  开启事务
```java
conn.setAutoCommit(false);
```

关闭自动提交

2.  回滚

遇到异常需要回滚

```java
conn.rollback();
// 或 设置安全点
// Savepoint s1 = conn.setSavepoint("s1");
// ...
// 归滚到安全点
// conn.rollback(s1);
```

3.  提交

```java
conn.commit();
```

#### JDBC批处理

一次请求，执行多条语句，使用批处理效率更高

1.  Statement的批处理

```java
Statement stmt = conn.createStatement(sql);
conn.setAutoCommit(false);

stmt.addBatch(INSERT_SQL);

stmt.addBatch(INSERT_SQL_2);

int[] count = stmt.executeBatch();

conn.commit();
```

2.  PreparedStatement的批处理

```java
conn.setAutoCommit(false);
String sql = "insert into user(name) values(?)";
PreparedStatement pstmt = conn.prepareStatement(sql);

pstmt.setString(1, "xiaohong");
pstmt.addBatch();

pstmt.setString(1,"xiaolei");
pstmt.addBatch();

conn.commit();
```

### 数据库连接池

连接池：系统初始化，将连接作为对象存储在内存中，当有用户需要连接数据库时，第一时间不是创建连接，而是查看连接池是否由空闲的连接，如果有直接使用。使用连接完毕，不是马上关闭资源，而是暂时放回连接池，以便下一次请求使用。

连接池几个重要的参数：

1.  初始连接数:连接池启动时的连接数
2.  最小连接数:不管释放空闲连接，数据库至少保持的连接数
3.  最大连接数:大于它的连接排队等待
4.  最大等待时间:池中没有可用连接时，连接池等待连接回归的最大时间

有三个常用的连接池

1.  c3p0
2.  druid
3.  HikariCP

#### c3p0

首先，导入数据库驱动相关的jar包和c3p0的jar包.
例如：
mysql-connector-java-5.0.8.jar
c3p0-0.9.1.2.jar

c3p0要使用一个默认的配置文件c3p0-config.xml:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<c3p0-config>
    <!-- 默认配置，如果没有指定则使用这个配置 -->
    <default-config> <!-- 基本配置 -->
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/lxq?serverTimezone=UTC</property>
        <property name="user">root</property>
        <property name="password">password</property>
        <!--扩展配置--> <!-- 连接超过30秒报错-->
        <property name="checkoutTimeout">30000</property>
        <!--30秒检查空闲连接 -->
        <property name="idleConnectionTestPeriod">30</property> <!-- 30秒不使用丢弃-->
        <property name="initialPoolSize">10</property>
        <property name="maxIdleTime">30</property>
        <property name="maxPoolSize">100</property>
        <property name="minPoolSize">10</property>
        <property name="maxStatements">200</property>
    </default-config>
    <!-- 命名的配置 -->
    <named-config name="abc">
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/lxq?serverTimezone=UTC</property>
        <property name="user">root</property>
        <property name="password">111</property>
        <!-- 如果池中数据连接不够时一次增长多少个 -->
        <property name="acquireIncrement">5</property>
        <property name="initialPoolSize">20</property>
        <property name="minPoolSize">10</property>
        <property name="maxPoolSize">40</property>
        <property name="maxStatements">20</property>
        <property name="maxStatementsPerConnection">5</property>
    </named-config>
</c3p0-config>
```

加载配置文件


```java
// 加载c3p0-config.xml中的default-config
ComboPooledDataSource dataSource = new ComboPooledDataSource();
// 加载c3p0-config.xml中的named-config
// ComboPooledDataSource dataSource = new ComboPooledDataSource("abc");

```

从数据源中获取连接

```java
Connection conn = dataSource.getConnection();
```

其他与JDBC操作无差别

#### druid

首先，同样导入数据库驱动相关的jar包和druid的jar包.
例如：
mysql-connector-java-5.0.8.jar
druid-1.0.9.jar

需要数据库配置相关的properties后缀的文件，比如db.properties

```java
# 驱动类名
driverClassName = com.mysql.cj.jdbc.Driver
# 数据库URL
url = jdbc:mysql://localhost:3306/lxq?serverTimezone=UTC
# 数据库用户名
username = root
# 数据用户密码
password = password
# 初始连接数
initialSize=5
# 最大活跃连接数
maxActive = 10
# 最小空闲连接数
minIdle = 5
# 最大等待连接时间，单位毫秒
maxWait = 3000
# 是否测试空闲连接
testWhileIdle = false
```

```java
InputStream in = JDBCUtils.class.getClassLoader().getResourceAsStream("db.properties");
Properties properties = new Properties();
properties.load(in);
DruidDataSource dataSource = DruidDataSourceFactory.createDataSource(properties);
```