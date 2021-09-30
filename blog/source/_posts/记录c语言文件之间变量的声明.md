---
title: c语言文件之间变量的定义和声明
date: 2019-06-02 12:41:56
tags: [C]
---

<br>
最近review了大一时候的期末实训学籍管理系统的代码。
发现自己并不明白多文件之间的变量定义和声明，在这里做个记录。
{% asset_img 1.png %}
建议使用codeblocks

## 让我们来看看程序是咋写的
### 文件目录结构
{% asset_img 2.png %}
```
主菜单   menu.c
成绩输入 stu1.c ：在文件stu.dat中增加一个或多个新的学生和相应的成绩；
成绩修改 stu2.c ：对已有学生的成绩进行修改；
查询成绩 stu3.c ：根据学号或者姓名查询成绩；
显示成绩 stu4.c ：显示所有学生的成绩（每页10个）；
统计成绩 stu5.c ：对所有学生的成绩，可以按照某一科进行升序排序；
注销学生 stu6.c ：注销某学生及其相关记录；
成绩导入 stu7.c ：将已经在其他文件里的学生成绩，导入到学生成绩数据文件； 
整个系统  mscore.c
```
## mscore.c (main)
``` c
#include<stdio.h>
#include<stdlib.h>
#include<windows.h>
#include<string.h>
#include "my.h"
int BFUN=0;/* 定义全局变量，用来显示学籍信息个数 */
void main()
{
	system("color 3f");
	OI();
}
```
## my.h

``` c
#ifndef MY_H_INCLUDED
#define MY_H_INCLUDED
#include<stdio.h>
#include<stdlib.h>
#include<windows.h>
#include<string.h>
#define NUMBER 200
extern  int BFUN;
struct stu
{
	char name[50];/* 姓名 */
	unsigned long num;/* 学号 */
	int math;/* 数学 */
	int physical;/* 物理 */
	int english;/* 英语 */
	int count; /* 总分 */

}boy[NUMBER],Temp;

/* 定义函数说明（全局说明） */
void INPUT();/* 1.输入学生信息 */

int QUERY(int q);/* 2 查询学生信息 */

void CHANGE();/* 3 修改学生信息 */

void DELETION();/* 4 删除学生信息 */

void SEQUENCE();/* 5 排列学生信息 */

void SHOW();/* 6 显示学生信息 */

void SAVE();/* 7 保存学生信息 */

void LAYIN();/* 9 导入信息 */

void LOGOUT();/* 退出程序界面 */

void OI();/* 操作选项 */

void OUTPUT(int i);/* 输出信息 */

void XG(int k);/* 修改信息调用 */
#endif // MY_H_INCLUDED
```
## stu1.c
``` c
#include "my.h"
void INPUT()/* 1.输入学生全部信息 */
{
	int i;
	int x;
	printf("\n\t\t* * * * * * * * * * * * * * * * >* * * * *\n");
	printf("\n\t\t*      你已进入学生信息录入模块    >     *\n");
	printf("\n\t\t* * * * * * * * * * * * * * * * >* * * * *\n");
	x=BFUN;
	while(1)/* 把全局变量BFUN的值赋给x */
	{

		printf("\t请输入第%d个学生的信息（键入‘0’退出>录入）\n",++x);
		printf("\n\t录入信息包含项【学号，姓名，数学，>物理，外语，总成绩】回车键入下一项”\n");
		printf("\n学号：");
		scanf("%d",&Temp.num);
		if(Temp.num==0)
		{
			system("cls");
			break;
		}
		for(i=0;i<=BFUN;i++)
			if(Temp.num==boy[i].num)
			{
				system("cls");
				printf("\n\n\t该学籍已被注册！\n\n")>;INPUT();
			}
		getchar();
		printf("\n姓名：");
		scanf("%s",&Temp.name);
		if(strlen(Temp.name)>20)/* 限制姓名字符长度>为20 */
		{
			system("cls");
			printf("\n\n\t姓名输入有误，超出限度。>\n\n");INPUT();
		}
		printf("\n数学成绩：");
		scanf("%d",&Temp.math);
		if(Temp.math<=0||Temp.math>=150)
		{
			system("cls");
			printf("\t成绩输入有误！\n\n\n");
			INPUT();
		}
		printf("\n物理成绩：");
		scanf("%d",&Temp.physical);

		if(Temp.physical<=0||Temp.physical>=100)
		{
			system("cls");
			printf("\t成绩输入有误！\n\n\n");
			INPUT();
		}
		printf("\n外语成绩：");
		scanf("%d",&Temp.english);
		if(Temp.english<=0||Temp.english>=150)
		{
			system("cls");
			printf("\t成绩输入有误！\n\n\n");
			INPUT();
		}
		getchar();
		BFUN=x;/* 上面函数完全正确执行后，才会把现在的>x值赋给BFUN */
		printf("\n\n\n");

		boy[BFUN-1]=Temp;
	}
}

```

>到这里就能直观的看出来 多个文件之间的调用关系：将结构体声明，函数声明，常量定义都写在一个自定义头文件里；然后在每一个文件中都`include`这个头文件就可以将功能模块化的分成多个文件啦。

#### 还有一个问题就是`extern`的用法:
>在主文件中定义了一个BFUN的全局变量，用来计算系统中学生的数量。
>而在当头文件，或者其他文件中想要访问这个变量就不需要再次定义了。这时候在别的地方需要声明的时候，只需要使用`extern`就ok了

[项目源码下载](https://github.com/allcando/StudentManagementSystem)