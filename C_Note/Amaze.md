# Amaze

## 一个字符串，输入一个字符，程序将该字符串中该字符删除

```c
delete_string(char str[],char ch)
{
    int i,j;
    for(i=j=0;str[i]!='\0';i++)
        if(str[i]!=ch)
            str[j++]=str[i];
    str[j]='\0';
    return;
}
```

----

## 读取字符串

```c
#include<stdio.h>
#include<string.h>
char* s_getchar(char *st,int n)
{
    char * ret_val;
    char *p;
    int i=0;

    ret_val=fgets(st,n,stdin);
    if(ret_val)
    {
        p=strchr(ret_val,'\n');
        if(p)
        *p='\0';
        else
        while(getchar()!='\n')
        continue;
    }
    return ret_val;
}
```

---

```c
#include<stdio.h>
#define MAX 10
struct student
{
    char name[10];
    int English;
    int math;
    int ID;
};
int main(void)
{
    struct student manage[MAX];
    int i=0;
    puts("Enter name:");
    for(;*fgets(manage[i].name,MAX,stdin)!='\n';i++)
    {
        puts("Enter English grade:");
        scanf("%d",&manage[i].English);
        puts("Enter math grade:");
        scanf("%d",&manage[i].math);
        puts("Enter ID:");
        scanf("%d",&manage[i].ID);
        puts("Enter name:");
        fflush(stdin);
    }

    struct student temp;
    for(int k=0;k<i-1;k++)
    for(int j=0;j<i-k-1;j++)
    {
        if(manage[j].math < manage[j+1].math)
        {
            temp=manage[j];
            manage[j]=manage[j+1];
            manage[j+1]=temp;
        }
    }

    puts("\n\n\n\n");

    if(manage[0].math < 70)
    puts("NONE");

    for(int j=0;j<i;j++)
    {
        if(manage[j].math < 70)
        continue;
        else
        printf("Name:%sEnglish:%d\nMath:%d\nID:%d\n\n",
        manage[j].name,manage[j].English,manage[j].math,manage[j].ID);
    }
}
```

- *fgets()可同时**读取字符串**和**判断第一个元素**（fgets()返回第一个元素的首地址）
- for(;*fgets(manage[i].name,MAX,stdin)!='\n';i++)一次循环**后先对i自增**，再进行条件判断

---

## Reverse

```c
#include<stdio.h>
#include<string.h>
int main(void)
{
    char sen[100];
    puts("Enter sentence:");
    gets(sen);

    char *p=sen;
    while(*p++);         //推出循环时，副作用会进行完成
    p--;

    char temp[100];
    char *q=temp;
    do
    {
        *q++=*--p;
    } while(p-sen);
    
    *q='\0';

    if(!strcmp(sen,temp))
    puts("Revers!");
    else
    puts("Fuck!");

}
```

---

## 鞍点

```c
#include<stdio.h>
#include<string.h>
#include<limits.h>
void saddle(int(*p)[3],int row,int col);
int main(void)
{
    int matrix[3][3]={
        {1,2,3},
        {4,5,6},
        {7,8,9}
    };
    saddle(matrix,3,3);
    return 0;
}

void saddle(int(*p)[3],int row,int col)
{
    int max[row];
    int min[col];

    for(int i=0;i<row;i++)
    max[i]=INT_MIN;
    for(int j=0;j<col;j++)
    min[j]=INT_MAX;

    for(int i=0;i<row;i++)
    for(int j=0;j<col;j++)
    {
        if(p[i][j]>max[i])
        max[i]=p[i][j];
    }

    for(int j=0;j<col;j++)
    for(int i=0;i<row;i++)
    {
        if(p[i][j]<min[j])
        min[j]=p[i][j];
    }

    for(int i=0;i<row;i++)
    for(int j=0;j<col;j++)
    {
        if(p[i][j]==max[i]&&p[i][j]==min[j])
        printf("Saddle:%d",max[i]);
    }
}

```

---

## 查找以e结尾单词

```c
#include<stdio.h>
#include<string.h>
#define MAX 100
int main(void)
{
    char str[MAX];
    puts("Enter your string:");
    fgets(str,MAX,stdin);
    *strchr(str,'\n')='\0';

    char *p=str;
    int count=0;
    while(*p)
    {
        if((*(p+1)==' ' || *(p+1)=='\0') && *p=='e')
        count++;
        p++;
    }

    printf("%d",count);

    return 0;
}
```

- &&优先级比||高
- *strchr(str,'\n')='\0';  ！！

---

## 判断素数

```c
#include<stdio.h>
#include<string.h>
#include<stdbool.h>
#include<math.h>
bool isprime(int);
int main(void)
{
    int num[100]={0};
    int i=0;
    do
    {
        puts("Enter your number:");
        scanf("%d",&num[i]);
        i++;
    } while (num[i-1] != -1);

    int sum=0;
    for(int j=0;j<i-1;j++)
    {
        if(isprime(num[j]))
        sum+=num[j];
    }

    printf("Sum:%d",sum);
    
    return 0;
}

bool isprime(int temp)
{
    bool prime;
    for(int div=1;sqrt(div)<=temp;div++)  //判断素数！！
    {
        if(temp%div==0)
        return prime=true;
        else 
        continue;
    }

    return prime=false;
}
```

----

```c
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<ctype.h>
void reverse_name(char *name);
int main(void)
{
    char name[]="Pi g Jo   n e";
    reverse_name(name);
    puts(name);

    return 0;
}

void reverse_name(char *name)
{
    char *p=name+1;
    char *q=name;
    char *temp=(char *)malloc(strlen(name)*sizeof(char));
    char *pp=temp;

    for(;;)
    {
        if(isalpha(*p) && isupper(*p))
        break;
        p++;
    }

    char *flag=p;
    
    while(*p)
    {
        if(!isspace(*p))
        {
            *pp=*p;
            pp++;
        }
        p++;
    }

    *pp++=',';

    while(flag-q)
    {
        while(!isspace(*q))
        {
            *pp=*q++;
            pp++;
        }
        q++;
    }

    *pp='.';
    *(++pp)='\0';
    strcpy(name,temp);
    
    return;
}
```

- char *name="Pi g Jo   n e"无法使用strcpy(name,temp);**默认name无法修改**、
- isupper()函数只能接受 **字母字符**

----

```c
#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#define MAX 20
void sort(char *strings[],int n);
int main(void)
{
    char *strings[20];
    int i=0;
    for(;*strings[i-1]!='\n';i++)
    {
        puts("Enter string:");
        strings[i]=(char *)malloc(MAX*sizeof(char));
        fgets(strings[i],MAX,stdin);
    }

    sort(strings,i-1);
    puts(*strings);

    return 0;

}

void sort(char *strings[],int n)     //选择排序
{
    char *temp;
    for(int top=0;top<n-1;top++)
    for(int seek=top+1;seek<n;seek++)
    {
        if(strlen(strings[seek])>strlen(strings[top]))
        {
            temp=strings[top];
            strings[top]=strings[seek];
            strings[seek]=temp;
        }
    }
}
```

不可将strlen(strings[seek]) > strlen(strings[top])替换成strlen(strings[seek])-strlen(strings[top])>0

**关键在于strlen()的返回值是一个无符号数。无符号数只有正数，没有负数。**

eg.

```c
int main(void)
{
    size_t a=strlen("bjhvjvkkvxk")-strlen("vkkvxk");
    printf("%zu",a);

}
```

**输出结果是**：18446744073709551611

## Static

### 1.修饰局部变量

```c
#define _CRT_SECURE_NO_WARNINGS 1
#include<stdio.h>
int f(int x)
{
	static int a = 0;//调用该函数时跳过该语句
	a++;//a=a+1；
	printf("%d\n", a);//打印a=1；
}
int main()
{
	int a = 0, i = 0;
	for (i = 0; i < 10; i++)//循环调用十次f函数
	{
		f(a);
	}
	return 0;
}

```

所以在出f函数时，a变量并没有被销毁，而是保存下来，在下一次调用f函数时，不会再创建a变量，而是跳过执行之后的语句

### 2.修饰全局变量

**静态全局变量仅对当前文件可见，其他文件不可访问，其他文件可以定义与其同名的变量，两者互不影响。**

### 3.pointer

`p->next`是开内存的操作

不能直接`p=pc`(只是单纯的切换了指向的对象)

### 4.‘  ’不为false 