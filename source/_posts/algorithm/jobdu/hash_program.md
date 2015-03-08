---
layout: post
title: hash应用
date: 2014-11-22
category: algorithm
tags: [algorithm]
toc: true
---
## 统计同成绩学生人数
**题目描述**
读入N名学生的成绩，将获得某一给定分数的学生人数输出。<!--more-->
**输入:**
测试输入包含若干测试用例，每个测试用例的格式为
第1行: N
第2行: N名学生的成绩，相邻两数字用一个空格间隔。
第3行：给定分数
当读到N=0时输入结束，其中N不超过1000,成绩分数为(包括)0到100之间的一个整数。
**输出**:
对每个测试用例，将获得给定分数的学生人数输出。
**样例输入:
3
80 60 90
60
2
85 66
0
5
60 75 90 55 75
75
0
**样例输出**
1
0
2

思路一我们可以将输入的分数保存在数组里，当需要查找时可将要查询的目标分数一次与这些分数比对，从而得出答案。
```c
#include <stdio.h>

int gradeStudent(int a[], int n, int k)
{
    int count = 0;
    for(int i = 0; i < n; i++)
    {
        if(a[i] == k)
        {
            count++;
        }
    }
    return count;
}

int main()
{
    freopen("text", "r", stdin);
    int n;
    while(scanf("%d", &n) != EOF && n != 0)
    {
        int a[n];
        for(int i = 0; i < n; i++)
        {
            scanf("%d", &a[i]);
        }
        int k;
        scanf("%d", &k);
        printf("%d\n", gradeStudent(a, n, k));
    }
    return 0;
}
```
思路二： 注意这类问题有一个共同点，输入的分数种类是有限的，可以考虑用Hash解决此类问题。输入的分数严格在0到100的区间之内，且为整数。我们可以为这101种可能分别计数，便能得知每一种分数的重复情况。
```
#include <stdio.h>

int main()
{
    int n;
    freopen("text", "r", stdin);
    while(scanf("%d", &n) != EOF && n != 0)
    {
        int Hash[101] = {0};
        for(int i = 1; i <= n; i++)
        {
            int x;
            scanf("%d", &x);
            Hash[x]++;
        }
        int x;
        scanf("%d", &x);
        printf("%d\n", Hash[x]);
    }
    return 0;
}
```

## Sort
