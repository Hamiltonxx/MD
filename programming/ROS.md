# Environment
```shell
source /opt/ros/humble/setup.bash
printenv | grep -i ROS
```

# turtlesim 和 rqt
turtlesim是ROS的hello world.  
rqt是GUI工具。  
```
ros2 pkg executables turtlesim
```
```
ros2 run turtlesim turtlesim_node
```
```
ros2 run turtlesim turtle_teleop_key
```
```shell
ros2 node list
ros2 topic list
ros2 service list
ros2 action list
```
# ROS 2 graph
## ros2 run
```
ros2 run <package_name> <executable_name>
```
## node
一个node控制轮子马达，一个node控制激光测距仪...每个node可以通过topic,service,action和参数来跟其他node收发数据。
```
ros2 node list
ros2 node info <node_name>
```
## topic
```
ros2 topic list (-t)
ros2 topic echo <topic_name>
ros2 topic info /trutle1/cmd_vel
ros2 interface show geometry_msgs/msg/Twist
ros2 topic pub <topic_name> <msg_type> '<args>'  # publish data directly onto a topic
ros2 topic pub --once /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear:{x:2.0,y:0.0,z:0.0},angular:{x:0.0,y:0.0,z:1.8}}"
```
因为 /teleop_turtle -> /turtle1/cmd_vel -> /turtlesim
```
ros2 topic echo /turtle1/cmd_vel
```
## service
Services are another method of communication for nodes in the ROS graph. Services are based on a call-and-response model,versus topics' publisher-subscriber model.
```shell
ros2 service list (-t)
ros2 service type <service_name>
ros2 service type /clear
ros2 service find <type_name>
ros2 interface show <type_name>
ros2 service call <service_name> <service_type> (<arguments>)
ros2 service call /clear std_srvs/srv/Empty  # clear the turtlesim window of any lines the turtle has drawn
ros2 service call /spawn turtlesim/srv/Spawn "{x:2, y:2, theta:0.2, name:''}"
```
## parameter
How to get,set,save and reload parameters in ROS 2?  
parameter = node属性
```shell
ros2 param list
ros2 param get <node_name> <parameter_name>
ros2 param get /turtlesim background_g
ros2 param set <node_name> <parameter_name> <value>
ros2 param set /turtlesim background_r 150
ros2 param dump <node_name>
ros2 param dump /turtlesim > turtlesim.yaml
ros2 param load <node_name> <parameter_file>
ros2 param load /turtlesim turtlesim.yaml
ros2 run <package_name> <executable_name> --ros-args --params-file <file_name>
ros2 run turtlesim turtlesim_node --ros-args --params-file turtlesim.yaml
```
## action
Actions are intended for long running tasks, built on services except actions can be canceled. They provide steady feedback(类似yield). An "action client" node sends a goal to an "action server" node that acknowledges the goal and returns a stream of feedback and a result.
```shell
ros2 action list (-t)
ros2 action info /turtle1/rotate_absolute
ros2 interface show turtlesim/action/RotateAbsolute
ros2 action send_goal <action_name> <action_type> <values>
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta:1.57}"
```
## Launching nodes
随着同时运行的node逐渐增多，逐一打开terminal来run就显得不合适了。这时需要 ros2 launch
```shell
ros2 launch turtlesim multisim.launch.py
```
```python
# turtlesim/launch/multisim.launch.py
from launch import LaunchDescription
import launch_ros.actions

def generate_launch_description():
  return LaunchDescription([
    launch_ros.actions.Node(namespace="turtlesim1",package='turtlesim',executable='turtlesim_node',output='screen'),
    launch_ros.actions.Node(namespace="turtlesim2",package='turtlesim',executable='turtlesim_node',output='screen')
  ])
```
## Recording and playing back
ros2 bag 是个用来记录topic数据的命令行工具。你可以用这个数据文件来复现实验过程。
```shell
sudo apt install ros-humble-ros2bag ros-humble-rosbag2-storage-default-plugins
mkdir bag_files
cd bag_files
```
```shell
ros2 topic list
ros2 topic echo /turtlesim1/cmd_vel
ros2 bag record <topic_name>
ros2 bag record /turtle1/cmd_vel
ros2 bag record -o subset /turtle1/cmd_vel /turtle1/pose  # 记录多个topic
ros2 bag info <bag_file_name>
ros2 bag info subset
ros2 bag play subset
ros2 topic hz /turtle1/pose
```

# Client libraries
## build工具colcon
```shell
sudo apt install python3-colcon-common-extensions
```
通常代码放在 src/, colcon会创建 build/,install/,log/  
```
mkdir -p ~/ros2_example_ws/src
cd ~/ros2_example_ws
git clone https://github.com/ros2/examples src/examples -b humble
```
运行
```
colcon build --symlink-install
```
直接把树莓派卡死。  
换到GPU Ubuntu Server上，则安装ros-humble-desktop失败，改装ros-humble-ros-base.则需要再安装example
```
sudo apt install ros-humble-example-interfaces
```
然后就能build成功。
然后把ros2_example_ws scp到树莓派。 结果 ros2 run rclcpp会报错。ros2 run rclpy却成功了。cpp的错得让C++开发人员看下。py成功了，说明这条路可行。以后不要在树莓派上build了。 或者树莓派也不要装desktop，只装base.
## swap交换空间
colcon build卡死问题。查了 [https://blog.csdn.net/wxz3wxz/article/details/70237302](https://blog.csdn.net/wxz3wxz/article/details/70237302) 发现是交换空间的问题。
```shell
free -m 
  Swap: 0  0  0
```
```shell
sudo mkdir /opt/image
sudo touch /opt/image/swap
sudo dd if=/dev/zero of=/opt/image/swap bs=1024 count=2048000
sudo mkswap /opt/image/swap
sudo swapon /opt/image/swap
free -m 
  Swap 1999  0  1999
sudo vim /etc/fstab
/opt/image/swap /swap   swap    defaults 0 0
```
## 创建package
Python package 的最小要求: package.xml; setup.py; setup.cfg; 包含 __init__.py的/<package_name>
```shell
workspace_folder/
  src/
    package_1/
      CMakeLists.txt
      package.xml
    package_2/
      setup.py
      package.xml
      resource/package_2
    package_n/
      ...
```
```shell
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python --node-name my_node my_package
cd ~/ros2_ws
colcon build (--packages-select my_package)
. install/local_setup.bash
ros2 run my_package my_node
    Hi from my_package
```
## 创建node
[https://roboticsbackend.com/category/ros2/](https://roboticsbackend.com/category/ros2/)
```shell
mkdir -p testws/src
cd testws/src
ros2 pkg create my_robot_tutorials --build-type ament_python
cd my_robot_tutorials/my_robot_tutorials
touch my_python_node.py 
```
```python
import rclpy
from rclpy.node import Node

class MyNode(Node):
    def __init__(self):
        super().__init__('my_node_name')
        self.create_timer(1, self.timer_callback)
    def timer_callback(self):
        self.get_logger().info("Hello ROS2")

def main(args=None):
    rclpy.init(args=args)
    node = MyNode()
    rclpy.spin(node)
    rclpy.shutdown()

if __name__ == '__main__':
    main()
```
```shell
# setup.py
entry_points = {
  'console_scripts': [
    'minimal_python_node = my_robot_tutorials.my_python_node:main'
  ],
}
# package.xml
<depend>rclpy</depend>
```
```shell
cd testws
colcon build --package-select my_robot_tutorials
# 如果有setuptools的Warning,则降低版本
# pip install setuptools==58.2.0 --upgrade
```
## publisher
```shell
cd testws/src
ros2 pkg create my_publisher --build-type ament_python --dependencies rclpy
cd my_publisher/my_publisher
touch temperature_sensor.py
```
```python
import rclpy
from rclpy.node import Node

from example_interfaces.msg import Int64
import random

class TemperatureSensorNode(Node):
    def __init__(self):
        super().__init__("temperature_sensor")
        self.temperature_publisher_ = self.create_publisher(Int64, "temperature", 10)
        self.temperature_timer = self.create_timer(2.0, self.publish_temperature)

    def publish_temperature(self):
        temperature = random.randint(20,30)
        msg = Int64()
        msg.data = temperature
        self.temperature_publisher_.publish(msg)

def main(args=None):
    rclpy.init(args=args)
    node = TemperatureSensorNode()
    rclpy.spin(node)
    rclpy.shutdown()

if __name__ == "__main__":
    main()
```
```shell
# install publisher in setup.py
entry_points={
  'console_scripts': [
    "temperature_sensor = my_publisher.temperature_sensor:main"
  ]
}
# build
cd testws
colcon build --packages-select my_publisher
```
```shell
ros2 run my_publisher temperature
```
```shell
ros2 topic echo /temperature
```
