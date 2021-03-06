cmake_minimum_required(VERSION 2.8.3)
project(pitt_object_table_segmentation)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
        message_runtime
        pcl_conversions
        pcl_ros
        roscpp
        sensor_msgs
        std_msgs
        tf
        pitt_msgs

        )

find_package(PCL REQUIRED)

## System dependencies are found with CMake's conventions
# find_package(Boost REQUIRED COMPONENTS system)


## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

################################################
## Declare ROS messages, services and actions ##
################################################

## To declare and build messages, services or actions from within this
## package, follow these steps:
## * Let MSG_DEP_SET be the set of packages whose message types you use in
##   your messages/services/actions (e.g. std_msgs, actionlib_msgs, ...).
## * In the file package.xml:
##   * add a build_depend and a run_depend tag for each package in MSG_DEP_SET
##   * If MSG_DEP_SET isn't empty the following dependencies might have been
##     pulled in transitively but can be declared for certainty nonetheless:
##     * add a build_depend tag for "message_generation"
##     * add a run_depend tag for "message_runtime"
## * In this file (CMakeLists.txt):
##   * add "message_generation" and every package in MSG_DEP_SET to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * add "message_runtime" and every package in MSG_DEP_SET to
##     catkin_package(CATKIN_DEPENDS ...)
##   * uncomment the add_*_files sections below as needed
##     and list every .msg/.srv/.action file to be processed
##   * uncomment the generate_messages entry below
##   * add every package in MSG_DEP_SET to generate_messages(DEPENDENCIES ...)

## Generate messages in the 'msg' folder
#add_message_files(
#   DIRECTORY msg
#   FILES
#)

## Generate services in the 'srv' folder
#add_service_files(
#   DIRECTORY srv
#   FILES
#)

## Generate actions in the 'action' folder
# add_action_files(
#   FILES
#   Action1.action
#   Action2.action
# )

## Generate added messages and services with any dependencies listed here
#generate_messages(
#   DEPENDENCIES
#   std_msgs
#   sensor_msgs
#   pcl_msgs
#   pitt_msgs
#   geometric_traking
#)


###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if you package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
        #  INCLUDE_DIRS include
        #  LIBRARIES object_table_segmentation
        #  CATKIN_DEPENDS other_catkin_pkg
        CATKIN_DEPENDS pcl_conversions roscpp sensor_msgs pcl_msgs pitt_msgs message_generation std_msgs
        #  DEPENDS system_lib
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
        ${catkin_INCLUDE_DIRS}
        ${PCL_INCLUDE_DIRS}
)
link_directories(${PCL_LIBRARY_DIRS})

## Declare a cpp library
add_library(pc_manager src/point_cloud_library/pc_manager.cpp)
add_library(pc_primitive src/point_cloud_library/pc_primitive.cpp)
target_link_libraries(pc_manager pc_primitive ${catkin_LIBRARIES} ${PCL_LIBRARIES})

################################################ PREPROCESSING !!

# arm filtering (SERVICE)
add_executable(arm_filter_srv src/segmentation_services/arm_filter_srv.cpp)
target_link_libraries(arm_filter_srv pc_manager ${catkin_LIBRARIES} ${PCL_LIBRARIES})
add_dependencies(arm_filter_srv pitt_msgs_generate_messages_cpp)

# deep filter (SERVICE)
add_executable(deep_filter_srv src/segmentation_services/deep_filter_srv.cpp)
target_link_libraries(deep_filter_srv pc_manager ${catkin_LIBRARIES} ${PCL_LIBRARIES})
add_dependencies(deep_filter_srv pitt_msgs_generate_messages_cpp)

#################################################### CLUSTER SEGMENTATION !!

# support segmentator (SERVICE)
add_executable(supports_segmentation_srv src/segmentation_services/supports_segmentation_srv.cpp)
target_link_libraries(supports_segmentation_srv pc_manager ${catkin_LIBRARIES} ${PCL_LIBRARIES})
add_dependencies(supports_segmentation_srv pitt_msgs_generate_messages_cpp)

# cluster segmentation (SERVICE)
add_executable(cluster_segmentation_srv src/segmentation_services/cluster_segmentation_srv.cpp)
target_link_libraries(cluster_segmentation_srv pc_manager pc_primitive ${catkin_LIBRARIES} ${PCL_LIBRARIES})
add_dependencies(cluster_segmentation_srv pitt_msgs_generate_messages_cpp)

# orchestrator segmentation orcestrator (NODE)
add_executable(obj_segmentation src/obj_segmentation.cpp)
target_link_libraries(obj_segmentation pc_manager pc_primitive ${catkin_LIBRARIES} ${PCL_LIBRARIES})
add_dependencies(obj_segmentation pitt_msgs_generate_messages_cpp)

#################################################  SHAPES SEGMENTATION (RANSAC) !!

# plane ransac segmentation (SERVICE)
add_executable(plane_segmentation_srv src/segmentation_services/plane_segmentation_srv.cpp)
target_link_libraries(plane_segmentation_srv pc_manager ${catkin_LIBRARIES} ${PCL_LIBRARIES})
add_dependencies(plane_segmentation_srv pitt_msgs_generate_messages_cpp)

# sphere ransac segmentation (SERVICE)
add_executable(sphere_segmentation_srv src/segmentation_services/sphere_segmentation_srv.cpp)
target_link_libraries(sphere_segmentation_srv pc_manager pc_primitive ${catkin_LIBRARIES} ${PCL_LIBRARIES})
add_dependencies(sphere_segmentation_srv pitt_msgs_generate_messages_cpp)

# cylinder ransac segmentation (SERVICE)
add_executable(cylinder_segmentation_srv src/segmentation_services/cylinder_segmentation_srv.cpp)
target_link_libraries(cylinder_segmentation_srv pc_manager pc_primitive ${catkin_LIBRARIES} ${PCL_LIBRARIES})
add_dependencies(cylinder_segmentation_srv pitt_msgs_generate_messages_cpp)

# cone ransac segmentation (SERVICE)
add_executable(cone_segmentation_srv src/segmentation_services/cone_segmentation_srv.cpp)
target_link_libraries(cone_segmentation_srv pc_manager pc_primitive ${catkin_LIBRARIES} ${PCL_LIBRARIES})
add_dependencies(cone_segmentation_srv pitt_msgs_generate_messages_cpp)

# ransac orchetrator node
add_executable(ransac_segmentation src/ransac_segmentation.cpp)
target_link_libraries(ransac_segmentation pc_manager ${catkin_LIBRARIES} ${PCL_LIBRARIES})
add_dependencies(ransac_segmentation pitt_msgs_generate_messages_cpp)

#color service
add_executable(color_services src/color_services/color_srv.cpp)
target_link_libraries(color_services pc_manager ${catkin_LIBRARIES} ${PCL_LIBRARIES} )
add_dependencies(color_services pitt_msgs_generate_messages_cpp)
#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
# install(TARGETS object_table_segmentation object_table_segmentation_node
#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_object_table_segmentation.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
