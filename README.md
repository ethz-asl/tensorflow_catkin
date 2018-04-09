tensorflow_catkin
==============

Catkin package wrapper for Tensorflow 1.7 C++

- Uses the CMake build and does __not__ require Bazel
- Builds the latest master branch as of 09/04/2018: commit [b8747830(https://github.com/tensorflow/tensorflow/tree/b874783ccdf4cc36cb3546e6b6a998cb8f3470bb)]
- Supports GPU using the `-DUSE_GPU` flag
  - Assumes that the root directories of CUDA, CUDNN and NCCL are provided either as environment variables `CUDA_ROOT`, `CUDNN_ROOT` and `NCCL_ROOT`, either as CMake flags `-DCUDA_ROOT`, `-DCUDNN_ROOT`, `-DNCCL_ROOT`
  - Supports linking against the run-time version of `libcuda` in `$CUDA_ROOT/lib64/stubs/`
- Currently only tested on CentOS 7.4

## Usage

#### package.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<package format="2">
  <name>test_tensorflow</name>
  <version>0.0.0</version>
  <description>test_tensorflow</description>
  <maintainer email="some@example.com">Author</maintainer>
  <license>Apache 2.0</license>

  <buildtool_depend>catkin_simple</buildtool_depend>
  <buildtool_depend>catkin</buildtool_depend>
  <depend>tensorflow_catkin</depend>
</package>
```

### CMakeLists.txt
```CMake
cmake_minimum_required(VERSION 2.8)
project(test_tensorflow)

find_package(catkin_simple REQUIRED)
catkin_simple()

cs_add_executable(example example.cpp)

cs_install()
cs_export()
```

### example.cpp
```C++
#include <tensorflow/core/public/session.h>
#include <tensorflow/core/platform/env.h>
#include <iostream>
using namespace std;
using namespace tensorflow;

int main()
{
    Session* session;
    Status status = NewSession(SessionOptions(), &session);
    if (!status.ok()) {
        cout << status.ToString() << endl;
        return 1;
    }
    cout << "Session successfully created." << endl;
}
```

