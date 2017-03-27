# CMake版本信息
cmake_minimum_required(VERSION 3.4.1)

# 支持-std=gnu++11
set(CMAKE_VERBOSE_MAKEFILE on)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11")

# 工程路径
set(pathToProject D:/android_studio_workspace/HelloARNative)

# 配置加载native依赖
include_directories(${pathToProject}/app/src/main/cpp/include)

# 添加待编译的文件
add_library(helloar SHARED ${pathToProject}/app/src/main/cpp/ar.hpp
                            ${pathToProject}/app/src/main/cpp/renderer.hpp
                            ${pathToProject}/app/src/main/cpp/ar.cc
                            ${pathToProject}/app/src/main/cpp/renderer.cc
                            ${pathToProject}/app/src/main/cpp/helloar.cc)

# 动态方式加载
add_library(lib_easyar SHARED IMPORTED)
# 引入so文件
set_target_properties(lib_easyar PROPERTIES IMPORTED_LOCATION ${pathToProject}/app/src/main/jniLibs/${ANDROID_ABI}/libEasyAR.so)

target_link_libraries(helloar
                        lib_easyar
                        GLESv2
                        log)