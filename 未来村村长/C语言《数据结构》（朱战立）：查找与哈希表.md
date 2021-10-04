## C语言《数据结构》（朱战立）：查找与哈希表

### 一、查找

#### 1、查找类型

静态查找：只检查是否存在

- ① 顺序查找：O(n)

- ② 折半查找：O(logn)

- ③ 分块查找

![image-20210702110128301](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702110128301.png)

动态查找：检查是否存在，不存在执行插入操作，存在执行删除操作或修改

- ① 二叉树查找：最好O(logn)，最坏O(n)
- B-树查找：O(logn)
- 红黑树查找：O(logn)
- ==哈希查找：O(1)==

#### 2、衡量查找算法效率的标准

##### ① 关键字比较次数

##### ② 平均查找长度

假设查找线性表为（a, b, c），查找a、b、c的概率分别为0.4、0.1、0.5。顺序查找算法的平均查找长度为：0.4×1+0.1×2+0.5×3 = 2.1

即平均需要将给定值与表中关键字值比较2.1次才能找到待查元素。

#### 3、算法实现

##### ① 顺序查找

```c
#include <SeqList.h>
int SeqSearch(SeqList S, DataType x)
/*在S中顺序查找记录x */
{ int i=0;
while ( i<S.size && S.list[i].key!=x.key ) i++;
if (i<S.size)
return i; /*查找成功，返回所查记录
在表中的序号*/
else
return -1; /*查找失败，返回值-1 */
}
```

##### ② 折半查找

- 将有序表中任一元素list[i]关键字值key与给定值k比较，
  有三种比较结果：
  （1） key = k，查找成功，list[i]即为待查元素；
  （2） key > k，说明待查元素若存在表中，一定排在list[i]之前；
  （3） key < k，说明待查元素若存在表中，一定排在list[i]之后。
- 缩小下一步查找区间
  - 每一次将查找区间的中间元素list[mid]关键字值key与给定值k比较。
  - 在每次关键字比较之后，若没有找到待查元素，即情况（1）未出现，则可根据比较结果将下一步的查找区间缩小为一半：情况（2）为前一半，情况（3）为后一半。

![image-20210702110013064](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702110013064.png)

```c
int BinarySearch(DataType list[], int n, DataType x)
/*在有序表<list[0]..list[n-1]>中折半查找记录x */
{ int low,high,mid;
low=0;//指示查找表区间的表头位置，初值为0；
high=n-1;//指示查找表区间的表尾位置；
while(low<=high)
{ mid=(low+high)/2;//查找区间中间记录下标
if(list[mid].key==x.key) return mid; /*查找成功，
返回所查记录在表中的序号。*/
else{
if(list[mid].key<x.key)
low=mid+1; //在查找区间后半部分查找。
else
high=mid-1; }//在查找区间前半部分查找。
}
return -low; /*查找失败。为了便于折半排序做了变动。*/
}
```

### 二、哈希散列表



#### 1、基本思想

建立一个散列表：通常为一维数组，散列的地址就是数组的下标

存储的结点为键值对key-value

结点存入时先对key进行处理，这里的函数关系成为散列函数或哈希函数，处理后的H(key)结果作为结点从存储地址

当存储地址发生冲突（即H(key)的值出现相同情况），会用开放地址法、链地址法等方法进行处理



#### 2、基本概念

##### ① 散列函数

把关键字key映射到散列表存储位置

- Address=H(key)

##### ② 装载因子

设装载因子为a，m为散列表的空间大小，n为填入散列表中的记录数

- a=n/m

##### ③ 冲突

某个散列函数对于不相等的关键字计算出了相同的散列地址，称为冲突



#### 3、Hash函数构造方法

考虑因数：

- 关键字的长度
- 散列表的大小
- 关键字的分布情况
- 记录的查找效率



##### ① 除留余数法

>  H (key) =key % p

##### ② 直接定址法

（1）取关键字值作为它的Hash地址： 

> H(key)=key

（2）取关键字值的某个线性函数：

> H(key)=a*key+b (a,b为常数)

##### ③ 数字分析法

选取其中各种符号分布均匀的若干位作为散列地址

##### ④ 平方取中法

先通过求关键字的平方值来扩大差别，再取其中的几位或其组合作为散列地址。

![image-20210702112951629](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702112951629.png)

##### ⑤ 折叠法（分段叠加法）

![image-20210702144644089](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702144644089.png)

##### ⑥ 霍姆法则

```c
int H (char *key, int tableSize )
{  int hashVal = 0;
while( *key != '\0' )
hashVal = 37* hashVal + *key++;
// *key是第key位的ASCII码值
// 数学的应用1：使用霍纳法则计算P n (x 0 )
// 数学的应用2：使用一个小素数37作为基数
hashVal %= tableSize; 
//将散列值约束在一个固定长度内：0...tableSize-1
if (hashVal <0)
hashVal += tableSize;
// 由于整数溢出，hashVal可能是个负值
return hashVal;
}
```

#### 4、冲突解决方法—开放地址法

##### ① 处理方法

把所有记录直接存储在散列表中。把发生冲突的关键字存储在表中另一个位置

如果要插入一个记录R，而另一个记录已经占据了R的基位置(发生冲突)，那么就把R存储在表中的其它地址内（由冲突解决策略
确定是哪个地址）。

- 当冲突发生时，使用某种方法为R的关键字key生成一个
  散列地址序列d0 ,d1 ,d2 ,... di  ,...dm-1  。

- 令d(i)是探查函数，其中d0 =H（key）称为key的基地址。  n 第i次冲突散列地址di = (di-1 + D) % m，i=1, 2, …, m-1，其中m是Hash表表长。

按上述地址序列依次探查，将找到的第一个开放的空闲
位置di 作为key的存储位置。
若所有后继散列地址都不空闲，说明该闭散列表已满，
报告溢出。

##### ② 算法思想

![image-20210702151451842](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702151451842.png)

![image-20210702151504891](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702151504891.png)

![image-20210702151526656](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702151526656.png)

##### ==③ 闭散列法==

闭散列法不能真正的删除，因为删除一个单元，可能导致查找线索中断，导致查找不成功。

![image-20210702152615724](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702152615724.png)

删除41，查找15

- 关键字41和15的基地址都是第2个单元，15被线性探查放
  到第3个单元

- 如果从表中删除41，对15的查找必须仍然探查第2个单元
  ，断线索

###### 处理方法：引入碑

设置一个特殊的标记位，来记录散列表中的单元状态

- 空单元(“Empty”)
- 非空单元，单元被占用（ “Active”）
- 非空单元，该单元已被删除（”Deleted”，碑 ）

不可以把空单元、已删除这两种状态，用特殊的值
标记，以区别于“单元被占用”状态。必须区别空单元是查找失败的判断依据，必须区别空单元。

被删除标记值称为==碑( tombstone )==
标志一个记录曾经占用这个单元，但是现在已经不再占用了

![image-20210702153420653](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702153420653.png)

![image-20210702153428418](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702153428418.png)

![image-20210702153436953](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702153436953.png)

##### ④ 算法实现

```c
typedef int KeyType;
typedef enum{
Empty, Active, Deleted } KindOfStatus ;  //枚举类型
typedef struct {
KeyType key; ………..;} DataType;
typedef struct {
DataType data;
KindOfStatus status; } HashItem;
typedef struct {
HashItem *ht; // Hash表，动态数组
int tableSize; // Hash表表长
int currentSize; // 当前表长（当前记录个数）
} HashTable; // Hash表的数据类型

int HashInitiate(HashTable * hash, int mSize)
{ 
hash->ht=(HashItem *)
malloc(sizeof(HashItem)*mSize);
if (hash->ht==NULL)
return 0;
else
{ hash->currentSize=0;
hash->tableSize=mSize;
for(int i=0; i<mSize; i++)
hash->ht[i]. status = Empty;
return 1;
}
}

int H(HashTable * hash, KeyType key)
//除留余数法Hash函数
{  int home=key % hash->tableSize; 
return home;
}
int D(int i)
//第i次冲突的探查位置增量函数
{  return i;  //线性探测再散列
}

int HashFind ( HashTable * hash, DataType x)
/*在Hash表中查找x.key的记录，采用开放定址法解决冲突。查
找成功返回值>0(该单元状态为Active)，查找失败返回值＜0。
当返回值为0时有歧义，需要进一步判别状态以及相等否*/
{  int home = H (x. key); // home记录x的基地址
int j = home; // 探查序列的位置
int i = 0; //i代表探查次数
while ( hash->ht[j]. status != Empty )
{  if ( x. key == hash->ht [j]. data. key &&
hash-> ht[j]. status ==Active ) //或...!=Deleted
{ return j; } //查找成功
i ++; //进行第i次探查以解决冲突
if ( i == m ) { j = hash-> tableSize; break; } //表满
j = ( home + D( i ) ) % hash -> tableSize;
}  //如果遇到碑，查找过程会顺着探查序列继续进行。
return –j;
/*探查到空单元或探测完所有探查序列，查找失败，返回探
查序列的尾部下标。*/
}

int HashDelete(HashTable *hash, DataType x)
/*在Hash表中删除x.key的记录。删除成功返回值1，删除失败
返回值0。*/
{  int i = HashFind(hash, x);
if ( i >=0 && hash->ht [i]. status ==Active && hash-
>ht [i]. data.key ==x.key) //查找成功（当i=0时有歧义）
{  hash-> ht[i]. status = Deleted; //将其状态标记为碑
hash-> currentSize --;
return 1;
}
else  //查找失败
return 0;
}

int HashInsert ( HashItem *hash, DataType x)
/*在Hash表中插入x。插入成功返回值1，插入失败返
回值0。*/
{  int i = HashFind(hash,x);
if ( i>=0 && hash->ht [i]. status == Active && hash-
>ht [i]. data.key == x.key) { //查找成功
cout << “表中已有此元素，不能插入！\n”;
return 0; }
else if ( i != – hash-> tableSize)//查找失败同时表未满
{  hash->ht[– i]. data=x;
hash->ht[– i]. status=Active;
hash-> currentSize ++;
return 1; //插入成功
}
else return 0; //表满，插入失败
}
```

#### 5、链表法

基本思想：为每一个Hash地址建立一个链表，凡散列地址为i的记录都插入到第i个链表中。

![image-20210702154005036](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702154005036.png)

##### ① 性能分析

1）查找情况的改进

开散列法解决冲突的哈希方法的时间复杂度是O(q），其
中q是最长链表的长度

- 最坏情况：如果各项散列在同一个桶中，最长的链长度是n，则它的查找效率是O(n)
  - 改进——选择设计一个好的散列函数

- 最好情况：如果所有项均匀散列到各桶，每个链的长度是n/m，则散列的查找效率是 O（n/m）
  - 改进：动态扩容
  - ![image-20210702154416131](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702154416131.png)

2）查找算法分析

ASL代表平均查找长度

![image-20210702154727022](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702154727022.png)

![image-20210702154744099](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702154744099.png)

![image-20210702154757250](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702154757250.png)

![image-20210702154825817](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702154825817.png)

![image-20210702154850328](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702154850328.png)

![image-20210702154903257](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210702154903257.png)















