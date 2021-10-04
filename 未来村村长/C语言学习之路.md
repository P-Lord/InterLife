# C语言学习之路

### 一、打印：

```c
#include<stdio.h>

int main()
{
	printf("sos\n\mama");
	return 0;
}
```

打印中的\可用来换行而不改变运行结果，\n是换行,程序每一条换行后用；隔开

### 二、变量：

数据类型：



```
#include<stdio.h>



int main()
{

	int a;           整数类型
	char b;          字符类型
	float c;         浮点数类型
	double d;        浮点数类型
	_bool e          布尔类型
	enum f           枚举类型

	a = 520;
	b = "F";
	c = 3.14;
	d = 3.141596253;
	printf("%d",a);
}
```

先声明变量属性，再给变量赋值



### 三、常量与宏定义：

常量:整型(520)，实型(3.14)，字符常量（普通“自主”，转义“\n”），字符串常量：“覅覅”

标识符：用来标识的字符

定义符号常量：#define  标识符  常量（预处理宏定义常量，相当于提前给变量赋值）

```
#include<stdio.h>

#define URL "www.zhongguo.com"

int main()
{
	printf("哈哈哈哈%s",URL);
	return 0;
 } 
```

sizeof（数据尺寸）：

```
#include<stdio.h>

int main()
{
	int a;

	a = 520;

	printf("%d",sizeof(int);
	printf("%d",sizeof a);
}
```

