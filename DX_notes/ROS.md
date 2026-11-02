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

logging 中的 bag 是用来监听并记录数据信息的，或者复现监听文件（记住publish
## 动态参数


## nodelet