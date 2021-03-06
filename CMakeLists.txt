CMAKE_MINIMUM_REQUIRED(VERSION 2.6 FATAL_ERROR)
CMAKE_POLICY(VERSION 2.6)
PROJECT(caffegraph)

ADD_DEFINITIONS(-std=c++11)

SET(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake" "${CMAKE_MODULE_PATH}")

FIND_PACKAGE(Torch REQUIRED)
FIND_PACKAGE(Protobuf REQUIRED)

INCLUDE_DIRECTORIES("${Torch_INSTALL_INCLUDE}/TH" "${PROTOBUF_INCLUDE_DIRS}" "${CMAKE_CURRENT_BINARY_DIR}")

LINK_DIRECTORIES("${Torch_INSTALL_LIB}" ${CMAKE_CURRENT_BINARY_DIR})

PROTOBUF_GENERATE_CPP(PROTO_SRCS PROTO_HDRS caffe.proto)

SET(src caffegraph.cpp ${PROTO_SRCS} layers.cpp)

FILE(GLOB luasrc *.lua)

ADD_LIBRARY(caffegraph MODULE ${src})
TARGET_LINK_LIBRARIES(caffegraph TH ${PROTOBUF_LIBRARIES})

SET_TARGET_PROPERTIES(caffegraph PROPERTIES PREFIX "lib" IMPORT_PREFIX "lib")

INSTALL(TARGETS caffegraph
  RUNTIME DESTINATION "${Torch_INSTALL_LUA_CPATH_SUBDIR}"
  LIBRARY DESTINATION "${Torch_INSTALL_LUA_CPATH_SUBDIR}")

INSTALL(FILES ${luasrc} DESTINATION "${Torch_INSTALL_LUA_PATH_SUBDIR}/caffegraph")
