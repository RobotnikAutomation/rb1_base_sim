cmake_minimum_required(VERSION 2.8.3)
project(rb1_base_gazebo)


find_package(catkin REQUIRED COMPONENTS
  gazebo_ros
  gazebo_ros_control
  diff_drive_controller
)



catkin_package()


include_directories(
  ${catkin_INCLUDE_DIRS}
)

install(DIRECTORY launch
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

install(DIRECTORY worlds
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})


