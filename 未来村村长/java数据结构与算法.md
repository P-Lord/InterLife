# java数据结构与算法【数据结构篇】

`算法是程序的灵魂，是面试的大考`

`数据结构是编程语言的衍生物，使得代码更漂亮`

`数据结构是算法的基础`

# 一、概要

（一）线性结构

1、最常用，特点为数据元素间存在一对一的线性关系，一般有==数组、队列、链表、栈==。

2、分为两种存储结构：链式存储结构和顺序存储结构，前者存储地址不连续，后者连续。

（二）非线性结构

1、一般有==二维数组、多维数组、广义表、树结构、图结构==。

2、大多算法与树结构、图结构关联。

# 二、稀疏数组（sparsearray）

`当一个数组中大部分为 0 或 同一个值 时,即去掉无意义的记录`

#### 1、处理方法：

**记录数组有几行几列，和不同的值。**

例：

[ 0 ] {总行数，总列数，有效值个数}

[ 1 ] {所在行，所在列，有效值}

......

**（1）二维数组转稀疏数组：**遍历总列数、总行数、有效值个数。根据sum创建稀疏数组，将二维有效数据存入稀疏数组。

（**2）稀疏数组转原始数组：**读取稀疏数组的第一行，根据第一行数据创建原始的二维数组，再读取后几行的数据赋值即可。



#### 2、代码实现：

```java
public class sparsearray {
    
    public static void main(String[] args) {
        //二维数组
        int[][] TowArr = new int[11][11];
        TowArr[0][0] = 1;
        TowArr[1][1] = 2;
        TowArr[1][2] = 2;
        TowArr[10][10] = 1;
        for(int[] row : TowArr){
            for(int data : row){
                System.out.printf("%d\t",data);
            }
            System.out.println();
        }

        //1、遍历找到非0个数
        int sum = 0;
        for(int i = 0 ; i < TowArr.length;i++){
            for(int j = 0;j < TowArr[i].length;j++){
                if(TowArr[i][j] != 0){
                    sum++;
                }
            }
        }

        //创建对应的稀疏数组
        int[][] sparseArr = new int[sum + 1][3];
        sparseArr[0][0] = TowArr.length;
        sparseArr[0][1] = TowArr[0].length;//稀疏数组的原数组行数列数各自相等
        sparseArr[0][2] = sum;


        //遍历插入有效值
        int count = 0;
        for(int i = 0 ; i < TowArr.length;i++){
            for(int j = 0;j < TowArr[i].length;j++){
                if(TowArr[i][j] != 0){
                    count++;
                    sparseArr[count][0] = i;
                    sparseArr[count][1] = j;
                    sparseArr[count][2] = TowArr[i][j];
                }
            }
        }

        //输出稀疏数组
        System.out.println();
        System.out.println("==========稀疏数组=============");
        for(int[] row : sparseArr){
            for(int data : row){
                System.out.printf("%d\t",data);
            }
            System.out.println();
        }

        //稀疏数组还原二维数组
        int[][] TOWArr2 =new int[sparseArr[0][0]][sparseArr[0][1]];
        System.out.println("==========二维数组恢复==========");
        for(int i = 1;i<sparseArr.length;i++){
            TOWArr2[sparseArr[i][0]][sparseArr[i][1]] = sparseArr[i][2];
        }

        for(int[] row : TOWArr2){
            for(int data : row){
                System.out.printf("%d\t",data);
            }
            System.out.println();
        }
    }
}
```



# 三、队列（Queue）

`有序列表，先入先出，队首出，队后加`

#### 1、处理方法：

建立环形数组（取模的方式实现）

定义front，rear分别代表队首第一个元素和队尾最后一个元素），初始值都为1

当队列满时（rear + 1）% maxSize == front 

当队列空时，rear == front 

队列中有效数据个数：(rear + maxSize - front) %maxSize



#### 2、代码实现：用%建立环形队列

```java
//编写ArrayQueued的队列，编写一个ArrayQueue类
class ArrayQueue{
    //声明属性
    private int maxSize,front,rear;//最大容量，队列首个元素，队列最后一个元素
    private int[] QueArr;//存放数据，模拟队列

    //调用构造器赋初始值
    public ArrayQueue(int arrMaxSize){
        maxSize = arrMaxSize + 1;
        QueArr = new int[maxSize];
        front = 0;//指向队列头部，即前一个位置
        rear = 0;//指向队列尾部，即最后一个数据
    }

    //判断队列是否满
    public boolean isFull(){
        return (rear + 1) % maxSize == front ;
    }

    //判断队列是否为空
    public boolean isEmpty(){
        return  rear == front;
    }

    //添加数据到队列
    public void addQueue(int n){
        if(isFull()){
            System.out.println("队列已满");
            return;
        }
        //直接加入数据
        QueArr[rear] = n;
        rear = (rear + 1) % maxSize;

    }

    //取出数据
    public int getQueue(){
        if(isEmpty()){
            //通过抛出异常处理
            throw new RuntimeException("队列空，不能取数据");
        }
        //这里需要分析出front是指向队列的第一个元素
        //1、先把front对应的值保存到一个临时变量
        //2、将front后移,考虑取模
        //3、将临时保存的变量返回
        int value = QueArr[front];
        front = (front + 1) % maxSize;
        return value;
    }

    //有效数据个数
    public int size(){
        return (rear + maxSize - front) % maxSize ;
    }
    //显示队列的数据
    public void showQueue(){
        //遍历
        if(isEmpty()){
            System.out.println("队列为空");
            return;
        }
        //从front开始遍历
        for (int i = front; i < front + size();i++) {
            System.out.printf("QueArr[%d]=%d\n",i % maxSize,QueArr[i % maxSize]);
        }
    }
        //显示队首
        public int headQueue(){
            //判断
        if(isEmpty()){
            System.out.println("没有数据");
            throw new RuntimeException("队列为空");
        }
            return QueArr[front];
        }
```



#### 3、scanner、boolean的用法

```java
   Scanner scanner = new Scanner(System.in);
        char key = ' ';
        boolean loop = true;
        while (loop){
            key = scanner.next().charAt(0);//接收一个字符
               case 'a':
                    System.out.println("输入一个数：");
                    int value = scanner.nextInt();
                    break;
                    }
                case 'e':
                    scanner.close();
                    loop = false;
                    System.out.println("程序退出");
                    break;
            	}
```



# 三、链表（Linked List）



### （一）单链表（Single Linked List）

`以结点的方式存储，每个节点包含data域、next域（指向下一个节点）`

`链表的各个节点不一定是连续存放的`

`链表分类：带节结点和无头节点的链表`

#### 1、处理方法：

设置头节点head

创建辅助节点temp

创造类属性中含他的对象next

使用temp遍历（temp=temp.next）使链表的元素.next(temp.next=添加元素名)指向下一个元素





#### 2、代码实现：

```java
//注意理解
/*
*Student temp = head
* 此时temp和head指向内存堆中同一个对象，即虽然head赋值给了temp，就是temp的变化也会影响head的变化
* 
*/
public class LInkedList {
    public static void main(String[] args) {

        Student student1 = new Student(1, "彭磊一号", "男");
        Student student2 = new Student(2, "彭磊二号", "女");
        Student student3 = new Student(3, "彭磊三号", "人妖");
        Student student4 = new Student(4, "彭磊四号", "不详");

        //创建一个链表
        SingleLinkedList singleLinkedList = new SingleLinkedList();
        singleLinkedList.addByOrder(student1);
        singleLinkedList.addByOrder(student4);
        singleLinkedList.addByOrder(student3);
        singleLinkedList.addByOrder(student2);

        //显示单链表
        singleLinkedList.ShowList();

        //修改单链表
        Student newstudent = new Student(2, "彭磊的二号修改", "不男不女");
        singleLinkedList.update(newstudent);
        //显示单链表
        System.out.println("==============");
        singleLinkedList.ShowList();

        //删除单链表
        System.out.println("==============");
        singleLinkedList.del(3);
        singleLinkedList.ShowList();

        System.out.println("链表长度为：" + singleLinkedList.getLength(singleLinkedList.getHead()));
        System.out.println("倒数第2个元素为" + singleLinkedList.FindLastIndexNode(singleLinkedList.getHead(), 1));

        //反转链表
        singleLinkedList.reverseList(singleLinkedList.getHead());
        singleLinkedList.ShowList();
    }
    
}

//定义一个Students,每个Students 对象就是一个节点
class Student{

    public int No;
    public String name;
    public String sex;
    //指向下一个结点
    public Student next;//相当于储存下一个节点的内容

    //构造器
    public Student(int No,String name,String sex){
        this.No =No;
        this.name = name;
        this.sex = sex;
    }

    //显示方法
    public String toString() {
        return "Student [no=" + No + ",name=" + name +",sex="+sex+"]";
    }
}


class SingleLinkedList{

    //创建头结点，头节点不移动，不存放具体数据
    private Student head = new Student(0,"","");

    public Student getHead(){
        return head;
    }

    //添加方法：添加节点到单向链表--找到最后的结点，转移next域
    public void  add(Student student){
        //因为head不能动，创建temp变量辅助遍历
        Student temp = head;
        //遍历链表，找到最后
        while(true){
            //找到链表最后则退出循环
            if(temp.next==null){
                break;
            }
            //没有找到则temp后移
            temp = temp.next;
        }
        //当退出while循环时，temp就指向链表的最后
        //将最后这个节点的next 指向新的结点
        temp.next = student;
    }
    //第二种添加方式:指定位置，将temp.next赋值到新节点.next,将新节点赋值给temp.next
    public void addByOrder(Student student){
        //temp找的是添加位置的前一个节点
        Student temp = head;
        boolean flag = false;//标志：添加的编号是否存在，默认为false
        while (true){
            if(temp.next == null){//说明temp已经在链表的最后
                break;
            }
            if(temp.next.No>student.No){//位置找到了，就在temp后边
                break;
            }else if(temp.next.No == student.No){//说明该编号已经存在
                flag = true;
                break;
            }
            temp = temp.next;//后移，相当于遍历
        }
        //判断flag的值
        if(flag){//不能添加
            System.out.println("准备插入的学生id已经存在");
        }else {//插入到链表中
            student.next = temp.next;
            temp.next = student;
        }
    }


    //顺序打印
   public void ShowList(){
        //判断链表是否为空
       if(head.next == null){
           System.out.println("链表为空");
           return;
       }
       //因为头结点不能动，借助辅助变量遍历
       Student temp = head.next;
       while (true){
           //判断是否到链表最后
           if(temp == null){
               break;
           }
           //输出节点信息
           System.out.println(temp);
           //没有到最后就一直后移，将next域后移
           temp = temp.next;
       }
   }



    //修改:主键不变
    public void update(Student student){
    if(head.next == null){
        System.out.println("链表为空~");
        return;
    }
    //找到修改节点
    //定义一个辅助变量
    Student temp = head.next;
    boolean flag = false;//标志：是否找到该节点
    while (true){
        if(temp == null){
            break;//已经遍历完
        }
        if (temp.No == student.No){
            flag = true;
            break;
        }
        temp = temp.next;
    }
    //根据flag判断是否找到要修改的节点
    if(flag){
        temp.name = student.name;
        temp.sex  = student.sex;
    }else {
        System.out.println("没有找到该id");
    }
    }

    //删除:找到需要删除节点的前一个节点temp，将temp.next.next赋值给temp.next,删除的节点无指向则会被垃圾回收机制处理
    public void del(int No){

        Student temp = head;
        boolean flag = false;//标志是否找到待删除节点
        while (true){
            if(temp.next == null){
                break;
            }
            if(temp.next.No == No){
             flag = true;
             break;
            }
            temp = temp.next;
        }
        //判断flag
        if(flag){//找到则删除
            temp.next = temp.next.next;
        }else {
            System.out.printf("未找到该节点%d",No);
        }
    }
    //方法：返回有效节点个数
    public static int getLength(Student head){
        if(head.next == null){
            return 0;
        }
        int length = 0;
        Student cur = head.next;
        while (cur != null){
            length++;
            cur = cur.next;
        }
        return length;
    }

    //查询倒数第k个节点：设置k变量，遍历到length-k即可
    public static Student FindLastIndexNode(Student head,int k){
        if(head.next==null){
            return null;
        }
        int length = getLength(head);
        if (k<=0 || k>=length){
            return null;
        }
        //定义辅助变量
        Student cur = head.next;
        for (int i = 0; i < length-k; i++) {
            cur = cur.next;
        }
        return cur;
    }
    //定义一个节点reverseHead = new Student();从头遍历原链表，每遍历一次便取出放在新的链表前端
    //原来链表的head.next = reverseHead.next
    public static void reverseList(Student head){
        //如果节点小于等于1，直接返回
        if(head.next == null || head.next.next == null){
            return;
        }
        //定义一个辅助指针，帮助我们遍历原来的链表
        Student cur = head.next;
        Student next = null;
        Student reverseHead = new Student(0,"","");
        //遍历原来的列表，每遍历一个节点，便取出放在reverseList中
        while (cur!=null){                                                   //cur = student1//cur用来遍历
            next = cur.next;//先暂时保存当前节点的下一个节点，因为后面需要使用          next=student2//next用来存取下节点的下一个节点
            cur.next = reverseHead.next;//将cur的下一个节点指向新的链表的最前端      cur.next = reverseHead.next //指针反转
            reverseHead.next = cur;//将cur连接到新的链表上                        reverseHead.next = student1//更新第一个节点
            cur = next;//让cur后移                                              cur = student2
        }
        //将head.next指向reverseHead.next,实现单链表的反转
        head.next = reverseHead.next;
    }
}
```



### （二）双链表（Double Linked List）

`以结点的方式存储，每个节点包含data属性、next属性、pre属性`

#### 1、处理方法：

在单链表的基础上增加pre属性

修改方法与单链表相同

增加方法增加student.pre = temp;

删除方法改为

```java
temp.pre.next = temp.next;
temp.next.pre = temp.pre;
```




#### 2、代码实现

```java
public class DoubleLinkedListDemo {
    public static void main(String[] args) {

    }
}

class DStudent{
    int no;
    String name;
    String sex;
    DStudent next;
    DStudent pre;

    //构造器
    public DStudent(int no,String name,String sex){
        this.no = no;
        this.name = name;
        this.sex = sex;
    }

    //显示
    public String toString(){
        return "[no:"+no+",name:"+name+",sex:"+sex+"]" ;
    }
}

class DoubleLinkedList{
    //建立一个头
    DStudent head = new DStudent(0,"","");


    //按顺序添加在最后
    public void add(DStudent student){
        DStudent temp = head;//建立temp：作用临时储存工具人/遍历的实质是不断更换temp的共用内存指向
        while (true){
            if(temp.next==null){
                break;
            }//遍历判断每一个元素的next是否为空，从head开始
            temp = temp.next;//因为head.next在第一次遍历后存在一个数据元素，
        }//遍历的结果为temp和最后一个元素共用一个内存，所以遍历结束后给最后一个元素的next赋值
        temp.next = student;
        student.pre = temp;//形成双向链表
    }


    //修改与单链表相同
    public void updata(DStudent student){
        DStudent temp = head;
        boolean flag = false;
        if(temp.next == null){
            System.out.println("链表为空");
        }
        while (true){
            if(temp.next.no == student.no){
                break;
            }
            if(temp.next == null){
                flag = true;
                break;
            }
            temp = temp.next;
        }
        if(flag){
            System.out.println("修改的数据不存在");
        }else {
            temp.next.name = student.name;
            temp.next.sex = student.sex;
        }
    }

    //删除
    public void del(int no){
        DStudent temp = head.next;
        boolean flag = false;
        if(temp.next == null){
            System.out.println("链表为空");
        }
        while (true){
            if(temp.no == no){
                if(temp.next==null){
                flag = true;
                }
                break;
            }
            temp = temp.next;
        }
        if (flag){
            temp.pre.next = null;
        }else {
            temp.pre.next = temp.next;
            temp.next.pre = temp.pre;
        }
    }

    //查看所有数据
    public void showList(){
        DStudent temp = head;
        while (true){
            if (temp.next == null){
                break;
            }else {
                temp = temp.next;
                System.out.println(temp.toString());
            }
        }
    }

    //查看链表长度
    public int getLength(){
        DStudent temp = head;
        int length=0;
        while (true){
            if (temp.next == null){
                break;
            }else {
                length++;
                temp = temp.next;
            }
        }
        return length;
    }
    //查看指定元素
    public void showIdEqual(int no){
        DStudent temp = head;
        boolean flag = false;
        if(temp.next == null){
            System.out.println("链表为空");
        }
        while (true){
            if(temp.next.no == no){
                break;
            }
            if(temp.next == null){
                flag = true;
                break;
            }
            temp = temp.next;
        }
        if(flag){
            System.out.println("查看的数据的id不存在");
        }else {
            System.out.println(temp.next);
        }
    }
    //定义一个节点reverseHead = new Student();从头遍历原链表，每遍历一次便取出放在新的链表前端
    //原来链表的head.next = reverseHead.next
    public static void reverseList(DStudent head){
        //如果节点小于等于1，直接返回
        if(head.next == null || head.next.next == null){
            return;
        }
        //定义一个辅助指针，帮助我们遍历原来的链表
        DStudent cur = head.next;
        DStudent next = null;
        DStudent reverseHead = new DStudent(0,"","");
        //遍历原来的列表，每遍历一个节点，便取出放在reverseList中
        while (cur!=null){                                                   //cur = student1//cur用来遍历
            next = cur.next;//先暂时保存当前节点的下一个节点，因为后面需要使用          next=student2//next用来存取下节点的下一个节点
            cur.next = reverseHead.next;//将cur的下一个节点指向新的链表的最前端      cur.next = reverseHead.next //指针反转
            reverseHead.next = cur;//将拿出的元素连接到新的链表上                   reverseHead.next = student1//更新第一个节点
            cur = next;//让拿出元素的下一个元素进入遍历                              cur = student2
        }
        //将head.next指向reverseHead.next,实现单链表的反转
        head.next = reverseHead.next;
    }
}
```



### （三）环形链表（Ring Linked List）

`以结点的方式存储，每个节点包含data域、next域`

#### 1、处理方法：

构建：

创建第一个节点，让first节点指向该节点，并形成环形

之后当我们每创建一个新的节点，就把该节点加入到已有的环形链表中

遍历：

让辅助变量指向first节点，通过while循环遍历该环形链表即可

即 curboy.next == first时结束遍历



#### 2、代码实现：

```java
public class RingLinkedListDemo {
    public static void main(String[] args) {
        //构建和遍历
        RingLinkedList ringLinkedList = new RingLinkedList();
        ringLinkedList.addBoys(5);
        ringLinkedList.showBoy();
    }
}

// 创建一个Boy类，表示一个节点
class Boy {
    private int no;// 编号
    private Boy next; // 指向下一个节点,默认null

    public Boy(int no) {
        this.no = no;
    }

    public int getNo() {
        return no;
    }

    public void setNo(int no) {
        this.no = no;
    }

    public Boy getNext() {
        return next;
    }

    public void setNext(Boy next) {
        this.next = next;
    }

}
class RingLinkedList{
    //创建first节点,不赋值
    Boy first = null;
    //添加小孩节点，构建成环形列表
    public void addBoys(int nums) {

        if (nums < 1) {
            System.out.println("nums  值不对！！");
            return;
        }
        //辅助指针，帮助构建环形链表
        Boy curBoy = null;


        for (int i = 1; i <= nums; i++) {
            Boy boy = new Boy(i);

            if (i == 1) {
                first = boy;
                first.setNext(first);
                curBoy = first;
            } else {
                boy.setNext(first);
                curBoy.setNext(boy);
                curBoy = boy;
            }
        }

    }

    //遍历
    public void showBoy(){
        if(first == null){
            System.out.println("链表为空");
            return;
        }
        Boy curBoy = first;
        while (true){
            System.out.printf("小孩的编号%d\n",curBoy.getNo());
            if (curBoy.getNext() == first){
                break;
            }
            curBoy = curBoy.getNext();
        }
    }
}
```



# 四、栈（stack）

`先入后出，栈顶（top）出栈（pop）、入栈（push）`

`出栈、入栈：出栈栈底不变，栈顶下移/上移`

`作用：处理递归调用、表达式转换、二叉树遍历、图形深度优先、子程序调用`

#### 1、处理方法：数组模拟栈

实现栈的思路分析

使用数组模拟栈，用定义top表示栈顶，初始化为-1

入栈：当有数据加入到栈时，top++；stack[top]=data

出栈：int values = stack[top];top--,return value

#### 2、代码实现：

```java
import java.util.Scanner;

public class StackDemo {
    public static void main(String[] args) {
        ArrayStack arrayStack = new ArrayStack(4);
        String key = "";
        boolean loop = true;
        Scanner scanner = new Scanner(System.in);
        while (loop){
            System.out.println("show:显示栈");
            System.out.println("exit:退出栈");
            System.out.println("push:入栈");
            System.out.println("show:出栈");
            key = scanner.next();
            switch (key){
                case "show":
                    arrayStack.list();
                    break;
                case "push":
                    System.out.println("请输入一个值");
                    int value = scanner.nextInt();
                    arrayStack.push(value);
                    break;
                case "pop":
                    try {
                        int res = arrayStack.pop();
                        System.out.println("出栈的数据为： " + res);
                    }catch (Exception e){
                        System.out.println(e.getMessage());
                    }
                    break;
                case "exit":
                    scanner.close();
                    loop = false;
                    break;
            }
        }
        System.out.println("程序退出了");
    }
}

class ArrayStack{
    private int maxSize;//栈的大小
    private  int[] stack;//数据栈仓库
    private int top = -1;//栈顶初始化为-1

    //构造器
    public ArrayStack(int maxSize){
        this.maxSize = maxSize;
        stack = new int[this.maxSize];
    }
    //栈满
    public boolean isFull(){
        return top == maxSize - 1;
    }

    public boolean isEmpty(){
        return top == -1;
    }

    //push入栈
    public void push(int value){
        if(isFull()){
            System.out.println("栈满");
            return;
        }
        top++;
        stack[top] = value;
    }
    //出栈pop
    public int pop(){
        //先判断栈是否空
        if(isEmpty()){
            //抛出异常
            throw new RuntimeException("栈空");
        }
        int value = stack[top];
        top--;
        return value;
    }
    //显示栈的情况[遍历栈]
    public void list(){
        if(isEmpty()){
            System.out.println("栈空");
            return;
        }
        for (int i = top; i >=0; i--) {
            System.out.printf("stack[%d]=%d\n",i,stack[i]);
        }
    }

}
```





# 五、哈希表（HashTable）

`数组装链表`

#### 1、处理方法：

先用哈希函数求值，然后除数组的长度。得出余数放到相应的数组中，若有冲突，则用链表连接。（每个元素包含对应的键和值）



#### 2、代码实现：

```java
package suanfa;

import java.util.Scanner;

public class HashT {
    public static void main(String[] args) {

        HashTable hashTable = new HashTable(7);
        hashTable.add(new Stu(1,"pl"));
        hashTable.add(new Stu(2,"zky"));
        hashTable.add(new Stu(3,"zl"));

        String key = "";
        Scanner scanner = new Scanner(System.in);
        while (true){
            System.out.println("add:添加学生信息");
            System.out.println("list:显示学生信息");
            System.out.println("find:查找学生信息");
            System.out.println("delete:删除学生信息");
            System.out.println("exit:退出学生系统");

            key=scanner.next();
            switch (key){
                case "add":
                    System.out.println("输入id");
                    int id = scanner.nextInt();
                    System.out.println("输入名字");
                    String name = scanner.next();
                    Stu stu = new Stu(id,name);
                    hashTable.add(stu);
                    break;

                case "list":
                    hashTable.list();
                    break;

                case "exit":
                    scanner.close();
                    System.exit(0);
                    break;

                case "delete":
                    System.out.println("输入要删除学生的id");
                    id = scanner.nextInt();
                    hashTable.Delete(id);
                    break;

                case "find":
                    System.out.println("输入要查找学生的id");
                    id = scanner.nextInt();
                    hashTable.Find(id);
                    break;
            }
        }


    }
}

//创建哈希表
class HashTable{
    private StuLinkedList[] stuLinkedListArray;
    private int size;

    public HashTable(int size){
        this.size = size;
        //初始化
        stuLinkedListArray = new StuLinkedList[size];
        for (int i = 0; i < size; i++) {
            stuLinkedListArray[i] = new StuLinkedList();
        }
    }

    //添加学生信息
    public void add(Stu stu){
        int stuLinkedListNo = hashFun(stu.id);
        //将stu添加到对应链表中
        stuLinkedListArray[stuLinkedListNo].add(stu);
    }

    //遍历hash表
    public void list(){
        for (int i = 0; i < size; i++) {
            stuLinkedListArray[i].list(i);
        }
    }

    public void Delete(int id){

        int stuLinkedListNo = hashFun(id);
        Stu stu = stuLinkedListArray[stuLinkedListNo].Del(id);

        if(stu != null){
            System.out.println("在第" + (stuLinkedListNo+1) +"链表删除该学生信息" + id + stu.name);
        }else {
            System.out.println("没有找到该学生信息");
        }
    }

    public void Find(int id){
        int stuLinkedListNo = hashFun(id);
        Stu stu = stuLinkedListArray[stuLinkedListNo].FindStu(id);
        if(stu != null){
            System.out.println("在第" + (stuLinkedListNo+1) +"链表找到该学生信息" + id + stu.name);
        }else {
            System.out.println("没有找到该学生信息");
        }
    }


    //编写hash函数,取模法
    public int hashFun(int id){
        return id % size;
    }
}



//创建元素组
class Stu{
    public int id;
    public String name;
    public Stu next;

    public Stu(int id, String name) {
        this.id = id;
        this.name = name;
    }
}

//创建链表
class StuLinkedList{
    //头指针，执行第一个Stu，head直接指向第一个学生信息
    private Stu head;

    //添加学生
    public void add(Stu stu){
        if(head == null){
            head = stu;
            return;
        }
        Stu curStu = head;//辅助指针
        while (true){
            if(curStu.next == null){
                break;
            }
            curStu = curStu.next;
        }
        curStu.next = stu;
    }

    public void list(int i){

        if(head == null){
            System.out.println(i + "号链表为空");
            return;
        }
        Stu curStu = head;
        while (true){
            System.out.printf(i + "号链表=> id = %d  name = %s\t",curStu.id,curStu.name);
            System.out.println();
            if(curStu.next == null){
                break;
            }
            curStu = curStu.next;
        }
    }

    //查找
    public Stu FindStu(int id){
        if(head == null){
            System.out.println("链表为空");
            return null;
        }
        Stu curStu = head;
        while (true){
            if(curStu.id == id){
                break;
            }
            if(curStu.next == null){
                curStu = null;
                break;
            }
            curStu = curStu.next;
        }
        return curStu;
    }

    public Stu Del(int id){
        if(head == null){
            System.out.println("链表为空");
            return null;
        }
        Stu curStu = head;
        Stu delStu = null;
        while (true){
            if(head.id == id){
                delStu = head;
                head = null;
                break;
            }
            if(curStu.next.id == id){
                delStu = curStu.next;
                break;
            }
            if (curStu.next == null){
                curStu = null;
                break;
            }
            curStu = curStu.next;
        }

        if(head == null){
            return delStu;
        } else if(curStu.next.next!=null) {
            curStu.next = curStu.next.next;
        }else {
            curStu.next = null;
        }
        return delStu;
    }
}
```



# 六、搜索二叉树



#### **1、基本概念**

节点的度：一个节点含有的子树的个数称为该节点的度； 如上图：A的为2

叶节点：度为0的节点称为叶节点； 如上图：G、H、I节点为叶节点

非终端节点或分支节点：度不为0的节点； 如上图：B、D、C、E、F节点为分支节点

双亲节点或父节点：若一个节点含有子节点，则这个节点称为其子节点的父节点； 如上图：A是B的父节点

孩子节点或子节点：一个节点含有的子树的根节点称为该节点的子节点； 如上图：B是A的孩子节点

兄弟节点：具有相同父节点的节点互称为兄弟节点； 如上图：B、C是兄弟节点

树的度：一棵树中，最大的节点的度称为树的度； 如上图：树的度为2

节点的层次：从根开始定义起，根为第1层，根的子节点为第2层，以此类推；

树的高度或深度：树中节点的最大层次； 如上图：树的高度为4
堂兄弟节点：双亲在同一层的节点互为堂兄弟；如上图：H、I互为兄弟节点

节点的祖先：从根到该节点所经分支上的所有节点；如上图：A是所有节点的祖先

子孙：以某节点为根的子树中任一节点都称为该节点的子孙。如上图：所有节点都是A的子孙

森林：由m棵互不相交的树的集合称为森林；



#### **2、实现过程**

创造一个二叉树类

- 类中有属性一个节点（为节点类的对象）：根节点

- 创造一个节点的内部类

- - 类中有属性两个节点：左右和数据value

- 判断方法：是否为空

  - 添加某个值：借助辅助节点变量，小走左大走右，找到左或右子节点为空值时插入
- 查找最大值：
  - 查找最小值：
- 删除某个值：
  - 查找某个值：
- 前序遍历：先结点（结点开始），然后从左（最小值开始）到右
  - 中序遍历：先左（最小值开始）、再结点、再右
- 后序遍历：先左再右、后结点



#### **3、代码实现**

```java
package 数据结构;

public class BinaryT {
    public static void main(String[] args) {

        BinarySortTree binarySortTree = new BinarySortTree();
        Node root = new Node(4,"a");
        binarySortTree.add(root);
        binarySortTree.add(new Node(2,"b"));
        binarySortTree.add(new Node(3,"c"));
        binarySortTree.add(new Node(6,"d"));

        binarySortTree.preOrder(root);//前序遍历先返回节点>左>右
        System.out.println("\n=========");
        binarySortTree.inOrder(root);//中序遍历先返回左>节点>右:从小到大
        System.out.println("\n=========");
        binarySortTree.nextOrder(root);//后序遍历先返回右>左>节点
    }
}


class Node {

    int no;
    String name;
    Node left;
    Node right;
    Node parent;

    public Node(int no, String name) {
        this.no = no;
        this.name = name;
    }

    public String toString() {
        return "node{" +
                "no=" + no +
                ", name='" + name + '\'' +
                '}';
    }
}

class BinarySortTree {

    Node root;

    public Node getRoot(){
        return root;
    }

    //添加
    public void add(Node x) {

        //如果根节点为空
        if (root == null) {
            root = x;
            return;
        }

        //如果不为空：遵循大右小左
        Node temp = root;

        while (true) {

            if (temp.no < x.no) {
                if (temp.right == null) {
                    temp.right = x;
                    x.parent = temp;
                    break;
                } else {
                    temp = temp.right;
                }
            } else if (temp.no > x.no) {
                if (temp.left == null) {
                    temp.left = x;
                    x.parent = temp;
                    break;
                } else {
                    temp = temp.left;
                }
            } else {
                temp.name = x.name;
                break;
            }
        }


    }
    //查找最小值
    public Node findMin(){
        //如果根节点为空
        if (root == null) {
            return root;
        }

        //如果不为空：遵循大右小左
        Node temp = root;
        while (true) {
            if(temp.left == null){
                return temp;
            }else {
                temp = temp.left;
            }
        }
    }

    public Node findMax(){
        //如果根节点为空
        if (root == null) {
            return root;
        }

        //如果不为空：遵循大右小左
        Node temp = root;
        while (true) {
            if(temp.right == null){
                return temp;
            }else {
                temp = temp.right;
            }
        }
    }

    //删除(找到》判断》处理)
    public Node del(int no){

        if(root == null || root.no ==no){
            return null;
        }

        Node temp = root;
        while (true){

            if (temp.no < no) {
                if (temp.right == null) {
                    return null;
                } else {
                    temp = temp.right;
                }
            } else if (temp.no > no) {
                if (temp.left == null) {
                    return null;
                } else {
                    temp = temp.left;
                }
            } else {
                //为叶子节点
                if(temp.right == null && temp.left == null){
                    temp.parent.left = null;
                    temp.parent.right = null;
                    return temp;
                }
                //只有一个叶子节点
                if(temp.right == null || temp.left == null){
                    if(temp.right == null){
                        temp.parent.left = temp.left;
                        return temp;
                    }else {
                        temp.parent.right = temp.right;
                        return temp;
                    }
                    //两个叶子节点
                }else {
                    //找到右子树最小值，取出其no和值
                    Node cur = temp.right;
                    while (true) {
                        if(cur.left == null){
                            break;
                        }else {
                            cur = cur.left;
                        }
                    }
                    temp.no = cur.no;
                    temp.name = cur.name;
                    cur.parent.left =null;
                    return cur;
                    }
                    }
                }
            }

    public void preOrder(Node root) {
        if (root != null) {
            System.out.print(root.toString()+" | ");
            preOrder(root.left);
            preOrder(root.right);
        }
    }

    public void inOrder(Node root) {
        if (root != null) {
            inOrder(root.left);
            System.out.print(root.toString()+" | ");
            inOrder(root.right);
        }
    }

    public void nextOrder(Node root) {
        if (root != null) {
            nextOrder(root.left);
            nextOrder(root.right);
            System.out.print(root.toString()+" | ");
        }
    }

}
```



# 七、堆



#### 1、堆特性：

1、堆中某个节点的值总是不大于（小顶堆）或不小于（大顶堆）其父节点的值

2、堆总是一棵完全二叉树

2、堆一般用数组实现



#### **2、实现过程**

- **构造方法**
- **成员变量**
- **成员方法**
- - 判断两个索引值的大小：[i]是否小于[j]
  - 交换两个索引的值
  - 删除最大元素：堆取出数据只能取出最大值，这样保证堆的特性
  - 插入一个元素（先添加>再比较找到>再交换）
  - 上浮算法找到正确位置
  - 下浮算法为添加的数据找到正确位置



### 3、代码实现

```java
package 数据结构;

public class heapStruction {
    public static void main(String[] args) {
        //创建堆对象
        Heap heap = new Heap(10);
        //往堆中存入字符串数据
        heap.insert(1);
        heap.insert(2);
        heap.insert(3);
        heap.insert(4);
        heap.insert(5);
        heap.insert(6);
        heap.insert(7);
        //通过循环从堆中删除数据
        int result = 0;
        while ((result = heap.delMax()) != 0){
            System.out.print(result + " ");
        }

    }

}

class Heap{

    private int[] items;//存储堆中的元素
    private int N;//记录堆中元素个数

    public Heap(int capacity){
        this.items =new  int[capacity + 1];
        this.N = 0;
    }

    //判断[i]是否小于[j]
    private boolean less(int i ,int j){
        return items[i] < items[j];
    }

    //交换[i]和[j]
    private void exch(int i , int j){
        int temp = items[i];
        items[i] = items[j];
        items[j] = temp;
    }

    //插入元素
    public void insert(int t){
        items[++N] = t;//废弃0索引，
        swim(N);
    }

    //上浮算法，使添加到k处的元素找到正确的位置
    private void swim(int k){
        //循环不断比较当前结点的值和其父结点的值，如果发现父节点的值比当前节点小，则交换位置
        while (k>1){
            //比较当前节点和其父节点
            if(less(k/2,k)){
                exch(k/2,k);
            }
            k/=2;
        }
    }

    //删除并返回最大值(根节点)
    public int delMax(){
        //最底层最右的树与根节点交换，然后删除根节点(元素个数减1)
        int max = items[1];
        exch(1,N);
        items[N] = 0;
        N--;
        //下沉调整堆为有序
        sink(1);
        return max;

    }

    //使用下沉算法，使索引处的元素能在堆中处于一个正确的位置
    private void sink(int k){
        //循环直到2k<=N向下比较与子节点的较大值交换:k与2k和2k+1比较的较大值比较
        while (2*k<N){
            //获取子节点较大节点
            int max;
            if(2*k + 1 <= N){
                max = less(2*k,2*k+1) ? 2*k + 1 : 2*k;
            }else {
                max = 2*k;
            }
            //比较节点与较大节点
            if(!less(k,max)){
                break;
            }
            exch(k,max);
            //变换k的值
            k = max;
        }

    }
}
```



# 八、图



### 1、基本概念

图由一组顶点和一组能够将两个顶点相连的边组成

自环：自己连接自己

平行边：连接同一对顶点的两条边

无向图：边无方向意义

有向图：不仅连接两个顶点，且具有方向



### 2、相关术语

相邻顶点：通过一条边相连时，我们称这两个顶点是相邻的，并且称这两条边依附与这两个顶点

度：连接该顶点的边数

子图：一幅图所有边的子集

路径：由边顺序连接的一系列路径

环：一条路径的起点与终点相同称为环

连通图：任意一个顶点都存在一条路径到另外一个顶点

非连通图：存在顶点间无路径到达



### 3、实现过程

基本原理：模拟所有顶点与连接顶点的边（邻接矩阵【二维数组】、邻接表【】）

邻接矩阵【二维数组】：索引表示顶点，索引的值表示两点是否连接（内存占用较大）

邻接表【一维数组插入队列】：一维数组表示顶点，队列表示邻接点

- 构造方法
- 成员变量
- - 记录顶点数量
  - 记录边的数量
- 成员方法
- - 获取顶点的数量
  - 获取边的数量
  - 向图中添加一条边
  - 获取和顶点相邻的所有顶点

### 4、代码实现

```java
package 数据结构;

import java.util.ArrayList;
import java.util.Arrays;

public class GraphS {
    public static void main(String[] args) {
        Graph graph = new Graph(5);
        String VertexValue[] = {"A","B","C","D","E"};
        for (String value : VertexValue) {
            graph.insertVertex(value);
        }
        graph.insertEdge(0,1,1);
        graph.insertEdge(1,2,1);
        graph.insertEdge(2,3,1);
        graph.insertEdge(3,4,1);

        graph.show();
    }

}

class Graph{

    private ArrayList<String> vertexList;//存储顶点集合
    private int[][] edges;//存储图对应的邻接矩阵
    private int numOfEdges;//表示边的数目


    //构造器
    public Graph(int n){
        //初始化矩阵和vertexList
        edges = new int[n][n];
        vertexList = new ArrayList<String>(n);
        numOfEdges = 0;
    }


    //显示图对应的矩阵
    public void show(){
        for (int[] link :edges) {//(数组中的数据类型加名字：数组)
            System.out.println(Arrays.toString(link));
        }
    }

    //常用方法
    //返回结点个数
    public int getNumOfVertex(){
        return vertexList.size();
    }
    //返回边的数目
    public int getNumOfEdges(){
        return numOfEdges;
    }
    //返回结点i（下标）对应的值
    public String getValueByIndex(int i){
        return vertexList.get(i);
    }
    //返回v1和v2的权值
    public int getWeight(int v1,int v2){
        return edges[v1][v2];
    }

    //插入结点
    public void insertVertex(String vertex){
        vertexList.add(vertex);
    }

    //添加边
    public void insertEdge(int v1,int v2,int weight){
        edges[v1][v2] = weight;//v1表示点的下标（第几个顶点）
        edges[v2][v1] = weight;
        numOfEdges++;
    }


}
```



### （一）图的深度优先遍历（递归）



**从初始访问结点出发，初始访问结点可能有多个领接结点，深度优先遍历的策略是：**

首先访问第一个邻接结点，然后再以这个被访问的领接结点作为初始结点，访问它的第一个领接结点，即每次访问完当前结点后首先访问当前结点的第一个领接结点。



##### 实现算法步骤：

1、访问初始结点v，并标记结点v为已访问

2、查找结点v的第一个领接点w

3、若w存在则执行【4】，不存在回到【1】，从v的下一个结点继续

4、若w未被访问，重复【123】

5、若w被访问，查找结点v的领接结点w的下一个邻接结点，转到【3】



##### 代码增加

```java
private ArrayList<String> vertexList;//存储顶点集合
private int[][] edges;//存储图对应的邻接矩阵
private int numOfEdges;//表示边的数目
//<增加>
private boolean[] isVisited;

//构造器
public Graph(int n){
    
    //初始化矩阵和vertexList
    edges = new int[n][n];
    vertexList = new ArrayList<String>(n);
    numOfEdges = 0;
    //定义boolean数组，记录某个结点是否被访问
    isVisited = new boolean[n];
}

//得到第一个邻接结点的下标w
public int getFirstNeighbor(int index){
    for (int j = 0; j < vertexList.size(); j++) {
        if(edges[index][j]>0){
            return j;
        }
    }
    return -1;
}
//根据前一个邻接结点的下标，来获取下一个邻接结点的下标
public int getNextNeighbor(int v1,int v2){
    for (int i = v2+1; i < vertexList.size(); i++) {
        if(edges[v1][i]>0){
            return i;
        }
    }
    return -1;
}

//深度优先遍历算法
//i第一次为0
private void dfs(boolean[] isVisited,int i){
    //首先访问该结点，输出
    System.out.print(getValueByIndex(i) + ">");
    //将该结点设置为已经访问过
    isVisited[i] = true;
    //查找结点i的第一个邻接结点
    int w = getFirstNeighbor(i);
    while (w != -1){//返回-1该方法停止获得一条路径
        if(!isVisited[w]){
            dfs(isVisited,w);
        }//如果w已经被访问，则查找邻接结点的下一个结点
        w = getNextNeighbor(i,w);
    }
}

//对dfs进行重载，遍历所有的结点并进行dfs
public void dfs(){
    //遍历所有的结点进行dfs
    for (int i = 0; i < getNumOfVertex(); i++) {
        if(!isVisited[i]){
            dfs(isVisited,i);
        }
    }
}//</增加>
```





### （二）图的广度优先遍历



类似于一个分层搜索的过程，广度优先遍历需要使用一个队列以保持访问过的结点的顺序，以便按这个顺序来访问这些结点的邻接结点。

##### 实现算法步骤：

1、访问初始结点v，并标记结点v为已访问

2、结点v进入队列

3、队列非空，继续进行，否则结束

4、出队列，取得头结点u

5、查找结点u的第一个邻接结点w

6、若结点u的邻接结点w不存在，则转到步骤3；否则循环执行以下三个步骤：

6.1若结点w尚未被访问，则访问结点w并标记为已访问。

6.2结点w入队列

6.3查找结点u的继w邻接结点后的下一个邻接结点w，转到步骤6



##### 代码增加

```java
//对一个结点进行广度优先遍历的方法
private void bfs(boolean[] isVisited,int i){
    int u ;//表示队列头结点对应下标
    int w ;//领接结点w
    //队列:结点访问顺序记录
    LinkedList queue = new LinkedList();
    //访问结点、输出结点信息
    System.out.print(getValueByIndex(i) + ">");
    //标记已访问
    isVisited[i] = true;
    //将结点加入队列
    queue.addLast(i);

    while (!queue.isEmpty()){
        //取出队列的头结点下标
        u = (Integer) queue.removeFirst();
        //得到第一个邻结点的下标w
        w = getFirstNeighbor(u);
        while (w != -1){//找到
            if(!isVisited[w]){
                System.out.print(getValueByIndex(w) + ">");
                //标记已经访问
                isVisited[w] = true;
                //入队
                queue.addLast(w);
            }
            //以u为前一个结点，找w后面的下一个邻结点
            w = getNextNeighbor(u,w); //体现出我们的广度优先
        }
    }
}

//遍历所有结点，都进行广度优先搜索
public void bfs(){
    for (int i = 0; i < getNumOfVertex(); i++) {
        if(!isVisited[i]){
            bfs(isVisited,i);
        }
    }
}
```













