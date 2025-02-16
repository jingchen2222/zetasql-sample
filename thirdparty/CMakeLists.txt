cmake_minimum_required(VERSION 3.10)
project(zetasql-sample-deps)

include(ExternalProject)

set(DEPS_PREFIX ${CMAKE_SOURCE_DIR}/usr)
set(DEPS_BUILD_DIR
    "${CMAKE_CURRENT_SOURCE_DIR}/deps"
    CACHE PATH "Dependencies build directory.")
set(DEPS_DOWNLOAD_DIR
    "${DEPS_BUILD_DIR}/downloads"
    CACHE PATH "Dependencies download directory.")

find_program(MAKE_EXE NAMES gmake nmake make)
ExternalProject_Add(
  protobuf
  URL https://github.com/protocolbuffers/protobuf/archive/v3.6.1.3.tar.gz
  URL_HASH
    SHA256=73fdad358857e120fd0fa19e071a96e15c0f23bb25f85d3f7009abfd4f264a2a
  PREFIX ${DEPS_BUILD_DIR}
  DOWNLOAD_DIR ${DEPS_DOWNLOAD_DIR}/protobuf
  INSTALL_DIR ${DEPS_PREFIX}
  BUILD_IN_SOURCE True
  CONFIGURE_COMMAND ./autogen.sh && ./configure --prefix ${DEPS_PREFIX}
                    CXXFLAGS=-std=c++11
  BUILD_COMMAND ${MAKE_EXE} -j$(nproc)
  INSTALL_COMMAND ${MAKE_EXE} install)

ExternalProject_Add(
  googletest
  URL https://github.com/google/googletest/archive//6f5fd0d7199b9a19faa9f499ecc266e6ae0329e7.tar.gz
  URL_HASH
    SHA256=51e6c4b4449aab8f31e69d0ff89565f49a1f3628a42e24f214e8b02b3526e3bc
  PREFIX ${DEPS_BUILD_DIR}
  DOWNLOAD_DIR ${DEPS_DOWNLOAD_DIR}/googletest
  INSTALL_DIR ${DEPS_PREFIX}
  CONFIGURE_COMMAND
  ${CMAKE_COMMAND} -H${DEPS_BUILD_DIR}/src/googletest -B. -DCMAKE_INSTALL_PREFIX=${DEPS_PREFIX}
    -DCMAKE_INSTALL_LIBDIR=lib -DBUILD_SHARED_LIBS=OFF -Dgtest_build_tests=OFF
    -DCMAKE_CXX_FLAGS=-std=c++11
  BUILD_COMMAND ${CMAKE_COMMAND} --build .
  INSTALL_COMMAND ${CMAKE_COMMAND} --build . --target install)
