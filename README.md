rb1_base_sim
=============

Packages for the simulation of the RB-1 Base

<p align="center">
  <img src="https://robotnik.eu/wp-content/uploads/2020/05/Robotnik-RB-1_Base-01_.jpg" width="275" />

  <img src="https://robotnik.eu/wp-content/uploads/2020/05/Robotnik_RB-1-Base_02_.jpg" width="280" />
    <img src="https://robotnik.eu/wp-content/uploads/2020/05/Robotnik_RB-1-BASE_03_.jpg" width="275" />
</p>


<h1> Packages </h1>

<h2>rb1_base_gazebo</h2>

This package contains the configuration files and worlds to launch the Gazebo environment along with the simulated robot.

<h2>rb1_base_sim_bringup</h2>

Launch files that launch the complete simulation of the robot/s.



<h1>Simulating RB-1 Base</h1>

## 1. Install the following dependencies:
  - rb1_base_common [link](https://github.com/RobotnikAutomation/rb1_base_common)
  - robotnik_msgs [link](https://github.com/RobotnikAutomation/robotnik_msgs)
  - robotnik_sensors [link](https://github.com/RobotnikAutomation/robotnik_sensors)
  - robotnik_base_hw_sim [link](https://github.com/RobotnikAutomation/robotnik_base_hw_sim)
  - robot_localization_utils [link](https://github.com/RobotnikAutomation/robot_localization_utils)
  - gazebo_ros_pkgs (Robotnik's fork) [link](https://github.com/RobotnikAutomation/gazebo_ros_pkgs)

    In the workspace install the packages dependencies:
    ```
    rosdep install --from-paths src --ignore-src -r -y
    ```  

## 2. Launch RB-1 Base simulation

1 robot by default, up to 3 robots at the same time.

## RB-1 Base
  ```
  roslaunch rb1_base_sim_bringup rb1_base_complete.launch
  ```

  Optional general arguments:
  ```
  <arg name="launch_rviz" default="true"/>
  <arg name="gazebo_world" default="$(find rb1_base_gazebo)/worlds/rb1_base_office.world"/>
  ```
  Optional robot arguments:
  ```
  <!--arguments for each robot (example for robot A)-->
  <arg name="id_robot_a" default="robot"/>
  <arg name="launch_robot_a" default="true"/>
  <arg name="has_elevator_robot_a" default="true"/>
  <arg name="x_init_pose_robot_a" default="0.0" />
  <arg name="y_init_pose_robot_a" default="0.0" />
  <arg name="z_init_pose_robot_a" default="0.0" />
  <arg name="init_yaw_robot_a" default="0.0" />
  <arg name="gmapping_robot_a" default="false"/>
  <arg name="amcl_and_mapserver_robot_a" default="true"/>
  <arg name="map_frame_robot_a" default="$(arg id_robot_a)_map"/>
  <arg name="map_file_robot_a" default="$(find rb1_base_localization)/maps/willow_garage/willow_garage.yaml"/>
  <arg name="move_base_robot_a" default="true"/>
  <arg name="pad_robot_a" default="true"/>
  ```
- Example to launch simulation with 3 RB-1 Base robots:
  ```
  roslaunch rb1_base_sim_bringup rb1_base_complete.launch launch_robot_a:=true launch_robot_b:=true launch_robot_c:=true
  ```
- Example to launch simulation with 1 RB-1 Base robot with navigation and localization:
  ```
  roslaunch rb1_base_sim_bringup rb1_base_complete.launch launch_robot_a:=true move_base_robot_a:=true amcl_and_mapserver_robot_a:=true
  ```
- Example to launch simulation with 2 RB-1 Base robot with navigation and localization sharing the same global frame:
  ```
  roslaunch rb1_base_sim_bringup rb1_base_complete.launch launch_robot_a:=true amcl_and_mapserver_robot_a:=true move_base_robot_a:=true map_frame_a:=/map launch_robot_b:=true amcl_and_mapserver_robot_b:=true move_base_robot_b:=true map_frame_b:=/map
  ```
- Example to launch simulation with 3 RB-1 Base robot with navigation and localization sharing the same global frame:
```
roslaunch rb1_base_sim_bringup rb1_base_complete.launch launch_robot_a:=true amcl_and_mapserver_robot_a:=true move_base_robot_a:=true map_frame_a:=/map launch_robot_b:=true amcl_and_mapserver_robot_b:=true move_base_robot_b:=true map_frame_b:=/map launch_robot_c:=true amcl_and_mapserver_robot_c:=true move_base_robot_c:=true map_frame_c:=/map
```

### RB-1 Base Dual Arm

There is also a version of the base plus a torso with two ur3 arms.

  <img src="https://robotnik.eu/wp-content/uploads/2020/11/rb1_dual_ur3_gazebo.png" width="275" />

- Example to launch rb1_base with two ur arms:
```bash
roslaunch rb1_base_sim_bringup rb1_dual_ur3_complete.launch amcl_and_mapserver:=true move_base:=true
```

- In case you want to use moveit to manipulate the arms:
```bash
roslaunch rb1_base_sim_bringup rb1_dual_ur3_complete.launch amcl_and_mapserver:=true move_base:=true moveit_movegroup:=true
```
* This simulation requires the use of the ros_planar plugin (Robotnik's fork).

### 3. Enjoy!
 You can use the topic "${id_robot}/robotnik_base_control/cmd_vel" to control the RB-1 Base robot or send simple goals using "/${id_robot}/move_base_simple/goal"
