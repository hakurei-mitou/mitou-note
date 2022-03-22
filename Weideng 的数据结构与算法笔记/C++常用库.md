# C++常用库

## 概述

当前列表只列出部分函数，并不会将库中的函数全部列出。

输入输出用熟一些基本的操作就可以了，文件相关的操作不是很重要。

STL（即算法库、容器库）是重点要掌握的内容。

## 输入输出库

###  < iostream >

标准流

- std::cin
- std::cout

### < sstream >

缓冲区（略）

### < fstream >

文件流（略）

### < cstdio >

C 改进库

#### 标准流

- getchar
- putchar
- puts
- scanf
- printf

#### 缓冲区

- sscanf

- sprintf

#### 文件流

- fopen
- fclose
- fread
- fwrite
- fgets

## 数值库

### < cmath >

常用数学函数

- abs

- fabs

- exp

	e 的给定次幂

- log

	以 e 为底

- log10

	以 10 为底

- log2 

	以 2 为底
- pow

- sqrt

### < numeric >

- iota

	C++ 11, 递增赋值

- accumulate

	范围求和

- gcd

	C++ 17，最大公因数

- lcm

	C++ 17，最小公倍数

## 算法库

### < algorithm >

#### 不修改序列的操作

- all_of, any_of, none_of

	检查范围内对谓词的满足情况

- for_each

	应用函数到范围中的元素
	
- count

	返回满足判定标准的元素数

- find

	寻找首个满足判定标准的元素
#### 修改序列的操作

可附带谓词、条件。

- copy

	范围复制到新的位置
	
- move

	范围移动到新的位置
	
- fill

	范围赋值
	
- transform

	范围转换，使用函数

- remove

	范围删除
	
- replace

	范围替换，依据判断标准
	
- swap

	交换两个元素的值
	
- swap_ranges

	交换两个范围的元素
	
- iter_swap

	交换两个迭代器所指向的元素

- unique

	在有序条件下移除范围连续重复元素
	
- unique_copy

	创建范围不含连续重复元素的副本
#### 划分操作

- partition

	将范围中的元素分为两组
	
- partition_copy

	复制一个范围，将各元素分为两组
	
- stable_partition

	将元素分为两组，同时保留相对顺序
	
- partition_point

	定位已划分范围的划分点
#### 排序操作

- is_sorted

	C++，检查范围是否升序

- is_sorted_until

	找出最大的已排序子范围

- sort

	快速排序（quick sort）
	对范围内的元素排序，不具有稳定性
	
- partial_sort

	排序一个范围的前 N 个元素
	
- partial_sort_copy

	对范围内的元素进行复制并部分排序
	
- stable_sort

	归并排序 （merge sort）
	对范围内的元素排序，同时保持稳定性
	
- nth_element

	将给定的范围部分排序，并确保其按给定位置排序后应有元素划分
#### 二分搜索操作（已排序范围上）

- lower_bound

	返回第一个大于等于给定值的元素的迭代器
	
- upper_bound

	返回指向第一个大于给定值的元素的迭代器
	
- binary_search

	确定元素是否存在于某一范围中

- equal_range

	返回匹配特定键值的元素范围
#### 最大最小操作

- max
	
- min

#### 排列操作

- next_permutation

- prev_permutation

## 工具库

### < cstdlib >

#### 数值字符串转换

- atof
	
	转换字节字符串为浮点值

- atoi、atol、atoll

	转换字节字符串为整数值

- strtol、strtoll

	转换字节字符串为整数值

### < ctime >

- ctime

## 数值极限

### 宏

- `__INT_MAX__`

- `- __INT_MAX__ - 1` （最小值为 负 max - 1）

## 字符串库

### < cctype >

关于字符类别的一系列函数

- isalnum

	是否是字母和数字

- isalpha

	是否是字母

- islower

- isupper

- isdigit

- isspace

- isblank

- tolower

- toupper

### < cstring >

#### 字符串检验

- strcpy

- strlen

- strcmp

- strchr

- strstr

#### 字符数组操作

- memcmp

- memset

- memcpy

- memmove

### < string >

string 类

## 容器库（STL， Standard Template Library， 标准模板库）

### 在C++标准中，STL 包含以下头文件：

- < algorithm >
- < vector >
- < bitset >
- < array >
- < stack >
- < queue >
- < deque >
- < forward_list >
- < list >
- < set >
- < unorderd_set >
- < map >
- < unorderd_map >
- < numeric >
- < functional >
- < iterator >
- < memory.h >
- < utility >

### 主要有以下内容：

#### 顺序容器

- array

	C++11，静态连续数组

- bitset

	位数组

- vector

	动态向量，可将模板类型置为 bool 特化为 bitset

- deque

	双端队列
	
- list

	双链表
	
- forward_list

	C++11，单链表

#### 容器适配器

- stack

	栈
	
- queue
	
	队列

- priority_queue

	优先队列，基于堆
#### 关联容器

基于红黑树（Red Black Tree）

- set

	唯一键集合，按照键排序

- multiset

	键集合，按照键排序
	
- map 

	键值对集合，按照键排序，键值唯一
	
- multimap

	键值对集合，按照键排序

#### 无序关联容器

基于散列（Hash、哈希）

- unordered_set

	C++11，唯一键集合，按照键生成散列
	
- unordered_multiset

	C++11，键的集合，按照键生成散列

- unordered_map

	C++11，键值对集合，按照键生成散列，键是唯一的

- unordered_multimap

	C++11，键值对集合，按照键生成散列

一般查找操作较多时选择无序关联容器。

