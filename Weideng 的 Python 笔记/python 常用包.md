# python 常用包

## os

虽然可以通过 `!`  使用系统命令，但 os 包能够提供跨平台的统一接口。

```python
import os
from os import Path

os.getcwd()   # 获取当前工作目录
```

## pathlib

从 python3.4 开始，pathlib 已经正式成为标准库，可用于以更简便的方式取代 os 库。

```python
import pathlib
from pathlib import Path


data_folder = Path("source_data/text_files/")

# 拼接路径
file_to_open = data_folder / "raw_data.txt"

# 读取文件
print(file_to_open.read_text())

# 文件以面向对象形式描述，可直接使用各属性
filename = Path("source_data/text_files/raw_data.txt")

print(filename.name)
# prints "raw_data.txt"

print(filename.suffix)
# prints "txt"

print(filename.stem)
# prints "raw_data"

if filename.exists():
    pass
```

## logging

python 内置的日志模块。

| 日志级别 | 使用场景                                                     |
| :------- | :----------------------------------------------------------- |
| DEBUG    | 记录详细信息，方便定位问题进行调试，在生产环境一般不开启 DEBUG 。 |
| INFO     | 记录关键代码点的信息，生产环境通常会设置为 INFO 级别。       |
| WARNING  | 记录某些不预期发生的情况，如磁盘不足。                       |
| ERROR    | 由于一个更严重的问题导致某些功能不能正常运行时记录的信息。   |
| CRITICAL | 当发生严重错误，导致应用程序不能继续运行时记录的信息。       |

日志等级（level）默认为 Warning ，低于 level 的日志都不会输出，高于 level 的日志输出到标准输出流，level 可重新设置。

```python
import logging


logging.basicConfig(
    level=logging.DEBUG,   # 设定日志级别
    filename="test.log",   # 将日志输出到文件
    filemode= 'w',   # 调整输出到文件的模式
    format='%(asctime)s %(levelname)s %(name)s %(message)s',   # 调整日志头内容
)

# 记录日志
logging.debug("this is debug")
logging.info("this is info")
logging.error("this is error")
```

## argparse

python 内置的命令参数解析器。

基本用法：

```python
import argparse


def parse_args():
    parser = argparse.ArgumentParser(description="Demo of argparse")
    parser.add_argument('--demo_name', default='parser.py', help='the name of this demo')

    args = parser.parse_args()
    return args

if __name__ == '__main__':
    args = parse_args()
    print(args.demo_name)
```

`add_argument` 的常用参数：

```python
import argparse


def parse_args():
    parser = argparse.ArgumentParser(description="Demo of argparse")

    parser.add_argument(
        '--demo_name',   # 指定传入参数名
        '-d',   # 别名，可多个，一般指定简写
        default='demo',   # 默认参数
        help='the name of this demo',   # 参数说明
        type=str,   # 将参数解析为 type 类型, 默认 str ，其它可 int bool
        required=True,   # 必须显式传入该参数
        choices=['demo', 'demoB', 'demoC'],   # 限定参数的可能值
        dest='name',   # 指定调用参数名，默认为传入参数名
        nargs=2   # 指定可以传入的参数个数，存储为列表，多个参数必须要在 choices 内
    )

    args = parser.parse_args()
    return args

if __name__ == '__main__':
    args = parse_args()
    print(args.name)



# 其中，nargs 可以设置为如下值：

值  含义
N   参数的绝对个数（例如：3）
'?'   0 或 1 个参数
'*'   0 或无限参数
'+'   无限，并且至少一个参数（1 或无限参数）


# 对于传入 bool 的情况:
# bool() 这个类型函数，有：
bool('True') = True
bool('False') = True # 会把 True 和 Flase 都解析为 True
bool('') = False   # 空串才会 False
bool(0) = False
bool(None) = False

# 可以自定义类型解析函数：
def str2bool(x):
    assert x in ('True', 'False')
    return x == 'True'

# 使用类型解析函数
parser.add_argument(
    '--bool',
    type=str2bool
)
```



