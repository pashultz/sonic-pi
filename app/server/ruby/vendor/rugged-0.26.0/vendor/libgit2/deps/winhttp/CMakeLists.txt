FIND_PROGRAM(DLLTOOL dlltool CMAKE_FIND_ROOT_PATH_BOTH)
IF (NOT DLLTOOL)
	MESSAGE(FATAL_ERROR "Could not find dlltool command")
ENDIF ()

SET(LIBWINHTTP_PATH "${CMAKE_BINARY_DIR}/deps/winhttp")
SET(LIBWINHTTP_PATH ${LIBWINHTTP_PATH} PARENT_SCOPE)
FILE(MAKE_DIRECTORY ${LIBWINHTTP_PATH})

IF (CMAKE_SIZEOF_VOID_P EQUAL 8)
	set(WINHTTP_DEF "winhttp64.def")
ELSE()
	set(WINHTTP_DEF "winhttp.def")
ENDIF()

ADD_CUSTOM_COMMAND(
	OUTPUT ${LIBWINHTTP_PATH}/libwinhttp.a
	COMMAND ${DLLTOOL} -d ${WINHTTP_DEF} -k -D winhttp.dll -l libwinhttp.a
	DEPENDS ${WINHTTP_DEF}
	WORKING_DIRECTORY ${LIBWINHTTP_PATH}
)

SET_SOURCE_FILES_PROPERTIES(
	${CMAKE_CURRENT_SOURCE_DIR}/src/transports/winhttp.c
	PROPERTIES OBJECT_DEPENDS ${LIBWINHTTP_PATH}/libwinhttp.a
)
