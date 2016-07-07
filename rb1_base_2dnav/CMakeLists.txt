cmake_minimum_required(VERSION 2.8.3)
project(rb1_base_2dnav)

find_package(catkin REQUIRED COMPONENTS  

)



catkin_package(

	
)


include_directories(
  ${catkin_INCLUDE_DIRS}
)

install(
  DIRECTORY config_navigation config_rviz launch maps
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

