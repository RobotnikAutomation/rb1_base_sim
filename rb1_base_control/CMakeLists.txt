cmake_minimum_required(VERSION 2.8.3)
project(rb1_base_control)


find_package(catkin REQUIRED)

catkin_package(
CATKIN_DEPENDS twist_mux 
robot_state_publisher
controller_manager
controller_manager_msgs
joint_state_controller
diff_drive_controller
)


include_directories(  ${catkin_INCLUDE_DIRS})

install(DIRECTORY launch
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

install(DIRECTORY config
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
