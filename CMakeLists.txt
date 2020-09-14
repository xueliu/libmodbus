cmake_minimum_required(VERSION 3.10)

# -- Project name
project(libmodbus)

# -- Various includes
include (GNUInstallDirs)
include(CheckIncludeFile)
check_include_file(arpa/inet.h HAVE_ARPA_INET_H)
check_include_file(byteswap.h HAVE_BYTESWAP_H)
check_include_file(errno.h HAVE_ERRNO_H)
check_include_file(fcntl.h HAVE_FCNTL_H)
check_include_file(limits.h HAVE_LIMITS_H)
check_include_file(linux/serial.h HAVE_LINUX_SERIAL_H)
check_include_file(netdb.h HAVE_NETDB_H)
check_include_file(netinet/in.h HAVE_NETINET_IN_H)
check_include_file(netinet/tcp.h HAVE_NETINET_TCP_H)
check_include_file(sys/ioctl.h HAVE_SYS_IOCTL_H)
check_include_file(sys/params.h HAVE_SYS_PARAMS_H)
check_include_file(sys/socket.h HAVE_SYS_SOCKET_H)
check_include_file(sys/time.h HAVE_SYS_TIME_H)
check_include_file(sys/types.h HAVE_SYS_TYPES_H)
check_include_file(termios.h HAVE_TERMIOS_H)
check_include_file(time.h HAVE_TIME_H)
check_include_file(unistd.h HAVE_UNISTD_H)

include(CheckFunctionExists)
check_function_exists(accept4 HAVE_ACCEPT4)
check_function_exists(fork HAVE_FORK)
check_function_exists(getaddrinfo HAVE_GETADDRINFO)
check_function_exists(gettimeofday HAVE_GETTIMEOFDAY)
check_function_exists(inet_ntoa HAVE_INET_NTOA)
check_function_exists(malloc HAVE_MALLOC)
check_function_exists(memset HAVE_MEMSET)
check_function_exists(select HAVE_SELECT)
check_function_exists(socket HAVE_SOCKET)
check_function_exists(strerror HAVE_STRERROR)
check_function_exists(strlcpy HAVE_STRLCPY)

include(CheckSymbolExists)
check_symbol_exists(TIOCSRS485 sys/ioctl.h HAVE_TIOCRS485)
check_symbol_exists(TIOCM_RTS sys/ioctl.h HAVE_TIOCM_RTS)

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/${CMAKE_PROJECT_NAME}-config.h.in ${CMAKE_CURRENT_BINARY_DIR}/config.h)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules/")

# -- Set c 99 standard default
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)

# when building, don't use the install RPATH already
# (but later on when installing)
SET(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE)
if (NOT (${CMAKE_INSTALL_PREFIX} STREQUAL "/usr" ) )
	SET(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}")
endif()

# -- Options for shared lib
option(${CMAKE_PROJECT_NAME}_build_shared_libs "build as a shared library" ON)
set(BUILD_SHARED_LIBS ${${CMAKE_PROJECT_NAME}_build_shared_libs})
option(build_test_program "build test programs" ON)

# -- Enable git tag based versioning
include (GitTagVersioning)

# -- Include the library part
include (src/CMakeLists.txt)

if (build_test_program)
	# -- Include the test part
	include (tests/CMakeLists.txt)
endif()


