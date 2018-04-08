cmake_minimum_required(VERSION 2.8.3)
project(tensorflow_catkin)

find_package(catkin_simple REQUIRED)

catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

SET(TENSORFLOW_SRC_PATH "tensorflow_src")
SET(TENSORFLOW_CMAKE_PATH "tensorflow/contrib/cmake")
ExternalProject_Add(tensorflow_src
  DEPENDS ${CONTRIB_NAME}
  GIT_REPOSITORY https://github.com/tensorflow/tensorflow.git
  GIT_TAG b874783ccdf4cc36cb3546e6b6a998cb8f3470bb
  UPDATE_COMMAND ""
  SOURCE_DIR ${TENSORFLOW_SRC_PATH}
  SOURCE_SUBDIR ${TENSORFLOW_CMAKE_PATH}
  BINARY_DIR tensorflow_build
  CMAKE_ARGS
    -DCMAKE_BUILD_TYPE=Release
    -Dtensorflow_BUILD_SHARED_LIB=ON
    -Dtensorflow_BUILD_ALL_KERNELS=ON
    -Dtensorflow_BUILD_CONTRIB_KERNELS=ON
    -Dtensorflow_ENABLE_GRPC_SUPPORT=ON  # Needed by tf_protos_cc...
    -Dtensorflow_ENABLE_SSL_SUPPORT=OFF
    -Dtensorflow_BUILD_CC_TESTS=OFF
    -Dtensorflow_BUILD_CC_EXAMPLE=OFF
    -Dtensorflow_BUILD_PYTHON_BINDINGS=ON  # Needed by tf_c_python_api...
    -DPYTHON_LIBRARY=${PYTHON_LIBRARY}  # likewise
    -Dtensorflow_BUILD_PYTHON_TESTS=OFF
    -Dtensorflow_ENABLE_GPU=OFF
    -DCMAKE_INSTALL_PREFIX=${CATKIN_DEVEL_PREFIX} .
  BUILD_COMMAND make -j8
  INSTALL_COMMAND make install -j8
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} tensorflow_src)
target_link_libraries(${PROJECT_NAME}
  ${CATKIN_DEVEL_PREFIX}/lib/libtensorflow${CMAKE_SHARED_LIBRARY_SUFFIX})

cs_install()

cs_export(
  INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
CFG_EXTRAS tensorflow-extras.cmake)
