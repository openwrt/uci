cmake_minimum_required(VERSION 2.6)

PROJECT(uci C)

SET(CMAKE_INSTALL_PREFIX /usr)

ADD_DEFINITIONS(-Os -Wall -Werror --std=gnu99 -g3 -I. -DUCI_PREFIX="${CMAKE_INSTALL_PREFIX}")

OPTION(UCI_DEBUG "debugging support" OFF)
OPTION(UCI_DEBUG_TYPECAST "typecast debugging support" OFF)
OPTION(BUILD_LUA "build Lua binding" ON)

CONFIGURE_FILE( ${CMAKE_SOURCE_DIR}/uci_config.h.in ${CMAKE_SOURCE_DIR}/uci_config.h )

SET(LIB_SOURCES libuci.c file.c util.c delta.c parse.c)

ADD_LIBRARY(uci SHARED ${LIB_SOURCES})
SET_TARGET_PROPERTIES(uci PROPERTIES OUTPUT_NAME uci)
TARGET_LINK_LIBRARIES(uci dl)

ADD_EXECUTABLE(cli cli.c)
SET_TARGET_PROPERTIES(cli PROPERTIES OUTPUT_NAME uci)
TARGET_LINK_LIBRARIES(cli uci dl)

ADD_LIBRARY(ucimap STATIC ucimap.c)

ADD_EXECUTABLE(ucimap-example ucimap-example.c)
TARGET_LINK_LIBRARIES(ucimap-example uci ucimap dl)

ADD_SUBDIRECTORY(lua)

INSTALL(FILES uci.h uci_config.h ucimap.h
	DESTINATION include
)

INSTALL(TARGETS uci cli
	ARCHIVE DESTINATION lib
	LIBRARY DESTINATION lib
	RUNTIME DESTINATION bin
)

