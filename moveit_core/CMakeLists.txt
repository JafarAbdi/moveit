cmake_minimum_required(VERSION 3.1.3)
project(moveit_core)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

if(NOT CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
  # Enable warnings
  add_compile_options(-Wall -Wextra
    -Wwrite-strings -Wunreachable-code -Wpointer-arith -Wredundant-decls -Wcast-qual
    -Wno-unused-parameter -Wno-unused-function)
endif()
if(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
  # This too often has false-positives
  add_compile_options(-Wno-maybe-uninitialized)
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
endif()

if(NOT CMAKE_CONFIGURATION_TYPES AND NOT CMAKE_BUILD_TYPE)
  message("${PROJECT_NAME}: You did not request a specific build type: Choosing 'Release' for maximum performance")
  set(CMAKE_BUILD_TYPE Release)
endif()

# boost::iostreams on Windows depends on boost::zlib
if(WIN32)
  set(EXTRA_BOOST_COMPONENTS zlib)
endif()
find_package(Boost REQUIRED system filesystem date_time thread iostreams ${EXTRA_BOOST_COMPONENTS})
find_package(Eigen3 REQUIRED)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/CMakeModules")

# TODO: Move collision detection into separate packages
find_package(Bullet 2.87)

# TODO(j-petit): Version check can be dropped when Xenial reaches end-of-life
if(BULLET_FOUND)
  set(BULLET_ENABLE "BULLET")
  set(BULLET_LIB "moveit_collision_detection_bullet")
  set(BULLET_INC "collision_detection_bullet/include")
  message(STATUS "Compiling with Bullet")
else()
  message(STATUS "Version of Bullet too old or not available: disabling Bullet collision detection plugin. Try using Ubuntu 18.04 or later.")
endif()

find_package(PkgConfig REQUIRED)
pkg_check_modules(LIBFCL_PC REQUIRED fcl)
# find *absolute* paths to LIBFCL_INCLUDE_DIRS and LIBFCL_LIBRARIES
find_path(LIBFCL_INCLUDE_DIRS fcl/config.h HINTS ${LIBFCL_PC_INCLUDE_DIR} ${LIBFCL_PC_INCLUDE_DIRS})
find_library(LIBFCL_LIBRARIES fcl HINTS ${LIBFCL_PC_LIBRARY_DIRS})


find_package(octomap REQUIRED)
find_package(urdfdom REQUIRED)
find_package(urdfdom_headers REQUIRED)

find_package(catkin REQUIRED
COMPONENTS
  tf2_eigen
  tf2_geometry_msgs
  eigen_stl_containers
  eigen_conversions
  geometric_shapes
  geometry_msgs
  kdl_parser
  moveit_msgs
  octomap_msgs
  random_numbers
  roslib
  rostime
  rosconsole
  sensor_msgs
  shape_msgs
  srdfdom
  std_msgs
  trajectory_msgs
  urdf
  visualization_msgs
  xmlrpcpp
)

set(VERSION_FILE_PATH "${CATKIN_DEVEL_PREFIX}/include")
# Pass the folder of the generated version.h to catkin_package() for export in devel-space
# This is how gencpp adds the folder of generated message code to the include dirs, see:
#   https://github.com/ros/gencpp/blob/e5acaf6/cmake/gencpp-extras.cmake.em#L51-L54
list(APPEND ${PROJECT_NAME}_INCLUDE_DIRS ${VERSION_FILE_PATH})
file(MAKE_DIRECTORY "${VERSION_FILE_PATH}/moveit")

set(THIS_PACKAGE_INCLUDE_DIRS
    background_processing/include
    exceptions/include
    backtrace/include
    collision_detection/include
    collision_detection_fcl/include
    ${BULLET_INC}
    constraint_samplers/include
    controller_manager/include
    distance_field/include
    collision_distance_field/include
    dynamics_solver/include
    kinematics_base/include
    kinematics_metrics/include
    robot_model/include
    transforms/include
    robot_state/include
    robot_trajectory/include
    kinematic_constraints/include
    macros/include
    planning_interface/include
    planning_request_adapter/include
    planning_scene/include
    profiler/include
    sensor_manager/include
    trajectory_processing/include
    utils/include
)

catkin_package(
  INCLUDE_DIRS
    ${THIS_PACKAGE_INCLUDE_DIRS}
  LIBRARIES
    moveit_exceptions
    moveit_background_processing
    moveit_kinematics_base
    moveit_robot_model
    moveit_transforms
    moveit_robot_state
    moveit_robot_trajectory
    moveit_planning_interface
    moveit_collision_detection
    moveit_collision_detection_fcl
    ${BULLET_LIB}
    moveit_kinematic_constraints
    moveit_planning_scene
    moveit_constraint_samplers
    moveit_planning_request_adapter
    moveit_profiler
    moveit_trajectory_processing
    moveit_distance_field
    moveit_collision_distance_field
    moveit_kinematics_metrics
    moveit_dynamics_solver
    moveit_utils
    moveit_test_utils
    ${OCTOMAP_LIBRARIES}
  CATKIN_DEPENDS
    eigen_stl_containers
    geometric_shapes
    geometry_msgs
    kdl_parser
    moveit_msgs
    octomap_msgs
    random_numbers
    sensor_msgs
    shape_msgs
    srdfdom
    std_msgs
    tf2_eigen
    tf2_geometry_msgs
    trajectory_msgs
    visualization_msgs
  DEPENDS
    Boost
    EIGEN3
    LIBFCL
    OCTOMAP
    urdfdom
    urdfdom_headers
    ${BULLET_ENABLE}
    )

include_directories(SYSTEM ${EIGEN3_INCLUDE_DIRS}
                           ${LIBFCL_INCLUDE_DIRS}
                           ${BULLET_INCLUDE_DIRS}
                           )

#catkin_lint: ignore_once external_directory  (${VERSION_FILE_PATH})
include_directories(${THIS_PACKAGE_INCLUDE_DIRS}
                    ${VERSION_FILE_PATH}
                    ${Boost_INCLUDE_DIRS}
                    ${catkin_INCLUDE_DIRS}
                    ${urdfdom_INCLUDE_DIRS}
                    ${urdfdom_headers_INCLUDE_DIRS}
                    ${OCTOMAP_INCLUDE_DIRS}
                    )

# Generate and install version.h
string(REGEX REPLACE "^([0-9]+)\\..*" "\\1" MOVEIT_VERSION_MAJOR "${${PROJECT_NAME}_VERSION}")
string(REGEX REPLACE "^[0-9]+\\.([0-9]+).*" "\\1" MOVEIT_VERSION_MINOR "${${PROJECT_NAME}_VERSION}")
string(REGEX REPLACE "^[0-9]+\\.[0-9]+\\.([0-9]+).*" "\\1" MOVEIT_VERSION_PATCH "${${PROJECT_NAME}_VERSION}")
set(MOVEIT_VERSION_EXTRA "Alpha")
set(MOVEIT_VERSION "${MOVEIT_VERSION_MAJOR}.${MOVEIT_VERSION_MINOR}.${MOVEIT_VERSION_PATCH}-${MOVEIT_VERSION_EXTRA}")
message(STATUS " *** Building MoveIt ${MOVEIT_VERSION} ***")
configure_file("version/version.h.in" "${VERSION_FILE_PATH}/moveit/version.h")
#catkin_lint: ignore_once external_file
install(FILES "${VERSION_FILE_PATH}/moveit/version.h" DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION}/moveit)

set(MOVEIT_LIB_NAME moveit_core)

add_library(${MOVEIT_LIB_NAME}
    exceptions/src/exceptions.cpp
    profiler/src/profiler.cpp
    background_processing/src/background_processing.cpp
    utils/src/lexical_casts.cpp
    utils/src/xmlrpc_casts.cpp
    utils/src/message_checks.cpp
    kinematics_base/src/kinematics_base.cpp
    robot_model/src/aabb.cpp
    robot_model/src/fixed_joint_model.cpp
    robot_model/src/floating_joint_model.cpp
    robot_model/src/joint_model.cpp
    robot_model/src/joint_model_group.cpp
    robot_model/src/link_model.cpp
    robot_model/src/planar_joint_model.cpp
    robot_model/src/prismatic_joint_model.cpp
    robot_model/src/revolute_joint_model.cpp
    robot_model/src/robot_model.cpp
    transforms/src/transforms.cpp
    robot_state/src/attached_body.cpp
    robot_state/src/conversions.cpp
    robot_state/src/robot_state.cpp
    robot_state/src/cartesian_interpolator.cpp
    robot_trajectory/src/robot_trajectory.cpp
    collision_detection/src/allvalid/collision_env_allvalid.cpp
    collision_detection/src/collision_matrix.cpp
    collision_detection/src/collision_octomap_filter.cpp
    collision_detection/src/collision_tools.cpp
    collision_detection/src/world.cpp
    collision_detection/src/world_diff.cpp
    collision_detection/src/collision_env.cpp
    collision_detection_fcl/src/collision_common.cpp
    collision_detection_fcl/src/collision_env_fcl.cpp
    collision_detection_fcl/src/collision_detector_fcl_plugin_loader.cpp
    kinematic_constraints/src/kinematic_constraint.cpp
    kinematic_constraints/src/utils.cpp
    planning_scene/src/planning_scene.cpp
    constraint_samplers/src/constraint_sampler.cpp
    constraint_samplers/src/constraint_sampler_manager.cpp
    constraint_samplers/src/constraint_sampler_tools.cpp
    constraint_samplers/src/default_constraint_samplers.cpp
    constraint_samplers/src/union_constraint_sampler.cpp
    planning_interface/src/planning_interface.cpp
    planning_interface/src/planning_response.cpp
    planning_request_adapter/src/planning_request_adapter.cpp
    trajectory_processing/src/iterative_time_parameterization.cpp
    trajectory_processing/src/iterative_spline_parameterization.cpp
    trajectory_processing/src/trajectory_tools.cpp
    trajectory_processing/src/time_optimal_trajectory_generation.cpp
    distance_field/src/distance_field.cpp
    distance_field/src/find_internal_points.cpp
    distance_field/src/propagation_distance_field.cpp
    collision_distance_field/src/collision_distance_field_types.cpp
    collision_distance_field/src/collision_common_distance_field.cpp
    collision_distance_field/src/collision_env_distance_field.cpp
    collision_distance_field/src/collision_env_hybrid.cpp
    kinematics_metrics/src/kinematics_metrics.cpp
    dynamics_solver/src/dynamics_solver.cpp
)

set_target_properties(${MOVEIT_LIB_NAME} PROPERTIES VERSION "${${PROJECT_NAME}_VERSION}")

target_link_libraries(${MOVEIT_LIB_NAME} ${catkin_LIBRARIES} ${urdfdom_LIBRARIES} ${urdfdom_headers_LIBRARIES} ${Boost_LIBRARIES})

install(TARGETS ${MOVEIT_LIB_NAME}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION})

add_subdirectory(version)
add_subdirectory(macros)
add_subdirectory(backtrace)
add_subdirectory(exceptions)
add_subdirectory(profiler)
add_subdirectory(utils)
add_subdirectory(background_processing)
add_subdirectory(kinematics_base)
add_subdirectory(controller_manager)
add_subdirectory(sensor_manager)
add_subdirectory(robot_model)
add_subdirectory(transforms)
add_subdirectory(robot_state)
add_subdirectory(robot_trajectory)
add_subdirectory(collision_detection)
add_subdirectory(collision_detection_fcl)
add_subdirectory(kinematic_constraints)
add_subdirectory(planning_scene)
add_subdirectory(constraint_samplers)
add_subdirectory(planning_interface)
add_subdirectory(planning_request_adapter)
add_subdirectory(trajectory_processing)
add_subdirectory(distance_field)
add_subdirectory(collision_distance_field)
add_subdirectory(kinematics_metrics)
add_subdirectory(dynamics_solver)

if(BULLET_ENABLE)
  add_subdirectory(collision_detection_bullet)
else()
  install(FILES collision_detection_bullet/empty_description.xml DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION} RENAME collision_detector_bullet_description.xml)
endif()
