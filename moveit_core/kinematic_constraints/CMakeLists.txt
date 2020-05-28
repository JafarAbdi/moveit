install(DIRECTORY include/ DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})

if(CATKIN_ENABLE_TESTING)
  find_package(moveit_resources REQUIRED)
  include_directories(${moveit_resources_INCLUDE_DIRS})

  catkin_add_gtest(test_constraints test/test_constraints.cpp)
  target_link_libraries(test_constraints moveit_test_utils ${MOVEIT_LIB_NAME})
endif()
