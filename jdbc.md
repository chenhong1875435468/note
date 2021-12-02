# JDBC

Java数据库连接，（Java Database Connectivity，简称JDBC）是Java语言中用来规范客户端程序如何来访问数据库的应用程序接口，提供了诸如查询和更新数据库中数据的方法。JDBC也是Sun Microsystems的商标。我们通常说的JDBC是面向关系型数据库的。

## `导包`



java.sql
javax.sql
导入mysql-connector-java-5.1.49
在idea中点击flie-projectstructure

modules-dependencise-加号-jar or directories

选择mysql-connector-java-5.1.49-bin.jar

建立测试库
`CREATE DATABASE jdbcStudy CHARACTER SET utf8 COLLATE utf8_general_ci;`

`USE jdbcStudy;`

`CREATE TABLE users(`
`id INT PRIMARY KEY,`
`NAME VARCHAR(40),`
`PASSWORD VARCHAR(40),`
`email VARCHAR(60),`
`birthday date`
`);`

`INSERT INTO users(id,NAME,PASSWORD,email,birthday)`
`VALUES(1,'zhansan','123456','zs@sina.com','1980-12-04'),`
`(2,'lisi','123456','lisi@sina.com','1981-12-04'),`
`(3,'wangwu','123456','wangwu@sina.com','1979-12-04')`



## `jdbc测试`

步骤：

加载驱动
链接数据库DriverManager
获得执行sql的对象 statement
获得返回的结果集
施放链接
注意：报错可能原因：

&没有替换为&amp
useSSL没有放在最前
上述两点改掉一点就正常了，也不知道为什么

```java
import java.sql.*;

public class test1 {
    public static void main(String[] args) throws Exception {
        //加载驱动
        Class.forName("com.mysql.jdbc.Driver");
        //用户信息和url
        String url = "jdbc:mysql://localhost:3306/jdbcstudy?		 useSSL=false&useUnicode=true&characterEncoding=utf8";
        String username="root";
        String password="123456";
        //链接成功 数据库对象
        Connection connection = DriverManager.getConnection(url,username,password);
        //执行SQL的对象
        Statement statement = connection.createStatement();
        //执行SQL的对象 去执行SQL 可能返回结果
        String sql="select * from users";
        ResultSet resultSet =  statement.executeQuery(sql);//返回的结果集
        while (resultSet.next()){
            System.out.println("id="+resultSet.getObject("id"));
            System.out.println("name="+resultSet.getObject("name"));
        }
        //施放链接
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```
解释

~~~java
---DriverManager---
//DriverManager.registerDriver(new com.mysql.jdbc.Driver());
Class.forName("com.mysql.jdbc.Driver");

String url = "jdbc:mysql://localhost:3306/jdbcstudy?useSSL=false&useUnicode=true&characterEncoding=utf8";
//mysql--3306
// 协议://主机地址:端口号/数据库名?参数1&参数2...
//oracle--1521
//jdbc:oracle:thin:@localhost:1521:sid

Connection
//链接成功 数据库对象 connection代表数据库
Connection connection = DriverManager.getConnection(url,username,password);
connection.rollback();
connection.rollback();
connection.setAutoCommit();

Statement
        statement.executeQuery();//查询操作 返回结果集ResultSet
        statement.execute();//可以执行任何sql
        statement.executeUpdate();//更新 插入 删除都是用这个，返回受影响的行数

ResultSet
//不知道类型就用Object
resultSet.getObject();
//知道类型可以直接使用对应类型获取
resultSet.getString();
resultSet.getInt();
resultSet.getFloat();
resultSet.getDouble();
resultSet.next();//移动到下一行数据
resultSet.beforeFirst();//移动到最前
resultSet.afterLast();//移动到最后
resultSet.previous();//移动到前一行
resultSet.absolute(i);//移动到第i行
~~~


代码实现
工具类
//db.properties存储信息降低耦合
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/jdbcstudy?useSSL=false&useUnicode=true&characterEncoding=utf8
username=root
password=123456



```java
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

public class JdbcUtils {
    private static  String driver=null;
    private static  String url=null;
    private static  String username=null;
    private static  String password=null;
    static {
        try {
            InputStream in = JdbcUtils.class.getClassLoader().getResourceAsStream("db.properties");       
       Properties properties=new Properties();
        assert in != null;
        properties.load(in);
        driver=properties.getProperty("driver");
        url=properties.getProperty("url");
        username=properties.getProperty("username");
        password=properties.getProperty("password");
        //驱动只用加载一次
        Class.forName(driver);

    }catch (Exception e){
        e.printStackTrace();
    }
}

//获取连接
public static Connection getConnection() throws SQLException {
    return DriverManager.getConnection(url,username,password);
}
//施放资源
public  static void release(Connection connection, Statement statement, ResultSet resultSet) throws Exception {
    if (resultSet!=null){
        resultSet.close();
    }
    if (statement!=null){
        statement.close();
    }
    if (connection!=null){
        connection.close();
    }
}
```
}



操作

~~~java
import utils.JdbcUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;

public class Test2 {
    public static void main(String[] args) throws Exception {
        Connection connection=null;
        Statement statement=null;
        ResultSet resultSet=null;
        try {
            connection= JdbcUtils.getConnection();
            statement=connection.createStatement();
            String sql="insert into users(id,name,password,email,birthday)" +
                    "values(100,'tzt','123456','123456@qq.com','1998-08-08');";
            int i=statement.executeUpdate(sql);
            if (i>0){
                System.out.println("插入成功！");
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,statement,resultSet);
        }
    }
}
~~~





## `SQL注入的问题`

SQL注入即是指web应用程序对用户输入数据的合法性没有判断或过滤不严，攻击者可以在web应用程序中事先定义好的查询语句的结尾上添加额外的SQL语句，在管理员不知情的情况下实现非法操作，以此来实现欺骗数据库服务器执行非授权的任意查询，从而进一步得到相应的数据信息。

sql存在漏洞，会被攻击导致数据泄露
比如登录业务中，需要查询账号密码所对应的用户（比对），可能用到如下sql语句：

select * from users where name='name' and password ='password'

其中name和password两个变量都是用户所传入的数据
如果用户构造合适的输入，比如：

String name=" ' or  1=1 -- ";
String password ="12412r1";//password在此例中的值不重要


那么如上sql语句拼接成了：

select * from users where name='  ' or  1=1 -- password ='12412r1'

就可以匹配到表中所有用户的信息

PreparedStatement
可以防止SQL注入，而且效率更高

~~~java
import utils.JdbcUtils;

import java.sql.*;

public class Test2 {
    public static void main(String[] args) throws Exception {
        Connection connection=null;
        PreparedStatement statement=null;
        ResultSet resultSet=null;
        try {
            connection= JdbcUtils.getConnection();
            //？占位符
            String sql="insert into users(id,name,password,email,birthday)" +
                    "values(?,?,?,?,?);";
            //和Statement的区别！！！！！！！！！
            statement=connection.prepareStatement(sql);
            //手动给参数赋值
            statement.setInt(1,99);
            statement.setString(2,"hhh");
            statement.setString(3,"12312313");
            statement.setString(4,"15612318@qq.com");
            statement.setDate(5,new java.sql.Date(new Date(1231).getTime()));
            int i=statement.executeUpdate();
            if (i>0){
                System.out.println("插入成功！");
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            JdbcUtils.release(connection,statement,resultSet);
        }
    }
}
~~~




IDEA链接数据库
点击窗口右侧database加号，选择MySQL


填写数据库链接信息
测试链接时有可能提示要下包，按照提示自动下载即可


然后apply OK就完成了
如图所示即为成功，不能有红线


可能出现的错误：

Server returns invalid timezone. Go to ‘Advanced’ tab and set ‘serverTimezone’ property manually.
临时解决：在MySQL登录后输入：
set global time_zone = '+8:00';

这种方法在重启MySQL后失效，需要重新设置

永久解决：在IDEA中，链接数据库的界面中高级设置，把时区设置为东八区



```java
      
     JDBC操作事务
    import utils.JdbcUtils;

    import java.sql.Connection;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.SQLException;

    public class Test3 {
        public static void main(String[] args) throws Exception {
            Connection connection=null;
            PreparedStatement statement=null;
            ResultSet resultSet=null;
            try{
                connection= JdbcUtils.getConnection();
                connection.setAutoCommit(false);//关闭自动提交 开启事务
                String sql1="update users set name='hhh' where name='hyx';";
                String sql2="update users set name='tzt' where name='ttt';";
                int i=1/0;
                statement=connection.prepareStatement(sql1);
                statement.executeUpdate();
                statement=connection.prepareStatement(sql2);
                statement.executeUpdate();//业务完毕提交事务
            connection.commit();
            System.out.println("成功");

        }catch (Exception e){
            e.printStackTrace();
            //如果失败则回滚
            connection.rollback();
        }finally {
            JdbcUtils.release(connection,statement,resultSet);
        }
    }
    }
```


数据库连接池
数据库链接–执行完毕–施放十分消耗资源
池化技术：准备一些预先的资源，过来就链接预先准备好的

若常用连接数10个
最小连接数：10个即可
最大连接数：15 业务最高承载上限，超过此值则排队等待
等待超时：等待时间超过一定值直接失败

编写连接池：实现DataSource接口

开源数据源实现

DBCP
C3P0
Druid：阿里巴巴

使用了数据库连接池之后，我们在项目开发中就不需要编写链接数据库的代码了！

DBCP
需要导入的包：

commons-dbcp-1.4
commons-pool-1.6

配置文件
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/jdbcstudy?useSSL=false&useUnicode=true&characterEncoding=utf8
username=root
password=123456

工具类

```java
package utils;

import org.apache.commons.dbcp.BasicDataSourceFactory;

import javax.sql.DataSource;
import java.io.InputStream;
import java.sql.*;
import java.util.Properties;

public class JdbcUtils_DBCP {
    private static DataSource dataSource=null;
static {
    try {
        InputStream in = JdbcUtils.class.getClassLoader().getResourceAsStream("db.properties");
        Properties properties=new Properties();
        assert in != null;
        properties.load(in);

        //创建数据源 工厂模式→创建
        dataSource=BasicDataSourceFactory.createDataSource(properties);
         }catch (Exception e){
        e.printStackTrace();
    }
}

//获取连接
public static Connection getConnection() throws Exception {
    return dataSource.getConnection();
}

//施放资源
public  static void release(Connection connection, Statement statement, ResultSet resultSet) throws Exception {
    if (resultSet != null) {
        resultSet.close();
    }
    if (statement != null) {
        statement.close();
    }
    if (connection != null) {
        connection.close();
    }

}
}
```

测试

~~~java
import utils.JdbcUtils_DBCP;

import java.sql.Connection;
import java.sql.Date;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class Test4 {
    public static void main(String[] args) throws Exception {
        Connection connection=null;
        PreparedStatement statement=null;
        ResultSet resultSet=null;
        try {
            connection= JdbcUtils_DBCP.getConnection();
            //？占位符
            String sql="insert into users(id,name,password,email,birthday)" +
                    "values(?,?,?,?,?);";
            //和Statement的区别！！！！！！！！！
            statement=connection.prepareStatement(sql);
            //手动给参数赋值
            statement.setInt(1,49);
            statement.setString(2,"few");
            statement.setString(3,"12312313");
            statement.setString(4,"15612318@qq.com");
            statement.setDate(5,new java.sql.Date(new Date(1231).getTime()));
            int i=statement.executeUpdate();
            if (i>0){
                System.out.println("插入成功！");
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            JdbcUtils_DBCP.release(connection,statement,resultSet);
        }
    }
}
~~~