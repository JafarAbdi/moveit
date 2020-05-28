install(DIRECTORY include/ DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})

# Unit tests
if(CATKIN_ENABLE_TESTING)
  find_package(moveit_resources REQUIRED)
  include_directories(${moveit_resources_INCLUDE_DIRS})

  catkin_add_gtest(test_robot_state test/robot_state_test.cpp)
  target_link_libraries(test_robot_state moveit_test_utils ${catkin_LIBRARIES} ${urdfdom_LIBRARIES} ${urdfdom_headers_LIBRARIES} ${MOVEIT_LIB_NAME})

  # As an executable, this benchmark is not run as a test by default
  add_executable(robot_state_benchmark test/robot_state_benchmark.cpp)
  target_link_libraries(robot_state_benchmark moveit_test_utils ${catkin_LIBRARIES} ${urdfdom_LIBRARIES} ${urdfdom_headers_LIBRARIES} ${MOVEIT_LIB_NAME} ${GTEST_LIBRARIES})

  catkin_add_gtest(test_cartesian_interpolator test/test_cartesian_interpolator.cpp)
  target_link_libraries(test_cartesian_interpolator moveit_test_utils ${catkin_LIBRARIES} ${urdfdom_LIBRARIES} ${urdfdom_headers_LIBRARIES} ${MOVEIT_LIB_NAME})

  catkin_add_gtest(test_robot_state_complex test/test_kinematic_complex.cpp)
  target_link_libraries(test_robot_state_complex moveit_test_utils ${catkin_LIBRARIES} ${urdfdom_LIBRARIES} ${urdfdom_headers_LIBRARIES} ${MOVEIT_LIB_NAME})

  catkin_add_gtest(test_aabb test/test_aabb.cpp)
  target_link_libraries(test_aabb moveit_test_utils ${catkin_LIBRARIES} ${urdfdom_LIBRARIES} ${urdfdom_headers_LIBRARIES} ${MOVEIT_LIB_NAME})
endif()
