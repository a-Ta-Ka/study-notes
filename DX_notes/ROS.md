# ROS的初步介绍

## ROS是什么？

ROS是一个**开发框架**，它里面由以下几个东西组成
- 应用功能
- 开发工具
- 通信机制
- 生态系统

它的主要作用是提高机器人研发中的**软件复用率**，为机器人软件开发提供一套标准化的“通信骨架”和“复用生态”，让开发者不必从零造轮子，而是专注于算法和业务逻辑。

### 应用功能

![[节点.png]]
**节点**是应用功能的组成部分，把一个个的节点组合起来，就构成了复杂的应用功能。

- **节点**：执行具体任务的进程（独立运行的可执行文件）。不同节点可使用不同的编程语言，且可分布式运行在不同主机上。
    
- **功能包**：把实现某类功能的节点代码、配置文件、依赖声明等集中放进一个文件夹，按照规范打包，就成为了一个功能包。它是 ROS 中组织和分发软件代码的基本单元，也是编译的基本单位。
    
- **元功能包**：对于复杂需求，把多个强相关的功能包集合起来，形成一个元功能包。通过复用别人的元功能包，就能快速拓展我们自己的应用能力。

### 开发工具
辅助我们启动、观察、调试节点的东西。

- **命令行工具**：最基础的工具。
    
- **图形化工具**：强大的视觉辅助（如 `RViz` 3D可视化、`rqt_graph` 系统拓扑图、`rqt_plot` 数据曲线）。
    
- **编译与运行工具**：把代码变成可执行文件并管理环境。

### 通信机制
ROS有两个基础而强大的通信机制，他们分别叫做话题和服务通信。
#### 话题通信![[话题通信.png]]
- **模式**：发布/订阅。
	  
- **特点**：**单向、异步**。发布者只管往外发（比如雷达一直发扫描数据），订阅者只管听，有数据就处理。
    
- **用途**：适合**连续不断的数据传输**。

#### 服务通信![[服务通信.png]]
- **模式**：请求/响应。
    
- **特点**：**双向、同步**。客户端发出请求后，会“阻塞等待”服务端算完并把结果发回来。
    
- **用途**：适合需要明确结果的指令。


| 比较方向 | 话题       | 服务    |
| ---- | -------- | ----- |
| 通信模式 | 发布/订阅    | 请求/相应 |
| 数据流向 | 单向       | 双向    |
| 交互方式 | 异步，不阻塞   | 同步，阻塞 |
| 关系   | 一对多（多对多） | 一对一   |
**其他通信方式**：

- **动作**：用于长耗时任务。
    
- **参数**：用于节点的全局配置。

### 生态系统
ROS有自己强大的生态系统，我们可以很方便的找到、运用前人代代相传积累下来的祖传代码，能让我们直接白嫖全世界顶尖开发者的成果。
![[ros中的生态系统.png]]

___

## ROS命令行工具
下面是一些ros里面最常用最核心的命令行工具及常用的命令行

- rostopic：话题工具
```bash
# 1. 列出当前系统中所有活跃的话题
rostopic list

# 2. 实时打印指定话题内流动的数据
rostopic echo /话题名

# 3. 查看指定话题的详细信息
rostopic info /话题名

# 4. 测试话题的发布频率
rostopic hz /话题名

# 5. 通过命令行手动向话题发送数据
rostopic pub /话题名 消息类型 "具体数据"
```


- rosservice：服务工具
```bash
# 1. 列出当前系统中所有可用的服务
rosservice list

# 2. 查看指定服务的详细信息
rosservice info /服务名

# 3. 查看指定服务的数据类型
rosservice type /服务名

# 4. 手动调用服务并传入参数
rosservice call /服务名 "请求参数"

# 5. 根据服务类型反查系统中哪些服务使用了它
rosservice find 服务类型
```


- rosparam：参数工具
```bash
# 1. 列出参数服务器中所有的参数名
rosparam list

# 2. 获取指定参数的当前值
rosparam get /参数名

# 3. 动态设置（修改）指定参数的值
rosparam set /参数名 具体数值

# 4. 将当前所有的参数保存到一个 YAML 文件中备份
rosparam dump 文件名.yaml

# 5. 从 YAML 文件中批量加载参数到系统中
rosparam load 文件名.yaml
```


- rosnode：节点工具
```bash
# 1. 列出当前系统里所有正在运行的节点
rosnode list
#也可以使用和这个 rqt_graph 图形化界面 

# 2. 查看指定节点的工作记录
rosnode info /节点名

# 3. 测试节点是否还能正常通信
rosnode ping /节点名

# 4. 在终端远程强制关闭指定的节点
rosnode kill /节点名

# 5. 清理僵尸节点
rosnode cleanup
```


- rosmsg：消息类型工具
```bash
# 1. 列出系统中所有可用的消息类型
rosmsg list

# 2. 查看某个具体消息类型的内部字段结构
rosmsg show 消息类型

# 3. 查看某个功能包里定义了哪些消息
rosmsg package 包名

```


- rossrv：服务类型工具
```bash
# 1. 列出系统中所有可用的服务类型
rossrv list

# 2. 查看某个服务类型的内部字段结构
rossrv show 服务类型

# 3. 查看某个功能包里定义了哪些服务
rossrv package 包名

```


- rosbag
```bash
#record是录制命令，-a是all，-O是来指定保存文件名，这个命令会录制所有的数据
rosbag record -a -O 名字

#打开播放某一个录制包，用以复现
rosbag play cmd_record.bag

```


___
## 工作空间
### 概念
一个用来**存放 ROS 功能包、进行编译、并管理运行环境的地方**。
我们通过catkin来快速构建工作空间；catkin像是CMake的拓展一样，它加上了ROS包的管理和环境配置的工具。
![[ros工作空间结构.png]]

- **src**（代码空间）：用来放ROS包的地方，无论是自己写的还是clone别人的
- **build**（编译空间）：放中间文件的地方，一般不用管？
- **devel**（开发空间）：可执行文件、库、脚本将放在这里
- **install**（安装空间）：和devel有点重合，将最终产物放在这里，用来打包发给别人。
> 在ROS2中，devel和install合在一起了

### 相关命令

首先是创造工作空间
```cpp
mkdir -p ~/ros_ws/src   //制造对应的文件夹
cd ~/ros_ws/src         //进入文件夹
catkin_init_workspace   //初始化此文件夹为工作空间
```

然后是编译工作空间
```cpp
cd ~/ros_ws/            //回到根目录
catkin_make             //进行一个编译
```

设置环境变量
```cpp
source devel/setup.bash   //让系统找得到你的功能包、库
```

### 功能包 

创造功能包
```cpp
cd ~/ros_ws/src
catkin_create_pkg 名字 依赖项... (std_msgs rospy roscpp) //创造！
```

编译功能包
```cpp
cd ~/ros_ws                        //回到根目录
catkin_make                        //编译一下
source ~/ros_ws/devel/setup.bash   //让系统能认识我的包
```

___
## 话题通信

在工作空间src中，我创造了一个名为learn_topic的功能包
`catkin_create_pkg learn_parameter roscpp rospy std_msgs`

再在里面的src中写上我们的话题通信代码，接下来就主要以代码来解释话题通信的基本实现。
### 发布者Publisher

重要步骤：
- 初始化ROS节点
- 创造节点句柄
- 创造发布者对象，绑定话题和消息类型
- 设置循环
- 发送消息
```cpp
#include <ros/ros.h>

#include <learn_topic/Person.h>  //引入msg类型头文件

//传入标准参数argc，argv
int main(int argc, char **argv)
{

//初始化节点，argc表示参数个数，argv是特殊命名，第三个是默认命名
ros::init(argc, argv, "velocity_publisher");

//创造节点句柄，通过句柄来调度ROS系统的资源
ros::NodeHandle n;

//创造发布者并命名，通过<句柄>.advertise来定义消息类型，话题名字，队列长度
ros::Publisher person_info_pub = n.advertise<learn_topic::Person>("/person_info", 10);

//设置循环频率
ros::Rate loop_rate(10);

//节点还活着ok就还能回你true
while (ros::ok())

{

//创造消息类型，并填充数据
learn_topic::Person person_msg;

person_msg.name = "Alice";

person_msg.age = 25;

person_msg.set = learn_topic::Person::female;

//调用发布者，发出消息
person_info_pub.publish(person_msg);

//打印日志
ROS_INFO("publish person info[%s, %d, %d]",
person_msg.name.c_str(), person_msg.age, person_msg.set);

//根据所给频率进行阻塞
loop_rate.sleep();

}

return 0;

}
```

### 订阅者Subscriber

重要步骤：
- 初始化ROS节点
- 创造节点句柄
- 创造订阅者对象，绑定话题名和回调函数
- 编写回调函数
- 调用 `ros::spin()` 或 `ros::spinOnce()` 让程序进入循环
  
```cpp
#include <ros/ros.h>

#include <learn_topic/Person.h>   //仍旧引入消息类型

//回调函数，会收到话题传来的消息类型，我这里只是打印在bash方便看
void poseCallback(const learn_topic::Person::ConstPtr& msg)

{

ROS_INFO("Person info: name: %s, age: %d, set: %d", msg->name.c_str(), msg->age, msg->set);

}

int main(int argc, char **argv)

{

//初始化节点
ros::init(argc, argv, "pose_subscriber");

//创造节点句柄
ros::NodeHandle n;

//创造订阅者对象，参数是话题名，缓存，回调函数
ros::Subscriber pose_sub = n.subscribe("/person_info", 10, poseCallback);

//一个不断循环的阻塞函数，当节点还活着，就时刻监听话题，有数据进来就回调函数
ros::spin();
//ros::spinOnce()只检测一次消息，有没有都往下走

return 0;

}
```

### 自定义话题消息

首先，先在功能包内创造一个msg文件夹，再在里面定义msg文件。
```cpp
//这就是上面没有提到的消息类型
string name
uint8 age
uint8 set

uint8 unknown = 0
uint8 male = 1
uint8 female = 2
```

再配置 `package.xml`
```xml
<build_depend>message_generation</build_depend>
//添加编译时能把msg文件转化为cpp、py文件的依赖
<exec_depend>message_runtime</exec_depend>
//添加运行时运行消息所需要的库
```

最后配置 `CMakeLists.txt
```cmake
# 1. 查找依赖
find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  std_msgs
  message_generation   # 告诉系统我要编译消息
)

# 2. 添加你定义的 .msg 文件
add_message_files(
  FILES
  Person.msg           # 你的消息文件名
)

# 3. 生成消息（必须加上 std_msgs）
generate_messages(
  DEPENDENCIES
  std_msgs             
# 导入类型，如果有用到其他消息类型请在这里加上其对应功能包的名字
)

# 4. 导出运行时依赖
catkin_package(
  CATKIN_DEPENDS roscpp rospy std_msgs message_runtime
)
# 告诉别人自己应该要用什么依赖

# 5. 解决编译顺序的问题,第一个参数写编译目标
add_dependencies(person_subscriber ${PROJECT_NAME}_generate_messages_cpp)
# 在用了这个消息类型的链接库函数后加
```

## 服务通信
在工作空间src中，我又创造了一个名为learn_service的功能包，再在里面的src中写上我们的服务通信代码，接下来也主要以代码来解释话题服务的基本实现。

### 客户端Client

重要步骤：
- 初始化ROS节点，创造节点句柄
- 等待服务端上线
- 创造客户端对象，绑定服务端
- 创造请求发送的数据
- 发送请求，阻塞的等待结果
- 读取相应数据
```cpp
#include <ros/ros.h>
#include <turtlesim/Spawn.h> // 引入服务类型头文件

int main(int argc, char **argv)
{
     
    ros::init(argc, argv, "turtle_spawn");
    
    ros::NodeHandle node;

    //阻塞等待名为 "spawn" 的服务上线
    ros::service::waitForService("spawn");

    // 创造客户端对象，绑定服务名 "spawn" 和服务类型 turtlesim::Spawn
    ros::ServiceClient add_turtle = node.serviceClient<turtlesim::Spawn>("spawn");

    // 构造服务数据对象（包含了请求 Request 和 响应 Response）
    turtlesim::Spawn srv;
    
    // 填充请求数据（Request）
    srv.request.x = 2.0;
    srv.request.y = 2.0;
    srv.request.name = "turtle2";
    
    ROS_INFO("Spawning turtle at (%.2f, %.2f, %s)", srv.request.x, srv.request.y, srv.request.name.c_str());

    // 发起调用（同步阻塞），把 srv 发出去
    // 此时程序会卡在这里，直到服务端处理完，把结果写回 srv.response
    add_turtle.call(srv);
    
    ROS_INFO("spwan turtle successfully [name:%s]", srv.request.name.c_str());

    return 0;
}
```

### 服务端Server

为了实现一些功能，所以里面加了一些话题通信来让效果更明显。
- 初始化节点创造句柄
- 编写服务回调函数，需要写好怎么处理请求，给出什么响应
- 创造服务端对象，声明服务名和绑定回调函数
- 设置循环来处理请求，实现相应功能

通过`rosservice call /turtle_command"{}"`来给服务端信号

```cpp
#include <ros/ros.h>
#include <geometry_msgs/Twist.h>
#include <std_srvs/Trigger.h>  // 引入标准触发服务类型(这个服务类型分为Request，为空;Response，含布尔和字符串；类型)

// 全局变量，第一个是用来发布数据的，第二个是看小乌龟是否在运动的
ros::Publisher turtle_vel_pub;
bool pubCommand = false;

// 服务回调函数
// 参数1：req 是客户端发来的请求
// 参数2：res 是我们准备返回给客户端的响应
bool CommandCallback(std_srvs::Trigger::Request &req, std_srvs::Trigger::Response &res)
{
    // 切换开关状态（取反）
    pubCommand = !pubCommand;

    ROS_INFO("Command publishing is %s", pubCommand ? "ON" : "OFF");

    // 填充响应数据
    res.success = true; 
    res.message = "change turtle command state!"; // 告诉客户端：我切换了开关

    // 返回 true 表示服务处理完毕；返回 false 客户端会收到报错
    return true;
}

int main(int argc, char **argv)
{
    // 初始化节点和句柄
    ros::init(argc, argv, "turtle_command_server");
    ros::NodeHandle n;

    // 创造服务端
    // 声明服务名 "/turtle_command" 和回调函数 CommandCallback
    // 当有客户端调用这个服务时，会自动执行上面的回调函数
    ros::ServiceServer command_service = n.advertiseService("/turtle_command", CommandCallback);

    // 创建话题发布者，用于让海龟动起来
    turtle_vel_pub = n.advertise<geometry_msgs::Twist>("/turtle1/cmd_vel", 10);

    ROS_INFO("Turtle command server is ready!");

    ros::Rate loop_rate(10);

    // 主循环
    while (ros::ok())
    {
	    // 如果检测到客户端的指令
        ros::spinOnce(); 

        // 检测海龟是否应该运动，是的话就发送指令让他动
        if (pubCommand)
        {
            geometry_msgs::Twist vel_msg;
            vel_msg.linear.x = 1.0;
            vel_msg.angular.z = 1.0;
            turtle_vel_pub.publish(vel_msg);
        }

        loop_rate.sleep();
    }

    return 0;
}
```

### 自定义服务数据
同样保证一个`srv`文件夹，在里面定义srv文件。
```cpp
// 服务数据 = 请求(Request) + 响应(Response)，可以用---来分隔
string name
uint8 age
uint8 sex

uint8 unknown = 0
uint8 male    = 1
uint8 female  = 2
---
string result
```

再去配置`package.xml`(这里和话题消息定义一样)
```xml
<build_depend>message_generation</build_depend>
<exec_depend>message_runtime</exec_depend>
```

最后配置`CMakelists.txt`
```bash
# 1. 查找依赖
find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  std_msgs
  message_generation   # 我要编译消息/服务
)

# 2. 添加你定义的 .srv 文件(只有这里和话题消息不一样)
add_service_files(
  FILES
  Person.srv           # 你的服务文件名
)

# 3. 生成服务
generate_messages(
  DEPENDENCIES
  std_msgs
# 导入类型
)

# 4. 导出运行时依赖
catkin_package(
  CATKIN_DEPENDS roscpp rospy std_msgs message_runtime
)
# 告诉别人自己应该要用什么依赖

# 5. 解决编译顺序的问题，第一个参数写编译目标
add_dependencies(turtle_command_server ${PROJECT_NAME}_generate_messages_cpp)
# 在用了这个服务类型的链接库函数后加
```

## 参数通信

![[参数通信.png]]

YAML这种数据格式常被用来保存参数，所以保存参数的文件也叫YAML参数文件。

将参数命令行放进.cpp文件里，就可以写一些固定的函数，来便捷完成一些操作。
下面的代码文件里，为了刷新海龟节点的状态，所以需要call一下它
`sleep(1)` 主要是起一个保险作用？
```cpp
#include <string>
#include <ros/ros.h>
#include <std_srvs/Empty.h>

int main(int argc, char **argv)
{
    int red,green,blue;
    ros::init(argc, argv, "parameter_config");
    ros::NodeHandle node;

    ros::param::get("turtlesim/background_r", red);
    ros::param::get("turtlesim/background_g", green);
    ros::param::get("turtlesim/background_b", blue);

    ROS_INFO("Background color is: R=%d, G=%d, B=%d", red, green, blue);

    ros::param::set("turtlesim/background_r", 255);
    ros::param::set("turtlesim/background_g", 255);
    ros::param::set("turtlesim/background_b", 255);

    ROS_INFO("Background color changed to: R=255, G=255, B=255");

    ros::service::waitForService("/clear");
    ros::ServiceClient clear_client =                                     node.serviceClient<std_srvs::Empty>("/clear");
    std_srvs::Empty clear_srv;
    clear_client.call(clear_srv);

    sleep(1); 
}
```

还有关于YAML参数文件的保存和读取
```bash
rosparam dump 命名
rosparam load 名字
```

## launch
### 介绍
Launch的作用是让我们能够用一个命令，批量启动和配置多个ROS节点。
他是一个XML格式的配置文件。
主要功能：
- 批量启动节点
- 自动启动Master
- 集中配置参数

我们同样还是在功能包中创造一个launch文件夹，再在里面创建.launch格式文件脚本，并通过此命令行来运行其：
`roslaunch <功能包名> 文件名.launch`    

### 编写代码
```xml
<!-- 先在开头结尾加上这个 -->
<launch>
...
</launch>

<!-- 启动节点，后三条分别是：输出到终端，崩溃重启，挂了关launch -->
<node pkg="节点所在功能包" name="节点运行名字" type="可执行文件名称"
            output="screen" respawn="true" required="ture"
            ns="命名空间" args="参数"/>


<!-- 设置参数 -->
<param name="参数名" value="参数值"/>
<rosparam file="指定YAML文件" command="load" ns="命名空间" />


<!-- 声明局部变量，只能在launch中用，default默认值，value固定值 -->
<!-- 通过 $(arg arg-name) 来调用 -->
<arg name="名字" default="值" | value="值" />


<!-- 重映射，直接把节点原本要发布或订阅的话题/服务，改个名字! -->
<remap from="/turtlebot/cmd_vel" to="/cmd_vel"/>


<!-- 嵌套，在此launch中引入执行另一个launch文件 -->
<include file="$(find 包名)/launch/other.launch" />



```

## rqt工具箱

### 概念

ROS基于QT框架，针对机器人开发提供了一些列可视化的工具，这些工具的集合就是rqt；
rqt的作用是实现方便的ROS可视化调试，并且在同一窗口中打开多个部件，提高开发效率，优化我们的体验。

rqt工具箱由三大部分组成

- rqt——核心实现，开发人员无需关注
    
- rqt_common_plugins——rqt 中常用的工具套件
    
- rqt_robot_plugins——运行中和机器人交互的插件(比如: rviz)

在终端通过`rqt`可以直接启动对应的工具箱界面，其中Plugins我们最常用？由于是图形化界面，所以使用起来还是非常方便的。

topic和services的就不说了太简单了

introspection 中的 node Graph 是用来查看节点之间数据传输的关系的，很好用![[节点数据关系.jpg]]

logging 中的 Console 是用来查看输出与报错信息的，还能关键字过滤信息

visualization 中的 plot 是用来绘制数据的，看数据波形挺好的

logging 中的 bag 是用来监听并记录数据信息的，或者复现监听文件（记的publish

configuration 中的 Dtbanuc Reconfigure 用来进行动态参数的调节
## 动态参数

#### **概念**

一种可以在运行时更新参数而无需重启节点的参数配置策略。
#### **作用**
主要应用于需要动态更新参数的场景，比如参数调试、功能切换等。典型应用:导航时参数的动态调试。

#### 客户端实现

**添加.cfg文件**,然后给它权限`chmod +x src/包名/cfg/dr.cfg`
```python
#! /usr/bin/env python
"""
 4生成动态参数 int,double,bool,string,列表
 5实现流程:
 6    1.导包
 7    2.创建生成器
 8    3.向生成器添加若干参数
 9    4.生成中间文件并退出
10
"""
# 导入参数生成器，这里是把A里的B全部导入进去了
from dynamic_reconfigure.parameter_generator_catkin import *

# 生成的文件放在哪个功能包名下
PACKAGE = "demo02_dr"

# 创建生成器实例
gen = ParameterGenerator()

# 向生成器添加若干参数
# 名字，类型，分组级别，描述，默认值，最小值，最大值
gen.add("int_param",int_t,0,"整型参数",50,0,100)
gen.add("double_param",double_t,0,"浮点参数",1.57,0,3.14)
gen.add("string_param",str_t,0,"字符串参数","hello world ")
gen.add("bool_param",bool_t,0,"bool参数",True)

# gen.const(名字，类型，实际数值，描述)定义选项
# gen.enum([const][][],名字)，生成菜单
many_enum = gen.enum([gen.const("small",int_t,0,"a small size"),
                gen.const("mediun",int_t,1,"a medium size"),
                gen.const("big",int_t,2,"a big size")
                ],"a car size set")

# edit_method用来指定为枚举
gen.add("list_param",int_t,0,"列表参数",0,0,2, edit_method=many_enum)

# 4.生成中间文件并退出，参数分别是包名，节点名，文件名前缀drConfig.h
exit(gen.generate(PACKAGE,"dr_node","dr"))
```

**配置 CMakeLists.txt**
```bash
generate_dynamic_reconfigure_options(
  cfg/dr.cfg
)
```


#### 服务端实现

代码
```cpp
#include "ros/ros.h"
#include "dynamic_reconfigure/server.h"  引入动态参数服务端的头文件
#include "demo02_dr/drConfig.h"          引入我们的.cfg
 /*  
    动态参数服务端: 参数被修改时直接打印
    实现流程:
        1.包含头文件
        2.初始化 ros 节点
        3.创建服务器对象
        4.创建回调对象(使用回调函数，打印修改后的参数)
        5.服务器对象调用回调对象
        6.spin()
*/

void cb(demo02_dr::drConfig& config, uint32_t level){
    ROS_INFO("动态参数解析数据:%d,%.2f,%d,%s,%d",
        config.int_param,
        config.double_param,
        config.bool_param,
        config.string_param.c_str(),
        config.list_param
    );
}

int main(int argc, char *argv[])
{
    //设置本地化环境，让我们能打中文
    setlocale(LC_ALL,"");
    
    // 初始化 ros 节点
    ros::init(argc,argv,"dr");
    
    // 创建动态参数服务器对象，输入我们的cfg.h
    dynamic_reconfigure::Server<demo02_dr::drConfig> server;
    
    //注册&&把一个普通函数包装成可调用对象
    //其中第二个函数(传入的函数，第一个实参，第二个实参)
    server.setCallback(boost::bind(&cb, _1, _2));
    
    ros::spin();
    return 0;
}
```


## pluginlib
pluginlib 是 ROS 的插件加载框架：主程序只依赖抽象基类，具体功能以插件形式编译成动态库，在运行时按名字动态加载。作用是把"主程序"与"功能实现"分开来，支持不重启、不重编主程序就切换或扩展功能。

**它的优点：**
- 结构清晰；
    
- 低耦合，易修改，可维护性强；
    
- 可移植性强，更具复用性；
    
- 结构容易调整，插件可以自由增减；

### 使用

创造功能包的时候多加上依赖   `pluginlib`
之后就配置 .vascode/c_cpp_properties.json文件中关于 includepath 选项的设置
```cpp
{
    "configurations": [
        {
            "browse": {
                "databaseFilename": "",
                "limitSymbolsToIncludedHeaders": true
            },
            "includePath": [
                "/opt/ros/noetic/include/**",
                "/usr/include/**",
                "/.../yyy工作空间/功能包/include/**" //配置 head 文件的路径 ,       如"/root/ros_ws/src/plugin/include/**"
            ],
            "name": "ROS",
            "intelliSenseMode": "gcc-x64",
            "compilerPath": "/usr/bin/gcc",
            "cStandard": "c11",
            "cppStandard": "c++17"
        }
    ],
    "version": 4
}

```

在 xxx/include/xxx下新建C++头文件: polygon_base.h，创造基类
```cpp
#ifndef XXX_POLYGON_BASE_H_   //头文件卫士，名字一般是此文件名全大写
#define XXX_POLYGON_BASE_H_

namespace polygon_base       //开一个与其他环境隔开的命名空间
{
  class RegularPolygon       //正常创造基类了
  {
    public:
      virtual void initialize(double side_length) = 0;
      virtual double area() = 0;
      virtual ~RegularPolygon(){}

    protected:
      RegularPolygon(){}
  };
};
#endif
```

在 xxx/include/xxx下新建C++头文件:polygon_plugins.h，创造衍生类，也就是插件
```cpp
#ifndef XXX_POLYGON_PLUGINS_H_
#define XXX_POLYGON_PLUGINS_H_
#include <xxx/polygon_base.h>   //基类的地址
#include <cmath>

namespace polygon_plugins   //正常写库
{
  class Triangle : public polygon_base::RegularPolygon
  {
    public:
      Triangle(){}

      void initialize(double side_length)
      {
        side_length_ = side_length;
      }

      double area()
      {
        return 0.5 * side_length_ * getHeight();
      }

      double getHeight()
      {
        return sqrt((side_length_ * side_length_) - ((side_length_ / 2) * (side_length_ / 2)));
      }

    private:
      double side_length_;
  };

  class Square : public polygon_base::RegularPolygon
  {
    public:
      Square(){}

      void initialize(double side_length)
      {
        side_length_ = side_length;
      }

      double area()
      {
        return side_length_ * side_length_;
      }

    private:
      double side_length_;

  };
};
#endif
```

在 src 目录下新建 polygon_plugins.cpp 文件，也就是注册插件
```cpp
//pluginlib 宏，可以注册插件类
#include <pluginlib/class_list_macros.h>
#include <xxx/polygon_base.h>
#include <xxx/polygon_plugins.h>

//参数1:衍生类 参数2:基类
PLUGINLIB_EXPORT_CLASS(polygon_plugins::Triangle, polygon_base::RegularPolygon)
PLUGINLIB_EXPORT_CLASS(polygon_plugins::Square, polygon_base::RegularPolygon)
```

接下来就是在 CMakeLists.txt 文件里构件插件库了

```cmake
include_directories(include)    # 在本包下，告诉如何去找.h
add_library(polygon_plugins src/polygon_plugins.cpp)  #插件动态库
```


还有xml的配置，功能包下新建文件:polygon_plugins.xml
```xml
<!-- 插件库的相对路径，生成在devel那里 -->
<library path="lib/libpolygon_plugins">
  <!-- type="插件类" base_class_type="基类"，后面是类名字 -->
  <class type="polygon_plugins::Triangle" base_class_type="polygon_base::RegularPolygon">
    <!-- 描述信息 -->
    <description>This is a triangle plugin.</description>
  </class>
  <class type="polygon_plugins::Square" base_class_type="polygon_base::RegularPolygon">
    <description>This is a square plugin.</description>
  </class>
</library>
```

再再在package.xml文件尾巴下设置如下内容，plugin等于上一步xml文件地址
```xml
<export>
  <xxx(包名) plugin="${prefix}/polygon_plugins.xml" />
</export>
```

### 实现
在src 下新建c++文件:polygon_loader.cpp
```cpp
//类加载器相关的头文件
#include <pluginlib/class_loader.h>
#include <xxx/polygon_base.h>

int main(int argc, char** argv)
{
  //类加载器 -- 参数1:，包名 参数2:基类文件名库
  pluginlib::ClassLoader<polygon_base::RegularPolygon> poly_loader("xxx", "polygon_base::RegularPolygon");

  try   //框定感受出错的范围
  {
    //创建插件类实例 -- 参数:插件类全限定名称，初始化
    boost::shared_ptr<polygon_base::RegularPolygon> triangle = poly_loader.createInstance("polygon_plugins::Triangle");
    triangle->initialize(10.0);

    boost::shared_ptr<polygon_base::RegularPolygon> square = poly_loader.createInstance("polygon_plugins::Square");
    square->initialize(10.0);

    ROS_INFO("Triangle area: %.2f", triangle->area());
    ROS_INFO("Square area: %.2f", square->area());
  }
  catch(...) //直接接住所有异常
  {
    ROS_ERROR("The plugin failed to load for some reason. Error: %s", ex.what()); //返回错误描述字符串
  }

  return 0;
}
```

然后再修改CMakeLists.txt文件
```camke
add_executable(polygon_loader src/polygon_loader.cpp)
target_link_libraries(polygon_loader ${catkin_LIBRARIES})
```
## nodelet

Nodelet 是一种把多个节点合并到同一个进程里运行的机制，目的是消除节点间通信的进程开销和拷贝开销，实现零拷贝、高吞吐的数据传递。

我们可以创造多个可动被动态加载进nodelet管理器的nodelet进程。

### 有关nodelet的使用

首先是一些相关的语法
```cpp
nodelet load pkg/Type manager - 加载一个nodelet插件到指定manager进程
nodelet standalone pkg/Type   - 把一个nodelet单独作为一个节点使用
nodelet unload name manager   - 卸载指定manager里的指定插件(注意name)
nodelet manager               - 启动一个manager进程
```

下面是一些命令行里的相关示例
```bash
rosrun nodelet nodelet manager __name:=mymanager

rosrun nodelet nodelet load nodelet_tutorial_math/Plus mymanager __name:=n1 _value:=100

rosrun nodelet nodelet load nodelet_tutorial_math/Plus mymanager __name:=n2 _value:=-50 /n2/in:=/n1/out
# 命名，加载普通私有变量，重映射
# __是特殊参数的定义，_就是自己定义的普通参数
```
### 实现nodelet

由于本质插件，其实和插件的实现很相似，而且不用自定义接口、不用使用类加载器加载插件类。

首先新建功能包，导入  roscpp   nodelet;
然后创造插件类并注册插件，和pluginlib的创造衍生类相似
```cpp
#include "nodelet/nodelet.h"
#include "pluginlib/class_list_macros.h"
#include "ros/ros.h"
#include "std_msgs/Float64.h"

namespace nodelet_demo_ns {
class MyPlus: public nodelet::Nodelet {
    public:
    MyPlus(){
        value = 0.0;
    }
    
    //这个函数在节点初始化后会自动调用，相当于节点初始化？
    void onInit(){
        //获取 NodeHandle
        ros::NodeHandle& nh = getPrivateNodeHandle();//返回私有命名空间，这个具体名字在后面的launch里面定义
        //从参数服务器获取参数
        nh.getParam("value",value);
        //创建发布与订阅对象
        pub = nh.advertise<std_msgs::Float64>("out",100);
        sub = nh.subscribe<std_msgs::Float64>("in",100,&MyPlus::doCb,this); //由于是类里面的回调函数，所以要this指定是谁在调用

    }
    //回调函数
    void doCb(const std_msgs::Float64::ConstPtr& p){
        double num = p->data;
        //数据处理
        double result = num + value;
        std_msgs::Float64 r;
        r.data = result;
        //发布
        pub.publish(r);
    }
    private:
    ros::Publisher pub;
    ros::Subscriber sub;
    double value;

};
}
//闭合这个命名空间和注册宏
PLUGINLIB_EXPORT_CLASS(nodelet_demo_ns::MyPlus,nodelet::Nodelet)
```

写好后我们就可以去构造插件库了
```cmake
...
add_library(mynodeletlib # 顺便起的插件库名字，在xml里面会用到
  src/myplus.cpp         # 指定加入的文件
)
...
target_link_libraries(mynodeletlib  # 要和上面的一样
  ${catkin_LIBRARIES}
)
```

新建 xml 文件，名称自定义,如:my_plus.xml（给插件库的解释）
```xml
<!-- lib+插件库名 -->
<library path="lib/libmynodeletlib">   
    <class name="demo04_nodelet/MyPlus"   type="nodelet_demo_ns::MyPlus" base_class_type="nodelet::Nodelet" ><!-- name要写在launch里的args，然后是 衍生类 和 基类 -->
        <description>hello</description>
    </class>
</library>
```

跳到package.xml在最底下写上这坨，用来导出插件
```xml
<export>
    <!-- Other tools can request additional information be placed here -->
    <nodelet plugin="${prefix}/my_plus.xml" />
</export>
```

最后可以写个launch，来执行nodelet
```xml
<launch>
<!-- pkg：用哪个包   type：哪个可执行程序   name：决定私有命名空间   args：加载命令，要和xml里面的name一致 -->
    <node pkg="nodelet" type="nodelet" name="my" args="manager" output="screen" />
    <node pkg="nodelet" type="nodelet" name="p1" args="load demo04_nodelet/MyPlus my" output="screen">
        <param name="value" value="100" />
        
        <!-- 话题重映射，把发到/p1/out 的消息改为发到 /con -->
        <remap from="/p1/out" to="con" />  
    </node>
    <node pkg="nodelet" type="nodelet" name="p2" args="load demo04_nodelet/MyPlus my" output="screen">
        <param name="value" value="-50" />
        <remap from="/p2/in" to="con" />
    </node>

</launch>
```