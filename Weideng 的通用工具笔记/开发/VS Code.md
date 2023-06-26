# VS Code

配置文件都在 `.vscode` 文件夹中。

命令 `code <dir>` 用 VS Code 打开指定文件夹。

## 常用功能

### 打开命令面板

Ctrl + Shift + P

可输入信息搜索。

### 预览编辑器

在只能同时打开一个选项卡（编辑器时）：

此时单击打开的是预览编辑器，双击打开的才是正式编辑器，正式编辑器才能多开。

若要取消这个，去掉下图设置选项中的勾：

![img](images/VS Code/clipboard-1617545753184.png)

### 三键快捷键

![img](images/VS Code/clipboard-1617545816471.png)

形如以上的快捷键，意思是：

先按住 Ctrl+K，松开后再按 W

### 实时说明（建议）

Ctrl + Space ：主动触发建议

Ctrl + Shift + Space ：主动触发参数提示

在参数提示中，会显示函数重载版本及函数完整信息。

![img](images/VS Code/clipboard-1617545895696.png)

上图：出现红框内容时，可以按 Alt + 上下键 翻页

若 IDE 的代码提升不正常，则很有可能是当前函数体或内部语法没有写正确，

也有可能是需要重新启动 IDE……

### 函数注释

函数的注释说明应该写在函数声明处，这样就可以实时显示。

### 查看函数

- 查看函数定义
	- Ctrl + 左键单击
	- 光标定位到函数，按F12
- 返回跳转前的位置
	- Ctrl + Shift + 8
	- Alt + 左箭头
- 撤销返回跳转
  - Alt + 右箭头


### 终端控制

![img](images/VS Code/clipboard-1617545953017.png)

上图：即 esc 下方那个按键

- 新建终端：``Ctrl + Shift + ` ``   。
- kill 终端：退出登录即可`exit` 或  `Ctrl + d`

常配合：`Ctrl + 1 or 2 or 3...` **聚焦**到第 `n` 个编辑器。

![img](images/VS Code/clipboard-1617545962118.png)

![img](images/VS Code/clipboard-1617545967129.png)

上两图，要先关闭数字小键盘输入才行。

### 折叠

(Folding)

- click 图标折叠或展开。
- shift + click 折叠或展开内部所有区域。
- Fold All ，(Ctrl+K Ctrl+0) folds all regions in the editor.
- Unfold All ， (Ctrl+K Ctrl+J) unfolds all regions in the editor.

## 常用插件

### 各种语言

#### C++

![img](images/VS Code/clipboard-1617546116459.png)

![img](images/VS Code/clipboard-1617546120863.png)

#### Python

Anaconda 插件已经下架，但其只是扩展包，仍可使用 Python 扩展，解释器路径选择 Conda 路径中的 Python 解释器即可。

![image-20210404223215337](images/VS Code/image-20210404223215337.png)

![image-20210404223238963](images/VS Code/image-20210404223238963.png)

Ctrl + Shift + P 打开命令面板，搜索即可使用 jupyter。（Python 扩展中本就包含了 Jupyter 扩展）

打开命令面板使用 `Pyhton: select interpreter` 即可切换 Conda 环境。

### 实用工具

![img](images/VS Code/clipboard-1617546125537.png)

![img](images/VS Code/clipboard-1617546131149.png)

![image-20220201102414786](images/VS Code/image-20220201102414786.png)

### 前端插件

#### 自动刷新

![img](images/VS Code/clipboard-1617546138192.png)

修改代码保存后，可自动刷新浏览器显示页面。

打开文件时：

![img](images/VS Code/clipboard-1617546152799.png)

### 远端连接

Remote Development（用里面的 **Remote SSH**）

![image-20220115212308036](images/VS Code/image-20220115212308036.png)



密钥免密登录配置：

- 在本地端生成公钥和私钥。
- 在远端的 `~/.ssh` 目录下 的 `authorized_keys` 下插入公钥。（没有则建立）

如果出现错误，注意：

- Windows 系统中，密钥最好在 Git Bash 里用命令生成，有时用 cmd 不行，原因未知。
- 权限问题，`.ssh/` 权限应为 700，`authorized_keys` 权限应为 600 。

### 数据库

SQLTools

![image-20220207193003189](images/VS Code/image-20220207193003189.png)

- 可能遇到的问题
	- MySQL 8.0 改变了密码加密方式：更换协议，改变端口。（具体参见文档） 
	- 不支持 NaN：指定相关项的确切值。

## C++ Windows 配置文件

入门配置，可做参考。

新建文件夹

**要求路径全英文**

文件目录下会有 .code 文件夹，这是VScode设置的文件夹，如果没有就自己建，

**.code文件夹包含最重要的三个文件如下：**

![img](images/VS Code/clipboard-1617546253741.png)

如果没有，自己建，内部可以配置各种编译选项

**可能需要下载相应语言编译器**

建议把编译器文件夹放在C盘根目录下

**下载编译器后设置系统环境变量（步骤如下）：**

控制面板->系统（或电脑属性）->高级系统设置

![img](images/VS Code/clipboard-1617546266468.png)

环境变量

![img](images/VS Code/clipboard-1617546280358.png)

在系统变量 Path 中新建

![img](images/VS Code/clipboard-1617546297109.png)

新建编译器 `C:\mingw64\bin` 路径即可

![img](images/VS Code/clipboard-1617546311179.png)

**三个文件内容及意义如下：**

![img](images/VS Code/clipboard-1617546328907.png)

该文件不能添加注释，但可以修改参数

```json
{
    "configurations": [
        {
            "name": "MinGW64",
            "intelliSenseMode": "gcc-x64",
            "compilerPath": "C:/mingw64/bin/g++.exe",
            "includePath": [
                "${workspaceFolder}"
            ],
            "cppStandard": "c++17"
        }
    ],
    "version": 4
}
```

**编译器路径不正确，会导致找不到头文件等错误**

![img](images/VS Code/clipboard-1617546397888.png)

调试器路径注意不要错误

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "C++ Launch (GDB)",                 // 配置名称，将会在启动配置的下拉菜单中显示
            "type": "cppdbg",                           // 配置类型，这里只能为cppdbg
            "request": "launch",                        // 请求配置类型，可以为launch（启动）或attach（附加）
            "targetArchitecture": "x64",                // 生成目标架构，一般为x86或x64
            "program": "${fileDirname}/build/${fileBasenameNoExtension}.exe", // 将要进行调试的程序的路径
            "args": [],                                 // 程序调试时传递给程序的命令行参数，一般设为空即可
            "stopAtEntry": false,                       // 设为true时程序将暂停在程序入口处，一般设置为false
            "cwd": "${workspaceRoot}",                  // 调试程序时的工作目录，一般为${workspaceRoot}
            "externalConsole": false,                    // 调试时是否显示控制台窗口，一般设置为true显示控制台
            "internalConsoleOptions": "neverOpen",      // 如果不设为neverOpen，调试时会跳到“调试控制台”选项卡",
            "MIMode": "gdb",                            // 指定连接的调试器
            "miDebuggerPath": "C:/mingw64/bin/gdb.exe", // 调试器路径
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for GDB",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": false
                }
            ],
            "preLaunchTask": "Compile" 
        }
    ]
}
```

![img](images/VS Code/clipboard-1617546426453.png)

![img](images/VS Code/clipboard-1617546441286.png)

``` json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Compile",
            "command": "g++",
            "args": [
                "${file}",   // 指定编译源代码文件
                "-o",
                "${fileDirname}/build/${fileBasenameNoExtension}.exe", // 指定输出文件名，不加该参数则默认输出a.exe
                "-O0",      // 优化级别
                "-ggdb3",   // 生成和调试有关的信息
                "-Wall",    // 开启额外警告
                "-static-libgcc",   // 静态链接
                "-std=c++17",       // 使用c++17标准
                "-finput-charset=UTF-8",    //输入编译器文本编码 默认为UTF-8
                "-fexec-charset=GB18030",   //输出exe文件的编码
                "-D _USE_MATH_DEFINES"
            ],

            "problemMatcher": {
                "owner": "cpp",
                "fileLocation": [
                    "absolute",
                ],
                "pattern": {
                    "regexp": "^(.*):(\\d+):(\\d+):\\s+(warning|error):\\s+(.*)$",
                    "file": 1,
                    "line": 2,
                    "column": 3,
                    "severity": 4,
                    "message": 5
                }
            },

            "type": "shell",
            
            "group": {
                "kind": "build",//指定的存放可执行文件的文件夹
                "isDefault": true
            },

            "presentation": {
                "echo": true,
                "reveal": "always", // 在“终端”中显示编译信息的策略，可以为always，silent，never
                 "focus": false,
                 "panel": "shared" // 不同的文件的编译信息共享一个终端面板
            },
        }
    ]
}
```

**新建一个build文件夹（可在上述文件中指定），用于存放可执行文件**

**要运行代码，必须要配置插件，并且新建文件要注意后缀名**

## python 调试

更全面的设置见[官方文档参考](https://code.visualstudio.com/docs/python/debugging#_debugging-by-attaching-over-a-network-connection)

### 命令行脚本

若要调试命令行运行的脚本，可以在脚本前加上延时语句（比如输入），执行命令后点击 Run and Debug ，选择 Debugging using attach process， 依据弹出的进程列表， attach 那个 python 进程。

![image-20230414141207547](images/VS Code/image-20230414141207547.png)

### 对文件方式

直接调试文件。

点击 run and debug （python：file） 即可。

### attach 方式

在本地和远端都要安装 debugpy ：

```shell
python -m pip install --upgrade debugpy
```

VS Code 配置文件：

```json
{
  "name": "Python: Attach",
  "type": "python",
  "request": "attach",
  "port": 5678,
  "host": "localhost",
  "pathMappings": [
    {
      "localRoot": "${workspaceFolder}", // Maps C:\Users\user1\project1
      "remoteRoot": "." // To current working directory ~/project1 ，注意修改，不然找不到要调试的文件。
    }
  ]
}
```

#### 启动方式 1

attach 进程。

1. 在要调试的程序中加入语句：

```shell
import debugpy

# 5678 is the default attach port in the VS Code debug configurations.
# Unless a host and port are specified, host defaults to 127.0.0.1
debugpy.listen(5678)   # 调试 server 监听调试端口
print("Waiting for debugger attach")
debugpy.wait_for_client() # 等待 client 接入调试
debugpy.breakpoint()
print('break on this line')
```

2. 启动程序。
3. 点击，启动 remote attach 。

#### 启动方式 2

使用命令启动程序进行调试：

```shell
# 注意修改参数
python3 -m debugpy --listen 1.2.3.4:5678 --wait-for-client -m myproject
```

## C++ 调试

[官方文档参考](https://code.visualstudio.com/docs/cpp/config-linux)

配置文件可在运行时让 IDE 自己生成初步的配置。

主要配置文件：

- `tasks.json` (compiler build settings)

	编译设置。

- `launch.json` (debugger settings)

	debug 设置。

- `c_cpp_properties.json` (compiler path and IntelliSense settings)

	编译路径和智能提示设置。

### 调试文件或简单项目

`c_cpp_properties.json` 示例：

```json
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [   // 只用于智能提示的头文件
                "${workspaceFolder}/**",   // 自动深入搜索
                "/usr/include/**"
            ],
            "defines": [],
            "compilerPath": "/usr/bin/g++", // 编译器
            "cStandard": "c17",
            "cppStandard": "gnu++14",
            "intelliSenseMode": "linux-gcc-x64"
        }
    ],
    "version": 4
}
```

`tasks.json` 示例：

```json
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "Build",  // 注意 label 用于标识 task
            "command": "/usr/bin/g++",   // 要执行的命令，编译则使用编译器
            "args": [   // 命令参数
                "-fdiagnostics-color=always",
                "-std=c++14",
                "-I '/usr/include/repo'",   // 手动指定头文件目录，该目录用于编译，与用于智能提示的头文件目录不通用。此处用指定详细到 库名 路径，不能用 **
                "-g",
                "${file}",
                "-o",
                "${fileDirname}/build/${fileBasenameNoExtension}",   // 指定可执行文件路径
            ],
            "options": {
                "cwd": "${fileDirname}" // 当前目录
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "detail": "Task generated by Debugger."
        }
    ]
}
```

`launch.json` 示例：

```json
{
    "version": "0.2.0",
    "configurations": [
    {
        "name": "(gdb) Launch",
        "type": "cppdbg",
        "request": "launch",
        "program": "${fileDirname}/build/${fileBasenameNoExtension}",   // 可执行程序的路径
        "args": [],
        "stopAtEntry": false,   // 是否在进入时暂停
        "cwd": "${fileDirname}",
        "environment": [],
        "externalConsole": false,
        "MIMode": "gdb",
        "miDebuggerPath": "/usr/bin/gdb",   // 指定调试器
        "setupCommands": [
            {
                "description": "Enable pretty-printing for gdb",
                "text": "-enable-pretty-printing",
                "ignoreFailures": true
            },
            {
                "description": "Set Disassembly Flavor to Intel",
                "text": "-gdb-set disassembly-flavor intel",
                "ignoreFailures": true
            }
        ],
        "preLaunchTask": "Build"   // 指定调试前的任务 label，即 tasks 中定义的编译行为。
    }
    ]
}
```

### 使用 Cmake

[官方文档参考](https://code.visualstudio.com/docs/cpp/CMake-linux)

全新流程：

Open the Command Palette (Ctrl+Shift+P) ：

1. 新建 Cmake 项目，`CMake: Quick Start`
2. 选择编译器，`CMake: Select a Kit`
3. Select a variant，`CMake: Select Variant`，包含四种，选择一种：
	- `Debug`: disables optimizations and includes debug info.
	-  `Release` : Includes optimizations but no debug info.
	-  `MinRelSize` : Optimizes for size. No debug info. 
	- `RelWithDebInfo` : Optimizes for speed and includes debug info.
4. 编译，`CMake: Build`
5. Debug，`CMake: Debug`

有时配置失误可以重启 VS Code 试一试。

一般调试：

Build 后 Debug 试试即可。

如果 CMakeLists 文件里有设置 `CMAKE_BUILD_TYPE`，那么要为 `Debug` 才能调试：

```cmake
# set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_BUILD_TYPE "Debug")

也可直接不设置 CMAKE_BUILD_TYPE 。
```

底边栏的图标可以快捷点击：

![image-20230502201133585](images/VS Code/image-20230502201133585.png)

与整体项目无关的独立子文件夹内的项目，建议对其打开文件夹后重新配置。

## 远程 GUI？？？

将远程的 GUI（窗口，可以为桌面）显示到本地。

首先将 remote SSH 设置为免密登录。



修改 ssh 配置文件：？？？？？？

```
Host 主机名
  HostName ip地址
  Port 22
  User 用户名
  ForwardX11 yes
  ForwardX11Trusted yes
```

