cmake_minimum_required(VERSION 3.5)

project(mqtt_test)

# generate version info


if(${CMAKE_BUILD_TYPE} STREQUAL Debug)
	message(STATUS "CMAKE_BUILD_TYPE is ${CMAKE_BUILD_TYPE}")
	add_definitions(-g -ggdb -gdwarf -funwind-tables -rdynamic -O0)
	add_definitions(-fno-stack-protector -fno-omit-frame-pointer)
elseif(${CMAKE_BUILD_TYPE} STREQUAL Release)
	message(STATUS "CMAKE_BUILD_TYPE is ${CMAKE_BUILD_TYPE}")
elseif(${CMAKE_BUILD_TYPE} STREQUAL RelWithDebInfo)
	message(STATUS "CMAKE_BUILD_TYPE is ${CMAKE_BUILD_TYPE}")
elseif(${CMAKE_BUILD_TYPE} STREQUAL MinSizeRel)
	message(STATUS "CMAKE_BUILD_TYPE is ${CMAKE_BUILD_TYPE}")
	add_definitions(-Os)
endif()

if(RKIPC_CROSS_COMPILE)
message(STATUS "CROSS COMPILE TOOL: ${RKIPC_CROSS_COMPILE}")
set (CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fdata-sections -ffunction-sections -Wl,-rpath-link,${PROJECT_SOURCE_DIR}/lib/${RKIPC_CROSS_COMPILE}")
set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fdata-sections -ffunction-sections -Wl,-rpath-link,${PROJECT_SOURCE_DIR}/lib/${RKIPC_CROSS_COMPILE}")
set (CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -Wl,--gc-sections -Wl,--as-needed")
endif()


cmake_minimum_required(VERSION 3.5)


aux_source_directory(./src SRCS)

add_definitions(-DISP_HW_V32)
#add_definitions(-g -ggdb)
# add_definitions(-Os)

# add_definitions(-fsanitize=address -g -ggdb -gdwarf -funwind-tables -rdynamic -O0)
# add_definitions(-fno-stack-protector -fno-omit-frame-pointer -fsanitize-recover=address)
# link_libraries(asan)

add_executable(${PROJECT_NAME} ${SRCS})
target_link_libraries(${PROJECT_NAME} pthread stdc++ paho-mqtt3c)

install(TARGETS ${PROJECT_NAME} RUNTIME DESTINATION bin)