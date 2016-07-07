cmake_minimum_required(VERSION 2.8.3)
project(rb1_base_2dnav)

find_package(catkin REQUIRED COMPONENTS 
	message_runtime 
	std_msgs 
	move_base
	gmapping
	amcl
	tf
	rviz
	map_server
	robot_pose_ekf
)



catkin_package(
	CATKIN_DEPENDS
	message_runtime
	
)


include_directories(
  ${catkin_INCLUDE_DIRS}
)

install(
  DIRECTORY config_navigation config_rviz launch maps
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

