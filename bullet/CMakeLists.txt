cmake_minimum_required(VERSION 2.6)
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)
#include(FindPkgConfig)
rosbuild_init()

rosbuild_add_boost_directories()

MAKE_DIRECTORY(${PROJECT_SOURCE_DIR}/src/)

# Build Bullet
if($ENV{MAKE})
execute_process(COMMAND cmake -E chdir ${PROJECT_SOURCE_DIR} $ENV{MAKE} -f Makefile.bullet
                RESULT_VARIABLE _make_failed)
else($ENV{MAKE})
execute_process(COMMAND cmake -E chdir ${PROJECT_SOURCE_DIR} make -f Makefile.bullet
                RESULT_VARIABLE _make_failed)
endif($ENV{MAKE})
if(_make_failed)
  message(FATAL_ERROR "Build of Bullet failed")
endif(_make_failed)

### local includes
#include_directories(include)
link_directories(${PROJECT_SOURCE_DIR}/lib)
add_definitions(-DBT_USE_DOUBLE_PRECISION -DBT_EULER_DEFAULT_ZYX)

### bullet unit tests for items i've patched
rosbuild_add_gtest(bullet_unittest test/bullet_unittest.cpp)
target_link_libraries(bullet_unittest LinearMath)
