## C语言《数据结构》（朱战立）：顺序表与链表

线性结构的特点是：除第一个和最后一个元素外，每个元素只有一个前驱数据元素和一个后继数据元素。线性表是一种可以在任意位置进行插入和删除数据元素操作的、由n(n≥0)个相同类型数据元素a0,a1,a2,...,an-1组成的线性结构。线性表有两种存储结构，一种是顺序存储结构，一种是链式存储结构。

### 一、顺序表

#### 1、存储结构

```c
typedef struct{
    Datatype list[MaxSize];
    int size;
}SeqList;
```

说明：建立一个数组list用来存储数据，size表示存入数据的数量

#### 2、操作实现

##### ① 初始化

```c
void ListInitiate(L){
    L->size = 0;
}
```

说明：初始化size的值为0

##### ② 插入数据元素

```c
int ListInsert(SeqList *L,int i,DataType x){
    int j;
    if(L->size >= MaxSize){
        print("顺序表已满无法插入");
        return 0;
    }
    else if(i<0 || i>L->size){
        printf("参数i不合法");
        return 0;
    }
    else{
        for(j=L->size;j>i;j--)
            L->list[j]=L->list[j-1];
        L->list[i]=x;
        L->size++;
        return 1
    }
}      
```

说明：通过for循环，将要插入位置i的后面元素向后移动，腾出空间，然后给i处赋值x

##### ③ 删除元素数据

```c
int ListDelete(SeqList *L,int i,DataType){
    int j;
    if(L->size<=0){
        print("顺序表已空");
        return 0;
    }
        else if(i<0 || i>L->size){
        printf("参数i不合法");
        return 0;
    }
    else{
        *x = L->List[i];
        for(j=i+1;j<=L-size-1;j++)
            L->list[j-1]=L->list[j];
        L->size--;
        return 1;
    }
}
```

说明：通过for循环，将i之后的元素全部向前移动一个单位

##### ④ 取数据元素

```c
int ListGet(SeqList L,int i,DataType *x){
    if(i<0 || i>L->size){
        printf("参数i不合法");
        return 0;
    }
    else{
        *x = L.list[i];
        return 1;
    }
}
```

##### ⑤ 求当前存储元素个数

```c
int ListLength(SeqList *L){
    return L.size;
}
```

### 二、单链表

#### 1、存储结构

```c
typedef struct Node{
    DataType data;
    struct Node *next;
}SLNode;
```

说明：单链表由一个一个结点连接形成，其中data用于存放数据，next用于存放下一个结点的地址

#### 2、操作实现

##### ① 初始化

```c
void ListInitiate(SLNode **head){
    *head = (SLNode *)malloc(sizeof(SLNode));
    (*head)->next = NULL;
}
```

说明：在初始化之前头指针没有具体的地址值，初始化指针，则需要存入指针的指针，这样*head才能操作头指针，给头指针进行malloc初始化，并初始化头指针的下一个结点为NULL

##### ② 插入元素

```c
int ListInsert(SLNode *head,int i,DataType x){
    SLNode *p,*q;
    int j;
    p = head;
    j=-1;
    while(p->next != NULL && j<i-1){
        p = p->next;
        j++;
    }
    if(j!=i-1){
        printf("插入元素位置参数出错");
        return 0;
    }
    q = (SLNode *)malloc(sizeof(SLNode));
    q->data = x;
    q->next = p->next;
    p->next = q;
    return 1;
}
```

说明：借助p临时指针作为辅助指针，通过p去遍历单链表，找到链表的尾部（即next为NULL的结点）,然后借助q临时指针去存入要存放的元素，将p的next赋值给q的next，即是q->next赋值为NULL，然后将q赋值给p->next

##### ③ 删除元素

```c
int ListDelete(SLNode *head,int i,DataType *x){
    SLNode *p,*s;
    int j;
    p = haed;
    j = -1;
    while(p->next != NULL && p->next->next != NULL && j<i-1){
        p = p->next;
        j++;
    }
    if(j!=i-1){
        print("删除位置参数出错");
            return 0;
    }
    s = p->next;
    *x = s->data;
    p->next = p->next->next;
    free(s);
    return 1;
}
```

说明：删除元素只要找到i所在的位置即可，找到位置以后让p->next连接它下一个结点的next，则中间结点自动脱离链表，通过free去释放它的内存即可。

##### ④ 取数据元素

```c
int ListGet(SLNode *head,int i,DataType *x){
        SLNode *p;
        int j;
        p = head;
        j=-1;
        while(p->next != NULL && j<i){
            p=p->next;
            j++
        }
        if(j!=i){
            print("取值出错");
            return 0;
        }
        *x = p->data;
        return 1;
    }
```

##### ⑤ 撤销单链表

```c
void Destroy(SLNode **head){
    SLNode *p,*p1;
    p=*head;
    while(p!=NULL){
        p1=p;
        p=p->next;
        free(p1);
    }
    *head = NULL;
}
```

说明：c语言没有java的自动垃圾回收机制，我们的结点空间是动态申请的，所以需要手动释放申请的内存空间。

### 三、循环单链表

两处改动即可实现

##### ① 初始化函数

```c
(*head)->next=NULL;
```

改为

```c
(*head)->next=*head;
```

##### ② 在其它函数中

改变判断条件

```c
p->next != NULL;
p->next->next != NULL;
```

改为

```c
p->next != head;
p->next->next != head;
```



### 四、双向链表

#### 1、存储结构

```c
typedef struct Node{
    DataType data;
    struct Node *next;
    struct Node *prior;
}DLNode;
```

#### 2、操作实现

##### ① 初始化

```c
void ListInitiate(DLNOde **head){
    *head = (DLNode *)malloc(sizeof(DLNode));
    (*head)->prior = *head;
    (*head)->next = *head;
}
```

##### ② 插入数据

```c
int ListInsert(DLNode *head,int i,DataType x){
    DLNode *p,*s;
    int j;
    p = head->next;
    j=0;
    while(p!=head && j<i){
        p=p->next;
        j++;
    }
    if(j!=i){
        print("输入出错");
        return 0;
    }
    s = (DLNode *)malloc(sizeof(DLNode));
    s->data = x;
    s->prior = p->prior;
    p->prior->next=s;
    s->next = p;
    p->prior = s;
    return 1;
}
```

##### ③ 删除数据

```c
int ListDelete(DLNode *head,int i,DataType *x){
    DLNode *p;
    int j;
    p=head->next;
    j=0;
    while(p->next!=head && j<i){
        p = p->next;
        j++;
    }
    if(j!=i){
        print("删除元素位置参数出错");
        return 0;
    }
    *x = p->data;
    p->prior->next = p->next;
    p->next->prior = p->prior;
    free(p);
    return 1;
}
```

说明：让前驱节点的next去连接该节点的next，让该结点next的前驱结点连接到该结点的前驱节点。









