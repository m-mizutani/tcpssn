CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
PROJECT(tcpssn)

if(NOT CMAKE_BUILD_TYPE)
    SET(CMAKE_BUILD_TYPE "Release")
endif()

if(POLICY CMP0015)
    cmake_policy(SET CMP0015 NEW)
endif()
if(POLICY CMP0042)
    cmake_policy(SET CMP0042 NEW)
endif()

      

SET(CMAKE_CXX_FLAGS_RELEASE "-Wall -O2 -std=c++0x")
SET(CMAKE_CXX_FLAGS_DEBUG   "-Wall -O0 -std=c++0x -g")
SET(EXECUTABLE_OUTPUT_PATH ${PROJECT_BINARY_DIR}/bin)
SET(LIBRARY_OUTPUT_PATH    ${PROJECT_BINARY_DIR}/lib)

IF(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    INCLUDE_DIRECTORIES(/usr/local/include)
    LINK_DIRECTORIES(/usr/local/lib)
ENDIF()

INCLUDE_DIRECTORIES(${INC_DIR} ${FLUENT_INCLUDES} ./src ./external/swarm/src)
LINK_DIRECTORIES(${LIB_DIR} ${FLUENT_LIBRARIES} ./external/)
FILE(GLOB BASESRCS 
	  "src/*.cc" "src/*.h" 
	  "external/swarm/src/swarm/*.hpp"
	  "external/swarm/src/*.cc" "external/swarm/src/*.hpp"
	  "external/swarm/src/proto/*.cc"
	  "external/swarm/src/proto/utils/*.cc" "external/swarm/src/proto/utils/*.hpp"
	  )
FILE(GLOB TESTSRCS "test/*.cc")

SET(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")




# Module code
ADD_LIBRARY(tcpssn SHARED ${BASESRCS})
TARGET_LINK_LIBRARIES(tcpssn pcap ev)

# Test code
ADD_EXECUTABLE(tcpssn-test ${TESTSRCS})
TARGET_LINK_LIBRARIES(tcpssn-test tcpssn pthread)

# Application (CLI) code
ADD_EXECUTABLE(tcpssn-bin apps/cli.cc)
SET_TARGET_PROPERTIES(tcpssn-bin
  PROPERTIES OUTPUT_NAME tcpssn)
TARGET_LINK_LIBRARIES(tcpssn-bin tcpssn)

INSTALL(TARGETS tcpssn-bin RUNTIME DESTINATION bin)
INSTALL(TARGETS tcpssn LIBRARY DESTINATION lib)
INSTALL(FILES src/tcpssn.h DESTINATION include/)
