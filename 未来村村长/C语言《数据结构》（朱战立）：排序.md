## C语言《数据结构》（朱战立）：排序

### 一、插入排序

每次将一个待排序的记录按其关键字大小插入到前面已经排好序的子序列中，直到全部记录插入完成。

每次往前面插入一个，寻找合适的位置进行插入。

```c
void insertSort(DataType a[],int n){
    int i,j;
    Datatype temp;
    for(i=0;i<n-1;i++){
        temp=a[i+1];
        j=i;
        while(j>-1 && temp.key< a[j].key){
            a[j+1]=a[j];
            j--;
        }
        a[j+1]=temp;
    }
}
```

初始时，a[0]之前排好序，插入a[1]，插入合适的位置

此时a[1]之前排好序，插入a[2]到合适的位置

依次循环

合适的位置：

- 用temp保存待排的值

- 若比它比前面的小，则它前面的数右移，在发现比它小的元素时，停止后移，找到位置进行插入



### 二、希尔排序

`插入排序的优化`

将表中的元素不断拆分为子表并不断排序：

- 子表由元素对应距离增量相同的元素组成
- 对子表进行排序，前大于后则交换位置

![image-20210707183627989](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210707183627989.png)

最后依次处理相当于直接插入排序

```c
void ShellSort(DataType a[],int n,int d[],int numOfD){
    int i,j,k,m,span;
    DataType temp;
    for(m=0;m<numOfD;m++){
        span=d[m];
        for(k=0;k<span;k++){
            for(i=k;i<n-span;i=i+span){
                temp = a[i+span];
                j=i;
                while(j>-1 && temp.key<a[j].key){
                    a[j+span]=a[j];
                    j=j-span;
                }
                a[j+span]=temp;
            }
        }
    }
}
```

### 三、快速排序

##### ① 第一次排序：

取第一个元素为基准值，然后从头尾开始依次比较

- 先从尾开始，找到比基准值小的，将其放到第一个元素的位置
- 从头开始，找到比基准值大的，将其放到第一次移动的元素的位置
- 再从尾开始，找到比基准值小的，将其放到第二次移动元素的元素的位置
- 再从头开始.........
- 最后把基准值放入头尾扫描相遇的位置，这样左边都比基准值小，右边都比基准值大

##### ② 第二次排序：

对左右子表进行相同的操作

##### ③ 后面的排序

继续对左右子表进行相同的操作

![快速排序](https://upload-images.jianshu.io/upload_images/6633377-ba8b8cc60e3df1a3.png?imageMogr2/auto-orient/strip)

```c
int Partition(int A[],int low,int high){
    int pivot=A[low];
    while(low<high){
        while(low<high&&a[high]>pivot) --high;
        A[low]=A[high];
        while(low<high&&A[low]<=pivot) ++low;
        A[high]=A[low];
    }
    A[low]=pivot;
    return low;
}

void QuickSort(int A[],int low,int high){
    if(low<high){
        int pivotopos = Partition(A,low,high);
        QuickSort(A,low,pricotpos-1);
        QuickSort(A,pivotpos+1,high);
    }
}
```

### 四、堆排序

基于数据结构堆，大顶堆和小顶堆。

大顶堆：

- 顺序存储的完全二叉树
- 根结点都大于左右孩子

![image-20210707190428326](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210707190428326.png)

![image-20210707190436803](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210707190436803.png)

![image-20210707190447006](C:\Users\官二的磊子\Desktop\未来村村长\图片\image-20210707190447006.png)



### 五、归并排序

![merge-sort-example.png](https://img2018.cnblogs.com/blog/1204203/201908/1204203-20190830110511143-1305736295.png)



![merge-sort.gif](https://img2018.cnblogs.com/blog/1204203/201908/1204203-20190830110518057-1157837875.gif)