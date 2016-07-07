cmake_minimum_required(VERSION 2.8.3)
project(rb1_base_purepursuit)


find_package(catkin REQUIRED COMPONENTS)



catkin_package()


install(DIRECTORY launch
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
