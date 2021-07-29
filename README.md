# Run-multiple-Turtlebot3-robot-in-gazebo-simulator
This repository shows the steps taken to  Run more than one of the Turtlebot3 robots on the gazebo using a teleop.

These were tested under ROS Melodic and Ubuntu 18.04.

# Dependencies


-Make sure you already have a running VM with ubuntu 18.04 OS, and already installed ROS Melodic inside it.

-Make sure you Install the Turtlebot3 package.

-type in the commands down below in the terminal

**1.Make a package**
```

$ cd ~/catkin_ws/src
$ catkin_create_pkg multiple_turtlebots_sim

```
**2.Make a folder for writing launch files.**

```

$ cd ~/catkin_ws/src/multiple_turtlebots_sim
$ mkdir launch

```

**3.Make a launch file for one robot.**

```

$ cd ~/catkin_ws/src/multiple_turtlebots_sim/launch
$ gedit one_robot.launch

```
Paste the code below.
```
<launch>
    <!-- Set arguments -->
    <arg name="init_pose"/>
    <arg name="robot_name"/>

    <!-- Load robot description -->
    <param name="robot_description" command="$(find xacro)/xacro --inorder $(find turtlebot3_description)/urdf/turtlebot3_waffle.urdf.xacro" />

    <!-- Spwan a robot into Gazebo -->
    <node name="spawn_urdf" pkg="gazebo_ros" type="spawn_model"
          args="$(arg init_pose) -urdf -param robot_description -model $(arg robot_name)"
          respawn="false" output="screen"/>
        
    <!-- Publich robot state -->
    <node pkg="robot_state_publisher" type="robot_state_publisher"
          name="robot_state_publisher" output="screen"/>
</launch>

```

**4.Make a launch file for multiple robots.**

```

$ cd ~/catkin_ws/src/multiple_turtlebots_sim/launch
$ gedit robots.launch

```
Paste the code below.
```
<launch>

    <!-- BEGIN ROBOT 1 -->
    <group ns="robot1">
        <param name="tf_prefix" value="robot1_tf"/>
        <include file="$(find multiple_turtlebots_sim)/launch/one_robot.launch">
            <arg name="init_pose" value="-x 2 -y 0 -z 0 -Y 3.14"/>
            <arg name="robot_name" value="Robot1"/>
        </include>
    </group>

    <!-- BEGIN ROBOT 2 -->
    <group ns="robot2">
        <param name="tf_prefix" value="robot2_tf"/>
        <include file="$(find multiple_turtlebots_sim)/launch/one_robot.launch">
            <arg name="init_pose" value="-x -2 -y 0 -z 0 -Y 0"/>
            <arg name="robot_name" value="Robot2"/>
        </include>
    </group>

</launch>

```

**5.Make a launch file for loading a gazebo world.**
```

$ cd ~/catkin_ws/src/multiple_turtlebots_sim/launch
$ gedit simulation.launch

```
Paste the code below.
```
<launch>

    <include file="$(find gazebo_ros)/launch/empty_world.launch">
        <arg name="world_name" value="$(find turtlebot3_gazebo)/worlds/turtlebot3_world.world"/>
        <arg name="paused" value="false"/>
        <arg name="use_sim_time" value="true"/>
        <arg name="gui" value="true"/>
        <arg name="headless" value="false"/>
        <arg name="debug" value="false"/>
    </include>

    <include file="$(find multiple_turtlebots_sim)/launch/robots.launch"/>

</launch>

```

**6.Now update the modified content.**
```

$ cd ~/catkin_ws && catkin_make
$ rospack profile


```
**7. launch the simulation.**
```

 roslaunch multiple_turtlebots_sim simulation.launch
 

```
**8.Move the robot.**


Open another terminal
```
for robot1 
rosrun teleop_twist_keyboard teleop_twist_keyboard.py /cmd_vel:=/robot1/cmd_vel

for robot2
rosrun teleop_twist_keyboard teleop_twist_keyboard.py /cmd_vel:=/robot2/cmd_vel

```

** If you  encountered this error [rospack] Error: package 'teleop_twist_keyboard' not found **

You can solve it by typing  this commands :

```
1) cd ~/catkin_ws/src

2) git clone https://github.com/ros-teleop/teleop_twist_keyboard

3) cd ~/catkin_ws

4) catkin_make

5) source ~/catkin_ws/devel/setup.bash

6) source ~/.bashrc

```
you should add the following lines to your bashrc script by running 
```
gedit ~/.bashrc

```
add this lines 
```

 source ~/catkin_ws/devel/setup.bash
 
```
   ** Results** 


![20](https://user-images.githubusercontent.com/86648269/127411066-d140ea79-d902-40cf-96be-6de48225087a.png)


![21](https://user-images.githubusercontent.com/86648269/127411165-b42c9996-0e54-4cc7-8cea-ca52e78879fb.png)


![22](https://user-images.githubusercontent.com/86648269/127411280-9e4346d3-9ab1-4b3c-93c6-c848727c3577.png)


![23](https://user-images.githubusercontent.com/86648269/127411296-cef276d4-5439-4086-bd8e-100c74069a3e.png)


![24](https://user-images.githubusercontent.com/86648269/127411304-1bd0f160-5df9-440b-9117-6a493d1f094f.png)









