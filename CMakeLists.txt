cmake_minimum_required(VERSION 2.8.3)
project(ros_caffe)

#add_definitions(-DCPU_ONLY=1)
## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  cv_bridge
  std_msgs
  sensor_msgs
  image_transport
  roscpp
  roslib
  cmake_modules
  message_generation
  dynamic_reconfigure
)

#set(CAFFE_INCLUDEDIR caffe/build/install/include)
#set(CAFFE_LINK_LIBRARAY caffe/build/install/lib)

set(CAFFE_INCLUDEDIR caffe/include caffe/distribute/include caffe/.build_release/src)
set(CAFFE_LINK_LIBRARAY /home/dinu94/ros_caffe_ws/src/ros_caffe/caffe/build/lib)

#set(OpenCV_INCLUDE_DIRS /usr/local/include )
#set(OpenCV_LIBRARIES /usr/local/lib )

set(CUDA_INCLUDEDIR /usr/local/cuda/include /usr/local/cuda/src)
set(CUDA_LINK_LIBRARAY /usr/local/cuda/lib64)

## System dependencies are found with CMake's conventions
find_package(Boost REQUIRED COMPONENTS system)
find_package(OpenCV REQUIRED)
##find_package(CUDA REQUIRED)

include(FindPkgConfig)

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
# add_message_files(
#   FILES
#   Message1.msg
#   Message2.msg
# )

## Generate services in the 'srv' folder
# add_service_files(
#   FILES
#   Service1.srv
#   Service2.srv
# )

## Generate actions in the 'action' folder
# add_action_files(
#   FILES
#   Action1.action
#   Action2.action
# )

## Generate added messages and services with any dependencies listed here
# generate_messages(
#   DEPENDENCIES
#   std_msgs  # Or other packages containing msgs
# )

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
        INCLUDE_DIRS include
        LIBRARIES ros_caffe
        CATKIN_DEPENDS
                cv_bridge
                std_msgs
                sensor_msgs
                image_transport
                roscpp
                cmake_modules
                message_generation
        DEPENDS system_lib
)

###########
## Build ##
###########

# ## Declare a cpp library
# # Tell CMake that the external project generated a library so we
# # can add dependencies here instead of later.
#add_library(ros_caffe UNKNOWN IMPORTED)
#set_property(TARGET ros_caffe
#  PROPERTY IMPORTED_LOCATION
#  ${ros_caffe_LIBRARIES}
#)

# ## Specify additional locations of header files
# ## Your package locations should be listed before other locations
include_directories( include
    ${catkin_INCLUDE_DIRS}
    ${OpenCV_INCLUDE_DIRS}
    ${CAFFE_INCLUDEDIR}
    ${CUDA_INCLUDEDIR}
)

# ## Declare a cpp executable
#add_library(ros_caffe src/ros_caffe_test.cpp)
add_library(ros_caffe src/testClassifier.cpp)
link_directories(${CAFFE_LINK_LIBRARAY})
add_executable(ros_caffe_node src/testClassifier.cpp)

# ## Add cmake target dependencies of the executable/library
# ## as an example, message headers may need to be generated before nodes
# make sure configure headers are built before any node using them
add_dependencies(ros_caffe
    ${PROJECT_NAME}_gencfg)

add_dependencies(ros_caffe_node
    ${PROJECT_NAME}_gencfg)

# ## Specify libraries to link a library or executable target against
target_link_libraries(ros_caffe_node
    ${catkin_LIBRARIES}
    ${OpenCV_LIBRARIES}
    ${CAFFE_LINK_LIBRARAY}
    ros_caffe
    glog
    caffe
    #cuda
)

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
install(TARGETS ros_caffe ros_caffe_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
install(FILES
   ${ros_caffe_LIBRARIES}
   DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_ros_caffe.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)

# link_directories(${CAFFE_LINK_LIBRARAY})
# add_executable(ros_caffe_test src/ros_caffe_test.cpp)
# target_link_libraries(ros_caffe_test ${catkin_LIBRARIES} ${OpenCV_LIBRARIES} caffe glog)

# add_executable(ros_caffe src/ros_caffe.cpp)
# target_link_libraries(ros_caffe ${catkin_LIBRARIES} ${OpenCV_LIBRARIES} caffe glog)
