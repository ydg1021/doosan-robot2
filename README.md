

# [Doosan Robotics](http://www.doosanrobotics.com/kr/)<img src="https://user-images.githubusercontent.com/47092672/97660147-142f1f00-1ab4-11eb-9d14-48f30a666cdc.PNG" width="10%" align="right">
[![license - apache 2.0](https://img.shields.io/:license-Apache%202.0-yellowgreen.svg)](https://opensource.org/licenses/Apache-2.0)
[![License](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](https://opensource.org/licenses/BSD-3-Clause)
[![support level: community](https://img.shields.io/badge/support%20level-community-lightgray.png)](http://rosindustrial.org/news/2016/10/7/better-supporting-a-growing-ros-industrial-software-platform)
# *overview*
    
    This package provides the function to control all models of Doosan robots in the ROS2(Foxy) environment.
    
    ※ Currently, ROS2 related packages are being updated rapidly to Humble. 
       Doosan packages will also be updated from time to time and features will be upgraded.
       
# _*article*_
<img src="https://user-images.githubusercontent.com/47092672/116173431-8a50cd80-a747-11eb-839c-b9f374d9a774.jpeg" width="80%">

[Doosan  Robotics Unveils Industry's First ROS Package that Supports ROS 2 Foxy Fitzroy](https://apnews.com/press-release/pr-newswire/technology-business-south-korea-materials-industry-robotics-511fcf63df0d36340748142a30e88319)


# *build*  
##### *Doosan Robot ROS2 Package is implemented at ROS2-Foxy.* (Updated on 24.01.24)
    ### Prerequisite installation elements before package installation 
    $ sudo apt-get install libpoco-dev
    
    ### We assume that you have installed the ros-foxy-desktop package using the apt-get command.
    ### We recommand the /home/<user_home>/ros2_ws/src
    $ mkdir -p ~/ros2_ws/src
    $ cd ~/ros2_ws/src
    $ git clone https://github.com/doosan-robotics/doosan-robot2.git
    $ git clone https://github.com/ros-controls/ros2_control.git
    $ git clone https://github.com/ros-controls/ros2_controllers.git
    $ git clone https://github.com/ros-simulation/gazebo_ros2_control.git
    $ cd ros2_control && git reset --hard 3dc62e28e3bc8cf636275825526c11d13b554bb6 && cd ..
    $ cd ros2_controllers && git reset --hard 83c494f460f1c8675f4fdd6fb8707b87e81cb197 && cd ..
    $ cd gazebo_ros2_control && git reset --hard 3dfe04d412d5be4540752e9c1165ccf25d7c51fb && cd ..
    $ git clone -b ros2 --single-branch https://github.com/ros-planning/moveit_msgs 
    $ cd ~/ros2_ws
    $ colcon build
    $ . install/setup.bash

##### colcon build error, I think dependency package list will help these problems
    ### If you got errors when you "colcon build" you need to download below packages (For install Ubuntu 20.04 and ROS2 first time users)
    $ sudo apt-get install ros-foxy-control-msgs
    $ sudo apt-get install ros-foxy-object-recognition-msgs
    $ sudo apt-get install ros-foxy-octomap-msgs
    $ sudo apt-get install ros-foxy-control-toolbox
    $ sudo apt-get install ros-foxy-gazebo-dev
    $ sudo apt-get install ros-foxy-gazebo-ros
    $ sudo apt-get install ros-foxy-gazebo-ros-pkgs
    $ pip install catkin_pkg
    $ pip install pyyaml
    $ pip install empy
    $ pip install -U setuptools
    $ source install/setup.bash
    $ source /opt/ros/foxy/setup.bash
    $ sudo apt-get update
    $ sudo apt-get install ros-foxy-ament-cmake-core

#### Everytime you build a package, always remember to do (from your ROS2 workspace): source install/setup.bash and source /opt/ros/foxy/setup.bash

#### dependency package list
    $ sudo apt-get install ros-foxy-control-msgs ros-foxy-realtime-tools ros-foxy-xacro ros-foxy-joint-state-publisher-gui ros-foxy-object-recognition-msgs ros-foxy-octomap-msgs ros-foxy-gazebo-ros ros-foxy-gazebo-ros-pkgs ros-foxy-control-toolbox

# *usage* <a id="chapter-3"></a>
### Joint State Publish
It can be run independently without a controller.
Using the robot model and the `joint_state_publisher_gui` package, you can see the robot moving on Rviz.
```bash
$ ros2 launch dsr_launcher2 dsr_joint_state_pub.launch.py model:=a0912 color:=blue
``` 
<img src="https://user-images.githubusercontent.com/47092672/97652654-40da3b00-1aa2-11eb-8621-2a36e3159de0.png" width="70%">

### RuntimeError
xacro 실행시 발생하는 오류일 수 있음. yaml 파일을 읽을때 발생하는 오류로 패키지가 필요함
    $ pip install pyyaml
    $ xacro /home/do/ros2_ws/install/dsr_description2/share/dsr_description2/xacro/a0912.urdf.xacro color:=blue
위 xacro 코드를 실행시 오류가 발생한다면 오류 로그를 확인하고 내 경우는 pyyaml을 설치하면 해결되었음
정상적이라면 정상적으로 코드가 출력됨

### 추가로 배운 것
1) dependency package list로 필요한 패키지 미리 다운로드하는 것
2) rosdep으로 cMakeList.txt에 존재하는 라이브러리 다운로드 하는거 같음
3) 초기단계라면 src말고 다른 build, log 등 파일 삭제하고 다시 colcon build 하면 될 듯
    $ rm -rf ~/ros2_ws/build
    $ rm -rf ~/ros2_ws/install
    $ rm -rf ~/ros2_ws/log
4) 이전 시도에서 gazebo 다운로드(최신버전)를 미리 안하고 진행했을 때 gazebo 실행 때 오류가 있었는데 이번에는 없었음


### Virtual Mode
If the "mode" argument is set to virtual, the DRCF emulator is automatically executed when launch.py ​​is executed.

##### Run dsr_control2 node 
You can execute the Control Node by using the command below.
```bash
$ ros2 launch dsr_launcher2 single_robot_rviz.launch.py model:=a0912 color:=blue
```
##### Run the example scripts
The robot can be driven by using the example scripts included in the dsr_example2 package.
Check that the controller and robot are connected normally, and enter the command below.
```bash
$ ros2 run dsr_example2_py dsr_service_motion_simple
```

### Real Mode
Use __real mode__ to drive a real robot   
The default IP of the robot controller is _192.168.127.100_ and the port is _12345_.

##### Run dsr_control2 node 
```bash
$ ros2 launch dsr_launcher2 single_robot_rviz.launch.py mode:=real host:=192.168.127.100 port:=12345
```

##### Run the example scripts
The robot can be driven by using the example scripts included in the dsr_example2 package.
Check that the controller and robot are connected normally, and enter the command below.
```bash
$ ros2 run dsr_example2_py dsr_service_motion_simple
```
---
### dsr_launcher2
The dsr_launcher2 package contains the launch.py ​​file that can link control node, rviz, and gazebo.
Therefore, it is necessary to connect with a real robot controller or emulator before executing the launch file.
Simulation can be performed with the command below.
```bash
$ ros2 launch dsr_launcher2 single_robot_rviz.launch.py
```
<img src="https://user-images.githubusercontent.com/47092672/97654894-3f5f4180-1aa7-11eb-83f0-90eb071d1f60.gif" width="70%">

```bash
$ ros2 launch dsr_launcher2 single_robot_gazebo.launch.py
```
<img src="https://user-images.githubusercontent.com/47092672/99232226-fe9c5200-2834-11eb-8719-f87cc56d55c7.gif" width="70%">

### Before install moveit

CMake version >= 3.22, 기본 CMake version in Ubuntu 20.04 = 3.16.3

$ sudo apt remove --purge cmake
$ wget https://github.com/Kitware/CMake/releases/download/v3.25.0/cmake-3.25.0.tar.gz
$ tar -zxvf cmake-3.25.0.tar.gz
$ cd cmake-3.25.0
$ ./bootstrap
$ make
$ sudo make install
$ cmake --version

여기서 /usr/local/bin에 설치된 CMake에서 cmake error가 발생한다면 PATH를 설정해줘야함

$ export PATH=/usr/local/bin:$PATH
$ echo 'export PATH=/usr/local/bin:$PATH' >> ~/.bashrc
$ source ~/.bashrc
$ cmake --version
>> cmake --version
cmake version 3.25.0

CMake suite maintained and supported by Kitware (kitware.com/cmake).




### Moveit
To use the moveit2 package, you need to install the following packages.
```bash
$ cd ~/ros2_ws/src
$ git clone https://github.com/ros-planning/moveit2
$ git clone -b ros2 --single-branch https://github.com/ros-planning/warehouse_ros
$ git clone -b ros2 --single-branch  https://github.com/ros-planning/warehouse_ros_mongo
$ git clone -b ros2 --single-branch https://github.com/ros-planning/srdfdom
$ git clone -b ros2 --single-branch https://github.com/ros-planning/geometric_shapes
$ git clone -b use_new_joint_handle https://github.com/ShotaAk/fake_joint
```
    
Please do the additional work below to build a fake_joint package that is compatible with our ROS2 package.
```bash
$ cd ~/ros2_ws/src
$ cp doosan-robot2/common2/resource/fake_joint_driver_node.cpp fake_joint/fake_joint_driver/src/fake_joint_driver_node.cpp
```
You can install the dependency package through the command below.
```bash
$ cd ~/ros2_ws
$ rosdep install -r --from-paths src --ignore-src --rosdistro foxy -y
$ rm -rf src/doosan-robot2/moveit_config_*/COLCON_IGNORE    # Command to activate the moveit package before colcon build
$ colcon build
$ . install/setup.bash
```
You can run moveit2 with fake_controller from the moveit_config package.
Please refer to the command format below.

> ros2 launch moveit_config_<robot_model> <robot_model>.launch.py
```bash
$ ros2 launch moveit_config_m1013 m1013.launch.py
```
Moveit2 can be executed in conjunction with the control node. Enter the following command to use moveit2's planning function in conjunction with the actual robot.
Refer to the arguments of the __Run dsr_control2 node__ item mentioned above.
```bash
$ ros2 launch dsr_control2 dsr_moveit2.launch.py
```
<img src="https://user-images.githubusercontent.com/47092672/102734069-3f324280-4382-11eb-9165-cdec6b52de17.gif" width="80%">
