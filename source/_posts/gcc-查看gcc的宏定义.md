---
title: 查看gcc的宏定义
date: 2021-06-06 15:45:48
category: 编译器
tags: gcc
---


## 1、查看gcc默认的内置宏定义

```
gcc -dM -E - < /dev/null
```

或者

```
gcc -dM -E helloworld.c
```

 helloworld.c为用户随意编写的c测试用例，随意拿个c程序就行。


-E 预处理后即停止，不进行编译。预处理后的代码送往标准输出。GCC忽略任何不需要预处理的输入文件。

-dM 告诉预处理器输出有效的宏定义列表(预处理结束时仍然有效的宏定义)。该选项需结合`-E'选项使用。


## 2、打开用户自行设置的宏定义

```
gcc -DDEBUG helloworld.c
```

helloworld.c文件中的宏定义DEBUG如下：

```
#ifdef DEBUG
    printf("DEBUG is defined ! ");
#else
    printf("DEBUG is not defined ! ");
#endif
```



## 总结：gcc内部默认的宏定义+用户自行设置的宏定义=所有的宏定义

