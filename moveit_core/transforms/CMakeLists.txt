install(DIRECTORY include/ DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})

# Unit tests
if(CATKIN_ENABLE_TESTING)
  catkin_add_gtest(test_transforms test/test_transforms.cpp)
  target_link_libraries(test_transforms ${MOVEIT_LIB_NAME})
endif()
