cmake_minimum_required(VERSION 2.6)

PROJECT(uci C)

SET(CMAKE_INSTALL_PREFIX /)

ADD_DEFINITIONS(-Os -Wall -Werror --std=gnu99 -g3)

IF(APPLE)
	SET(CMAKE_SHARED_MODULE_CREATE_C_FLAGS "${CMAKE_SHARED_MODULE_CREATE_C_FLAGS} -undefined dynamic_lookup")
ENDIF(APPLE)

EXECUTE_PROCESS(
	COMMAND  lua -e "for k in string.gmatch(package.cpath .. \";\", \"([^;]+)/..so;\") do if k:sub(1,1) == \"/\" then print(k) break end end"
	OUTPUT_VARIABLE LUAPATH
	RESULT_VARIABLE LUA_CHECK_RES
	OUTPUT_STRIP_TRAILING_WHITESPACE
)

IF(NOT ${LUA_CHECK_RES} EQUAL 0 OR ${LUAPATH} EQUAL "")
	MESSAGE(SEND_ERROR "Lua was not found on your system")
ENDIF()

ADD_LIBRARY(uci_lua MODULE uci.c)
SET_TARGET_PROPERTIES(uci_lua PROPERTIES
	OUTPUT_NAME uci
	PREFIX ""
)
TARGET_LINK_LIBRARIES(uci_lua uci)

INSTALL(TARGETS uci_lua
	LIBRARY DESTINATION ${LUAPATH}
)