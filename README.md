# RBX1

# Introduction
In this project the communication between the rbx1 package and the ROS environment will be made, through the same topic, using the remap function in the fake_turtlebot.launch  file to let the robot control by keyboard.

# Install RBX1 repository
In a terminal, first go to your <catkin_ws> source folder. Write:
```
cd ~/<catkin_ws>/src
```
Then, copy the RBX1 repository from author's github:
```
git clone https://github.com/pirobot/rbx1.git
```
Or you can install directly from ROS repository:
```
sudo apt-get install ros-<distro>-rbx1
```
if you get any trouble, try installing ROS dependencies:
```
sudo apt-get install python-rosinstall
```
# Topics identification
First, in a terminal, you should run the ROS environmetn with:
```
roscore
```
You should see something similar to:
```
... logging to /home/user/.ros/log/00dd2b82-a49c-11eb-87a4-0025d3d70f40/roslaunch-user-X427LKG-5298.log
Checking log directory for disk usage. This may take awhile.
Press Ctrl-C to interrupt

started roslaunch server http://luisc-X441UVK:43315/
ros_comm version 1.12.17

SUMMARY
========

PARAMETERS
 * /rosdistro: kinetic
 * /rosversion: 1.12.17

NODES

auto-starting new master
process[master]: started with pid [5314]
ROS_MASTER_URI=http://luisc-X441UVK:11311/

setting /run_id to 00dd2b82-a49c-11eb-87a4-0025d3d70f40
process[rosout-1]: started with pid [5328]
started core service [/rosout]
```
In another terminal, you should run the fake_turtlebot.launch using:
```
roslaunch rbx1_bringup fake_turtlebot.launch
```
After run the launch, in another terminal we review the executing topics with:
```
rostopic list
```
You should see something similar to:
```
user@user-X427LKG:~$ rostopic list
/diagnostics
/joint_states
/odom
/rosout
/rosout_agg
/tf
/tf_static
/cmd_vel
```
Now, in another terminal we run the turtle_teleop_key:
```
rosrun turtlesim turtle_teleop_key
```
After run the launch, in another terminal we review the executing topics with:
```
rostopic list
```
You should see something similar to:
```
user@user-X427LKG:~$ rostopic list
/diagnostics
/joint_states
/odom
/rosout
/rosout_agg
/tf
/tf_static
/cmd_vel
/turtle1/cmd_vel
```
# Remap
As you can see, /cmd_vel and /turtle1/cmd_vel topics corresponds to fake_turtlebot.launch of rbx1 arbotix package and turtle_teleop_key of turtlesim package respectively. To allow the communication between them, in the fake_turtlebot.launch, you should remap the topic adding:
```
<remap from="/cmd_vel" to="/turtle1/cmd_vel"/>
```
The fake_turtlebot.launch should be:
```
<launch>
  <param name="/use_sim_time" value="false" />

  <!-- Load the URDF/Xacro model of our robot -->
  <arg name="urdf_file" default="$(find xacro)/xacro.py '$(find rbx1_description)/urdf/turtlebot.urdf.xacro'" />
   
  <param name="robot_description" command="$(arg urdf_file)" />
    
  <node name="arbotix" pkg="arbotix_python" type="arbotix_driver" output="screen" clear_params="true">
      <rosparam file="$(find rbx1_bringup)/config/fake_turtlebot_arbotix.yaml" command="load" />
      <param name="sim" value="true"/>
      <remap from="/cmd_vel" to="/turtle1/cmd_vel"/>
  </node>

  <node name="robot_state_publisher" pkg="robot_state_publisher" type="state_publisher">
      <param name="publish_frequency" type="double" value="20.0" />
  </node>

</launch>
```
# Execution
