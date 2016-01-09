set(libusb_SRC
	core.c
	descriptor.c
	hotplug.c
	io.c
	strerror.c
	sync.c
	hotplug.h
	libusb.h
	libusbi.h
	version.h
	version_nano.h
)

set(libusb_SOURCES
	${libusb_SRC}
)

include_directories(./)

if(WIN32)
	list(APPEND libusb_SOURCES
		os/poll_windows.c
		os/threads_windows.c
		os/windows_usb.c
		os/poll_windows.h
		os/threads_windows.h
		os/windows_common.h
		os/windows_usb.h
	)
	include_directories(../msvc)
elseif(UNIX)
	list(APPEND libusb_SOURCES
		os/linux_netlink.c
		os/linux_udev.c
		os/linux_usbfs.c
		os/poll_posix.c
		os/threads_posix.c
		os/linux_usbfs.h
		os/poll_posix.h
		os/threads_posix.h
	)
endif()

add_library(libusb SHARED
	${libusb_SOURCES}
)

if(WIN32)
	configure_file(	${CMAKE_CURRENT_SOURCE_DIR}/libusb-1.0.def ${CMAKE_CURRENT_BINARY_DIR}/libusb/libusb-1.0.def @ONLY)
	if(MSVC)
		set_target_properties(libusb PROPERTIES LINK_FLAGS "/DEF:libusb/libusb-1.0.def")
	endif()
endif()

set_target_properties(libusb
    PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${CMAKE_SYSTEM_PROCESSOR}/lib"
    LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${CMAKE_SYSTEM_PROCESSOR}/lib"
    RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/${CMAKE_SYSTEM_PROCESSOR}/bin"
)

target_link_libraries (libusb
	${SDL2_LIBRARIES}
)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY build)
set_target_properties(libusb PROPERTIES
	PREFIX "lib"
	OUTPUT_NAME "usb-1.0"
)
