cmake_minimum_required(VERSION 3.4.1)

set (CMAKE_VERBOSE_MAKEFILE ON)
set (CMAKE_CXX_STANDARD 14)
set (CMAKE_CXX_FLAGS "-DFOLLY_NO_CONFIG=1 -DFOLLY_HAVE_CLOCK_GETTIME=1 -DFOLLY_HAVE_MEMRCHR=1 -DFOLLY_USE_LIBCPP=1 -DFOLLY_MOBILE=1 -DON_ANDROID")

set (PACKAGE_NAME "rnmultithreading")
set (BUILD_DIR ${CMAKE_SOURCE_DIR}/build)

# rnmultithreading shared

add_library(
        ${PACKAGE_NAME}
        SHARED
        src/main/cpp/cpp-adapter.cpp
        ../cpp/RNMultithreadingInstaller.cpp
        ../cpp/ThreadPool.cpp
)

# includes

file (GLOB LIBFBJNI_INCLUDE_DIR "${BUILD_DIR}/fbjni-*-headers.jar/")

target_include_directories(
        ${PACKAGE_NAME}
        PRIVATE
        ${LIBFBJNI_INCLUDE_DIR}
        ${BUILD_DIR}/third-party-ndk/boost
        ${BUILD_DIR}/third-party-ndk/double-conversion
        ${BUILD_DIR}/third-party-ndk/folly
        ${BUILD_DIR}/third-party-ndk/glog
        "${NODE_MODULES_DIR}/react-native/React"
        "${NODE_MODULES_DIR}/react-native/React/Base"
        "${NODE_MODULES_DIR}/react-native/ReactAndroid/src/main/jni"
        "${NODE_MODULES_DIR}/react-native/ReactAndroid/src/main/java/com/facebook/react/turbomodule/core/jni"
        "${NODE_MODULES_DIR}/react-native/ReactCommon"
        "${NODE_MODULES_DIR}/react-native/ReactCommon/callinvoker"
        "${NODE_MODULES_DIR}/react-native/ReactCommon/jsi"
        "${NODE_MODULES_DIR}/react-native/ReactCommon/jsi"
        "${NODE_MODULES_DIR}/react-native-reanimated/Common/cpp/headers/Tools"
        "${NODE_MODULES_DIR}/react-native-reanimated/Common/cpp/headers/SpecTools"
        "${NODE_MODULES_DIR}/react-native-reanimated/Common/cpp/headers/SharedItems"
        "${NODE_MODULES_DIR}/react-native-reanimated/Common/cpp/headers/Registries"
        "${NODE_MODULES_DIR}/react-native-reanimated/Common/cpp/hidden_headers"
        "../cpp"
)

# find libraries

file (GLOB LIBRN_DIR "${BUILD_DIR}/react-native-0*/jni/${ANDROID_ABI}")
file (GLOB LIBJSC_DIR "${BUILD_DIR}/android-jsc*.aar/jni/${ANDROID_ABI}")
file (GLOB LIBREANIMATED_DIR "${BUILD_DIR}/react-native-reanimated-*.aar/jni/${ANDROID_ABI}")


find_library(
        LOG_LIB
        log
)
find_library(
        FBJNI_LIB
        fbjni
        PATHS ${LIBRN_DIR}
        NO_CMAKE_FIND_ROOT_PATH
)
find_library(
        JSEXECUTOR_LIB
        jscexecutor
        PATHS ${LIBRN_DIR}
        NO_CMAKE_FIND_ROOT_PATH
)
find_library(
        JSC_LIB
        jsc
        PATHS ${LIBJSC_DIR}
        NO_CMAKE_FIND_ROOT_PATH
)
find_library(
        REANIMATED_LIB
        reanimated
        PATHS ${LIBREANIMATED_DIR}
        NO_CMAKE_FIND_ROOT_PATH
)

# linking

find_library(
        FBJNI_LIBRARY fbjni
        PATHS ${libfbjni_link_DIRS}
        NO_CMAKE_FIND_ROOT_PATH
)

# build shared lib

target_link_libraries(
        ${PACKAGE_NAME}
        ${LOG_LIB}
        ${REANIMATED_LIB}
        ${JSC_LIB}
        ${JSEXECUTOR_LIB}
        ${FBJNI_LIB}
        android
)