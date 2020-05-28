install(DIRECTORY include/ DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})

if(CATKIN_ENABLE_TESTING)
  find_package(moveit_resources REQUIRED)
  include_directories(${moveit_resources_INCLUDE_DIRS})

  catkin_add_gtest(test_planning_scene test/test_planning_scene.cpp)
  target_link_libraries(test_planning_scene ${MOVEIT_LIB_NAME})

  catkin_add_gtest(test_multi_threaded test/test_multi_threaded.cpp)
  target_link_libraries(test_multi_threaded moveit_planning_scene moveit_test_utils ${catkin_LIBRARIES} ${urdfdom_LIBRARIES} ${urdfdom_headers_LIBRARIES})
endif()
