# JDBC

## 一、概念

1、数据库驱动：程序连接数据库的中介

2、JDBC：简化对数据库的操作，提供了java操作数据库的规范。



## 二、相关对象或概念解释

==DriverManager== `加载驱动`

```java
//DriverMAnager.registerDriver(new Drinver());
Class.forName("com.mysql.cj.jdbc.Driver");
```



==URL==

```java
//jdbc:mysql://localhost:3306/study?useUnicode=true&characterEncoding=utf8&useSSL=true
//协议 jdbc:mysql://主机：端口/数据库名？参数&
```



==Connection== `数据库的对象，用驱动去连接`

```java
Connection connection = DriverManager.getConnection(url,username,password);
```



==Statement== `数据库的执行对象-数据库的手下，用sql对象Connection去创造`

```java
Statement statement = connection.createStatement();

statement.executeQuery("sql语句");//执行发送SQL查询语句，返回查询结果
statement.executeUpdate("sql语句");//执行发送SQL更新、插入、删除语句，返回受影响的行数
statement.execute("sql语句");//执行发送所有的SQL语句
```



==ResultSet== `查询结果集，用来封装statement返回的结果`

```java
ResultSet resultSet = statement.executeQuery("sql语句");//将statement返回的结果封装
resultSetgetObject();//获得行值，可将Object换成相应的类型
resultSet.Next();//移动到下一行数据

resultSet.Previous();//移动到上一行数据
resultSet.beforeFirst();//移动到最前面
```



==释放连接== `因为sql对象，执行对象，数据库是连接使用的，不释放会占用计算机资源`

```sql
resultSet.close();
statement.close();
connection.close();
```





## 三、第一个JDBC程序

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.SQLException;

public class JDBCtest {
    public static void main(String[] args)  throws ClassNotFoundException,SQLException{
        //1、加载驱动
        Class.forName("com.mysql.cj.jdbc.Driver");

        //2、预备用户信息
        String url = "jdbc:mysql://localhost:3306/school?useUnicode=true&characterEncoding=utf8&useSSL=true";
        String username = "root";
        String password = "123456";

        //3、连接驱动，建立sql的对象connection
        Connection connection = DriverManager.getConnection(url,username,password);

        //4、建立sql的执行对象
        Statement statement = connection.createStatement();

        //5、建立结果对象，返回sql执行对象执行sql语句
        String sql = "SELECT * FROM student";
        ResultSet resultSet = statement.executeQuery(sql);

        while (resultSet.next()){
            System.out.println("name= "+ resultSet.getObject("name"));
            System.out.println("email= "+resultSet.getObject("email"));
            System.out.println("======================");
        }
        //6、释放连接
        resultSet.close();
        statement.close();
        connection.close();
    }
}
```



## 四、Statement对象详解

JDBC中的statement对象，用于向数据库发送SQL语句，只需要通过这个对象向数据库发送增删改查语句即可。

Statement对象的executeUpdate方法，用于向数据库发送增、删、改语句，执行后返回一个整数（增、删、改语句导致数据库中数据变化的行数）。

Statement.eaecuteQuery("sql语句")方法用于向数据库发送查询语句，创建存储对象ResultSet封装其返回的查询结果。

>增、删、改、查语句

```java
eaecuteQuery("sql查询语句")//增、删、改
Statement statement = connection.createStatement;
String sqlcreate = "insert into 表名(列名)... values(值)";
String sqldelete = "delete from 表名(列名) where 条件语句";
String sqlupdate = "update 表名 set name='' where name=''";
int num = statement.executeUpdate(sqlxxxx);
if(num>0){
	System.out.print("执行成功")
}

String sqlread = "表名(列名)... values(值)";
ResultSet resultset = statement.eaecuteQuery(sqlread);
while(resultset.next){
	resultSet.getobject();//获取值
}
```



## 五、提取工具类

### （1）工具类创建步骤

#### 1、书写db.properties文件储存用户信息

```java
driver = com.mysql.cj.jdbc.Driver
url = jdbc:mysql://localhost:3306/school?useUnicode=true&characterEncoding=utf8&useSSL=true
username = root
password = 123456
```

#### 2、书写工具类java文件

```java
import java.io.InputStream;
import java.sql.SQLException;
import java.util.Properties;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;


public class jdbcUtils {


    private static String driver = null;
    private static String url = null;
    private static String username = null;
    private static String password = null;

    static {

        try{
            //1、读取properties中的内容
            InputStream in = jdbcUtils.class.getClassLoader().getResourceAsStream("db.properties");
            Properties properties = new Properties();
            properties.load(in);

            driver = properties.getProperty("driver");
            url = properties.getProperty("url");
            username = properties.getProperty("username");
            password = properties.getProperty("password");

            //2、加载驱动
            Class.forName(driver);

        }catch (Exception e){
            e.printStackTrace();
        }
    }

    //3、连接数据库对象
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url,username,password);
    }
    //4、释放资源
    public static void release(Connection conn,Statement st,ResultSet rs) {
        if(rs!=null) {
            try {
                st.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if(st!=null) {
            try {
                st.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
            if(conn!=null){
                try {
                    st.close();
                }catch (SQLException e){
                    e.printStackTrace();
                }
        }
    }
}

```

### （2）使用工具类

```java
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class Test {
    public static void main(String[] args){

        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet = null;

        try {
            connection = jdbcUtils.getConnection();//获得数据库对象
            statement = connection.createStatement();//获得SQL执行对象

            String sql = "INSERT INTO student VALUES (28,'pp','123456','男','2001-06-06','153@qq.com')";

            int i = statement.executeUpdate(sql);
            if (i>0){
                System.out.println("执行成功");
            }
            /*resultSet = statement.executeQuery(sql为查询语句)
            while(resultSet.next()){
        		resultSet.getObject();
         }
         */

        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }finally {
            jdbcUtils.release(connection,statement,resultSet);
        }
    }
}
```



## 六、PreparedStatement对象 ` 防止SQL注入，效率更高`

防注入机制：把传递的参数当作字符，忽略输入的转义字符

### 区别:

1、需要预编译SQL语句

2、手动参数赋值

3、执行语句无参数

```java
//对象声明不同
PreparedStatement st = null;
//书写sql语句置换值为?
String sqldelete = "delete from student where cord=?";
//预编译sql语句，不执行
st = conn.prepareStatement(sql);
//手动给参数赋值
st.setInt(1,38);//id
//执行语句无参数
int i = st.executeUpdate();//rs = st.executeQuery();
```



### 例子：

```java
package PLORD;
import PLORD.utils.jdbcUtils;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;


public class prepareST {
    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement st = null;



        try {
            conn = jdbcUtils.getConnection();

            //插入
            String sql = "insert into `student`(cord,`name`,`pwd`,`sex`,`birthday`,`email`) values (?,?,?,?,?,?)";
            st = conn.prepareStatement(sql);//预编译sql语句，不执行
            //手动给参数赋值
            st.setInt(1,38);//id
            st.setString(2,"九九");
            st.setString(3,"123456");
            st.setString(4,"妖");
            //注意sql.Date
            st.setDate(5,null);
            st.setString(6,"16013");
            //执行语句
            int i = st.executeUpdate();
            if (i>0){
                System.out.println("插入成功");
            }


            //删除
            String sqldelete = "delete from student where cord=?";
            st = conn.prepareStatement(sqldelete);//预编译sql语句，不执行
            //手动给参数赋值
            st.setInt(1,36);//id
            //执行语句
            int n = st.executeUpdate();
            if (n>0){
                System.out.println("删除成功");
            }
			
            
            //查询
            String sql = "select  * from student";
            st = conn.prepareStatement(sql);//预编译sql语句，不执行
            //执行语句
            rs = st.executeQuery();
            while (rs.next()){
                System.out.println("查询成功");
            }


        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }


    }

}
```



## 七、事务

### 1、ACID原则

原子性：整体完成，部分不能完成

一致性：总数不变

隔离性：多个进程互不干扰

持久性：一旦提交不可逆



隔离性问题：

脏读：一个事务执行读取了另一个事物未提交的事务

不可重复读：在同一个事务内，重复读取表中的数据，表数据发生变化

虚读：在一个事务内，读取到插入数据，导致前后读取不一致



### 2、事务相关java语句

```java
//关闭自动提交 = 开启事务
conn.setAutoCommit(false);
//提交事务
conn.commit();
//事务回滚，一般在catch中，失败默认回滚
conn.rollback();
```



### 3、java模仿转账

```java
import PLORD.utils.jdbcUtils;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;


public class shiwu {

    public static void main(String[] args) {
        Connection conn = null;
        PreparedStatement preparedStatement = null;
        ResultSet resultSet = null;

        try {
            conn = jdbcUtils.getConnection();
            //开启事务
            conn.setAutoCommit(false);

            //事务双方
            String sql1 = "update account set money = money - 10 where name = 'A'";
            preparedStatement = conn.prepareStatement(sql1);
            preparedStatement.executeUpdate();

            String sql2 = "update account set money = money + 10 where name = 'B'";
            preparedStatement = conn.prepareStatement(sql2);
            preparedStatement.executeUpdate();

            //业务完毕提交事务
            conn.commit();
            System.out.println("操作成功");



        }catch (SQLException e){
            try {
                conn.rollback();
            }catch (SQLException e1){
                e1.printStackTrace();
            }
            e.printStackTrace();
        }finally {
            jdbcUtils.release(conn,preparedStatement,resultSet);
        }

    }
}

```











