set(MOVEIT_LIB_NAME moveit_collision_detection)

# unit tests
if(CATKIN_ENABLE_TESTING)
  catkin_add_gtest(test_world test/test_world.cpp)
  target_link_libraries(test_world ${MOVEIT_LIB_NAME} ${catkin_LIBRARIES} ${urdfdom_LIBRARIES} ${urdfdom_headers_LIBRARIES} ${Boost_LIBRARIES})

  catkin_add_gtest(test_world_diff test/test_world_diff.cpp)
  target_link_libraries(test_world_diff ${MOVEIT_LIB_NAME} ${catkin_LIBRARIES} ${urdfdom_LIBRARIES} ${urdfdom_headers_LIBRARIES} ${Boost_LIBRARIES})

  catkin_add_gtest(test_all_valid test/test_all_valid.cpp)
  target_link_libraries(test_all_valid ${MOVEIT_LIB_NAME} ${catkin_LIBRARIES} ${urdfdom_LIBRARIES} ${urdfdom_headers_LIBRARIES} ${Boost_LIBRARIES})
endif()

install(DIRECTORY include/ DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})
