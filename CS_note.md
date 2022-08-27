CS 筆記
===

**[String 字串](https://hackmd.io/LVjWmeW0RtCVXyyG6hnxag)**

**[Sorting 演算法](https://hackmd.io/X5mITXctTRWcIndQeFoFEw)**

**[Data Structure 資料節構](https://hackmd.io/2rOZs4ewRdyWszr9cXbytw)**

**[變數宣告](https://hackmd.io/zlht-GAQQ9WbO9r_Ca3ZcA)**

**[檔案處理](https://hackmd.io/O1rrjJa1RPSQ2TyIj5MQig)**

**[其他](https://hackmd.io/Fjz_cSjaR4SRtqHVdPLzaQ)**

## 指標

用`int*` 宣告`int`的指標


```c=
int main(void)
{
    int  x=1;
    int* z=&x;
    printf("address of x : %p\n",z);
    printf("address of x : %p\n",&x);
    
    printf("x = %d\n",x);
    printf("x = %d\n",*z);
}
```
Result
```
address of x : 0x7fffffffdc4c
address of x : 0x7fffffffdc4c
x = 1
x = 1
```

### swap

#### 方法一
```c
// ^ xor
a = a^b;
b = a^b;
a = a^b;
```
`a` `b` 交換

#### 方法二
```c=
#include <stdio.h>

int swap(int* a,int* b);

int main(void)
{
    int  x,y;
    printf("x = ");
    scanf("%d",&x);

    printf("y = ");
    scanf("%d",&y);

    printf("x = %d ; y = %d\n",x,y);
    swap(&x,&y);
    printf("x = %d ; y = %d\n",x,y);
}

int swap(int* a,int* b)
{
    int tmp = *a;
    *a = *b;
    *b = tmp;
}
```

指標string

value` s[i]` is equivalent to `*(s+i)`
addrs`&s[i]` is equivalent to ` (s+i)`

```c=
#include <stdio.h>
#include <string.h> //strlen

int main(void)
{
    //char *s = (char*)"HI!";
    char s[] = "HI!";
    printf("%s\n",s); //HI!
    
    //ver1
    printf("%c",*s);
    printf("%c",*(s+1));
    printf("%c",*(s+2));
    printf("\n");
    
    //ver2
    printf("%c",s[0]);
    printf("%c",s[1]);
    printf("%c",s[2]);
    printf("\n");
    
    //ver3
    for(int i=0 , n = strlen(s); i<n ; i++)
    { 
        printf("%c",s[i]);
    }
    printf("\n");
}
```

```c=
#include <stdio.h>

int main(void)
{
    char* str[]={"piece","of","cake"};
    int i,j;

    for(i=0;i<3;i++)
    {
        printf("%s",str[i]);
        printf(" ");
    }
    printf("\n");
}
```

scanf
```c=
#include <stdio.h>
#include <stdlib.h>
int main(void)
{
    //int
    int x;
    printf("int x = ");
    scanf("%d", &x);
    printf("x = %d\n", x);

    //char
    char c;
    printf("char c = ");
    scanf(" %c", &c);
    printf("c = %c\n", c);

    //string
    //scanf 不需&
    char s[8];
    printf("string s = ");
    scanf(" %7s", s);
    printf("s = %s", s);
}

```

## malloc

```c=
#include <stdio.h>
#include <stdlib.h>

void malloc_float2(float** p,unsigned int sz);

int main(void)
{
    float* ptr = NULL;
    int i, n = 100;
    
    malloc_float2(&ptr,n);

    for(i=0;i<n;i++)
    {
        ptr[i] = (float) rand()/RAND_MAX;  
        printf("%d : %f\n",i,ptr[i]);
    }
    free(ptr);
}

void malloc_float2(float** p,unsigned int sz)
{
    //p=&ptr ptr=*p
    *p = (float*)malloc(sz*sizeof(float));
}
```

:::warning
use `valgrind` to check memory leak
:::

### 一維陣列
:::info
```c
double* ptd;
ptd = (double*) malloc(array_size * sizeof(double) );
```
:::

```c=
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    double* ptd;
    int array_size,i;

    printf("# of doubles you want?\n");
    scanf("%d",&array_size);

    ptd = (double*) malloc(array_size * sizeof(double) );

    if(ptd == NULL)
    {
        printf("memory allocation failed\n");
        exit(EXIT_FAILURE);
    }

    for(i=0;i<array_size;i++)
    {
        ptd[i] = (double) rand()/RAND_MAX; //0~1之間
        if(ptd[i]>0.5)
        {
            printf("%d : %f\n",i,ptd[i]);
        }
    }

    free(ptd);

    return 0;
}
```

#### 透過副程式 malloc 



### 二維陣列
```c=
#include <stdio.h>
#include <stdlib.h>

int main(void)
{   
    int row,col;
    int i,j;

    printf("row col = \n");
    scanf("%d %d",&row,&col);

    int** array_2D = (int**) malloc(col*sizeof(int*));

    for (i = 0; i < row; i++)
    {
        array_2D[i] = (int*) malloc(row*sizeof(int)); 
    }

    //assign
    for(i = 0; i < row*col ;i++)
    {
        array_2D[i/col][i%col] = i;
    }

    for (i = 0; i < row; i++) 
    {
        for (j = 0; j < col; j++) 	
        {
            printf("%d ",array_2D[i][j]); //輸出元素的地址 
        }
        printf("\n");
    }

    //free
    for (int i = 0; i < col; i++) //記得釋放空間
    {
        free(array_2D[i]);
    }
    free(array_2D);
    return 0;
}
```
### dangling pointer
副程式 回傳指標時

:::danger
```c=
#include <stdio.h>

int* f(void)
{
    int a = 3;
    return &a;
}
```
```c=
#include <stdio.h>

int* f(void)
{
    //int a = 3;
    int a[]={1,2,4};
    return a;
}
```
:::

:::success
```c=
#include <stdio.h>

int* f(void)
{
    int* a;
    a = (int*)malloc(10*sizeof(int));
    return a;
}
```
:::


## getchar 
```c=
#include <stdio.h>

int main(void)
{
    char c;
    printf("char:\n");
    
    while ((c = getchar())!='\n')
    {
        //putchar(c);
        printf("%c",c);
    }
    
    //putchar('\n');
    printf("\n");
}
```
### scan any string (<100) and printf

```c=
#include <stdio.h>

int main(void)
{
    int i=0;
    char c[100];
    printf("char:\n");
    
    while ((c[i] = getchar())!='\n')
    {
        i++;
    }
    c[i+1] = '\0';
    printf("%s\n",c);
}

```

only scan first char
```c=
#include <stdio.h>

int main(void)
{
    int i = 0;

    while(getchar() != 'y' )
    {
        i++;
        printf("n = %d ?",i);
        while(getchar() != '\n');
    }
    printf("\n");
}
```
## fgets



## freopen fopen

freopen
```c    
//讀檔案 到 input
freopen("file_name.txt","r",stdin);
//寫檔案 到 output
freopen("file_name.txt","w",stdout);
```
fopen
```c
FILE *fin; //typedef FILE

fin = fopen("file_name.txt","r");
fscanf(fin,"%d",&in_data);
```

```clike=
#include <stdio.h>
int data[100000];

int main(void)
{   
    int i,j,tmp,num_data;
    FILE *fin,*fout;

    fin = fopen("unsorted.txt","r");
    fscanf(fin,"%d",&num_data);
    for(i=0;i<num_data;i++)
    {
        fscanf(fin,"%d",&data[i]);
    }

    //sorting data[i]
    for(i=0;i<num_data-1;i++)
    {
        for(j=0;j<num_data-j-1;j++)
        {
            if(data[j]>data[j+1])
            {
                tmp = data[j];
                data[j] = data[j+1];
                data[j+1] = tmp;
            }
        }
    }

    fout = fopen("sorted.txt","w");
    fprintf(fout,"%d\n\n",num_data);
    for(i=0;i<num_data;i++)
    {
        fprintf(fout,"%d\n",data[i]);
    }
}
```

Primer Number
===
Sieve of Eratosthenes [(wiki link)](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)
![](https://i.imgur.com/LTftgGS.png)


```c=
algorithm Sieve of Eratosthenes is
    input: an integer n > 1.
    output: all prime numbers from 2 through n.

    let A be an array of Boolean values
    initially all set to true.
    
    for i = 2, 3, 4, ..., sqrt(n) do
        if A[i] is true
            for j = i^2, i^2+i, i^2+2i, i^2+3i, ..., n do
                set A[j] := false

    return all i such that A[i] is true.
```

```c=
#include <stdio.h>

#define N 1000

int A[N+1]; //N is too large

int main(void)
{
    int i,j;

    for(i=0;i<=N;i++) A[i]=1;

    for(i=2;i*i<=N;i++)
    {
        if(A[i]) 
        {
            for(j=i*i;j<=N;j=j+i)
            {
                A[j]=0;
            }
        }
    }

    for(i=2;i<=N;i++)
    {
        if(A[i]) printf("%d, ",i);
    }
    printf("\n");
}

```


## gcd

```c=
#include <stdio.h>
#include <string.h>

int gcd(int a,int b);

int main(void)
{
    int a,b;
    
    while(1)
    {
        printf("a = ");
        scanf("%d",&a);

        printf("b = ");
        scanf("%d",&b);

        printf("gcd(a,b) = %d\n",gcd(a,b));
    }
}

int gcd(int a,int b)
{
    if(b>a) 
        return gcd(b,a);
    else if(a%b==0) 
        return b;
    else
        return gcd(b,a%b); 
}
```

## 四則運算

PreFix 前序式 -> InFix 中序式

```c=
#include <stdio.h>
#include <ctype.h>

int calcul(void);

int main(void)
{   
    while (1)
    {
        printf("Enter Prefix : (space between char) \n");
        printf(" = %d\n",calcul());
    }
    
}

int calcul(void)
{
    int c;
    int ans;
    int op1,op2;

    c = getchar();

    if(isspace(c))
    {
        ans = calcul();
    }
    else if(c == '+')
    {
        printf("(");
        op1 = calcul();
        printf("+");
        op2 = calcul();
        printf(")");
        ans = op1 + op2;
    }
    else if(c == '-')
    {
        printf("(");
        op1 = calcul();
        printf("-");
        op2 = calcul();
        printf(")");
        ans = op1 - op2;
    }
    else if(c == '*')
    {
        printf("(");
        op1 = calcul();
        printf("*");
        op2 = calcul();
        printf(")");
        ans = op1 * op2;
    }
    else if(isdigit(c))
    {
        ungetc(c,stdin);
        scanf("%d",&ans);
        printf("%d",ans);
    }
    return ans;
}

```