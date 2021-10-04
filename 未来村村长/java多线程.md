# java多线程基础知识整理



## 一、简要概念

### （1）注意点

1、进程（Process）：动态概念，程序执行的一次过程

2、线程（Thread）：独立执行的路径。一个进程包含若干各线程，main（）是主线程，很多线程是模拟出来的，真正的多线程需要多个cpu。

3、核心概念：

a.程序运行时，即使没有自己创建线程，后台也会有多个线程，如主线程，gc线程；

b.main（）称为主线程，为系统的入口，用于执行整个程序；

c.一个进程中，若开辟了多个线程，线程的运行由调度器安排调度，调度器是与操作系统紧密相关的，先后顺序是不能认为的干预的；

d.对于同一份资源操作时，会出现资源抢夺的问题，需要加入并发控制；

e.线程会带来额外的开销，如cpu调度时间，并发控制开销；

f.每个线程在自己工作内存交互，内存控制不当会造成数据不一致。



### （2）线程五大状态

创建状态: `New` 

就绪状态: `Waiting` 

阻塞状态: `Timed_Waiting`

运行状态:`Runnable`

死亡状态：`Terminated`



## 二、线程创建

### （1）三种创建方式：

1、Thread class `重点`：继承Thread类

2、Runnable接口 `重点`：实现Runnable接口

3、Callable接口 `了解`：实现Callable接口



### （2）继承Thread类

#### 步骤

1、自定义线程类继承Thread类

2、重写run()方法，编写线程执行体

3、创建线程对象，调用start方法启动线程

```java
//创建线程方式一：继承Thread类，重写run()方法，调用start开启线程
public class Threadtest extends Thread {
    //run方法线程体
    @Override
    public void run() {
        for (int i1 = 0; i1 < 2000; i1++) {
            System.out.println("===="+i1);
        }
    }

    public static void main(String[] args) {
        //main主线程

        //创建一个线程对象
        Threadtest threadtest = new Threadtest();

        //调用start()方法开启线程
        threadtest.start();

        for (int i = 0; i < 2000; i++) {
            System.out.println("+++" + i);

        }
    }

}
```

#### 运行总计：

a.run方法体线程和主线程是交替运行的；

b.线程开启不一定立即执行，由cpu调度执行。

c.缺点：单继承具有局限性



### （2）实现Runnable接口

#### 步骤

1、自定义类实现Runnable接口

2、实现run()方法，编写线程执行体

3、使用代理Thread(自定义类)线程对象，调用start()方法启动线程

```java
//创建线程方式二：实现runnable接口，重写run方法，执行线程需要丢入runnable接口实现类
public class Threadtest  implements Runnable{
    //run方法线程体
    @Override
    public void run() {
        for (int i1 = 0; i1 < 2000; i1++) {
            System.out.println("===="+i1);
        }
    }


    public static void main(String[] args) {
        //创建runnable接口实现类对象
        Threadtest threadtest = new Threadtest();

        //创建一个线程对象，通过对象（代理）开启线程
        new Thread(threadtest).start();


        //等同于 new Thread(threadtest).start()

        for (int i = 0; i < 2000; i++) {
            System.out.println("+++" + i);

        }
    }

}
```



#### 龟兔赛跑案例

```java
public class Threadtest  implements Runnable {
    @Override
    public void run() {
        int i = 0;
        //模拟赛事过程
        while(i<=101) {
                //模拟跑步过程
                System.out.println(Thread.currentThread().getName() + "跑了》》》" + i++ + "米");

            //模拟冠军判断
            if(i==100){
                System.out.println("冠军 是"+Thread.currentThread().getName());
                System.exit(0);
            }

                //模拟兔兔睡觉觉
                if(Thread.currentThread().getName() == "兔兔" && i%10==0) {

                    try {
                        Thread.sleep(200);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }

        }
    }
    

    public static void main(String[] args) {
        //创建包含接口Runnable的对象
        Threadtest threadtest = new Threadtest();
        //创建Thread对象，代理执行threadtest
        new Thread(threadtest,"兔兔").start();
        new Thread(threadtest,"龟龟").start();

    }

}
```



#### 静态代理模式：`相同的接口，代理对象赋值参数为真实对象`

1、真实对象和代理对象都要实现同一个接口

2、代理对象必须代理真实角色

3、优点：代理对象用于进行更多处理，真实对象可以专注自己的事



#### 线程方法

| 方法            | 说明                                       |
| --------------- | ------------------------------------------ |
| setPriority（） | 更改线程优先级                             |
| sleep（）       | 让正在执行的线程体在指定毫秒数内休眠       |
| join（）        | 等待该线程终止                             |
| yield（）       | 暂停当前正在执行的线程对象，并执行其他线程 |
| interrupt（）   | 中断线程，不推荐使用该方法                 |
| isAlive（）     | 测试线程是否处于活动状态                   |



## 三、Lambda表达式 `简化代码`

### （1）函数式接口

1、定义：只包含唯一一个抽象方法的接口

2、作用：对于函数式接口，我们可以通过lambda表达式来创建该接口的对象

### （2）从类到lambda的简化

```java
public class Threadtest  {
    //静态内部类
    static class like1 implements Ilike{
        @Override
        public void lambda() {
            System.out.println("i like 2");
        }
    }

    public static void main(String[] args) {

        Ilike like = new like();
        like.lambda();

        like = new like1();
        like.lambda();


        //局部内部类
        class like2 implements Ilike{
            @Override
            public void lambda() {
                System.out.println("i like 3");
            }
        }
        like = new like2();
        like.lambda();

        //匿名内部类
        like = new Ilike(){
            @Override
            public void lambda() {
                System.out.println("i like 4");
            }
        };
        like.lambda();

        //lambda简化
        like = ()-> {
            System.out.println("i like 5");
        };
        like.lambda();
    }

}

//定义一个函数式接口
interface Ilike{
    void lambda();
}

//类
class like implements Ilike{
    @Override
    public void lambda() {
        System.out.println("i like 1");
    }
}

```



### （3）语法

>Lambda语句—接口为函数式接口：接口名 对象名 = 参数 ->{执行语句｝;

```
单行执行语句
XXX xxx = a-> System.out.println("xxx"+a);
xxx.X(0);

多行执行语句
XXX xxx = a-> {
System.out.println("xxx"+a)
System.out.println("xxx"+a)
System.out.println("xxx"+a)
};
xxx.X(0);

多个参数需要括号
XXX xxx = (a,b)->System.out.println("xxx"+a);
xxx.X(0,0);
```





## 四、线程常用方法

### （1）停止

建议使用标志符使线程停止

#### 1、具体步骤

a.设置标志位（线程属性）

```java
private boolean flag = true;
```

b.设置标志位转换方法

```java
public void stop(){this.flag = false;}
```

c.调用stop方法（一般加if判断）

```java
t.stop();
```



#### 2、测试运行案例

```java
public class Threadtest implements Runnable {
    //设置标志位
    private boolean flag = true;
    @Override
    public void run() {
        int i = 0;
        while(flag){
            System.out.println("run" + i++);
        }
    }

    //设置公开的方法停止线程，转换标志位
    public void stop(){
        this.flag = false;
    }

    public static void main(String[] args) {
        Threadtest t = new Threadtest();
        new Thread(t).start();

        for (int i = 0; i < 1000; i++) {
            System.out.println("main"+i);

            if(i==900){
                //调用stop方法切换标志位
                t.stop();
                System.out.println("run线程停止,主线程继续");
            }

        }
    }

}
```



### （2）休眠

#### 1、sleep()方法解析

sleep（200）表示当前线程阻碍200毫秒（1000毫秒=1秒），sleep存在异常InterruptedException，调用需要try/catch，sleep时间到达后被阻碍线程进入就绪状态，sleep可以模拟网络延时、倒计时等。

每个对象都有一把锁，sleep不会释放锁

#### 2、sleep语句

```java
 try {
        Thread.sleep(200);
      } catch (InterruptedException e) {
        e.printStackTrace();
      }
```

#### 3、案例：模拟倒计时

```java
public class Threadtest  {
    public static void main(String[] args) {
        timeDown(10);
    }


    public static  void timeDown(int times){

        while (true){
            
            try {
                Thread.sleep(1000);
            }catch (InterruptedException e){
                e.printStackTrace();
            }

            System.out.println("====="+times--+"=====");
            if (times==0){
                break;
            }
        }
    }
}
```



#### 案例：刷新系统时间

```java
public class Threadtest  {
    public static void main(String[] args) {
        //获取系统当前时间
        Date starttime = new Date(System.currentTimeMillis());

        //更新当前时间
        while(true){
            try {
                Thread.sleep(1000);
                System.out.println(new SimpleDateFormat("HH:mm:ss").format(starttime));
                starttime = new Date(System.currentTimeMillis());
            }catch(InterruptedException e){
                e.printStackTrace();;
            }
        }
    }
}
```



### （3）礼让

#### 1、yield()方法解析

让当前正在执行的线程暂停，不阻塞线程，将线程从运行状态转为就绪状态。

让CPU重新调度，但礼让的成功与否由CPU决定。

#### 2、礼让代码模拟

```java
public class Threadtest  {

    public static void main(String[] args) {
        new Thread(new YIE(),"a").start();
        new Thread(new YIE(),"b").start();
    }
}

class YIE implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"开始执行");
        Thread.yield();
        System.out.println(Thread.currentThread().getName()+"停止执行");

    }
}
```

### （4）强制执行 `插队`

#### 1、join()方法解析

join合并线程，导致其它线程阻塞，待此线程执行完成后，再执行其它线程。



#### 2、插队模拟

```java
public class Threadtest implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(i + "号不要脸插队");
        }
    }

    public static void main(String[] args) {
        //线程启动
        Thread thread = new Thread(new Threadtest());
        thread.start();

        //主线程
        for (int i = 0; i < 100; i++) {
            if(i==50){
                try {
                    thread.join();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println(i + "号同学排队");
        }
    }
}
```





### （5）观察状态

#### 1、观察语句

```java
Thread.State state = thread.getState();
System.out.println(state);
```

#### 2、观察模拟

```java
public class Threadtest {

    public static void main(String[] args) {
        Thread thread = new Thread(()->{
            for (int i = 0; i < 5; i++) {
                try{
                    Thread.sleep(1000);
                }catch (InterruptedException e){
                    e.printStackTrace();
                }
            }
            System.out.println("====");
        });
        //观察状态

        //线程就绪
        Thread.State state = thread.getState();
        System.out.println(state);
        //线程启动
        thread.start();
        state = thread.getState();
        System.out.println(state);
        //
        while(state != Thread.State.TERMINATED){//terminated后不能再次start
            try {
                Thread.sleep(1000);
                state = thread.getState();
                System.out.println(state);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
	}
}
```



### （6）线程的优先级



#### 1、相关概念

java提供一个线程调度器来监控程序中启动后进入就绪状态的所有线程，线程调度器按照优先级决定应该调度哪个线程来执行

线程的优先级用数字表示（Thread.MAX_PRIORITY = 10,Thread.NORM_PRIORITY = 5,Thread.MIN_PRIORITY = 1）

可以用getPriority()，setPriority(int xxx)来获取或改变优先级

#### 2、相关语句

```java
//获取当前执行线程的优先级
Thread.currentThread().getPriority();

//设置优先级
Thread t = new Thread(mypriority,"1号");
t.setPriority(1);
```



#### 3、线程优先级模拟

```java
public class Threadtest {
    public static void main(String[] args) {
        //主线程的默认优先级
        System.out.println(Thread.currentThread().getName()+"优先级为"+Thread.currentThread().getPriority());

        MYPRIORITY mypriority = new MYPRIORITY();

        Thread t1 = new Thread(mypriority,"1号");
        Thread t2 = new Thread(mypriority,"2号");
        Thread t3 = new Thread(mypriority,"3号");
        Thread t4 = new Thread(mypriority,"4号");

        //先设置优先级再启动
        t1.start();

        t2.setPriority(1);
        t2.start();

        t3.setPriority(4);
        t3.start();

        t4.setPriority(Thread.MAX_PRIORITY);
        t4.start();


    }


}

class MYPRIORITY implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"优先级为"+Thread.currentThread().getPriority());
    }
}

```



### （7）守护线程 `支线任务`

#### 1、相关概念

线程分为用户线程和守护线程，虚拟机必须确保用户线程执行完毕，虚拟机不等待守护线程执行。当所有用户线程执行完毕后，即使守护线程没有执行完毕也会停止。



#### 2、相关语句

```java
Thread thread1 = new Thread(new LORD());
thread1.setDaemon(true);
```



#### 3、守护模拟

```java
public class Threadtest {
    public static void main(String[] args) {
        Thread thread1 = new Thread(new LORD());
        Thread thread2 = new Thread(new P());

        thread1.setDaemon(true);//默认是false，表示为用户线程，true为守护线程

        thread1.start();
        thread2.start();

    }
}

class LORD implements Runnable{
    @Override
    public void run() {
        while(true) {
            System.out.println("LORD在看着你");
        }
    }
}

class P implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 36500; i++) {
            System.out.println("你不知道");
        }
        System.out.println("============你去看他了==========");
    }
}
```





## 五、线程同步机制 `🔒`

### （1）线程同步机制 `排队等待`

#### 1、概念

**a.并发：**同一个对象被多个线程同时操作

**b.队列:**多个需要访问该对象的线程进入这个对象的等待池形成队列

**c.锁：**一个线程访问该对象时，该对象会上锁让其它线程无法访问,

**d.线程同步：**为处理并发情况的等待机制，多个需要访问该对象的线程进入这个对象的等待（该对象自我上锁）池形成队列，依次进行——队列+锁

#### 2、锁机制(synchronized)

需要修改对象中的资源的时候才需要锁

**安全但是：**

一个线程持有锁会导致其它所有需要此锁的线程等待，在多线程竞争下，加锁、释放锁会调度延时，降低性能。

如果一个优先级高的线程等待一个优先级低的线程释放锁，会导致优先级倒置，降低性能。

### （2）同步方法

> 用法一方法：public synchronized void method(int args){}

synchronized方法默认锁的对象本身，或者是class。

```java
class Threadtest {
    public static void main(String[] args) {

        BUYTICKEET buytickeet = new BUYTICKEET();

        new Thread(buytickeet,"我").start();
        new Thread(buytickeet,"欧皇").start();
        new Thread(buytickeet,"加速包🐕").start();
    }
}

class BUYTICKEET implements Runnable{
     int tickets = 10;
     boolean flag = true;

    @Override
    public synchronized void run(){
        while (flag){
            try {
                buy();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    private void buy() throws InterruptedException{
        if (tickets<=0){
            flag=false;
        }
        Thread.sleep(100);
        System.out.println(Thread.currentThread().getName()+"买到表的号码为"+tickets--);
    }
}
```



> 用法二synchronized块 `需要锁的是变化的对象`

```java
class Threadtest {
    public static void main(String[] args) {
        Account account = new Account(150,"银行卡");

        bank b = new bank(account,100,"pl");
        bank c = new bank(account,100,"zky");
        b.start();
        c.start();
    }
}

//账户
class Account{
    int money;
    String IDname;

    public Account(int money, String IDname) {
        this.money = money;
        this.IDname = IDname;
    }
}

//银行
class bank extends Thread{
    Account account;
    int drawmoney;
    int nowmoney;

    public bank(Account account,int drawmoney,String Iname){
        super(Iname);
        this.account=account;
        this.drawmoney=drawmoney;

    }

    //取钱
    public void run() {
//锁的对象是需要增删改的对象
        synchronized (account) {
            //判断有没有钱
            if (account.money - drawmoney < 0) {
                System.out.println("你的余额不足");
                return;
            }

            account.money = account.money - drawmoney;
            nowmoney = nowmoney + drawmoney;

            System.out.println(account.IDname + "余额为" + account.money);
            System.out.println(this.getName() + "手里的钱为" + nowmoney);
        }

    }
}
```



### （3）死锁

#### 1、概念

某个同步块同时拥有两个以上对象的锁，而导致两个或者多个线程都在等待对方释放资源，导致执行停止

#### 2、产生死锁条件

a.一个资源每次只能被一个进程使用

b.一个进程对请求资源阻塞时，对以获得资源保持不放

c.进程已获得资源，在未使用完之前，不能强行剥夺

d.若干进程之间形成循环等待资源的关系

#### 3、代码解释

  synchronized () {}

大括号的左边代表获取资源为资源加锁，大括号的右边代表资源使用完毕释放锁

```java
class Threadtest {
    public static void main(String[] args) {
        Makeup g1 = new Makeup(0,"猴子屁股");
        Makeup g2 = new Makeup(1,"老虎屁股");
        g1.start();
        g2.start();
    }
}

class Lipstick{}

class Mirror{}

class Makeup extends Thread {

    static Lipstick lipstick = new Lipstick();
    static Mirror mirror = new Mirror();
    int choice;
    String girlName;

    Makeup(int choice, String girlName) {
        this.choice = choice;
        this.girlName = girlName;
    }

    @Override
    public void run() {
        try {
            makeup();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    //需要拿到对方的资源
    private void makeup() throws InterruptedException {
        if (choice == 0) {
            synchronized (lipstick) {
                System.out.println(this.girlName + "获得口红的锁");
                Thread.sleep(1000);
                synchronized (mirror) {
                    System.out.println(this.girlName + "获得镜子的锁");
                }
            }
        } else {
            synchronized (mirror) {
                System.out.println(this.girlName + "获得镜子的锁");
                Thread.sleep(2000);
				//锁里面放置了锁
                synchronized (lipstick) {
                    System.out.println(this.girlName + "获得口红的锁");

                }
            }
        }
    }
}
```

### （4）Lock锁 `显示的锁`

#### 1、相关语句

```java
    ReentrantLock lock = new ReentrantLock();
try{
    lock.lock();
    }finally {
    	lock.unlock();
}	
```



#### 2、案例：

```java
import java.util.concurrent.locks.ReentrantLock;

//死锁：两个线程僵持抱着对象需要的资源
class Threadtest {
    public static void main(String[] args) {
        TestLock testLock = new TestLock();
        new Thread(testLock).start();
        new Thread(testLock).start();
        new Thread(testLock).start();
    }
}

class TestLock implements Runnable{
    int ticketnums = 10;
    ReentrantLock lock = new ReentrantLock();



    @Override
    public void run() {
        while (true) {
            try {
                lock.lock();
                if (ticketnums > 0) {
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(ticketnums--);
                }
            }finally {
                lock.unlock();
            }
        }

    }
}
```



## 六、线程协作与通信

### （1）生产者与消费者问题 `锁不能解决的通信问题`

`生产者《》消费者`

> > 线程同步> > > 生产者卖一件东西，生产者只能在那件东西被消费后生产，消费者只能在生产者生产完毕后才能消费。

> > 线程通信 > > > 生产者生产后要告知消费者消费，消费者消费要告知生产者生产



### （2）解决线程之间通信的方法（均为object方法）

`生产者《-仓库管理员-》消费者`

==wait()== `该线程等待其它线程通知，等待会释放锁，sleep()方法不会`

==wait(long timeout)== `指定等待的毫秒数`

==notify()== `唤醒一个处于等待的线程`

==notifyAll()== `唤醒同一个对象上所有调用wait()方法的线程，优先唤醒优先级高者`

通信基本语句：

```java
try {
       this.wait();
}catch (InterruptedException e){
       e.printStackTrace();
}
this.notifyAll();
```

1、管程法

2、信号灯法

```java
//定义信号灯
boolean flag = true;
//改变信号灯
this.flag = !this.flag;
//判断等待
if(flag){
try {
       this.wait();
}catch (InterruptedException e){
       e.printStackTrace();
}
this.notifyAll();
```



## 七、线程池

提前建立多个线程，便于管理，同时关闭，提高性能

```java
public class Threadtest {
    public static void main(String[] args) {
        //创建服务，创建线程池 newFixedThreadPool(10)为线程池大小
        ExecutorService service = Executors.newFixedThreadPool(10);
        //
        service.execute(new Mythread());
        service.execute(new Mythread());
        service.execute(new Mythread());
        service.execute(new Mythread());

        //关闭线程池
        service.shutdown();
    }

}

class Mythread implements Runnable{
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println(Thread.currentThread().getName()+i);
        }
    }
}
```











