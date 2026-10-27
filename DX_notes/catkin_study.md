# catkin
## 概念

catkin像是CMake的拓展一样，它加上了ROS包的管理和环境配置的工具。
![[ros工作空间结构.png]]

- **src**（代码空间）：用来放ROS包的地方，无论是自己写的还是clone别人的
- **build**（编译空间）：放中间文件的地方，一般不用管？
- **devel**（开发空间）：可执行文件、库、脚本将放在这里
- **install**（安装空间）：和devel有点重合，将最终产物放在这里，用来打包发给别人。
> 在ROS2中，devel和install合在一起了

## 工作空间

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

## 功能包

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

***