# ROS

- 机器人操作系统（Robot Operating System，ROS）

	对机器人的软硬件进行抽象模块化的一套软件框架。

[视频课程参考文档-赵虚左](http://www.autolabor.com.cn/book/ROSTutorials/)

ROS 是进程（也称为 Node，节点）的分布式框架，每个节点由一个进程表示。

ROS 一般在 Ubuntu 上使用，新版本 ROS2 也可在 Windows 10 上使用，可能工作是使用历史上的版本，所以建议使用 Ubuntu 。

ROS 程序一般使用 C++ 或 Python 。

## 使用注意

- 安装的版本一定要和 Ubuntu 的版本对应上，否则会找不到包。
- 安装可参考：[Ubuntu20.04.4安装ROS Noetic详细教程 - 知乎](https://zhuanlan.zhihu.com/p/515361781)

## ROS 程序构建基本步骤

1. 创建工作空间（workspace）

	1. `mkdir -p demo_ws/src`
	2. 在工作空间内使用 catkin_make 命令建立环境，会生成 build devel 两个文件夹。

2. 创建功能包并添加依赖

	一般在 src 子目录下建立，使用 `catkin_create_pkg <自定义包名> roscpp rospy std_msgs` 命令。

	- 包名没有特定后缀，无后缀即可。
	- roscpp 为 C++ 实现的库。
	- rospy 为 Python 实现的库。
	- std_msgs 为标准消息库。

3. 编辑源文件

4. 编辑配置文件（基于 CMake）

5. 编译及运行

### C++

- 在包内编辑源代码文件：

```c++
#include "ros/ros.h"   // 头文件

int main(int argc, char *argv[])
{
    //执行 ros 节点初始化
    ros::init(argc,argv,"hello");   // 节点名称
    
    //创建 ros 节点句柄(本例非必须)
    ros::NodeHandle nh;
    
    //控制台输出 hello world
    ROS_INFO("hello world!");   // 输出日志

    return 0;
}
```

- 编辑包内的 Cmakelist.txt 文件：

```shell
add_executable(
	指定编译后的目标文件名（不加扩展名 .o）
    src/源文件名.cpp
)
    
target_link_libraries(
    目标文件名
	${catkin_LIBRARIES}
)
```

- 设置基本运行环境变量 `source ~/工作空间/devel/setup.bash` 。（不必每次反复执行，也可添加进环境变量配置文件）
- 在工作空间目录执行 `catkin_make` 进行编译。
- 启动核心 `roscore` 。
- 启动节点：`rosrun 包名 C++节点（即可执行文件名）` 。可执行文件名（不加扩展名 `.out`，与设置的目标文件名一致即可）

### Python

- 在包内建立 `scripts` 文件夹。
- 编辑 Python 文件：

```python
#! /usr/bin/env python   # 指定解释器

import rospy   # 导入包

if __name__ == "__main__":   # 主入口
    rospy.init_node("hello")   # 节点名称
    rospy.loginfo("Hello World!!!!")
```

- 添加可执行权限：`chmod +x 文件名.py ` 。
- 编辑包内的 Cmakelist.txt 文件：

```shell
catkin_install_python(
    PROGRAMS scripts/文件名.py
    DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)
```

- 设置基本运行环境变量 `source ~/工作空间/devel/setup.bash` 。
- 在工作空间目录执行 `catkin_make` 进行编译。
- 启动核心 `roscore` 。
- 启动节点：`rosrun 包名 文件名.py` 。

### launch 文件

launch 文件可以一次启动多个节点，自动完成启动核心等动作（仍要先使用 `source` 和进行编译）。

- 在包内建立 launch 文件夹，然后建立 `文件名.launch` 文件（XML 格式），设置启动的节点：

```xml
<launch>
    <!-- 输出 hello world 的节点 -->
    <node pkg="helloworld" type="demo_hello" name="hello" output="screen" />
    
    <!-- 海龟示例 -->
    <node pkg="turtlesim" type="turtlesim_node" name="t1"/>
    <node pkg="turtlesim" type="turtle_teleop_key" name="key1" />
</launch>
```

- 标签及属性：

	- node

		包含的某个节点。

	- pkg

		功能包。

	- type

		被运行的节点文件名（不是路径）

		- C++ 为编译后的可执行文件名。
		- Python 为源文件名（有可执行权限）。

	- name

		为节点命名。

	- output

		日志输出到屏幕上要为 `output="screen"`。

- 运行 launch 文件：`roslaunch 包名 launch文件名` 。

## ROS 架构

### ROS 文件系统

即 ROS 的项目文件夹结构。

![image-20221031184020264](images/ROS/image-20221031184020264.png)

- WorkSpace 
	
	自定义的工作空间。

    - build

        编译空间，用于存放 CMake 和 catkin 的缓存信息、配置信息和其他中间文件。

    - deve

        开发空间，用于存放编译后生成的目标文件，包括头文件、动态&静态链接库、可执行文件等。

    - src 源码

        - `CMakeLists.txt`

            工作空间内多个包的编译的配置。

        - package

            功能包（ROS 基本单元）包含多个节点、库与配置文件，包名所有字母小写，只能由字母、数字与下划线组成

            - `CMakeLists.txt`

            	包的配置编译规则，比如源文件、依赖项、目标文件。

            - `package.xml`

            	包的信息，如：包名、版本、作者、依赖项等（以前版本是 `manifest.xml`）。

            - scripts

            	存储 Python 或 Shell 等脚本文件。
  
            - src
  
            	存储 C++ 源文件。
  
            - include
  
            	头文件。
  
            - msg
  
            	消息通信格式文件。
  
            - srv
  
            	服务通信格式文件。
  
            - action
  
            	动作格式文件。
  
            - launch
  
            	存放 launch 文件。
  
            - config
  
            	配置信息。

### ROS 计算图

- `rqy_graph` 

	节点启动后使用，可以启动 ROS 的节点数据通信图形演示程序。

## ROS 通信机制

主要有三种形式：

- 话题通信（发布订阅模式）
- 服务通信（请求响应模式）
- 参数服务器（参数共享模式）

### 话题通信

#### 角色

- ROS Master

	管理者，根据话题建立 Talker 和 Listener 到连接。

- Talker（Publisher）

	发布者。

- Listener（Subscriber）

	订阅者。

#### 通信流程

使用 RPC 协议和 TCP 协议。

![image-20221031192235106](images/ROS/image-20221031192235106.png)

1. 注册信息

	使用 RPC 注册话题和自身节点信息。（Talker 或 Listener，都可存在多个）

2. 话题匹配

	Master 根据注册表中的信息进行匹配，并通过 RPC 向 Listener 发送 Talker 的 RPC 地址信息。

3. 连接请求

	Listener 根据接收到的 RPC 地址，通过 RPC 向 Talker 发送连接请求，传输订阅的话题名称、消息类型以及通信协议（TCP/UDP）。

4. 确认请求

	Talker 接收到 Listener 的请求后，也是通过 RPC 向 Listener 确认连接信息，并发送自身的 TCP 地址信息。

5. 建立连接

	Listener 通过 TCP 与 Talker 建立连接。（建立连接后可关闭 Master）

6. 发布消息

	Talker 可以开始发布消息。

#### C++

- 发布者

```c++
#include "ros/ros.h"
#include "std_msgs/String.h"   // 数据类型

int main(int argc, char *argv[]) {

    // 传入参数。
    // 设置节点名称，要保证在 ROS 网络中唯一。
    ros::init(argc,argv,"publisher");
    
    ros::NodeHandle nh;

    // 注册话题。
    // 发布的消息类型，话题名，消息队列最大长度。
    ros::Publisher publisher = nh.advertise<std_msgs::String>("topic_name", 10);

    // 设置执行频率，这里是 1 Hz，即每秒 1 次。
    ros::Rate rate(1);
    
    // 消息类型
    std_msgs::String msg;

    char str[] = "hello";

    // 若当前节点正在运行 ok
    while(ros::ok) {
        msg.data = str;
        publisher.publish(msg);

        // 打印日志
        ROS_INFO("publish: %s", str);   

        // 按设置的频率睡眠
        rate.sleep();

        // 让 subscriber 立即处理一次回调函数。
        ros::spinOnce();   // 这句也可不加，官方建议加上。
    }

    return 0;
}
```

- 订阅者

```c++
#include "ros/ros.h"
#include "std_msgs/String.h"


// 回调函数，处理收到的信息，一次一条，由 ConstPtr 指向。
void process_msg(const std_msgs::String::ConstPtr & msg_p) {
    ROS_INFO("get: %s", msg_p->data.c_str());
}

int main(int argc, char * argv[]) {
    ros::init(argc, argv, "subscriber");

    ros::NodeHandle nh;

    ros::Subscriber subscriber = nh.subscribe<std_msgs::String>("topic_name", 10, process_msg);

    // 自动循环处理回调函数。
    ros::spin();

    return 0;
}
```

#### Python

- 发布者

```python
#! /usr/bin/env python   # 指定解释器

import rospy   # 导入包
from std_msgs.msg import String

if __name__ == "__main__":   # 主入口
    rospy.init_node("publisher_py")
    
    publisher = rospy.Publisher("topic_name", String, queue_size=10)
    msg = String("hello hello")

    rate = rospy.Rate(1)
    while not rospy.is_shutdown():
        publisher.publish(msg)
        
        rospy.loginfo(f"publish {msg.data}")
        rate.sleep()
```

- 订阅者

```python
#! /usr/bin/env python   # 指定解释器

import rospy   # 导入包
from std_msgs.msg import String

def process_data(msg):
    rospy.loginfo(f"get {msg}")

if __name__ == "__main__":   # 主入口
    rospy.init_node("subscriber_py")

    publisher = rospy.Subscriber("topic_name", String, process_data, queue_size=10, )
    
    rospy.spin()   # 阻塞当前线程，使回调函数线程不断处理。
```

- 记得添加可执行权限。
- 注意，rospy 中没有 `spinOnce()` 函数，其有的 `.spin()` 函数功能为使该线程阻塞。rospy 有自己的处理回调函数的线程。

#### 消息类型

##### 基本类型

ROS 常用数据类型包括：

- int32，uint32 ...

- float32，float64

- string

- time

	时间。

- duration

	时间间隔。

- header

	包含时间戳和坐标信息。

##### 自定义消息类型

即自定义一个消息类，其内的变量成员是 ROS 的原生类型。

1. 在工作空间建立 `msg` 目录创建 `类名.msg` 文件，最后会生成包含该文件指定数据类型的类，文件格式如下：

```
# 以下数据类型必须使用小写
string name
int32 age
...
```

2. 修改配置文件。

```
# 在 package.xml 中添加编译依赖和执行依赖。  
<build_depend>message_generation</build_depend>
<exec_depend>message_runtime</exec_depend>

# 以下为编辑 CMakeLists.txt 文件

# 在 find_package 添加 message_generation
find_package(catkin REQUIRED COMPONENTS
	std_msgs   # 必须有
	message_generation
)

# 配置 msg 源文件
add_message_files(
  FILES
  <类名>.msg
)

# 生成消息时依赖于 std_msgs
generate_messages(
  DEPENDENCIES
  std_msgs
)

#执行时依赖
catkin_package(
  CATKIN_DEPENDS roscpp rospy std_msgs message_runtime   # 添加了 message_runtime
)
```

3. 使用 `catkin_make` 编译即可生成可以被 Python 或 C++ 调用的中间文件。
  - 在 `devel/include` 中会生成 `类名.h` 文件，可由 C++ 导入。
  - 在 `devel/lib` 中会生成 `_类名.py` 文件，可由 Python 导入。

- C++ 使用

```
# 导入
#include "包名/类名.h"
//可修改 VS Code 的 C++ include 路径，以供代码提示。

# 将包名作为其的名称空间。
包名::类名 obj   //建立对象

# 修改 CMakeLists.txt 的 add_dependencies
add_dependencies(原文件生成的目标文件名 ${PROJECT_NAME}_generate_messages_cpp)
```

- Python 使用

```python
# 导入
from 包名.msg import 类名
//可修改 VS Code 的 Python include 路径，以供代码提示。


```

