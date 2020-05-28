install(DIRECTORY include/ DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})

if(CATKIN_ENABLE_TESTING)
  find_package(orocos_kdl REQUIRED)
  find_package(angles REQUIRED)
  find_package(tf2_kdl REQUIRED)
  find_package(moveit_resources REQUIRED)

  include_directories(${orocos_kdl_INCLUDE_DIRS} ${angles_INCLUDE_DIRS} ${tf2_kdl_INCLUDE_DIRS} ${moveit_resources_INCLUDE_DIRS})

  catkin_add_gtest(test_constraint_samplers
    test/test_constraint_samplers.cpp
    test/pr2_arm_kinematics_plugin.cpp
    test/pr2_arm_ik.cpp
  )

  target_link_libraries(test_constraint_samplers
    moveit_test_utils
    ${MOVEIT_LIB_NAME}
    ${catkin_LIBRARIES}
    ${angles_LIBRARIES}
    ${orocos_kdl_LIBRARIES}
    ${urdfdom_LIBRARIES}
    ${urdfdom_headers_LIBRARIES}
  )
endif()
