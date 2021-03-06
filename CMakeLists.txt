cmake_minimum_required(VERSION 2.8.3)
project(octomap_pa)

find_package(PCL REQUIRED)
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

find_package(octomap REQUIRED)

# bugfix for g++-Warning
#   "<command-line>:0:15: warning: missing whitespace after the macro name"
remove_definitions(-DDISABLE_LIBUSB-1.0)

find_package(catkin REQUIRED COMPONENTS
  roscpp

  std_msgs
  geometry_msgs
  sensor_msgs
  nav_msgs
  std_srvs
  message_generation

  tf
  laser_geometry


  pcl_ros
  pcl_conversions

  octomap_ros
  octomap_msgs

  parameter_pa
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
#  ${OCTOMAP_INCLUDE_DIRS}
)

add_service_files(
  FILES
    OctomapPaFileName.srv
    OctomapPaGetSize.srv
)
generate_messages(
  DEPENDENCIES
    std_msgs
    geometry_msgs
    sensor_msgs
    nav_msgs
    std_srvs
)

catkin_package(
  DEPENDS

  INCLUDE_DIRS
    include

  CATKIN_DEPENDS
    roscpp

    std_msgs
    geometry_msgs
    sensor_msgs
    nav_msgs
    std_srvs
    message_runtime

    tf
    laser_geometry

    pcl_ros
    pcl_conversions

    octomap_ros
    octomap_msgs

    parameter_pa

  LIBRARIES
    ${PROJECT_NAME}_internal
    ${PROJECT_NAME}_ros
    ${PROJECT_NAME}_stamped_ros
)



## internal library
add_library(${PROJECT_NAME}_internal
  src/time_pa.cpp
  src/addcloud_parameter.cpp

  src/octree_base_pa_ros_parameter.cpp
  src/octree_base_pa_node_parameter.cpp
)
add_dependencies(${PROJECT_NAME}_internal
  ${catkin_EXPORTED_TARGETS}
)
target_link_libraries(${PROJECT_NAME}_internal
  ${catkin_LIBRARIES}
#  ${OCTOMAP_LIBRARIES}
)

## simple octomap library (ros)
add_library(${PROJECT_NAME}_ros
  src/octree_pa_node.cpp
  src/octree_pa_ros.cpp
)
add_dependencies(${PROJECT_NAME}_ros
  ${PROJECT_NAME}_internal
)
target_link_libraries(${PROJECT_NAME}_ros
  ${PROJECT_NAME}_internal
)

## stamped octomap library (ros)
add_library(${PROJECT_NAME}_stamped_ros
  src/octree_stamped_pa.cpp
  src/octree_stamped_pa_ros.cpp
  src/octree_stamped_pa_ros_parameter.cpp

  src/octree_stamped_native_ros.cpp
)
add_dependencies(${PROJECT_NAME}_stamped_ros
  ${PROJECT_NAME}_internal
)
target_link_libraries(${PROJECT_NAME}_stamped_ros
  ${PROJECT_NAME}_internal
)



## simple node
add_executable(octree_pa_node
  src/octree_pa_node.cpp
)
add_dependencies(octree_pa_node
  ${PROJECT_NAME}_ros

  ${PROJECT_NAME}_generate_messages_cpp
  ${PROJECT_NAME}_gencpp

  ${${PROJECT_NAME}_EXPORTED_TARGETS}
)
target_link_libraries(octree_pa_node
  ${PROJECT_NAME}_ros
)

## stamped node
add_executable(octree_stamped_pa_node
  src/octree_stamped_pa_node.cpp
)
add_dependencies(octree_stamped_pa_node
  ${PROJECT_NAME}_stamped_ros

  ${PROJECT_NAME}_generate_messages_cpp
  ${PROJECT_NAME}_gencpp

  ${${PROJECT_NAME}_EXPORTED_TARGETS}
)
target_link_libraries(octree_stamped_pa_node
  ${PROJECT_NAME}_stamped_ros
)

## stamped node (internal version)
add_executable(octree_stamped_native_node
  src/octree_stamped_native_node.cpp
)
add_dependencies(octree_stamped_native_node
  ${PROJECT_NAME}_stamped_ros

  ${PROJECT_NAME}_generate_messages_cpp
  ${PROJECT_NAME}_gencpp

  ${${PROJECT_NAME}_EXPORTED_TARGETS}
)
target_link_libraries(octree_stamped_native_node
  ${PROJECT_NAME}_stamped_ros
)

## Install ##
install(
  TARGETS
    ${PROJECT_NAME}_internal
    ${PROJECT_NAME}_ros
    ${PROJECT_NAME}_stamped_ros
    octree_pa_node
    octree_stamped_pa_node
    octree_stamped_native_node
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h" PATTERN "*.hxx"
)

install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
  FILES_MATCHING PATTERN "*.launch"
)
install(DIRECTORY config/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/config
  FILES_MATCHING PATTERN "*.yaml" PATTERN "*.rviz"
)
