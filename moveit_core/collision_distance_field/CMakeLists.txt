if(CATKIN_ENABLE_TESTING)
  find_package(moveit_resources REQUIRED)
  include_directories(${moveit_resources_INCLUDE_DIRS})

  catkin_add_gtest(test_collision_distance_field test/test_collision_distance_field.cpp)
  target_link_libraries(test_collision_distance_field  ${MOVEIT_LIB_NAME})
endif()

install(DIRECTORY include/ DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})
