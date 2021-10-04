# GUI+java+MySQL|案例简单的学生管理系统源码全部奉上，求各位大佬点评

想要复制源码直接使用的同学，记得修改jdbc的数据，并针对自己的数据库进行一些语句的修改哦。

因为是第一次实现，所以很多地方没有做好，欢迎大家修改，并增加功能。

挂着，等阅读量上去可以出详细教程。

## 启动

```java
import java.sql.SQLException;

public class MainLoad {

    public static void main(String[] args) throws ClassNotFoundException,SQLException{
        new mainFrame().loadJFrame();
        new JDBCworker().close();

    }

}
```

## 主界面（查询）

```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.sql.ResultSet;
import java.sql.SQLException;

class mainFrame extends JFrame{
    JButton button_refer,button_increase,button_alter,button_delet;
    JTextField jTextname;
    static JTextArea ShowArea;
    JPanel Spanel,Xpanel;
    JScrollPane scroll;//滚动窗口
    JLabel label;
    static String sql;


    //一、设置窗口
    //设置大小，布局，背景颜色，是否可见，关闭事件
    public void loadJFrame(){
        super.setTitle("学生信息管理系统");
        setBounds(60,100,188,108);
        setLayout(new GridLayout(4,1));
        setVisible(true);
        setBackground(Color.BLUE);
        setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);

        //二、设置窗口的组件
        //按钮、标签、面板、文本框
        button_refer = new JButton("查询");
        button_increase = new JButton("增加");
        button_alter = new JButton("修改");
        button_delet = new JButton("删除");
        jTextname = new JTextField(10);//查询面板
        ShowArea = new JTextArea();//结果显示面板
        Spanel = new JPanel();
        Xpanel = new JPanel();
        scroll = new JScrollPane(ShowArea);
        label = new JLabel("ps：\n输入想要查询的姓名，若无结果，则你的查找不存在。\n输入\t*\t得到所有数据\n=======\n");

        //三、组件布局设置
        Spanel.setLayout(new GridLayout(1,3));
        Xpanel.setLayout(new GridLayout(1,5));
        Spanel.add(jTextname);
        Spanel.add(button_refer);
        Xpanel.add(button_delet);Xpanel.add(button_alter);Xpanel.add(button_increase);
        add(Spanel,BorderLayout.NORTH);
        add(scroll,BorderLayout.SOUTH);
        add(Xpanel,BorderLayout.CENTER);
        add(label,BorderLayout.CENTER);

        //按钮事件设置
        //查询
        button_refer.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                ShowArea.setText("");
                String name = jTextname.getText().trim();//trim将内容以字符串的形式返回
                JDBCworker jdbCworker = new JDBCworker();
                if(name.equals("*")){
                    sql = "select * from ybstudent";
                }else {
                sql = "select * from ybstudent where name='"+name+"'";}
                ResultSet st = jdbCworker.Refer(sql);
                while (true){
                    try {
                        if (!st.next()){
                            break;
                        }
                        ShowArea.append("姓名：" + st.getString(1) +"\n");
                        ShowArea.append("年龄：" + st.getString(2) +"\n");
                        ShowArea.append("编号：" + st.getString(3) +"\n=======\n");
                    } catch (SQLException throwables) {
                        throwables.printStackTrace();
                    }

                }
                
            }
        });

        button_increase.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                new IncreaseFrame().loadJFrame();
            }
        });

        button_delet.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                new DeleteFrame().loadJFrame();
            }
        });

        button_alter.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                new AlterFrame().loadJFrame();
            }
        });
}
}
```



## 添加类界面

```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

class IncreaseFrame extends JFrame {
    JButton button1;
    JTextField jTextname,jTextage;
    JTextArea ShowArea;
    JPanel Spanel, Xpanel;
    JLabel nameLabel,ageLabel;


    //一、设置窗口
    //设置大小，布局，背景颜色，是否可见，关闭事件
    public void loadJFrame() {
        //窗口设置
        setBounds(200, 200, 188, 108);
        setLayout(new GridLayout(4, 4));
        setVisible(true);
        setBackground(Color.BLUE);
        setDefaultCloseOperation(WindowConstants.HIDE_ON_CLOSE);

        //设置窗口的组件
        nameLabel = new JLabel("姓名");
        ageLabel = new JLabel("年龄");
        jTextname = new JTextField(10);
        jTextage = new JTextField(10);
        button1 = new JButton("添加");
        Spanel = new JPanel(new GridLayout(1,2));
        Xpanel = new JPanel(new GridLayout(1,2));

        //组件添加
        add(Spanel,new GridLayout(2,1));
        add(Xpanel,new GridLayout(2,2));
        add(button1,new GridLayout(3,1));
        Spanel.add(nameLabel,new GridLayout(1,1));
        Spanel.add(jTextname,new GridLayout(1,2));
        Xpanel.add(ageLabel,new GridLayout(1,1));
        Xpanel.add(jTextage,new GridLayout(1,2));

        //事件添加
        button1.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String name = jTextname.getText().trim();
                String age = jTextage.getText().trim();
                String sql = "INSERT INTO ybstudent(`name`,age) VALUES ('"+ name +"','" + age + "');";
                if(name.equals("")){
                    new mainFrame().ShowArea.append("\n===========\n添加失败，因为姓名不能为空\n===========\n");}
                else {
                JDBCworker jdbCworker = new JDBCworker();
                jdbCworker.DAI(sql);
                jTextname.setText("");
                jTextage.setText("");
                new mainFrame().ShowArea.append("\n===========\n添加了一条关于"+ name +"的信息\n===========\n");}
            }
        });


    }
}
```



## 删除类界面

```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class DeleteFrame extends JFrame{

    JButton button1;
    JTextField jTextname;
    JTextArea ShowArea;
    JPanel Spanel, Xpanel;
    JLabel nameLabel;

    //一、设置窗口
    //设置大小，布局，背景颜色，是否可见，关闭事件
    public void loadJFrame() {
        //窗口设置
        setBounds(200, 200, 188, 108);
        setLayout(new GridLayout(4, 4));
        setVisible(true);
        setBackground(Color.BLUE);
        setDefaultCloseOperation(WindowConstants.HIDE_ON_CLOSE);

        //设置窗口的组件
        nameLabel = new JLabel("姓名");
        jTextname = new JTextField(10);
        button1 = new JButton("删除");
        Spanel = new JPanel(new GridLayout(1,2));

        //组件添加
        add(Spanel,new GridLayout(2,1));
        add(button1,new GridLayout(3,1));
        Spanel.add(nameLabel,new GridLayout(1,1));
        Spanel.add(jTextname,new GridLayout(1,2));

        //事件添加
        button1.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String name = jTextname.getText().trim();
                String sql = "DELETE FROM ybstudent WHERE `name`='" + name + "';";
                int n = JOptionPane.showConfirmDialog(null,"是否修改该信息","修改操作确认",JOptionPane.YES_NO_OPTION);
                if(n == JOptionPane.YES_OPTION){
                if(name.equals("")){
                    new mainFrame().ShowArea.append("\n===========\n删除失败，不存在空姓名数据\n===========\n");}
                else {
                    JDBCworker jdbCworker = new JDBCworker();
                    jdbCworker.DAI(sql);
                    jTextname.setText("");
                    new mainFrame().ShowArea.append("\n===========\n删除了了关于"+ name +"的所有信息\n===========\n");}
            }else {
                    jTextname.setText("");
                }
            }
        });


    }
}
```



## 修改类界面

```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

class AlterFrame extends JFrame {
    JButton button1;
    JTextField jTextname,jTextage,JJname;
    JPanel Spanel, Xpanel,Ypanle;
    JLabel nameLabel,ageLabel,JJLable;


    //一、设置窗口
    //设置大小，布局，背景颜色，是否可见，关闭事件
    public void loadJFrame() {
        //窗口设置
        setBounds(200, 200, 188, 108);
        setLayout(new GridLayout(4, 4));
        setVisible(true);
        setBackground(Color.BLUE);
        setDefaultCloseOperation(WindowConstants.HIDE_ON_CLOSE);

        //设置窗口的组件
        JJLable = new JLabel("修改数据的姓名为");
        nameLabel = new JLabel("姓名");
        ageLabel = new JLabel("年龄");
        JJname = new JTextField(10);
        jTextname = new JTextField(10);
        jTextage = new JTextField(10);

        button1 = new JButton("修改");
        Spanel = new JPanel(new GridLayout(2,2));
        Xpanel = new JPanel(new GridLayout(2,2));
        Ypanle = new JPanel(new GridLayout(2,2));

        //组件添加
        add(Ypanle,new GridLayout(2,1));
        add(Spanel,new GridLayout(2,2));
        add(Xpanel,new GridLayout(2,3));
        add(button1,new GridLayout(3,1));
        Spanel.add(nameLabel,new GridLayout(1,1));
        Spanel.add(jTextname,new GridLayout(1,2));
        Xpanel.add(ageLabel,new GridLayout(1,1));
        Xpanel.add(jTextage,new GridLayout(1,2));
        Ypanle.add(JJLable,new GridLayout(1,1));
        Ypanle.add(JJname,new GridLayout(1,2));

        //事件添加
        button1.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String name1 = JJname.getText().trim();
                String name2 = jTextname.getText().trim();
                String age = jTextage.getText().trim();

                String sql1 = "DELETE FROM ybstudent WHERE `name`='" + name1 + "';";
                String sql2 = "INSERT INTO ybstudent(`name`,age) VALUES ('"+ name2 +"','" + age + "');";
                int n = JOptionPane.showConfirmDialog(null,"是否修改该信息","修改操作确认",JOptionPane.YES_NO_OPTION);
                if(n == JOptionPane.YES_OPTION){
                if(name1.equals("")){
                    new mainFrame().ShowArea.append("\n===========\n修改失败，不存在空姓名数据\n===========\n");}
                else {
                    JDBCworker jdbCworker = new JDBCworker();
                    jdbCworker.DAI(sql1);
                    jdbCworker.DAI(sql2);
                    jTextname.setText("");
                    JJname.setText("");
                    jTextage.setText("");
                    new mainFrame().ShowArea.append("\n===========\n修改了关于"+ name1 +"的信息\n===========\n");}
            }else {
                    jTextname.setText("");
                }
            }
        });
    }
}
```



## JDBC连接工具类

```java
import java.sql.*;
import java.sql.PreparedStatement;


class JDBCworker {
        //预备调用所需要的信息
        String url = "jdbc:mysql://localhost:3306/yiban?useUnicode=true&characterEncoding=utf8&useSSL=true";
        String username = "root";
        String password = "123456";
        static String BC1 = "操作成功";
        PreparedStatement Pstatement = null;
        Connection connection = null;
        ResultSet resultSet = null;

        //查询方法
        public ResultSet Refer(String sql){

            try {
                //连接驱动
                Class.forName("com.mysql.cj.jdbc.Driver");
                //利用驱动建立sql的对象connect
                connection = DriverManager.getConnection(url, username, password);
                //预编译查询语句
                Pstatement = connection.prepareStatement(sql);
                resultSet = Pstatement.executeQuery();

            }catch (Exception e){
                e.printStackTrace();
            }

            return resultSet;
        }

    public void DAI(String sql) {

        try {
            //连接驱动
            Class.forName("com.mysql.cj.jdbc.Driver");
            //利用驱动建立sql的对象connect
            connection = DriverManager.getConnection(url, username, password);
            //预编译查询语句
            Pstatement = connection.prepareStatement(sql);
            //执行增删改语句
            int DD = Pstatement.executeUpdate();
            if(DD != 1) BC1 = "操作失败";

        }catch (Exception e){
            System.out.println("数据库连接或语句执行出现错误");
        }finally {
            this.close();
        }
    }

        public void close() {
            try {
                if(resultSet != null) {
                    Pstatement.close();
                    connection.close();
                    resultSet.close();
                }
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
}

```



