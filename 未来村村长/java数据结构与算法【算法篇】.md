# java数据结构与算法【算法篇】



# 算法时间复杂度计算

- - ### 事前估算法

时间频度T(n)：一个算法的时间与算法中语句的执行次数成正比，哪个算法语句执行次数多，它花费的时间就多。一个算法中的语句执行次数称为时间频度（或语句频度）。

随着n的变大，可以忽略低次项、常数项、系数。

#### 时间复杂度：

- 基本操作语句重复执行次数是问题规模n的某个函数值，用T(n)表示

- 若有某个函数f(n)）,使得当n趋近于无穷大，T(n)/f(n)的极限值为不等于0的常数，则称f(n)是T(n)的同数量级函数，记为：T(n)=O(f(n))，称O(f(n))为算法的时间复杂度

- T(n)不同，时间复杂度可能相同。

##### 计算方法：

- 低阶或常数保留

- 有高阶则去系数，保留最高次项

##### 常见的时间复杂度：

- 从大到小：

- - 常数阶(无循环等复杂结构：不随某个变量的变化而增长)

  - 对数阶（一层while，n为限制大小）

  - ```java
    int i = 1;
    while(i<n){
    	i = i*2
    }
    ```

  - 线性阶(一层for)

  - 平方阶（双层for,n为循环次数）

  - 立方阶

  - k次方阶（嵌套k次for循环）

  - 指数阶

- 避免使用指数阶的算法



#### 算法空间复杂度：

一个算法在运行过程中临时占用储存空间的大小。缓存就是用空间换时间。



# 一、递归

### 1、递归调用机制

* 当程序执行到一个方法时，会在栈中开辟一次独立的空间 `一次开一个`,执行完一个方法关闭一个空间
* 如果有限制条件,递归执行的第一个指令是最后一个不满足限制的语句 `避免方法的无限循环`
* 方法中的变量在栈空间中独立不会相互影响,返回有结果会带到下一个调用的语句,无结果继续执行
* 方法中使用的是引用类型的话,就会共享该引用类型

### 2、八皇后问题（经典回溯算法案例）

```java
public class digui {
    int max = 8;
    int[] array = new int[max];
    static int count = 0;
    public static void main(String[] args){

        digui digui = new digui();
        digui.check(0);
        System.out.println("一共有解法："+count + "种");
    }

    //实现棋子的放置
    public void check(int n) {
        if (n == max){
            print();
            return;
        }
        for (int i = 0; i < max; i++) {
            array[n] = i;
            if(judge(n)){
                check(n+1);
            }
        }
    }

    //放置皇后检测是否冲突
    private  boolean judge(int n){
        //1、array[i] == array[n] 表示判断第n个皇后是否和前面的n-1个皇后在同一列
        //2、Math.abs(n-i)==Math.abs(array[n]-array[i]判断是否在同一个斜线
        for (int i = 0; i < n; i++) {
            if(array[i] == array[n] || Math.abs(n-i)==Math.abs(array[n]-array[i])){
                return false;
            }
        }
        return true;
    }

    private void print(){
        count++;
        for (int i = 0; i < array.length; i++) {
            System.out.print(array[i] +"");
        }
        System.out.println();
    }
}
```





# 二、排序算法（SortAlgorithm）



### （一）冒泡排序



#### 1、基本原理

设立两层循环

第一层循环：每次循环完成，所有数字比较后会在末尾产生一个最大值

第二层循环：每两个数字的位置比较一次，逆序则替换，由于第一层循环产生一个最大值则下次比较次数-1

优化：设立flag，如果第二层循环中未发现逆序则退出第一层循环



#### 2、代码实现

```java
public class oO {
    public static void main(String[] args) {
        int[] list = {9, 8, 7, 6, 5, 4, 3, 10};
        bubbleSort(list);
        System.out.print("冒泡排序后列表：");
        for (int a = 0; a < list.length; a++) {
            System.out.print(list[a] + " ");
        }
    }

    public static void bubbleSort(int[] array) {

        int temp = 0;
        boolean flag = false;

        for (int i = 0; i < array.length - 1; i++) {

            for (int j = 0; j < array.length - 1 - i; j++) {

                if (array[j] > array[j + 1]) {
                    flag = true;
                    temp = array[j + 1];
                    array[j + 1] = array[j];
                    array[j] = temp;
                }
            }

            if (!flag) {
                break;
            } else {
                flag = false;
            }

            for (int a = 0; a < array.length; a++) {
                System.out.print(array[a] + " ");
            }
                System.out.println();
            }
        }
    }
```



### （二）选择排序



#### 1、基本原理

两层循环，设立最小值min，开始假设第一位为最小值

第一层循环：依次假设每一位为最小值，一次循环产生一个最小值在前排

第二层循环：所有数字与最小值比较，产生更小值，更新最小值大小，与最小值位置替换，每次从假设最小值的后一位开始循环



#### 2、代码实现

```java
public class oO {
    public static void main(String[] args) {
        int[] list = {9, 8, 7, 6, 5, 4, 3, 10};
        selectSort(list);
        System.out.print("选择排序后列表：");
        for (int a = 0; a < list.length; a++) {
            System.out.print(list[a] + " ");
        }

    }

    public static void selectSort(int[] array) {

        for (int i = 0; i < array.length - 1; i++) {
            
            int min = array[i];

            for (int j = i + 1; j < array.length; j++) {
                if (min > array[j]) {
                    min = array[j];
                    array[j] = array[i];
                    array[i] = min;
                }
            }

            for (int a = 0; a < array.length; a++) {
                System.out.print(array[a] + " ");
            }
                System.out.println();
            }
        }
    }
```





### （三）插入排序



#### 1、基本原理

让左侧数据依次归位，让右侧未插入数据取出一个数据放置到左侧数据中的合适位置

第一层for循环：更新归位数据

第二层循环：右侧第一位（待插入的数）依次和已经归为的数据比较，找到合适的位置则插入



#### 2、代码实现

```java
public class oO {
    public static void main(String[] args) {
        int[] list = {9, 8, 6, 7, 3, 4, 5, 10};
        insertSort(list);
        System.out.print("插入排序后列表：");
        for (int a = 0; a < list.length; a++) {
            System.out.print(list[a] + " ");
        }

    }

    public static void insertSort(int[] array) {

        for (int i = 1; i < array.length; i++) {

            int insertingNum = array[i];
            int insertingIndex = i - 1;


            while (insertingIndex>=0 && insertingNum<array[insertingIndex]){

                    array[insertingIndex + 1] = array[insertingIndex];
                    insertingIndex--;

            }

            if(insertingIndex+1!=i){
                array[insertingIndex+1]=insertingNum;
            }


            for (int a = 0; a < array.length; a++) {
                    System.out.print(array[a] + " ");
                }
                    System.out.println();
                }
        }
    }
```



### （四）希尔排序



#### 1、基本原理

总长度除以2，按增量（相同步长）分组，交换逆序的数，依次缩小增量，继续替换



#### 2、代码实现

```java
import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;

public class oO {
    public static void main(String[] args) {
        int[] list = {9, 8, 6, 7, 3, 4, 5, 10};
        ShellSort(list);
        System.out.println("希尔排序后列表： " + Arrays.toString(list));
        Date date = new Date();
//        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        System.out.println(date);

    }

    public static void ShellSort(int[] array) {
        int count = 0;
        for (int gap = array.length / 2; gap > 0; gap /= 2) {//决定进行几轮

            for (int i = gap; i < array.length; i++) {//分组，遍历各组所有元素
                int Index = i;
                int insertingNum = array[Index];
                if (array[Index] < array[Index - gap]) {
                    while (Index - gap >= 0 && insertingNum < array[Index - gap]) {
                        //移动
                        array[Index] = array[Index - gap];
                        Index -= gap;
                    }
                    //找到位置
                    array[Index] = insertingNum;
                }
                System.out.println("希尔排序第" + ++count + "轮： " + Arrays.toString(array));
            }

        }
    }
}
```



### （五）快速排序

#### 1、基本原理

分而治之：选择基准数，小左大右，持续上述操作（递归）



#### 2、代码实现

```java
import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;

public class oO {
    public static void main(String[] args) {
        int[] list = {8, 7, 5, 3, 6, 9, 2, 1,4};
        quickSort(list,0,list.length-1);
        System.out.println("快速排序后列表： " + Arrays.toString(list));
        Date date = new Date();
//        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        System.out.println(date);

    }


    public static void quickSort(int[] arr,int low,int high){
        int i,j,temp,t;
        if(low>high){
            return;
        }
        i=low;
        j=high;
        //temp就是基准位
        temp = arr[low];

        while (i<j) {
            //先看右边，依次往左递减
            while (temp<=arr[j]&&i<j) {
                j--;
            }
            //再看左边，依次往右递增
            while (temp>=arr[i]&&i<j) {
                i++;
            }
            //如果满足条件则交换
            if (i<j) {
                t = arr[j];
                arr[j] = arr[i];
                arr[i] = t;
            }

        }
        //最后将基准为与i和j相等位置的数字交换
        arr[low] = arr[j];
        arr[j] = temp;
        //递归调用左半数组
        quickSort(arr, low, j-1);
        //递归调用右半数组
        quickSort(arr, j+1, high);
    }
}
```



### （六）归并排序

#### 1、基本原理

分而治之：先拆分到个体，然后再合并，此过程中依次调整顺序



#### 2、代码实现

```java
import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;

public class oO {
    public static void main(String[] args) {
        int[] list = {8, 7, 5, 3, 6, 9, 2, 1,4};
        int[] temp = new int[list.length];
        mergeSort(list,0,list.length-1,temp);
        System.out.println("快速排序后列表： " + Arrays.toString(list));
        Date date = new Date();
//        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        System.out.println(date);

    }

    public static void mergeSort(int[] arr, int left, int right, int[] temp) {
        if(left < right) {
            int mid = (left + right) / 2; //中间索引
            //向左递归进行分解
            mergeSort(arr, left, mid, temp);
            //向右递归进行分解
            mergeSort(arr, mid + 1, right, temp);
            //合并
            merge(arr, left, mid, right, temp);

        }
    }

    //合并的方法
    /**
     *
     * @param arr 排序的原始数组
     * @param left 左边有序序列的初始索引
     * @param mid 中间索引
     * @param right 右边索引
     * @param temp 做中转的数组
     */
    public static void merge(int[] arr, int left, int mid, int right, int[] temp) {

        int i = left; // 初始化i, 左边有序序列的初始索引
        int j = mid + 1; //初始化j, 右边有序序列的初始索引
        int t = 0; // 指向temp数组的当前索引

        //(一)
        //先把左右两边(有序)的数据按照规则填充到temp数组
        //直到左右两边的有序序列，有一边处理完毕为止
        while (i <= mid && j <= right) {//继续
            //如果左边的有序序列的当前元素，小于等于右边有序序列的当前元素
            //即将左边的当前元素，填充到 temp数组
            //然后 t++, i++
            if(arr[i] <= arr[j]) {
                temp[t] = arr[i];
                t += 1;
                i += 1;
            } else { //反之,将右边有序序列的当前元素，填充到temp数组
                temp[t] = arr[j];
                t += 1;
                j += 1;
            }
        }

        //(二)
        //把有剩余数据的一边的数据依次全部填充到temp
        while( i <= mid) { //左边的有序序列还有剩余的元素，就全部填充到temp
            temp[t] = arr[i];
            t += 1;
            i += 1;
        }

        while( j <= right) { //右边的有序序列还有剩余的元素，就全部填充到temp
            temp[t] = arr[j];
            t += 1;
            j += 1;
        }


        //(三)
        //将temp数组的元素拷贝到arr
        //注意，并不是每次都拷贝所有
        t = 0;
        int tempLeft = left; //
        //第一次合并 tempLeft = 0 , right = 1 //  tempLeft = 2  right = 3 // tL=0 ri=3
        //最后一次 tempLeft = 0  right = 7
        while(tempLeft <= right) {
            arr[tempLeft] = temp[t];
            t += 1;
            tempLeft += 1;
        }
    }
}
```



### （七）基数排序



#### 1、基本原理

建立下标0~9的十个数组作为桶

将每个数的个位数取出，将这个数放到对应下标的桶（0~9）中，然后按照这个桶的顺序依次取出放到原来的数组

将每个数的十位数取出，将这个数放到对应下标的桶（0~9）中，然后按照这个桶的顺序依次取出放到原来的数组

将每个数的百位数取出，将这个数放到对应下标的桶（0~9）中，然后按照这个桶的顺序依次取出放到原来的数组

​																						......

#### 2、代码实现

```java
import java.text.SimpleDateFormat;
import java.util.Arrays;
import java.util.Date;

public class oO {
    public static void main(String[] args) {
        int[] list = {8, 7, 5, 3, 6, 9, 2, 1,4};
        radixSort(list);
        System.out.println("基数排序后列表： " + Arrays.toString(list));

        //时间显示
        Date date = new Date();
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        System.out.println(date);

    }


    public static void radixSort(int[] array) {

        int[][] bucket = new int[10][array.length];//装数桶
        int[] bucketElementCount = new int[10];//用来记录每个桶（0~9）装了几个数字/计数桶
        int max = array[0];//假设第一个数最大
        for (int i = 0; i < array.length; i++) {
            if (array[i] > max){
                max = array[i];
            }
        }
        int maxLength = (max + "").length();//获得最大值长度/决定循环次数

        for (int k = 0; k < maxLength; k++) {

            //将数组中的数据放入桶中
            for (int i = 0; i < array.length; i++) {
                int digitOfElement = (int) (array[i] / Math.pow(10, k) % 10);//取出相应的位上的数
                bucket[digitOfElement][bucketElementCount[digitOfElement]] = array[i];//装桶
                bucketElementCount[digitOfElement]++;//计数
            }
            //按照顺序依次取出数据放到原数组
            int index = 0;
            for (int j = 0; j < bucketElementCount.length; j++) {
                if (bucketElementCount[j] != 0) {
                    for (int l = 0; l < bucketElementCount[j]; l++) {
                        array[index] = bucket[j][l];//取数
                        index++;
                    }
                }
                bucketElementCount[j] = 0;//将计数桶的元素清零
            }
        }
    }
}
```



### （八）堆排序



#### 1、基本原理

利用堆的数据结构实现排序效果



#### 2、代码实现

```java
package 算法;

import java.util.Arrays;
import java.util.List;

public class SortHeap {
    public static void main(String[] args) {
        int[] list = {1,5,89,3,7,2};
        HeapSort.sort(list);
        System.out.println(Arrays.toString(list));
    }
}

class HeapSort{

    private static boolean less(int[] heap,int i,int j){
        return heap[i] < heap[j];
    }

    private static void exch(int[] heap,int i,int j){
        int temp = heap[i];
        heap[i] = heap[j];
        heap[j] = temp;
    }

    //将原数组构造成堆
    private static void creatHeap(int[] array,int[] heap){

        //把array中的元素拷贝到heap中
        System.arraycopy(array,0,heap,1,array.length);

        //对堆中的元素做下沉调整，调整的范围为长度的一半处往索引1处扫描
        for (int i = (heap.length)/2; i > 0 ; i--) {
            sink(heap,i,heap.length-1);
        }


    }

    //将array数组中的数据从小到大排序
    public static void sort(int[] array){
        //构建堆
        int[] heap = new int[array.length+1];
        creatHeap(array,heap);
        //定义一个变量，记录未排序的元素中最大的索引
        int N = heap.length - 1;
        //通过循环，交换1索引处的元素和排序的元素中最大的索引处的元素
        while (N!=1){
            //交换元素
            exch(heap,1,N);
            //排序交换后最大元素所在的索引，让它不要参与堆的下沉调整
            N--;
            //需要堆索引1处的元素进行堆的下沉调整
            sink(heap,1,N);
        }
        //把heap中的数据负责到原数组
        System.arraycopy(heap,1,array,0,array.length);



    }

    //在heap堆中，对target处的元素做下沉，范围是0~range
    private static void sink(int[] heap,int target,int range){

        while (2*target <= range){
            //1、找出当前结点的较大子节点
            int max;
            if(2*target + 1 <= range){
                if(less(heap,2*target,2*target + 1)){
                    max = 2*target +1;
                }else {
                    max = 2*target;
                }
            }else {
                max = 2 * target;
            }

            //2、比较当前节点的值和较大子节点的值
            if(!less(heap,target,max)){
                break;
            }
            exch(heap,target,max);

            target = max;
        }
    }
}
```





# 三、查找算法



### （一）线性查找



#### 1、基本原理

从头开始，按顺序比较查找



#### 2、代码实现

```java
public class Select {

    public static void main(String[] args) {
        int arr[] = {1,3,6,7,8,2,6};
        int index = seqSearch(arr,1);
        if(index == -1){
            System.out.println("没有找到");
        }else {
            System.out.println("该数据的位置为第" + index +"位");
        }
    }

    public static int seqSearch(int arr[],int value){

        for (int i = 0; i < arr.length; i++) {
            if(arr[i] == value){
                return i;
            }
        }
        return -1;

    }
}
```



### （二）二分查找（重点）



#### 1、基本原理

限于有序数列，从中间开始比较，每次省去一半序列，逐步省去，得到结果

确定数组中间下标：mid = (left + right)/2

将查找的数与mid比较：findingNum ? arr[mid]

若

findingNum > arr[mid]：则递归向右

findingNum < arr[mid]：则递归向左

直到

arr[mid] == findingNum：找到该数，结束递归

left > right：找不到，结束递归



#### 2、代码实现

```java
package suanfa;

import java.util.ArrayList;
import java.util.Collections;

public class Select {

    public static void main(String[] args) {
        int arr[] = {1,2,3,4,5,6,7,8,8,8,8,8,8,8,8,8,8,8,8,9};

        ArrayList<Integer> resIndex = binarySearch(arr,0,arr.length - 1,8);
        Collections.sort(resIndex);
        System.out.println(resIndex);
    }

    public static ArrayList binarySearch(int arr[], int left, int right, int findingValue) {

        if (left>right || findingValue<arr[0] || findingValue>arr[arr.length-1]){
            return new ArrayList<Integer>();
        }

        int mid = (right + left)/2;
        int midVal = arr[mid];

        if(findingValue > midVal){
            return binarySearch(arr,mid+1,right,findingValue);
        }else if(findingValue < midVal){
            return binarySearch(arr,left,mid - 1,findingValue);
        }else {

            ArrayList<Integer> resIndexlist = new ArrayList<Integer>();

            int temp = mid - 1;
            while (true) {
                if (temp < 0 || arr[temp] != findingValue) {
                    break;
                } else {
                    resIndexlist.add(temp);
                    temp -= 1;
                }
            }
                resIndexlist.add(mid);

            temp = mid + 1;
            while (true){
                    if(temp>arr.length - 1 || arr[temp]!=findingValue){
                        break;
                    }else {
                        resIndexlist.add(temp);
                        temp += 1;
                    }
                }
            return resIndexlist;
        }
    }
}

```





### （三）插值查找

`分布均匀时才比二分法好`

#### 1、基本原理

将公式换成比例式

#### 2、代码替换

```java
int mid = left + (right - left) * (findingValue - arr[left])/(arr[right] - arr[left]);
```





# 四、动态规划









































