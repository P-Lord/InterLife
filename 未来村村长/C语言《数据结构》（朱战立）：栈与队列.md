## C语言《数据结构》（朱战立）：栈与队列

### 一、栈

`先进后出，后进先出`

![image-20210702185931352](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702185931352.png)



#### 1、ADT

数据元素： ai ,i=0,1,…,n-1 (n≥0)，ai类型为DataType
结构：对所有的数据元素a i (0≤i≤n-2)存在次序关系<ai，ai+1 >, a0无前趋，an-1 无后继。
逻辑操作：设S为 Stack 型

```c
StackInitiate(S);  //构造一个空栈S。
StackNotEmpty(S);  //判断栈S非空否，若S非空，则返回1，否则返回0。
StackPush(S,x);  //在栈S的栈顶插入一个值为x的元素，插入成功返回1，失败返回0。
StackPop(S,x);  //删除栈S的栈顶元素，被删除的栈顶元素通过x带回，删除成功返回1，失败返回0。
StackTop(S,x);  //取栈S中栈顶元素，并由x带回。
```

#### 2、顺序栈的实现

##### ① 类型定义

```c
#define MaxStackSize <栈能存放的最大元素数>
typedef char DataType;
typedef struct
{ DataType stack [MaxStackSize];
int top;
} SeqStack;
SeqStack S;
```

说明：首先定义类型SeqStack的组成

- stack数组：用与存储数据
- top：用于表示存储数据所占用的长度

##### ② 栈的初始化

```c
void SeqStackInitiate (SeqStack *S)
{ S->top=0; }
//时间复杂度： O(1)
```

说明：初始化存储数据占的长度为0

##### ③ 判断栈是否为空

```c
int SeqStackNotEmpty(SeqStack *S){
    if(S->top=0)
        return 0;
    else
        return 1;
}
```

##### ④ 入栈

```c
int StackPush (SeqStack * S, DataType x){ 
    if (S->top>=MaxStackSize)
	return 0； //判栈满否，栈满异常退出
	else {
		S->stack [S->top]＝x；
		S->top++；
		return 1；
	}
}//时间复杂度：O(1)
```

说明：传入值x，赋值后使top增1，表示存入数据长度+1

##### ⑤ 出栈

```c
int StackPop (SeqStack * S, DataType *x){ 
    if (S->top<=0) //判栈非空否，栈空异常退出
		return 0;
	else {
		S->top--;
		*x=S->stack[S->top];
		return 1;
	}
}
//时间复杂度：O(1)
```

说明：出栈是将取出的值存入*x，然后使top-1，这样取栈顶就取不到该元素，就认为出栈

##### ⑥ 取栈顶元素

```c
int SeqStackTop(SeqStack *S, DataType *x){ 
    if(S->top<=0){  //判栈非空否，栈空异常退出
        printf ("The Stack is empty\n");
		return 0;
	}
	else{ 
    	*x=S->stack[S->top-1];
	return 1;
	}
}
//时间复杂度：O(1)
```

说明：传入一个*x用来copy栈顶元素的内容，再取值前先判断是否为空

#### 3、单链栈的实现

##### ① 结点类型定义

```c
Typedef struct snode{
    DataType data;
    struct snode *next;
}LSNode;
SLNode *head;
```

说明：以单链表来表示栈，则数据类型为单链表的节点类型

- data存放数据
- next存放指向下一个结点的指针

##### ② 判断栈是否为空

```c
int StackTop (LSNode *head){
	if (head->next==NULL)
		return 0;
	else
		return 1;
}
//时间复杂度：O(1)
```

##### ③ 入栈

```c
int StackPush (LSNode *head, DataType x){ 
    LSNode *p; //p：新生成结点指针
	p=(LSNode*)malloc(sizeof(LSNode))；
	/*p结点插入在栈顶位置*/
	p→data=x；
	p→next=head→next； //老栈顶
	head→next=p; //新栈顶
	return 1;
}//时间复杂度：O(1)
```

说明：入栈先声明临时结点p

- 存放传入的数据x
- 使p的next指向头结点的next
- 使头结点的next指向p

这样插入的值永远在头结点之后

![image-20210702193919733](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702193919733.png)

![image-20210702193935950](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702193935950.png)

![image-20210702194005240](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702194005240.png)

![image-20210702194027138](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702194027138.png)

##### ③ 出栈

```c
int StackPop (LSNode *head, DataType *x){ 
    LSNode *p=head→next;  //p：缓冲指针
	if (p==NULL){ 
        printf (“栈空出错\n”);
		return 0; }
	head→next=p→next; /*删除p所指的栈顶结点*/
	*x=p→data;
	free (p);
	return 1;
}
```

![image-20210702194856506](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702194856506.png)

![image-20210702194941273](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702194941273.png)

![image-20210702195045167](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702195045167.png)

![image-20210702195059996](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702195059996.png)

##### ④ 读取栈顶元素

```c
int SLStackTop(SLNode *head, DataType *x) 
/* 在以head为头指针的带头结点的单链栈中，读取栈顶元素，
由第二个参数返回 */ 
{  SLNode *p=head;
	if (p->next==NULL ){ 
		printf("空表不能取元素! \n");
		return 0; 
} 
	*x=p->next->data; 
	return 1;
}//时间复杂度：O(1)
```

#### 4、栈的应用

实际应用中，顺序栈比链栈用得更广泛

- 顺序栈容易根据栈顶位置，进行相对位移、快速定位、并读取栈的内部元素
-  顺序栈读取内部元素的时间为O(1)，而链栈为O(n)
- 一般来讲，栈不允许“读取内部元素”，只能在栈顶操

如果先访问或处理的对象最后才能完成所有对它的处理，或者，最后访问或处理的对象最先完成所有对它的处理，则采用==栈==作为未完成处理的对象的缓存数据结构。



### 二、队列

`先进先出、后进后出`

![image-20210702190118494](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702190118494.png)

#### 1、ADT

ADT Queue
数据元素： a i ，i=0,1,…,n-1 (n≥0)，类型为DataType
结构：对所有的数据元素a i (0≤i≤n-2)存在次序关系<ai ， ai+1 >, a0无前趋，an-1无后继。
逻辑操作：设Q为 Queue 型

```c
QueueInitiate(Q) //构造一个空队列Q。
QueueNotEmpty(Q)  
    /*判断队列Q非空否，若Q非空，则返回1，否则返回0。*/
QueueAppend(Q, x)  
    /*在队列Q的队尾插入一值为x的元素，插入成功返回1，失败返回0。*/
QueueDelete(Q, x)  
    /*删除队列Q的队头元素，被删除的队头元素通过x带回，删除成功返回1，失败返回0。*/
QueueGet(Q, x)  //取队列Q的队头元素并由x带回。
```

#### 2、顺序队列的实现（循环队列）

##### ① 类型定义

```c
#define MaxQueueSize <队列允许存放的最大元素数>
typedef char DataType;
typedef struct
{ 
DataType queue[MaxQueueSize];
int front; /* 队头指示器，指向实际队头元素位置*/
int rear; /*队尾指示器，指向实际队尾元素的 下一个位置*/
int count; /* 当前表长 */
} SeqQueue;
```

说明：定义SeqQueue类型的组成

- queue：数组用于存放数据
- front：指向队列头
- rear：指向队列尾
- count：表示表长

##### ② 初始化队列

```c
void QueueInitiate (SeqCQueue *Q){
	Q->count=0;
	Q→rear＝0；
	Q→front=0；
}
```

##### ③ 入队

```c
int QueueAppend(SeqCQueue *Q,DataType x){
    if(Q->count>0 && Q->rear==Q->front)
        return 0;
    else{
        Q->queue[Q->rear] = x;
        Q->rear=(Q->rear+1)%MaxQueueSize;
        Q->count++;
        return 1;
    }
}
```

说明：入队时

- 将x赋值到queue[rear]，即队列的尾部增加元素
- 然后将(rear+1)%MaxQueueSize，将rear移动到下一位（若rear在队列尾部，则移动到头部去，这样rear=front且count为4表示队列满）
- count+1表示长度增加1

![image-20210702202604031](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702202604031.png)

![](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702202520784.png)

##### ④ 出队

```c
int QueueDelete (SeqCQueue *Q, DataType *x)
//q所指向的顺序循环队列的队头元素赋给*x，并删除
{ 
    if (Q→count==0) //队列空否判断
		return 0;
	else {
		*x =Q →queue[ Q→front]; //返回队头元素
		Q→front=(Q →front＋1) % MaxQueueSize；//逻辑上删除原队头元素
		Q →count－－; //表长减一
		return 1;
	}
}//时间复杂度O(1)
```

说明：x值取出，front后移，rear不变

![image-20210702203113037](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702203113037.png)

#### 3、单链队列及其实现

##### ① 类型定义

```c
typedef struct qnode{ 
    DataType data;
	struct qnode *next;
} LQNode; /*结点类型*/

typedef struct{ 
    LQNode *front;
	LQNode *rear;
} LQueue; /*单链队列类型*/

LQueue Q;
```

说明：定义类型LQueue组成：

- LQNode *front：链表头指针
- LQNode *rear：链表尾指针

##### ② 入队

```c
int QueueAppend (LQueue *Q, DataType x){ 
    LQNode *p；
	if ((p=(LQNode *) malloc (sizeof (LQNode)))
==NULL) 
        return 0；
	p->data=x;
	p->next=NULL;
	Q->rear->next=p; /*p结点链入队尾结点之后*/
	Q->rear=p; /*尾指针指向新的队尾结点*/
	return 1;
}
```

说明：入队插入就插在尾结点之后，然后让尾指针指向新的尾结点

##### ③ 出队

```c
int QueueDelete (LQueue *Q, DataType *x){ 
    LQNode *p;
	if (Q->front->next==NULL) 
        return 0;
	p=Q->front->next;
	*x=p->data;
	Q->front->next=p->next; //删除p所指的队头结点
	if (p->next==NULL) Q->rear=Q->front;
	free (p);
	return 1；
}
```

说明：出队是将头指针指向的结点取出，然后头指针指向之前头结点的下一位，即Q->front->next=p->next;

#### 4、队列的应用

CPU执行

主机与外部设备通信





















