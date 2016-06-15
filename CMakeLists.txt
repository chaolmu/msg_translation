cmake_minimum_required(VERSION 2.8.3)
project(msg_translation)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS message_generation std_msgs roscpp actionlib tf control_msgs brics_actuator pr2_msgs)
find_package(Boost REQUIRED COMPONENTS thread date_time filesystem system)
find_package(youbot_driver REQUIRED)



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
#   std_msgs
# )


catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES node
  CATKIN_DEPENDS roscpp rospy std_msgs   pr2_msgs
  brics_actuator
#  DEPENDS system_lib
)

###########
## Build ##
###########
ADD_DEFINITIONS(-DYOUBOT_CONFIGURATIONS_DIR=${youbot_driver_CONFIG_DIR})
## Specify additional locations of header files
## Your package locations should be listed before other locations
# include_directories(include)
include_directories(
        include
	${catkin_INCLUDE_DIRS}
	${Boost_INCLUDE_DIR}
	${youbot_driver_INCLUDE_DIRS}
)

## Declare a C++ library
# add_library(node
#   src/${PROJECT_NAME}/node.cpp
# )

## Add cmake target dependencies of the library
## as an example, code may need to be generated before libraries
## either from message generation or dynamic reconfigure
# add_dependencies(node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Declare a C++ executable
 add_executable(msg_translation 
        src/msg_translation.cpp
        src/MsgTranslationWrapper.cpp
        src/NodeConfiguration.cpp


)

## Add cmake target dependencies of the executable
## same as for the library above
# add_dependencies(node_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Specify libraries to link a library or executable target against
 target_link_libraries(msg_translation
   ${catkin_LIBRARIES}   ${youbot_driver_LIBRARIES}
 )

add_dependencies(msg_translation ${catkin_EXPORTED_TARGETS})





OPTION(USE_SETCAP "Set permissions to access ethernet interface without sudo" ON)

SET(SUDO_COMMAND gksudo)
IF($ENV{USE_NORMAL_SUDO})
    SET(SUDO_COMMAND sudo)
ENDIF($ENV{USE_NORMAL_SUDO})

IF(USE_SETCAP)
add_custom_command(TARGET node_node POST_BUILD
    #COMMAND sudo setcap cap_net_raw+ep youbot_oodl
    COMMAND ${SUDO_COMMAND} setcap cap_net_raw+ep $<TARGET_FILE:youbot_driver_ros_interface>
)    
endif(USE_SETCAP)

