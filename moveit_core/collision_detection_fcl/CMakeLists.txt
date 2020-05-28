install(DIRECTORY include/ DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})

install(FILES ../collision_detector_fcl_description.xml DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

if(CATKIN_ENABLE_TESTING)
  find_package(moveit_resources REQUIRED)
  include_directories(${moveit_resources_INCLUDE_DIRS})

  catkin_add_gtest(test_fcl_collision_detection test/test_fcl_collision_detection_pr2.cpp)
  target_link_libraries(test_fcl_collision_detection moveit_test_utils ${MOVEIT_LIB_NAME} ${Boost_LIBRARIES})

  catkin_add_gtest(test_fcl_collision_detection_panda test/test_fcl_collision_detection_panda.cpp)
  target_link_libraries(test_fcl_collision_detection_panda moveit_test_utils ${MOVEIT_LIB_NAME} ${Boost_LIBRARIES})
endif()
