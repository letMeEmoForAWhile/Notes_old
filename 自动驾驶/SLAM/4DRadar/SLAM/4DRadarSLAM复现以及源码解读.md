# 一、依赖包：

- PCL(依赖VTK)：`sudo apt install ros-melodic-pcl-ros  `

- Eigen3: 忘了，没有通过

- OpenMP：（安装GCC即可）

- g2o：图优化,`sudo apt install ros-melodic-libg2o`

- ndt_omp :在src中，`git clone https://github.com/koide3/ndt_omp.git`

- GTSAM:不能在src中使用`catkin_make`，联合编译，使用源码安装方法，版本选择为`4.0.3`

  ```bash
   git clone -b 4.0.3 https://github.com/borglab/gtsam.git
  ```

  ```
  cd gtsam
  mkdir build && cd build
  cmake ..
  cmake --build .
  sudo make install
  ```
  


## 安装VTK(忽略)

1、在github上下载VTK库

2、编译安装

```
mkdir build
cd build
ccmake .. //进入cmake图形化页面

//在图形化页面中设置
CMAKE_BUILD_TYPE=RELEASE \

//设置完成按c configure，之后按g生成文件

//文件生成后
make -j8

sudo make install
```

3、验证

# https://kitware.github.io/vtk-examples/site/Cxx/GeometricObjects/CylinderExample/



4、配置路径

```
vim ~/.bashrc

//在文件中添加,/path_to_VTK是VTK库的路径
export LD_LIBRARY_PATH=/path_to_VTK/lib:$LD_LIBRARY_PATH

source ~/.bashrc
```



## 安装PCL(忽略)

```
mkdir build
cd build
cmake ..
```

仍然报错没有VTK

```
-- The imported target "vtk" references the file
   "/usr/bin/vtk"
but this file does not exist.  Possible reasons include:

* The file was deleted, renamed, or moved to another location.
* An install or uninstall procedure did not complete successfully.
* The installation package was faulty and contained
  "/usr/lib/cmake/vtk-6.3/VTKTargets.cmake"
  but not all the files it references.

CMake Error at cmake/pcl_find_vtk.cmake:96 (message):
  Missing vtk modules: vtkRenderingOpenGL2;vtkRenderingContextOpenGL2
Call Stack (most recent call first):
  CMakeLists.txt:392 (include)
```

在/usr/bin/目录下能找到vtk6，将其复制一份，重命名为vtk

```
sudo cp ./vtk6 ./vtk
```

中间的错误消失，但仍显示Missing vtk modules

直接使用apt一键安装

```
sudo apt install libpcl-dev
```

# 二、编译

在主目录下

```
mkdir build
cd build
cmake ..
```

## 错误1：没有安装ndt_omp

```
-- Could not find the required component 'ndt_omp'. The following CMake error indicates that you either need to install the package with the same name or change your environment so that it can be found.
CMake Error at /opt/ros/melodic/share/catkin/cmake/catkinConfig.cmake:83 (find_package):
  Could not find a package configuration file provided by "ndt_omp" with any
  of the following names:

    ndt_ompConfig.cmake
    ndt_omp-config.cmake
```

##### 原因分析：

没有安装ndt_omp，无法使用`apt install` 一键安装

##### 解决方法：

在另一个SLAM中找到odt_omp安装方法,并发现之前的项目管理方法错误

https://www.cnblogs.com/chenlinchong/p/11763481.html

以下为正确的项目管理方法

1. 创建ros工作空间

   ```
   // #catkin_ws表示ros的工作空间。在该工作空间中需要创建src文件夹,存放需要的库
   mkdir -p #catkin_ws/src  //该项目中，#catkin_ws具体为catkin_4DRadarSLAM
   ```

2. 在src文件目录下下载我们需要的工程和库

   ```
   cd catkin_4DRadarSLAM/src
   git clone https://github.com/zhuge2333/4DRadarSLAM.git
   git clone https://github.com/koide3/ndt_omp.git //这一步解决上述错误
   ```

3. 初始化工作空间

   ```
   catkin_init_workspace //博客中有这一步，官方教程中无
   ```

4. 使用catkin_make编译

   ```
   cd .. //退出src目录，返回工作空间主目录
   catkin_make 
   ```

   这时出现错误2

## 错误2:没有安装fast_gicp

```
-- Could NOT find fast_gicp (missing: fast_gicp_DIR)
-- Could not find the required component 'fast_gicp'. The following CMake error indicates that you either need to install the package with the same name or change your environment so that it can be found.
CMake Error at /opt/ros/melodic/share/catkin/cmake/catkinConfig.cmake:83 (find_package):
  Could not find a package configuration file provided by "fast_gicp" with
  any of the following names:

    fast_gicpConfig.cmake
    fast_gicp-config.cmake

```

##### 原因分析：

没有安装fast_gicp

##### 解决方法：

github上搜索fast_gicp,然后git clone 到src目录中

```
git clone https://github.com/SMRT-AIST/fast_gicp.git
```

## 错误3：没有安装GTSAM

```
CMake Error at 4DRadarSLAM/CMakeLists.txt:56 (find_package):
  By not providing "FindGTSAM.cmake" in CMAKE_MODULE_PATH this project has
  asked CMake to find a package configuration file provided by "GTSAM", but
  CMake did not find one.
```

##### 原因分析：

没有GTSAM

##### 解决方法1：

同上,github上搜索gtsam,然后git clone 到src目录中

### 显示新错误3.1：

```
CMake Error at /opt/ros/melodic/share/catkin/cmake/catkin_workspace.cmake:100 (message):
  This workspace contains non-catkin packages in it, and catkin cannot build
  a non-homogeneous workspace without isolation.  Try the
  'catkin_make_isolated' command instead.
```

##### 原因分析：

ros中包含catkin_make不支持的包

##### 解决方法：

在工作空间外下载gtsam,单独编译安装

### 新错误3.2：

```
../gtsam/libgtsam.so.4.3.0：对‘std::experimental::filesystem::v1::__cxx11::path::_M_split_cmpts()’未定义的引用
../gtsam/libgtsam.so.4.3.0：对‘std::experimental::filesystem::v1::status(std::experimental::filesystem::v1::__cxx11::path const&)’未定义的引用
../gtsam/libgtsam.so.4.3.0：对‘std::experimental::filesystem::v1::__cxx11::path::_M_find_extension() const’未定义的引用
../gtsam/libgtsam.so.4.3.0：对‘std::experimental::filesystem::v1::__cxx11::path::parent_path() const’未定义的引用
collect2: error: ld returned 1 exit status
examples/CMakeFiles/CameraResectioning.dir/build.make:110: recipe for target 'examples/CameraResectioning' failed
make[2]: *** [examples/CameraResectioning] Error 1
CMakeFiles/Makefile2:21918: recipe for target 'examples/CMakeFiles/CameraResectioning.dir/all' failed
make[1]: *** [examples/CMakeFiles/CameraResectioning.dir/all] Error 2
Makefile:165: recipe fo
```

##### 原因分析：

与c++版本有关，gcc 7仅支持C ++ 17实验filesystem命名空间

##### 解决方法1：

https://blog.csdn.net/qq_34113993/article/details/103245234?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-103245234-blog-121900484.235%5Ev38%5Epc_relevant_anti_vip_base&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-103245234-blog-121900484.235%5Ev38%5Epc_relevant_anti_vip_base&utm_relevant_index=1

g++ -v查看当前版本

安装4.8版本的g++，并修改默认g++版本使用4.8版。

camke 时发现GNU版本仍为7.5

cmake编译时指定编译器版本，此条未实验。

##### 解决方法2：

在github分支上选择gtsam4.0.3版本，编译安装成功。



这时catkin_make,不再提示没有包，但出现新错误

## 错误4：没有安装fast_apdgicp.hpp

```
fatal error: fast_gicp/gicp/fast_apdgicp.hpp: 没有那个文件或目录
 #include <fast_gicp/gicp/fast_apdgicp.hpp>
          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
```

##### 原因分析：

可能是fast_gicp版本错误

##### 解决方法：

在gtihub上搜索fast_apdgicp,发现作者自己写了一个库：fast_apdgicp

https://github.com/zhuge2333/fast_apdgicp

继续catkin_make，出现

## 错误5：没有barometer包

```
catkin_ws/src/4DRadarSLAM/apps/radar_graph_slam_nodelet.cpp:71:10: fatal error: barometer_bmp388/Barometer.h: No such file or directory
 \#include <barometer_bmp388/Barometer.h>
```

##### 原因分析：

作者没有提供相应文件

##### 解决方法：

在github代码4DRadarSLAM的`Issues`区中发现作者对该问题有解答，提供了一个新的包。

在src目录下

```
git clone https://github.com/zhuge2333/barometer_bmp388.git
```

## 错误6：'std'没有'make_unique'成员

```
error: ‘make_unique’ is not a member of ‘std’
```

##### 原因分析：

是因为 **std**::**make _ unique**在C++14以后新加入的函数；

##### 解决方法：

修改`catkin_workspace\src\4DRadarSlam\CMakeLists.txt`

在开头添加

```cmake
set(CMAKE_CXX_STANDARD 14)  # 使用 C++14 标准
set(CMAKE_CXX_STANDARD_REQUIRED ON)
```

## 错误7：'std'没有'mutex'成员

```bash
/home/dearmoon/projects/4DRadarSlam/src/4DRadarSLAM/include/radar_graph_slam/loop_detector.hpp:98:8: error: ‘mutex’ in namespace ‘std’ does not name a type
```

##### 原因分析：

没有添加头文件\<mutex>

##### 解决方法：

在提示错误的文件中添加头文件

```
#include <mutex>  
```



## 至此，catkin_make成功

# 三、运行

下载好作者提供的数据包之后，

修改rosbag_play_radar_carpark1.launch文件中的路径

### 

在工作空间根目录下运行

```
roslaunch radar_graph_slam radar_graph_slam.launch
```

出现

## 错误1

```
RLException: [radar_graph_slam.launch] is neither a launch file in package [radar_graph_slam] nor is [radar_graph_slam] a launch file name
The traceback for the exception was written to the log file
```

##### 原因分析：

https://blog.csdn.net/qq_44164791/article/details/130351276

没有将项目的环境变量添加到系统路径中。

##### 解决方法：

方法不唯一，这里直接对环境变量文件进行修改。

1. 打开环境变量文件

   ```
   sudo  vim ~/.bashrc
   ```

2. 在文件最后加入语句

   ```
   source ~/catkin_ws/devel/setup.bash
   export ROS_PACKAGE_PATH=${ROS_PACKAGE_PATH}:~/catkin_ws/
   ```

   注意：~/catkin_ws/表示自己的ros工作空间目录，

3. 保存并退出后，执行以下命令

   ```
   source ~/.bashrc
   ```

## 重新运行，成功

发现闭环检测失败，可能是没有启动某些模块。

# 四、代码解析

## A 、问题

##### 1、包的名称为什么是radar_graph，而不是4DRadarSLAM

radar_graph_slam文件中

```xml
 <include file="$(find radar_graph_slam)/launch/rosbag_play_radar_carpark1.launch" />
```

包名应该是4DRadarSLAM，但这里显示radar_graph_slam

答：

##### 2、为什么需要两种类型的点云，radarpoint_raw和radarpoint_xyzi

radarpoint_raw包含了xyz和强度信息以及多普勒速度，

radarpoint_xyzi包含了xyz和强度信息，

能不能只使用radarpoint_raw

答：

##### 3、eagle->msg的消息格式是什么

##### 4、odom_msgs的数据格式是什么，为什么要使用队列

##### 5、初始位姿矩阵的作用是什么

初始位姿矩阵是描述机器人相对于全局世界坐标系的初始位置和方向的矩阵。

1. 初始化：用于初始化机器人在全局坐标系中的初始位置和方向。
2. 坐标系转换：通过初始位姿矩阵，可以将传感器数据从机器人本体坐标系转换到全局坐标系中，或者反之。
3. 运动模型初始化：可以用于初始化机器人的运动状态。
4. 路径规划和导航：机器人需要知道自己的初始位置，以便规划路径。
5. 系统状态估计：用作状态估计的起点。

## B、概念

##### 1、tf变化

在ROS中，通过使用`tf(transform)`，我们可以知道任何时刻、任何两个坐标系之间的相对位置和方向。具体用途：

1. 提供坐标转换服务：

   在任意两个坐标系之间查询一个点或者向量的变换。

2. 时间旅行：

   tf数据在时间上是连续的，因此可以查询过去或未来某个时刻两坐标系之间的变换。(这取决于保存了多少历史数据和==预测了多少未来数据==)

3. 支持多坐标系

   一个机器人可能拥有多个传感器，每个传感器都有自己的坐标系。使用`tf`，所有这些坐标系可以转换为一个共同的坐标系，如`base_link`或`odom`

4. 方便的可视化

   RViz利用`tf`数据来正确地显示来自不同传感器的数据。这样无论数据来自哪个传感器或在哪个坐标系中，都可以在同一个视图中共同显示。

5. 自动管理坐标系关系

   当机器人的一个部分相对于另一个部分移动时(例如，一个机器人的手臂相对于它的身体)，tf会自动跟踪这些变化，并为应用程序提供实时的、准确的坐标转换。

##### 2、里程计信息

包含机器人运动的测量，包括平移和旋转信息。

- **平移信息(Translation)**：机器人相对于起始点在x、y和z轴上的平移量。
- **旋转信息(Rotation)**：机器人相对于起始点的旋转角度。可以用欧拉角或四元数来表示。

##### 3、点云消息的帧ID

指点云数据所在坐标系的标识符。

帧ID指定了点云数据相对于某个参考系的位置和方向。例如，如果点云数据表示激光雷达扫描的结果，帧ID可能是与激光雷达坐标系相关的标识符。

具体例子：

- map：标准的、全局的坐标系
- base_link：与机器人相关的坐标系
  - 原点：通常选择在机器人的底盘中心或某个显著的参考点
  - 坐标轴：通常选择机器人的前进方向为x轴，左侧为y轴，垂直于底盘的方向为z轴。
  - 方向：遵循右手定则。

ROS中，机器人相关的坐标系通常由TF(Transform)库进行管理。便与机器人系统的各个部件(例如激光雷达、相机、底盘等)之间的坐标变换。

##### 4、nav_msgs

ROS的一个库

包含了一系列与**导航**和**路径规划**相关的消息类型，用于在ROS系统中传递与机器人导航和路径规划相关的信息。

常见的消息类型如下：

- `Odometry`：包含机器人的里程计信息，如位置、方向和速度。
- `Path`：表示机器人的轨迹或路径，通常由一系列的位姿(位置和方向)组成
- `MapMetaData`：包含地图的元数据信息，如地图的分辨率、大小、原点等
- `GetMap`和`GetPlan`：用于请求地图和路径规划服务的请求消息

##### 5、geometry_msgs

ROS的一个库

包含了一系列与**几何**和**运动学**相关的消息类型，用于在ROS系统中传递与机器人姿态、位置、变换等信息。

常见的消息类型图下：

- `Point`：表示三位空间中的一个点，具有x、y、z坐标
- `Quaternion`：表示四元数，通常用于表示旋转姿态
- `Pose`和`PoseStamped`：分别表示三位空间中的姿态(位置和旋转)和带时间戳的姿态
- `Twist`和`TwistStamped`：表示线速度和角速度，以及带时间戳的线速度和角速度
- `Transform`和`TransformStamped`：

##### 6、初始位姿矩阵

用于初始化机器人在全局坐标系中的初始位置和方向。

## C、文件

### 1、apps/preprocessing_nodelet.cpp

从`ground truth`文件中读取每一行，作为`odom_msgs`队列的元素，每个odom消息包含位置和方向数据

##### 三个订阅者：

- imu_sub
  - 话题：`imuTopic`，即/vectornav/imu
  - 消息类型：
  - 回调函数：&PreprocessingNodelet::imu_callback
    - 从输入的imu_msg获取信息并调整，得到imu_data并发布
    - 同时，判断odom消息是否需要更新，若需要，更新后重新发布

- points_sub：
  - 话题：`pointCloudTopic`，从config/params.yaml中可知，pointCloudTopic即/radar_enhanced_pcl
  - 回调函数：&PreprocessingNodelet::cloud_callback
    - 输入： `sensor::PointCloud::ConstPtr& eagle_msg`
    - `radarpoint_raw`
    - `radarpoint_xyzi`
    - `radarcloud_raw`
    - `radarcloud_xyzi`
    - 两个opencv对象，用于存储原始点和转换后的点：`ptMat`，`dstMat`
    - `dstMat`：原始点云与转换矩阵`Radar_to_livox`相乘。

- command_sub
  - 话题：/conmand
  - 回调函数：&PrecessingNodelet::command_callback


##### 八个发布者：

- points_pub
  - 话题：/flitered_points

  - 消息类型：sensor_msgs::PointCloud2


- colored_pub
  - 话题：/colored_points
  - 消息类型：sensor_msgs::PointCloud2


- imu_pub
  - 话题：/imu
  - 消息类型：sensor_msgs::Imu


- gt_pub
  - 话题：/aftmapped_to_init
  - 消息类型：nav_msgs::Odometry


- ==pub_twist==
  - 话题：topic_twist,即/eagle_data/twist
  - 消息类型：gemetry_msgs::TwistWithConvarianceStamped


- ==pub_inlier_pc2==
  - 话题：topic_inlier_pc2，即/eagle_data/inlier_pc2
  - 消息类型：sensor_msgs::PointCloud2


- ==pub_outlier==
  - 话题：topic_outlier_pc2, 即/eagle_data/outlier_pc2
  - 消息类型：sensor_msgs::PointCloud2


- ==pc2_raw_pub==
  - 话题：/eagle_data/pc2_raw
  - 消息类型：sensor_msgs::PointCloud2

### 2、apps/radar_graph_slam_nodelet.cpp

定义了一个类:RadarGraphSlamNodelet

#### 成员函数：

##### 1、参数初始化: virtual void onInit()

- 描述：初始化参数

##### 2、点云回调函数：cloud_callback()

- 描述：将接受到的点云扔到关键帧队列中
- 参数：
  - `odom_msg`：里程计信息，即当前帧到基座之间的旋转、平移信息
  - `cloud_msg`：点云信息，即当前帧各个点的x、y、z坐标和强度等
- 相关变量：
  - 构造的关键帧：`KeyFrame::Ptr keyframe(new KeyFrame(keyframe_index, stamp, odom_now, accum_d, cloud));`
    - 包含关键帧索引、时间戳、当前的里程计信息、累计的距离、点云

##### 3、imu_callback()

- 描述：根据IMU数据的四元数部分计算初始的机器人初始位姿矩阵`initial_pose`

- 参数：

  - `imu_odom_msg`:接受到的imu数据消息，包含机器人在某个时间点的惯性测量单元数据，如加速度、角速度等信息。

    `sensor_msgs::Imu`常见的成员：

    - `Header`：时间戳`stamp`
    - `linear_acceleration`：包含机器人在三个坐标轴上的线性加速度
    - `angular_velocity`：包含机器人在三个坐标轴上的角速度
    - `orientation`（可能有）：包含机器人的方向信息，通常表示为四元数x、y、z、w

    在这个`imu_callback()`中主要处理`imu_odom_msg->orientation`部分

- 相关变量：

  - `initial_pose`：初始位姿矩阵，有imu中的四元数经过一系列转换得到。

————————————————————————————————————————————————————————————

##### 创建slam需要用到的对象:

- graph_slam, keyframe_updater, loop_detector，map_cloud_generator等等

- 对应类的定义都在src/radar_graph_slam/文件夹下

##### 订阅者

- odom_sub
  - 话题：odomTopic, 即/odom
  - 消息类型：nav_msgs::Odometry

- cloud_sub
  - 话题：/flitered_points
  - 消息类型：sensor_msgs::PointCloud2


##### 发布者

- markers_pub
  - 话题：/radar_graph_slam/markers
  - 消息类型：visualization_msgs::MarkerArray

- odom2base_pub
  - ==将雷达里程计转换为基线==
  - 话题：/radar_graph_slam/odom2base
  - 消息类型：geometry_msgs::TransformStamped

- aftmapped_odom_pub
  - 话题：/radar_graph_slam/aftmapped_odoml
  - 消息类型：nav::Odometry

- aftmapped_odom_incremenral_pub
  - 话题：/radar_graph_slam/aftmapped_odoml_incremenral
  - 消息类型：nav::Odometry
- map_points_pub
  - 话题：/radar_graph_slam/map_points
  - 消息类型：sensor_msgs::PointCloud2

- read_uintil_pub
  - 话题：/radar_graph_slam/read_until
  - 消息类型：std_msgs::Header

- odom_frame2frame_pub
  - 话题：/radar_graph_slam/odom_frame2frame
  - 消息类型：nav_msgs::Odometry

### 3、apps/scan_matching_odometry_nodelet.cpp

##### 订阅者：

- ego_vel_sub
  - 话题：/eagle_data/twist
  - 消息类型：geometry_msgs::TwistWithCovarianceStamped
  - 回调函数：&ScanMatchingOdometryNodelet::pointcloud_callback
- points_sub
  - 话题：/filtered_points
  - 消息类型：sensor_msgs::PointCloud2
  - 回调函数：同上

<!--上述消息同步处理-->

- imu_sub
  - 话题：/imu
  - 回调函数：&ScanMatchingOdometryNodelet::command_callback
- command_sub
  - 话题：/command
  - 回调函数：&ScanMatchingOdometryNodelet::command_callback

##### 发布者：

- read_until_pub
  - 话题：/scan_matching_odometry/read_until
  - 消息类型：std_msgs::Header
- odom_pub
  - 雷达扫描匹配的里程计
  - 话题：odomTopic，即/odom
  - 消息类型：nav_msgs::Odometry

- trans_pub
  - <!--雷达扫描匹配的转换？？？-->
  - 话题：/scan_matching_odometry/transform
  - 消息类型：geometry_msgs::TransformStamped
- status_pub
  - 话题：/scan_matching_odometry/status
  - 消息类型：ScanMatchingStatus
- aligned_points_pub
  - 话题：/aligned_points
  - 消息类型：sensor_msgs::PointCloud2
- submap_pub
  - 话题：/radar_graph_slam/submap
  - 消息类型：sensor_msgs::PointCloud2



### include/utility_radar.h

##### 参数服务器

### launch/radar_grapg_slam.launch

- 启动三个radar_graph_slam中的三个节点和rviz节点

- 加载数据

  - ```xml
    <include file="$(find radar_graph_slam)/launch/rosbag_play_radar_carpark1.launch" />
    ```

    
