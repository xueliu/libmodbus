configure_file(${CMAKE_CURRENT_LIST_DIR}/unit-test.h.in "${CMAKE_CURRENT_BINARY_DIR}/unit-test.h" @ONLY)

foreach(TEST bandwidth-client bandwidth-server-many-up bandwidth-server-one random-test-client random-test-server version unit-test-client unit-test-server)

add_executable(${TEST} "")

target_sources(
	${TEST}
	PRIVATE
	${CMAKE_CURRENT_LIST_DIR}/${TEST}.c
)

target_include_directories(
	${TEST}
	PRIVATE
	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
	${CMAKE_CURRENT_BINARY_DIR}
)

target_link_libraries(
	${TEST}
	PRIVATE
	modbus
)

install (
	TARGETS ${TEST}
	RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

endforeach()