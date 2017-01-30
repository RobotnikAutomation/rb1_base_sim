# Launch files
roslaunch rb1_base_gazebo rb1_base.launch
roslaunch rb1_base_purepursuit purepursuit.launch
roslaunch rb1_base_purepursuit purepursuit_marker.launch

# Running RVIZ
rosrun rviz rviz (add InteractiveMarkers /path_marker/update), click with right button mouse on the first marker for options to add waypoints or start/stop route.
