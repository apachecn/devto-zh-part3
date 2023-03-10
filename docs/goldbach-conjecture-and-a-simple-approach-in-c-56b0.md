# 哥德巴赫猜想和 C 语言中的一种简单方法

> 原文：<https://dev.to/hakan/goldbach-conjecture-and-a-simple-approach-in-c-56b0>

哥德巴赫猜想是声称每一个大于 2 的双整数都可以写成两个素数之和。是无法证明的最大的数学问题之一。

在最初的哥德巴赫假说中，哥德巴赫声称每一个大于 2 的整数都可以写成 3 个素数之和。这种说法是在假设数字 1 是质数的情况下提出的。但是现在 1 已经不是质数了。这个被我们视为哥德巴赫猜想的主张，由欧拉修正的“每一个大于 2 的双整数都可以写成两个素数之和”组成。

比如说；4，6，8，10 和 12 可以写成两个素数对。

4 = 2+2
6 = 3+3
8 = 3+5
10 = 5+5

这个例子是 C 代码，它允许将大于 2 的双整数写成两个质数。如 124620；

124620 = 19 + 124601

```
 #include <stdio.h> 
//Function declerations
int is_prime(int n);
void goldbach(int g);

int main(){
    int number = 0;
    while(1){
        printf("Enter even number:");
        scanf("%d",&number);
        if(number>2 && number%2==0){
            goldbach(number);
        }
        else{
            printf("Incorrect number!\n");
        }
        printf("\n");
    }
    return 0;
}

//Check is prime number?
int is_prime(int n){
    int flag = 1;
    for (int j = 2; j < n/2; j++)
    {
        if((n%j) == 0){
            return flag-1;
        }
    }
    return flag;
}

//Goldbach solution of a double integer greater than 2
void goldbach(int g){
    int i = 2;

    for (int j = g-i; j > 2; j--)
    {
        if(is_prime(i) == 1 && is_prime(j) == 1)
        {
            printf("%d = %d + %d\n",g,i,j);
            break;
        }
        i++;
    }
} 
```

所有两对素数。例如 16；

16 = 3+13
16 = 5+11
16 = 11+5
16 = 13+3

```
#include <stdio.h> 
//Buffer for primes
int primes[100000] = {2};
int j = 0;

//Function declerations
int is_prime(int n);
void goldbach(int g);

int main(){
    int number = 0;
    while(1){
        printf("Enter even number:");
        scanf("%d",&number);
        if(number>2 && number%2==0){
            goldbach(number);
        }
        else{
            printf("Incorrect number!\n");
        }
        printf("\n");
    }
    return 0;
}

//Check is prime?
int is_prime(int n){
    int flag = 1;
    for (int j = 2; j < n/2; j++)
    {
        if((n%j) == 0){
            return flag-1;
        }
    }
    return flag;
}

//Goldbach solutions of a double integer greater than 2.
void goldbach(int g){
    int flag = 0;

    if(primes[j]<g){
        for (int i = primes[j]+1; i < g; i++)
        {
            if(is_prime(i) == 1){
                j++;
                primes[j] = i;
            }
        }       
    }

    for (int i = 0; i < j; i++)
    {
        for (int k = 0; k < j; k++)
        {
            if(primes[i] + primes[k] == g){
                printf("%d = %d + %d\n",g,primes[i],primes[k]);
                break;
            }
        }
    }

} 
```