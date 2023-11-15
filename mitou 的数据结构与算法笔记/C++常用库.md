# C++ 常用库

当前列表只列出部分函数，并不会将库中的函数全部列出。

本笔记大部分是 STL（Standard Template Library， 标准模板库）的内容，STL 包含了算法库、工具库、迭代器库、容器库等内容。

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

### < utility >

- pair 类

	提供两个元素的共同封装，是 tuple 的特殊情况。

```c++
构造
    std::pair<T1, T2> pair;
成员
    first
    second
```

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

- string 类

	可以理解为一个特殊的顺序容器。

```c++
构造
    std::string str;
元素访问
    []
    front, back
    c_str
迭代器
    begin, end
容量
    empty
    size, length
操作
    clear
    insert
    erase
    push_back, pop_back 针对单个字符
    append 可后附字符串或多个字符
    +=
    compare 字典序
    starts_with, ends_with 是否是前后缀（C++20）
    contains 是否含子串（C++23）
    substr 返回指定子串
查找
    find 查找字符
数值转换
    stoi, stol, stoll 转换为整数
    stoul, stoull 转换为无符号整数
    stof, stod, stold 转换为浮点型

模式匹配使用 strstr 函数。
```

## 容器库

### 头文件

```c++
<bitset>
<array>
<vector>
<stack>
<queue>
<deque>
<forward_list>
<list>
<set>
<unorderd_set>
<map>
<unorderd_map>
```

### 所含容器

#### 顺序容器

- array

	C++11，静态连续数组（固定大小）。

- bitset

	位数组，定长

- vector

	动态向量，可将模板类型置为 bool 特化为变长的 bitset，但操作没有定长 bitset 丰富。

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

	优先队列，基于堆。

#### 关联容器

即有序关联容器，基于**红黑树（Red Black Tree）**，元素有序。

- set

	集合，键值唯一。

- multiset

	集合，键值可重复。
	
- map 

	映射，键值唯一。
	
- multimap

	映射，键值可重复。

#### 无序关联容器

基于**散列（Hash、哈希）**，解决冲突使用链地址法，元素无序。

- unordered_set

	C++11，集合，键值唯一。
	
- unordered_multiset

	C++11，集合，键值可重复。

- unordered_map

	C++11，映射，键值唯一。（唯一键没有同义词链）

- unordered_multimap

	C++11，映射，键值可重复。

### 容器概览

容器间具有大量用法一致或相似的函数，以下只列出部分。

#### array

一般不使用，直接使用 `int arr[];` 。

#### bitset

```c++
构造
    std::bitset<N> biset;
元素访问
    []
    test
    all (c++11), any, none 检查是否为 true
    count 返回 true 的数量
容量
    size
修改器
    与或非移位
    set 置为 true
    reset 置为 false
    flip 翻转位
转换
    to_string
    to_ulong, to_ullong 返回无符号整数表示
```

#### vector

变长数组，以倍增的形式增长。

```c++
构造
    std::vector<int> vec;
	assign
元素访问
    []
    front, back
    data 直接访问底层数组
迭代器
    begin, end
容量
    empty
    size
    reserve 预留存储空间
修改器
    clear
    insert
    emplace, emplace_back 在指定位置直接构造元素，比先创建临时对象快。
    erase
    push_back, pop_back
```

#### stack

```c++
push
top
pop
```

#### 队列等

```c++
修改器
    push_front, push_back
    pop_front, pop_back
```

#### 链表等

```c++
操作
    merge 合并
    reverse 反转
    unique 去重
```

#### set map 等

```c++
访问
    []
查找
    count 计数
    find 查找，返回迭代器
    contains (C++ 20) 是否存在

    equal_range 返回两个封装在 pair 中的迭代器，指示范围
    lower_bound
    upper_bound
修改器
    insert
    [] 若没有该 key 则插入 value 
    clear
 	erase
桶接口
    begin，end 指定的桶的开始或末尾
    bucket_count 返回桶数
    max_bucket_count 返回桶的最大数量
    bucket_size 指定桶中的元素数量
    bucket 返回特定键的桶
观察器
    hash_function 返回使用的散列函数，可用于查看某个 key 的散列计算结果
    key_eq 返回比较键值相等性的函数
    
multi
    不能使用 []
unordered
    不能使用 equal_range, lower_bound, upper_bound
    自定义 key 类型需要自定义哈希函数
```

### 注意事项

#### map 的英文

- 集合 Set
- 映射 Map（动词，动名词形式为 Mapping）

#### 优先级

以优先队列为例。

- STL **重载比较符号**操作，只能重载小于（$\lt$）号
- 容器比较的是元素优先级
	- 优先级高的先出队（即小于号比较的数字的意义是优先级大小）
	- 比如 int 类型默认较大的数优先级高，先出队（数字小则优先级小，不优先）。

- 多级比较
	- 优先级相等的元素的入队出队顺序不确定。
	- 考虑增加参数，重载为多级比较，以严格限定顺序。

#### 关联容器的选择

- 当查找操作较多时，使用无序关联容器。（哈希表）
- 当需要元素的顺序性质或修改操作较多，使用有序关联容器。（红黑树）
