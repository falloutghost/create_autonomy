cmake_minimum_required(VERSION 2.8.3)
project(ca_driver)

find_package(catkin REQUIRED COMPONENTS
  ca_msgs
  diagnostic_msgs
  diagnostic_updater
  geometry_msgs
  nav_msgs
  roscpp
  roslint
  sensor_msgs
  std_msgs
  tf
)

catkin_package(
  INCLUDE_DIRS include
  CATKIN_DEPENDS
    ca_msgs
    diagnostic_msgs
    diagnostic_updater
    geometry_msgs
    nav_msgs
    roscpp
    sensor_msgs
    std_msgs
    tf
  DEPENDS libcreate
)

include(ExternalProject)
ExternalProject_Add(libcreate
  GIT_REPOSITORY https://github.com/falloutghost/libcreate_patched
  GIT_TAG 2.0.1
  PREFIX ${CATKIN_DEVEL_PREFIX}
  CONFIGURE_COMMAND cmake .
  BUILD_COMMAND make
  INSTALL_COMMAND echo "No install"
  BUILD_IN_SOURCE 1
)

set(libcreate_PATH "${CATKIN_DEVEL_PREFIX}/src/libcreate/")

roslint_cpp()
roslint_add_test()

include_directories(
  include
  ${libcreate_PATH}/include
  ${catkin_INCLUDE_DIRS}
)

link_directories(${libcreate_PATH})

add_executable(${PROJECT_NAME} src/create_driver.cpp)

target_link_libraries(${PROJECT_NAME}
  ${libcreate_LIBRARIES}
  ${catkin_LIBRARIES}
)

add_dependencies(${PROJECT_NAME} ${catkin_EXPORTED_TARGETS})

install(TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY include
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
  PATTERN ".svn" EXCLUDE
)

install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
  PATTERN ".svn" EXCLUDE
)

install(DIRECTORY config/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/config
  PATTERN ".svn" EXCLUDE
)
