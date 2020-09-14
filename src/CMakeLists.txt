set(LIB_NAME modbus)

add_library(${LIB_NAME} "")

set (
	${LIB_NAME}_PUBLIC_HEADERS
	${CMAKE_CURRENT_LIST_DIR}/modbus.h
	${CMAKE_CURRENT_LIST_DIR}/modbus-rtu.h
	${CMAKE_CURRENT_LIST_DIR}/modbus-tcp.h
)

target_include_directories(
	${LIB_NAME}
	PRIVATE
	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
	${CMAKE_CURRENT_BINARY_DIR}
)

target_sources(
	${LIB_NAME}
	PRIVATE
	${CMAKE_CURRENT_LIST_DIR}/modbus.c
	${CMAKE_CURRENT_LIST_DIR}/modbus-data.c
	${CMAKE_CURRENT_LIST_DIR}/modbus-rtu.c
	${CMAKE_CURRENT_LIST_DIR}/modbus-tcp.c
)

# --  Generate *-version.h
set(LIBMODBUS_VERSION_MAJOR ${${CMAKE_PROJECT_NAME}_VERSION_MAJOR})
set(LIBMODBUS_VERSION_MINOR ${${CMAKE_PROJECT_NAME}_VERSION_MINOR})
set(LIBMODBUS_VERSION_MICRO ${${CMAKE_PROJECT_NAME}_VERSION_PATCH})
set(LIBMODBUS_VERSION       ${${CMAKE_PROJECT_NAME}_VERSION_SHORT})
configure_file(${CMAKE_CURRENT_LIST_DIR}/${LIB_NAME}-version.h.in "${CMAKE_CURRENT_BINARY_DIR}/${LIB_NAME}-version.h" @ONLY)

set_target_properties(${LIB_NAME} PROPERTIES VERSION    ${${CMAKE_PROJECT_NAME}_VERSION_SHORT})
set_target_properties(${LIB_NAME} PROPERTIES SOVERSION  ${${CMAKE_PROJECT_NAME}_VERSION_SHORT})
set_target_properties(${LIB_NAME} PROPERTIES PUBLIC_HEADER "${CMAKE_CURRENT_BINARY_DIR}/modbus-version.h;${${LIB_NAME}_PUBLIC_HEADERS}")

# --  Add the install targets
install (
	TARGETS ${LIB_NAME}
	EXPORT ${LIB_NAME}_targets
	LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR} COMPONENT shlib
	ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
	PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${LIB_NAME}
	INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${LIB_NAME}
)

# -- Add pkg config file
set(prefix      ${CMAKE_INSTALL_PREFIX})
set(exec_prefix ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_BINDIR})
set(libdir      ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR})
set(includedir  ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_INCLUDEDIR}/${LIB_NAME})

configure_file ("${PROJECT_SOURCE_DIR}/${CMAKE_PROJECT_NAME}.pc.in" "${PROJECT_BINARY_DIR}/${CMAKE_PROJECT_NAME}.pc" @ONLY)
install (FILES ${PROJECT_BINARY_DIR}/${CMAKE_PROJECT_NAME}.pc DESTINATION lib/pkgconfig)

# -- Write cmake package config file
include (CMakePackageConfigHelpers)
write_basic_package_version_file(
	"${CMAKE_CURRENT_BINARY_DIR}/${LIB_NAME}/${LIB_NAME}-config-version.cmake"
	VERSION ${${CMAKE_PROJECT_NAME}_VERSION_SHORT}
	COMPATIBILITY AnyNewerVersion
)

export(
	EXPORT ${LIB_NAME}_targets
	FILE "${CMAKE_CURRENT_BINARY_DIR}/${LIB_NAME}/${LIB_NAME}-targets.cmake"
)

set(ConfigPackageLocation lib/cmake/${LIB_NAME})

install(
	EXPORT ${LIB_NAME}_targets
	FILE ${LIB_NAME}-targets.cmake
	DESTINATION ${ConfigPackageLocation}
)

install(
	FILES ${PROJECT_SOURCE_DIR}/cmake/${LIB_NAME}-config.cmake "${CMAKE_CURRENT_BINARY_DIR}/${LIB_NAME}/${LIB_NAME}-config-version.cmake"
	DESTINATION ${ConfigPackageLocation}
	COMPONENT Devel
)
