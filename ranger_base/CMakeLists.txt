cmake_minimum_required(VERSION 3.5)
project(ranger_base)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(controller_manager REQUIRED)
# find_package(topic_tools REQUIRED)
find_package(ranger_msgs REQUIRED)
find_package(ugv_sdk REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)

include_directories(
  include
  ${EIGEN3_INCLUDE_DIR}
)

set(dependencies
  geometry_msgs
  nav_msgs
  rclcpp
  std_msgs
  tf2
  tf2_ros
  ranger_msgs
  sensor_msgs
  tf2_geometry_msgs
)

add_library(ranger_messenger SHARED
  src/ranger_messenger.cpp
)

target_include_directories(ranger_messenger PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

# target_link_libraries(ranger_messenger ${dependencies})
ament_target_dependencies(ranger_messenger rclcpp tf2 tf2_ros std_msgs nav_msgs sensor_msgs ranger_msgs ugv_sdk ${dependencies})

add_executable(ranger_base_node 
    src/ranger_base_node.cpp
    # src/ranger_messenger.cpp
)

target_link_libraries(ranger_base_node ranger_messenger)
ament_target_dependencies(ranger_base_node rclcpp tf2 tf2_ros std_msgs nav_msgs sensor_msgs ranger_msgs ${dependencies})

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include/${PROJECT_NAME}
)

# install(
#   TARGETS ${PROJECT_NAME}
#   EXPORT ${PROJECT_NAME}
#   LIBRARY DESTINATION lib
#   ARCHIVE DESTINATION lib
#   RUNTIME DESTINATION bin
#   INCLUDES DESTINATION include
# )

install(
  TARGETS ranger_messenger
  EXPORT ranger_messenger
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
  INCLUDES DESTINATION include
)

install(TARGETS ranger_base_node
  DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY launch
  DESTINATION share/${PROJECT_NAME}/)

# install(DIRECTORY ascent
#   DESTINATION share/${PROJECT_NAME})

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # uncomment the line when a copyright and license is not present in all source files
  #set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # uncomment the line when this package is not in a git repo
  #set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()


# ament_export_targets(ranger_messenger HAS_LIBRARY_TARGET)
ament_export_dependencies(${dependencies})
ament_export_include_directories(include)
ament_export_libraries(ranger_messenger)
# ament_export_targets(${PROJECT_NAME} ranger_messenger)

ament_package()

# include_directories(
#   include
#   ${catkin_INCLUDE_DIRS}
#   ${EIGEN3_INCLUDE_DIR}
# )

# add_library(ascent INTERFACE)
# target_include_directories(ascent INTERFACE
# 	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
# 	$<INSTALL_INTERFACE:include>)

