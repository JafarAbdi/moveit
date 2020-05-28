install(DIRECTORY include/ DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})

if(CATKIN_ENABLE_TESTING)
  catkin_add_gtest(test_voxel_grid test/test_voxel_grid.cpp)
  target_link_libraries(test_voxel_grid ${MOVEIT_LIB_NAME})

  catkin_add_gtest(test_distance_field test/test_distance_field.cpp)
  target_link_libraries(test_distance_field ${MOVEIT_LIB_NAME})
endif()
