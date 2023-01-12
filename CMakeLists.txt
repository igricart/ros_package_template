cmake_minimum_required(VERSION 3.10.2)
project(package_template)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Find catkin macros and libraries
set(PKG_DEPS
  std_msgs
)

find_package(catkin REQUIRED COMPONENTS ${PKG_DEPS})
find_package(Eigen3 3.3 REQUIRED NO_MODULE)
find_package(Boost REQUIRED)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS ${PKG_DEPS}
  DEPENDS Boost PCL
)

# ############
# #  Build  ##
# ############
include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
)

add_library(code_template
  src/code_template.cpp
)

add_dependencies(code_template
  ${code_template_EXPORTED_TARGETS}
  ${catkin_EXPORTED_TARGETS}
)
target_link_libraries(code_template
  ${catkin_LIBRARIES}
  Eigen3::Eigen
  ${Boost_LIBRARIES}
)

# adding nodes
add_executable(code_template_node src/code_template_node.cpp)
target_link_libraries(code_template_node
  ${PROJECT_NAME}
)

# ############
# # Install ##
# ############
install(
  TARGETS ${PROJECT_NAME}
  code_template
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(
  DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

install(DIRECTORY launch
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

# ############
# # Testing ##
# ############
if(CATKIN_ENABLE_TESTING)
  find_package(rostest REQUIRED)

  # Emergency docking
  add_rostest_gtest(code_template_test
    test/code_template_test.test
    test/src/code_template_test.cpp
  )
  target_link_libraries(code_template_test
    ${PROJECT_NAME}
  )
endif()
