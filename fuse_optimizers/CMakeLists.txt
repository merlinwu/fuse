cmake_minimum_required(VERSION 2.8.3)
project(fuse_optimizers)

find_package(catkin REQUIRED COMPONENTS
  fuse_core
  fuse_graphs
  pluginlib
  roscpp
)

catkin_package(
  INCLUDE_DIRS
    include
  LIBRARIES
    ${PROJECT_NAME}
  CATKIN_DEPENDS
    fuse_core
    fuse_graphs
    pluginlib
    roscpp
)

###########
## Build ##
###########

add_compile_options(-std=c++14 -Wall -Werror)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)

## fuse_optimizers library
add_library(${PROJECT_NAME}
  src/batch_optimizer.cpp
  src/optimizer.cpp
)
add_dependencies(${PROJECT_NAME}
  ${catkin_EXPORTED_TARGETS}
)
target_link_libraries(${PROJECT_NAME}
  ${catkin_LIBRARIES}
)

## batch_optimizer node
add_executable(batch_optimizer_node
  src/batch_optimizer_node.cpp
)
add_dependencies(batch_optimizer_node
  ${catkin_EXPORTED_TARGETS}
)
target_link_libraries(batch_optimizer_node
  ${PROJECT_NAME}
  ${catkin_LIBRARIES}
)

#############
## Install ##
#############

install(
  TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION}
)

install(
  TARGETS batch_optimizer_node
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

#############
## Testing ##
#############

if(CATKIN_ENABLE_TESTING)
  find_package(roslint REQUIRED)
  find_package(rostest REQUIRED)

  # Lint tests
  set(ROSLINT_CPP_OPTS "--filter=-build/c++11,-runtime/references")
  roslint_cpp()
  roslint_add_test()
endif()
