cmake_minimum_required(VERSION 3.8)
project(arm_description)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
# uncomment the following section in order to fill in
# further dependencies manually.
# find_package(<dependency> REQUIRED)

install(DIRECTORY urdf DESTINATION share/${PROJECT_NAME})
install(DIRECTORY meshes DESTINATION share/${PROJECT_NAME})
install(DIRECTORY rviz DESTINATION share/${PROJECT_NAME})
install(DIRECTORY launch DESTINATION share/${PROJECT_NAME})

# Custom command to generate the .dsv file
set(GZ_SIM_RESOURCE_PATH_DSV "${CMAKE_CURRENT_BINARY_DIR}/share/${PROJECT_NAME}/environment/gz_sim_resource_path.dsv")
file(WRITE ${GZ_SIM_RESOURCE_PATH_DSV} "prepend-non-duplicate;GZ_SIM_RESOURCE_PATH;\$AMENT_CURRENT_PREFIX/share/champ_description\n")

install(
  FILES ${GZ_SIM_RESOURCE_PATH_DSV}
  DESTINATION share/${PROJECT_NAME}/environment
)

add_executable(arm_node src/arm_node.cpp)
target_include_directories(arm_node PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>)
target_compile_features(arm_node PUBLIC c_std_99 cxx_std_17)  # Require C99 and C++17

install(TARGETS arm_node
  DESTINATION lib/${PROJECT_NAME})

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()
