# 网络编程 `交流性`

## 一、概念性 `java万物皆对象`

### （1）网络编程的要素

1、通信双方的地址：IP和端口号

2、网络通信协议：规则

## 二、ip

#### 1、ip地址：InetAddress

唯一定位一台网络上的计算机，127.0.0.1为本机localhost

#### 2、ip分类

ipv4：4个字节组成，0~255

ipv6：128位，8个无符号整数表示

公网：互联网

私网：局域网

#### 3、域名：记忆ip

IP：www.jd.com

#### 4、ip的类

```java
//测试ip
public class address {
    public static void main(String[] args) {
        try {
            //查询本机地址
            InetAddress inetAddress = InetAddress.getByName("127.0.0.1");
            System.out.println(inetAddress);

            InetAddress inetAddress2 = InetAddress.getByName("localhost");
            System.out.println(inetAddress2);

            InetAddress inetAddress3 = InetAddress.getLocalHost();
            System.out.println(inetAddress3);


            //查询网址ip地址
            InetAddress inetAddress1 = InetAddress.getByName("www.baidu.com");
            System.out.println(inetAddress1);


            //常用方法
            System.out.println(inetAddress1.getCanonicalHostName());//规范的名字
            System.out.println(inetAddress1.getHostAddress());//ip
            System.out.println(inetAddress1.getHostName());//获得域名，或者自己电脑的名字

        } catch (UnknownHostException e) {
            e.printStackTrace();
        }
    }
}
```

## 三、端口port

#### 1、概念

端口表示计算机上一个程序的进程。

ip是楼，端口是房间，资源是家具。

不同的进程有不同的端口号，用来区分软件，0~65535

分为TCP/UDP，单个协议下，端口号不能冲突

#### 2、分类

a.公有端口 0~1023

HTTP:80

HTTPS:443

FTP:21

b.程序注册端口：1024~49151,分配用户或者程序

Yomcat:8080

MySQL：3306

Oracle：1521

c.动态、私有：49152~65535

```bash
#查看所有端口
netstat -ano
#查看指定端口
netstat -ano|findstr "3306"
```

#### 3、测试端口

```java
public class address {

    public static void main(String[] args) {
        InetSocketAddress inetSocketAddress = new InetSocketAddress("127.0.0.1",3306);
        System.out.println(inetSocketAddress);

        System.out.println(inetSocketAddress.getAddress());
        System.out.println(inetSocketAddress.getHostName());
        System.out.println(inetSocketAddress.getPort());
        
    }

}
```



## 四、通信协议

#### 1、TCP/IP协议族

TCP:用户传输协议 `打电话，连接稳定，三次问候才握手，四次问候才分手，明确客户端和服务端界限`

UDP：用户数据报协议 `发短信，不连接不稳定，不明确客户度与服务端界限`

IP：网络互连协议

#### 2、协议分层

应用层、传输层、网络层、数据链路层



## 五、TCP实现聊天



















