cmake_minimum_required(VERSION 2.8.3)
project(mas_navigation_tools)

## Compile as C++11, supported in ROS Kinetic and newer
add_compile_options(-std=c++14)

find_package(catkin REQUIRED COMPONENTS
    actionlib
    message_generation
    geometry_msgs
    laser_geometry
    message_filters
    nav_msgs
    pcl_ros
    roscpp
    roslint
    std_msgs
    rospy
    rviz

)

find_package(PCL 1.7 REQUIRED)

add_action_files(DIRECTORY ros/action
  FILES
    OccupancyChecker.action
)

generate_messages(
  DEPENDENCIES
    geometry_msgs
    actionlib_msgs
)

catkin_package(CATKIN_DEPENDS
    geometry_msgs
    nav_msgs
    std_msgs
    actionlib_msgs
)

include_directories(
    common/include/
    ros/include/
    ${catkin_INCLUDE_DIRS}
    ${PCL_INCLUDE_DIRS}
)

### library
add_library(path_length_calculator common/src/path_length_calculator.cpp)

### EXECUTABLES
add_executable(path_length_calculator_node ros/src/path_length_calculator_node.cpp)
target_link_libraries(path_length_calculator_node ${catkin_LIBRARIES} path_length_calculator)

add_executable(pose_array_to_path_node ros/src/pose_array_to_path_node.cpp)
target_link_libraries(pose_array_to_path_node ${catkin_LIBRARIES})

add_executable(laser_distances_node
    ros/src/laser_distances_node.cpp
)
target_link_libraries(laser_distances_node
    ${catkin_LIBRARIES}
    ${PCL_LIBRARIES}
)

### TESTS
if(CATKIN_ENABLE_TESTING)
  find_package(roslaunch REQUIRED)

  roslaunch_add_file_check(ros/launch)
endif()

roslint_cpp()

### INSTALLS
install(PROGRAMS
  ros/scripts/pose_visualiser
  ros/scripts/save_base_map_poses_to_file
  ros/scripts/map_saver
  ros/scripts/occupancy_checker
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(TARGETS path_length_calculator
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

install(TARGETS path_length_calculator_node
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(TARGETS pose_array_to_path_node
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY ros/launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/ros/launch
)

## Map annotation tool
link_directories(${catkin_LIBRARY_DIRS})

## This setting causes Qt's "MOC" generation to happen automatically.
set(CMAKE_AUTOMOC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)
## This plugin includes Qt widgets, so we must include Qt.
## We'll use the version that rviz used so they are compatible.
if(rviz_QT_VERSION VERSION_LESS "5")
  message(STATUS "Using Qt4 based on the rviz_QT_VERSION: ${rviz_QT_VERSION}")
  find_package(Qt4 ${rviz_QT_VERSION} EXACT REQUIRED QtCore QtGui)
  ## pull in all required include dirs, define QT_LIBRARIES, etc.
  include(${QT_USE_FILE})
else()
  message(STATUS "Using Qt5 based on the rviz_QT_VERSION: ${rviz_QT_VERSION}")
  find_package(Qt5 ${rviz_QT_VERSION} EXACT REQUIRED Core Widgets)
  ## make target_link_libraries(${QT_LIBRARIES}) pull in all required dependencies
  set(QT_LIBRARIES Qt5::Widgets)
endif()

add_definitions(-DQT_NO_KEYWORDS)


set(SRC_FILES
  ros/src/map_annotation_tool.cpp
)

set(HEADER_FILES
   ros/include/map_annotation_tool.h
)

add_library(map_annotation_tool ${SRC_FILES} ${HEADER_FILES})

target_link_libraries(map_annotation_tool ${QT_LIBRARIES} ${catkin_LIBRARIES} ${SDL2_LIBRARY} ${GLEW_LIBRARIES})

install(TARGETS map_annotation_tool
    RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
    ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
    LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

install(FILES plugin_description.xml DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
