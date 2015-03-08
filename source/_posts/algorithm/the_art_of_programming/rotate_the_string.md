---
layout: post
title: 旋转字符串
category: algorithm
date: 2014-11-18
tags: [algorithm]
latextMath: true
toc: true
---
## 题目描述
给定一个字符串，要求把字符串前面的若干个字符移动到字符串的尾部，如把字符串”abcdef”前面的的’a’和’b’移动到字符串尾部,使得原本字符串变成字符串”cdefab”,请写一个函数完成此功能，要求对长度为n的字符串操作的时间复杂度为O(n),空间复杂度为O(1)。
<!--more-->
### 思路一、**按序放置按序法**
只需将字符串按照所需的空间位置进行摆放即可，所需时间复杂度为O(n),空间复杂度为O(n);
```c
#include <stdio.h>
#include <string.h>

void LeftRotateString(char *s, int n, int m)
{
    char tmp[n+1];
    int k = 0;
    for(int i = m; i < n; i++)
    {
        tmp[k++] = s[i];
    }

    for(int i = 0; i < m; i++)
    {
        tmp[k++] = s[i];
    }
    strcpy(s, tmp);
}

int main()
{
    int n = 10;
    int m = 4;
    char s[11] = "0123456789";
    LeftRotateString(s, n, m);
    printf("%s\n", s);
    return 0;
}
```
### 思路二、**暴力移位法**
即一次移动移位，将第一位移至末尾，其他依次前移，如果需要移动m位至后端，则需要m\*n次操作。时间复杂度为O(m\*n)，空间复杂度为O(1)。
```c
#include <stdio.h>

void leftshifttone(char *s, int n)
{
    char t= s[0];
    for(int i = 1; i < n; ++i)
    {
        s[i-1] = s[i];
    }
    s[n-1] = t;
}

void leftshift(char *s, int n, int m)
{
    while(m--)
    {
        leftshifttone(s, n);
    }
}

int main()
{
    int n = 10;
    int m = 4;
    char a[11] = "0123456789";
    leftshift(a, 10, 4);
    printf("%s\n", a);
    return 0;
}
```
### 思路三: 三步反转法
将一个字符串分成X和Y两个部分，在每部分字符串上定义反转操作,如\\(X^T\\),即把X的所有字符串反转(如，\\(X=”abc”\\), 那么\\(X^{T}=”cba”\\)，那么就得出下面结论：\\((X^{T}Y^{T})^{T}=YX\\),解决了字符串的反转问题。
```c
#include <stdio.h>

void ReverseString(char *s, int from, int to)
{
    while(from < to)
    {
        char tmp = s[from];
        s[from++] = s[to];
        s[to--] = tmp;
    }
}

void LeftRotateString(char *s, int n, int m)
{
    m %= n;
    ReverseString(s, 0, m-1);
    ReverseString(s, m, n-1);
    ReverseString(s, 0, n-1);
}

int main()
{
    int n = 10;
    int m = 4;
    char s[11] = "0123456789";
    LeftRotateString(s, n, m);
    printf("%s\n", s);
    return 0;
}
```
### 举一反三
1. 链表翻转，给出一个链表和一个数k,比如,链表为1->2->3->4->5->6，k=2,则反转后2->1->6->5->4->3，若k=3,反转后3->2->1->6->5->4,若k=4,翻转后4->3->2->1->6->5,用程序实现。

解题思路，先将链表用尾插法逆转,在用双指针插值法，确定第k-1位指针地址，和头指针进行交换。
```
#include <stdio.h>
#include <stdlib.h>

typedef struct list
{
    int data;
    struct list *next;
}list;

void PrintLink(list *head)
{
    list *p = head->next;
    while(p != NULL)
    {
        printf("%d->", p->data);
        p = p->next;
    }
    printf("NULL\n");
}

void CreateLink(list *head)
{
    head->next = NULL;
    for(int i = 6; i >= 1; i--)
    {
        list node;
        list *p = (list *)malloc(sizeof(node));
        p->data = i;
        p->next = head->next;
        head->next = p;
    }
}

void ReverseLink(list *head)
{
    list *p = head->next;
    head->next = NULL;
    while(p != NULL)
    {
        list *q = p;
        p = p->next;
        q->next = head->next;
        head->next = q;
    }
}

void RotateLink(list *head, int k)
{
    int cnt = 0;
    ReverseLink(head);
    list *p = head;
    list *pl = head;
    for(int i = 0; i < k; i++)
    {
        pl = pl->next;
    }
    while(pl->next != NULL)
    {
        p = p->next;
        pl = pl->next;
    }
    pl->next = head->next;
    head->next = p->next;
    p->next = NULL;
}

int main()
{
    list *head;
    int k = 2;
    CreateLink(head);
    PrintLink(head);
    RotateLink(head, k);
    PrintLink(head);
    return 0;
}
```
2. 编写程序，在原字符串中把字符串尾部的m个字符移动字符串的头部，要求：长度为n的字符串操作事件复杂度为O(n),空间复杂度为O(1)。例如，原子字符转为”llovebaofeng”,m=7,输出结果为：“baofenllove”。

**算法思路同例子中的一样三步反转法。**

3. 单词翻转，输入一个英文句子，翻转句子中的单词顺序，但单词内字符串的顺序不变，句子中单词以空格隔开，标点符号和普通字母一样处理，例如，输入”I am a student.”,输出”student. a am I”

