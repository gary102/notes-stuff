容易忘：文件读写， 内存管理，枚举，指针数组 int *p[2], 数组指针(我又忘了怎么写...), 函数指针 int (* fun)(int, int)，错误处理，
格式控制符
回文数

## 1. C 存储类
auto, register, static, extern

auto 是 局部变量默认的存储类
register 类定义变量可能存储在寄存器中
 static
 修饰全局变量时，使变量的作用域限制在声明它的文件内，全局变量的默认行为。
 修饰局部变量时，不要使其进入和离开作用域时创建和销毁，如函数中，重复调用不会让其重新初始化。
 extern 提供其他文件或位置变量的引用
 ```c
 /* 
  * extern 示例1
  * output: 3
  */
 #include <stdio.h>
 int x, y;
 int add2num(){
    extern int x, y;
    x = 1, y = 2;
    return x + y;
 }
int main(){
    printf("%d\n", add2num());
    return 0;
}
```
```c
/*
 * static 示例 
 */
#include <stdio.h>
static int count = 10;
void func(){
	static int thingy = 5; // 只会初始化一次
	thingy ++;
	printf("%d %d\n", count, thingy);
}

int main(){
	while(count --){
		func();
	}
}
```
/*
 * extern 示例2
 */

// file 1: main.c
#include <stdio.h>
    int count = 5;
    extern void write_extern();
    int main(){
	    write_extern();
}

// file 2: support.c
#include <stdio.h>
    extern int count;
    void write_extern(){
	    printf("%d\n", count);
}
// compile: gcc main.c support.c -o extern_1-1

2. C 运算符
算术运算符 + - * / % ++ --
关系运算符 == != > >= < <= 
逻辑运算符 && || !
位运算符 & | ^ ~ << >>
赋值运算符 = += -= *= /= %= <<= >>= &= ^= |=
杂项运算符 sizeof(), &, *, ?:(a>b?a:b)
运算符优先级和结合性

3. 函数参数，形式参数
传值调用
引用调用

4. C 数组
C 传递数组给函数
函数形式参数:
方式 1: void func(int *p)
方式 2: void func(int array[10])
方式 3: void func(int array[])
/*
 * C 从函数返回数组
 */
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
int *getrandom(){
	static int r[10];
	int i;
	srand((unsigned)time(NULL));
	for(i = 0; i<10;i++){
		r[i] = rand();
	}
	return r;
}

int main(){
	int *p = getrandom();
	int i;
	for(i = 0; i<10; i++){
		printf("%d ", *(p++));
	}
	return 0;
}

5. C enum(枚举)
/*
 * 枚举变量
 */
#include <stdio.h>
int main(){
	enum weekday{Mon =1, Tue, Wed, Thu, Fri, Sat, Sun};
	enum weekday day;
	scanf("%d", &day);
	switch(day){
	    case Mon: puts("Mon");break;
	}
	return 0;
}

// 整形转为枚举
int i = 1;
enum weekday day = (enum weekday)i;

6. C 指针
C 指针的算术运算
++ -- + - < <= > >= == !=
C 指针数组 int *p[3], char *name = "Jerry", char *names[] = {"One", "Two"}

/* 
 * 
 */
#include <stdio.h>
int main(){
	int *p[3];
	int a = 1, b=2, c=3;
	p[0] = &a;
	p[1] = &b;
	p[2] = &c;
	printf("%d %d %d\n", *(p[0]), *(p[1]), *(p[2]));
	return 0;
}
C 指向指针的指针
int var = 10;
int *pvar = &var;
int **ppvar = &pvar;
// access 
var, *pvar, **ppvar

C 传递指针给函数，引用赋值
C 从函数返回指针。不允许返回函数内局部变量的地址，除非定义局部变量为 static 变量

7. C 函数指针与回调函数
C 函数指针
int max(int, int);
int (*p)(int, int) = &max; //& 可以省略
函数调用, p(a, b);

回调函数， 函数指针作为某个函数的参数
/* 
 *
 */ 
void populate_array(int *array, size_t array_size, int (*getnextvalue)(void)){
	for(size_t i = 0; i< array_size, i++){
		array[i] = getnextvalue();
	}
}

int getrandomvalue(){
	return rand();
}

int arr[10];
populate_array(arr, 10, getrandomvalue);

8. C 字符串
#include <string.h>
strcat, strcpy, strlen, strcmp, strstr(s1, s2), strchr(s1, ch)

9. C 结构体
struct{
int a;
char b;
double c;
} s1;

struct SIMPLE{
int a;
char b;
double c
};
struct SIMPLE t1, t2[20], *t3;

typedef struct{
int a;
char b;
double c;
} SIMPLE2;
SIMPLE2 u1, u2[20], *u3;

struct COMPLEX{
char string[100];
struct SIMPLE a;
};

struct NODE{
char string[100];
struct NODE *next_node;
};

// 两个结构体相互包含，需要对其中一个结构体进行不完整声明
struct B;
struct A{
struct B *pb;
// other member;
};
struct B{
struct A *pa;
// other member;
};

结构体作为函数参数
void printbook(struct Book book); // 示例函数声明，调用: printbook(book)
void printbook(struct Book *pbook); // 这样的声明也可以,调用: printbook(&book)
指向结构的指针
struct Book *pbook;
pbook = &book1;
pbook->title;

位域，划分几个比特位为不同的区域
struct bs{
int a:8;
int b:2;
int c:6;
} data;
无名位域，作填充
位域指针，用法和结构体相似
10. 共用体
union Data{
	int i;
	float f;
	char str[20];
}data;
共用体占据的内存由成员中占据最大内存的成员决定

访问成员， .
多次赋值，成员的值会损坏
同一时间只使用一个变量，共用体的正确使用
// 老师，学生示例程序

11. C typedef
typedef unsigned char BYTE;

typedef struct Books{
char title[50];
char author[50];
} Book;
Book book1, book2; // 使用

typedef vs #define
typedef 编译器， #define 预处理器
#define N 10;
typedef unsigned int UINT;

12. C 输入与输出
scanf, printf, getchar, putchar, gets, puts

13. C 文件读写
打开文件 FILE *fopen(const char *filename, const char *mode);
关闭文件 int fclose(FILE *fp);
写入文件 int fputc(int c, FILE *fp);
int fputs(const char *s, FILE *fp);
int fprintf(FILE *fp, const char *format);
读取文件
int fgetc(FILE *fp);
char *fgets(char *buf, int n, FILE *fp);
int fscanf(FILE *fp, const char *format);

二进制IO函数 fread, fwrite

14. C 预处理器
预处理器CPP指令
#define #undef
#include 
#ifdef
#ifndef
#if #endif
#else
#elif
#error
#pragma

// ex. 
#undef FILE_SIZE
#define FILE_SIZE 42

// ex.
#ifndef MSG
#define MSG "You wish!"
#endif

// ex. gcc -DDEBUG
#ifdef DEBUG
/* Your debugging statements here */
#endif

预定义宏
__DATE__ __TIME__ __FILE__ __LINE__ __STDC__
MMM DD YYYY HH:MM:SS string 
预处理器运算符
宏延续运算符 \
#define message_for(a, b) \
	printf(#a " and " #b ": We love you!\n)
字符串常量化运算符 #
标记粘贴运算符 ##
#define tokenpaster(n) printf("token " #n "= %d", token##n)
int token34 = 40;
tokenpaster(34);
// output: token34 = 40

#defined() 运算符
#if !defined MSG
#define MSG "You wish."
#endif

参数化的宏
15. C 头文件
只引用一次头文件
#ifndef HEADER_FILE
#define HEADER_FILE
the entire header file
#endif
有条件引用
#if SYSTEM_1
#include "system_1.h"
#elif SYSTEM_2
#include "system_2.h"
#elif SYSTEM_3
...
#endif

#define SYSTEM_H "system_1.h"
#include SYSTEM_H

16. C 强制类型转换
17. C 错误处理
#include <errno.h>
errno, perror(), strerror()
exit(EXIT_FAILURE) // include stdlib.h
exit(EXIT_SUCCESS)
18. C 递归
阶乘
斐波那契数列
19. C 可变参数
#include <stdarg.h>
double average(int num,...){
	double sum;
	va_list valist;
	va_start(valist, num);
	for(int i=0;i<num;i++){
		sum += va_arg(valist, int);
	}
	va_end(valist);
	return sum/num;
}
average(2,1,2); // 1.5
20. 内存管理
#include <stdlib.h>
void *calloc(int num, int size);
void free(void *address);
void *malloc(int num);
void *recalloc(void *address, int newsize);

21. C 命令行参数
int main(int argc, char *argv[]);
int main(int whatever, char *whareverv[]);
argc 参数个数， argv 指针数组， argv[0] 存储程序的名称
多个参数之间用空格分隔， 如果参数本身带空格，传递参数的时候把参数放在双引号 "" 或单引号 '' 内部。
22. C 排序算法

23. 示例
1) 判断奇偶
int is_odd(int a){
	if(a&1)
		return 1;
	else 
		return 0;
}

2) leapyear
int is_leapyear(int a){
	if((a%4==0 && a%100!=0) || a%400==0)
		return 1;
	return 0;
}

3) GCD
/*
 *
 */ 

int get_GCD(int a, int b){
	int gcd;
	for(int i=1;i<=a&&i<=b;i++){
		if(a%i==0 && b%i ==0)
			gcd = i;
	}
	return gcd;

}

/*
 *
 */
int get_GCD(int a, int b){
	while(a!=b){
		if(a>b){
			a -= b;
		}
		else{
			b -= a;
		}
	}
	return a;
}

/*
 * 辗转相除法，递归
 */
int get_GCD(int a, int b){
	if(b!=0){
		return get_GCD(b, a%b);
	}
	else 
		return a;
}

/*
 *
 */
int get_GCD(int x, int y){
	int gcd=x<y?x:y;
	while(x%gcd || y%gcd)
		gcd --;
	return gcd;
}
4) LCM 最小公倍数
/*
 *
 */
int get_LCM(int a, int b){
	return a*b/get_GCD(a, b);
}

5) 判断是几位数
int judge(int a){
	int re = 1;
	while(a/10!=0){
		re ++;
		a /= 10;
	}
	return re;
}
6) n! 循环， 递归， math.h double pow(double base, double)
7) 回文数 

other notes:
字符串表示 字符数组 char mystr[10], 字符串常量 char *pstr = "hello"

指针变量作为函数参数
数组 字符串 动态分配的内存等数据集合，无法直接传入函数内部，可传递他们的指针
为什么使用指针
传参是赋值，集合数据内存拷贝效率低

int max(int *parr, int len);
int max(int arr[6], int len); 可传递多于6个元素的数组
int max(int arr[], int len);

数组指针引入后，访问数组元素
1. 下标 arr[i] = p[i]
2. 指针 *(p+i) = *(arr+i)

返回值为指针的函数： 注意返回的指针不要指向函数内的局部变量， static 修饰的局部变量除外
#include <stdlib.h>
#include <stdio.h>
int *getrandom(){
    static int r(10)={0};
    for(int i=0;i<10;i++){
        *(r+i) = rand();
    }
    return r;
}

int main(){
    // using getrandom function.
    int *p = getrandom()
    // now p point a array
    for(int i=0;i<10;i++)
        printf("%d\n", p[i]);
    return 0;
}

指针数组，数组的每个元素都是指针
int *p[3], int *(p[3])

int a = 1, b =2, c = 3;
int *p[] = {&a, &b, &c};

二维数组指针, 数组的指针
int a[3][4];
int (*p)[4] = a;
*(p+1) // a[1][0]
*(*(p+1)+1) // a[1][1]

函数指针
函数占用一段连续的内存，函数名，内存区域首地址
int max(int, int);
int (*pmax)(int, int) = max
pmax(1, 2); // call


指针总结
指针存放:
基本数据类型， 数组（数组指针）， 函数（函数指针）， 指针变量（二级指针）

int *p;
int **p;
int *p[N]; 指针数组
int (*p)[N]; 数组指针
int (*p)(); 函数指针
int *p(); 返回值为int *的函数

结构体指针
struct stu{
    int a;
    double b
} stu1, class[5], *pstu1 = &stu1; // & 必不可少
访问结构体成员
(*p).member // 括号不可少
p->menber
结构体指针作为函数参数， 结构体成员可能很多， 传指针效率高

