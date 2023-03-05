# AI 技巧及注意事项

## 项目结构

各组分间相互促进思考，反复修改，书写不是严格顺序。

- 项目说明（Project Description）

	- 背景
	- 目标
	- 数据
	- 策略
	- 代码环境
- 导入包（Import Package）

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

import json

import torch
import torch.nn as nn
from torch.utils.data import Dataset, DataLoader
```

- 配置（Configuration）

  使用 `Config` 类，保存配置参数和超参数。

  - 使用字典则无代码提示。
  - 需要频繁修改的项靠下放。
  - 配置只包含基本不变的参数。
  - 使用 `runing_test`，要全程考虑为其特化编码。

```python
class Config:   # 配置类  
    # data path
    data_path = './data'

    # local or cloud
    if 'kaggle/working' in os.getcwd(): 
        at_cloud = True
        # cloud items
    else:
        at_cloud = False
        # local items

    # environment
    device = 'cuda' if torch.cuda.is_available() else 'cpu'   # 类变量
    seed = 923
    model_save_path = './model.ckpt'   # state of model parameters
    # more data: checkpoint_path = './checkpoint.pth'

    # NN structure

    # ...

    # training
    n_example = 20000  # all examples
    n_epoch = 1000
    early_stop = 200
    batch_size = 64

    # runing test
    runing_test = True   # need be modified by hand
    if runing_test:
        n_example = 20
        n_epoch = 10

    def __init__(self):    # 必要时可扩展
        pass

print(f"Using {Config.device} device")
```

- 随机数种子（Random Seed）

```python
def random_seed(seed): 
    torch.backends.cudnn.deterministic = True   # 卷积都使用默认的卷积算法
    torch.backends.cudnn.benchmark = False   # 关闭系统卷积算法选择优化（带随机性）
    np.random.seed(seed)   # 为随机算法设置种子。
    torch.manual_seed(seed)   # 为 CPU 设置种子。
    if torch.cuda.is_available():
        torch.cuda.manual_seed_all(seed)   # 为所有 GPU 设置种子。
```

- 数据处理（Data Processing）

  各种处理函数，便于四处调用。

- 数据集成（Data Integration）

  建立 Dataset 类。

- 模型结构（Model Structure）

- 训练循环（Training Loop）

- 测试，预测，推断（Test，Prediction，Inference）

- 损失曲线（Loss Curve）

- 时空（Time and Space）

	对时间和空间情况进行估计与分析

- 表现分析（Performance Analysis）

	分析模型的表现。

## 项目风格

### 工程风格

使用 main 函数统筹各部分函数：

- 便于清晰描述项目流程，也便于扩展到多参数对比训练，多批次数据训练等。
- 简明，模块化，易扩展，但不便于反复调试，每次调试都要重复计算。
- 输出都集中到 main 函数底部 ，查看需要来回跳转。

```python
def main():
	same_seed(Config.seed)
	...
	...

# begin    
main()   # 扩展时能被当作一般函数调用

# 扩展
# def project()
config1
main(xxx)
config2
main(xxx)
```

### Jupyter Notebook 风格

不使用 main 函数统筹函数，而是直接使用 main Cell 统筹流程：

- 不必每次调试都从头开始运行，可以反复利用已经计算出的结果和输出测试信息。
- 变量命名要准确，Notebook 风格会存在大量全局变量。
- 分散输出，便于描述思路。

### 混合风格

- 使用基本结构。
- 工程风格为主，但不使用 main 函数统筹项目。
- 将 main 函数应当包含的内容按照 Notebook 风格分散开，为单元编写整体函数，于单元内调用。（相当于将全局当作一个 main 函数）便于单元测试，分散输出和利用已有计算信息。

```python 
# 一般 begin 位置，相当于把全局当作 main 。

# Cell 1

def operation1():
    pass

operation1()   # 调用

# Cell 2

...
operation2   # 不是函数，是 notebook 风格的全局域的操作。
...

# Cell 3

def other_functions():
    pass

def operation3():
    other_functions()

operation3()   # 调用，对于以后不必每次都重新运行的操作，适当新开一个 Cell ，比如建立数据集。
...
```

## 运行测试

云端和本地都要运行测试，因为云端可能有 GPU 等环境问题：

- 本地（Local）
- 云端（Cloud）

程序流程测试：

- 小数据量，小 epoch 。

  项目构建全程使用，测试程序问题。

- 全数据量，小 epoch 。

  训练代码构建完毕后使用，主要测试全数据下是否有数据加载错误。

  - 数据处理在小数据量已经测试过，可适当删减，以加快训练速度。
  - 大 epoch 不必测试，因为小 epoch 已经足够。

## 包版本

包版本的分析不是必需的，在需要解决包问题时再做此工作。

```python
'''origin:
输出...
'''

def package_version():

    ! python -V

    import pkg_resources
    import types

    poorly_named_packages = {
        "PIL": "Pillow",
        "sklearn": "scikit-learn"
    }   # package name is different to import name

    imports = []
    for name, val in globals().items():
        if isinstance(val, types.ModuleType):
            name = val.__name__.split(".")[0]
        elif isinstance(val, type):
            name = val.__module__.split(".")[0]
        if name in poorly_named_packages.keys():
            name = poorly_named_packages[name]
        imports.append(name)

    for x in pkg_resources.working_set:
        if x.project_name in imports and x.project_name != "pip":
            print(f"{x.project_name} {x.version}")

package_version()
```

## 时空

设置收集信息的类及函数，在需要的地方使用及打印。

估计与分析的思想是一致的，只是主要目的不同：

- 估计可以给项目决策和实际训练提供参考，目的是预估训练消耗。
- 分析可比较准确地展示出项目使用了多少资源，目的是展示训练消耗。

没有必要每次都估计和分析，依实际需求而定。

估计和分析可以在项目编码完了再进行修改添加，没有必要一开始就考虑为其特殊化。

估计相当困难：

- 用少量 example 估计时，时间与 example 往往不是线性关系。
- 用少量 epoch 估计时，时间往往随着计算资源（GPU）的分配等导致 epoch 的训练时间不同，而估计偏差大。

建议使用 early_stop 来尽早停止训练，并保存模型和优化器的所有参数。

### 基本概念

- FLOPS（floating point operations per second）

	每秒浮点运算次数。

- FLOPs（floating point operations）（s 表复数）

	（总）浮点运算次数，可衡量计算量。

- GFLOPs

	十亿次浮点运算，$1 GFLOPs = 10^9 FLOP$ 。
	
- MACs（Multiply–Accumulate Operations）

  有时也用 MAdd 表示，指乘加（$a + b \times c$）运算数，包含一个乘法，一个加法操作。通常 MACs 是 FLOPs 的两倍。

### 空间

- 外存（External Memory，Disk）

	主要是数据集大小。

- 内存（Memory，RAM）

	数据，模型，中间变量，系统环境。

- 显存（Video Memory，可用 GPU 简要表示）

	数据，batch，模型，中间变量。

- 模型（Model Size）

	参数量，大小。

### 时间

- 数据处理
- CPU，GPU 占用
- FLOPs
- 数据处理时间
- 训练时间
- 测试时间

### 工具

#### 运行时间

- `time.time()`

	真实时间。单位：秒。

- `time.perf_counter()`

	性能计数器的值，是 CPU 时间，包括睡眠时间，精度高。

- `time.process_time()`

	进程运行时间，是 CPU 时间，不包括睡眠时间。

```python
import time

begin = time.time()
end = time.time()

runing_time = end - begin
```

#### 显卡信息

- `nvidia-smi` 命令。

	可以查看显卡的信息。

#### 网络参数和计算量

- 有一定限制，具体限制见官方信息
	- 拼接的模型组分的引用不能识别到，需要单独传入。
	- 只支持卷积层和线性层。
- torchstat 包

```python
from torchstat import stat

stat(model, (input_shape))
```

- torchsummary 包

```python
# install torch-summary

from torchsummary import summary

summary(model, input_shape)
```

## 时空控制

### 空间控制

- 进行空间分析。

- 及时回收大空间变量。

  可使用 gc 模块。

```python
import gc

# 删除变量引用
del variable1, variable2   # 引用计数

# 立即回收垃圾
gc.collect()
```

- 全体数据分步读取，输入项目。（分步内仍能保证数据有足够的随机性时使用）

  - 每个分步跑完一遍 epoch 后，模型遇到的是全新的数据，训练不稳定。
  - 形成 batch 时只能每一步内 shuffle ，不能全局 shuffle 。
  - 此时建议缩减 epoch ，增大全体数据的循环次数。（数据处理会增加）
  - 训练流程变复杂，需要注意 epoch 数量的处理。
- 用到时再读取和处理。（数据处理简单，且 epoch 较少时采用）

  - 多个 epoch 会反复 I/O 和处理数据，反复 I/O 和复杂的处理过程，会耗费大量的时间。
  - I/O 是非常耗时的，可能在 epoch 中反复数据处理消耗的时间还没有一次 I/O 多。
- 将处理后的数据存入文件并构造索引，用到时再读取。（数据处理复杂，样本较大，epoch 较多时采用）
  - 利用外存空间，为了提升 I/O 效率，需要合理设置文件的大小和构造索引。
  - 当每次取数据只读取存储的文件的一小部分时，可能 I/O 次数会非常多。

```python
# 张量存储与读取
torch.save(obj, 'xxx.pt')
torch.load('xxx.pt')
```

- 调整数据类型。

### 时间控制



## Kaggle 使用

- 时间
  - CPU 与 GPU，每周限制 30 小时，每次 CPU 和 GPU session 限制 12 小时，TPU session 限制 9 小时。
  - 时间容量刷新时间为每周六北京时间早 8 点。
  - 后台可以同时运行两个 session 。
  - 计时包括交互运行时间和后台运行时间。
  - 关闭页面前要记得关闭 session ，否则会一直占用运行时长直到 20 分钟超时。
- 存储
  - 外存 70 G 。
  - 内存 13 G 。（环境本身约占 500 M，建议以 1 G 计算）
  - 显存 16 G 。
  - 输出文件夹（working）容量 20 G 。
- 技巧
  - Kaggle 不会在 12 小时限制前终止正在运行的程序。
  - 当时长快用完时，在后台跑一个 12 小时的程序，等于白嫖约 12 小时；跑两个 12 小时的程序，等于白嫖约 24 小时。
  - 在训练完后可以在 Kaggle Jupyter Notebook 界面点击查看当次训练的存储和计算资源的占用情况。
- 注意
	- persistence 选项只针对 interactive session ，对 save version 不起作用。


## SkLearn 算法选择图

![](images/AI 技巧及注意事项/SkLearn 算法选择图.png)

![](images/AI 技巧及注意事项/SkLearn 算法选择图（中文）.png)

## 训练技巧汇总

？？？





## 编码杂项

- PyTorch 中方法名末尾如果带下划线，表示会修改变量本身（in-place）：`.abs_(), .abs()  ` 。
- `assert` 可用于调试。
- Anaconda 不能直接安装的第三方包可以进入 anaconda prompt 中用 pip 安装。

## 注意点杂项

- 图像任务比单纯的数据任务更需要泛化性。