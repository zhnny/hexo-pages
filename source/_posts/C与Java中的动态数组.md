---
title: C与Java中的动态数组
date: 2022-03-15 00:24:11
description: 概述Java和C中的动态数组原理
categories:
- Java
tags:
- Java
- C
---

## 1. 引言

在实际的编程中，往往会发生这种情况，即所需的内存空间取决于实际输入的数据，而无法预先确定。对于这种问题，用静态数组的办法很难解决。

动态数组，是相对于静态数组而言。静态数组的长度是预先定义好的，在整个程序中，一旦给定大小后就无法改变。而动态数组则不然，它可以随程序需要而重新指定大小。



## 2. Java中的动态数组

Java动态数组是一种可以任意伸缩数组长度的对象，在Java中比较常用的是ArrayList，ArrayList是javaAPI中自带的java.util.ArrayList。

在JDK1.8源码中，ArrayList的自动增长以适应数组长度的实现代码如下：

```java
    /**
     * Increases the capacity to ensure that it can hold at least the
     * number of elements specified by the minimum capacity argument.
     *
     * @param minCapacity the desired minimum capacity
     */
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```

简言之，就是不断判断原来那个数组长度够不够用，不够就增加原来的一半，还不够就继续增加，然后再将原来的数组的值拷贝过来

可见其存在一定的空间浪费和增长时反复拷贝的性能损失



## 3. C/C++中的动态数组

C语言中，数组长度必须在创建数组时指定，并且只能是一个常数，不能是变量。动态数组的内存空间是从堆(heap)上分配(即动态分配)的。是通过执行代码而为其分配存储空间。当程序执行到这些语句时，才为其分配。程序员自己负责释放内存。

C的动态数组示例：

```c
#include <stdio.h>
#include <stdlib.h>
int main(){
    int arrLen;  // 数组长度
    int *array;  // 数组指针
    int i;  // 数组下标
    printf("输入数组长度：");
    scanf("%d", &arrLen);
    
    // 动态分配内存空间，如果失败就退出程序
    array = (int*)malloc( arrLen*sizeof(int) );
    if(!array){
        printf("创建数组失败！\n");
        exit(1); 
    }
    // 向内存中写入数据
    for(i=0; i<arrLen; i++){
        array[i] = i+1;
    }
    
    // 循环输出数组元素
    for(i=0; i<arrLen; i++){
        printf("%d  ", array[i]);
    }
    
    printf("\n");
    free(array); 
    
    system("pause");
    return 0;
}
```

malloc() 用来动态分配指定大小的内存空间，以字节计，其原型为：

```c
void *malloc( size_t size );
```

size_t 是一种自定义数据类型，在 stddef.h 头文件中定义为：

 ```c
  typedef unsigned int size_t;  // 无符号整型
 ```

malloc()返回值类型为 void *，这并不是说该函数调用后无返回值，而是返回一个内存结点的地址，该地址的类型为void(无类型或类型不确定)，即一段存储区的首址，其具体类型无法确定，只有使用时根据各个域值数据再确定。



## 4. 参考资料

\[1][Java ArrayList动态数组 - JokerJason - 博客园 (cnblogs.com)](https://www.cnblogs.com/jokerjason/p/10419129.html)

\[2][C语言实现动态数组，克服静态数组大小固定的缺陷_C语言中文网 (biancheng.net)](http://c.biancheng.net/cpp/html/2790.html)

