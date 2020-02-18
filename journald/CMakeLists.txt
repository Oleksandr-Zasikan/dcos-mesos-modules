



set(JOURNALD_SRC 
	journald.hpp 
	lib_journald.hpp	
	lib_journald.cpp)

set(JOURNALD_INCL 
		${MESOS_ROOT}/include 
		${MESOS_ROOT}/3rdparty/libprocess/include 
		${MESOS_ROOT}/3rdparty/stout/include
		${MESOS_ROOT}/build/include 		
		${MESOS_ROOT}/build/3rdparty/curl-7.61.0/src/curl-7.61.0/include 
		${MESOS_ROOT}/build/3rdparty/protobuf-3.5.0/src/protobuf-3.5.0/src 
		${MESOS_ROOT}/build/3rdparty/rapidjson-1.1.0/src/rapidjson-1.1.0/include 
		${MESOS_ROOT}/build/3rdparty/picojson-1.3.0/src/picojson-1.3.0 
		${MESOS_ROOT}/build/3rdparty/glog-0.4.0/src/glog-0.4.0/src/windows 
		${MESOS_ROOT}/build/3rdparty/boost-1.65.0/src/boost-1.65.0 
		)

include_directories(${JOURNALD_INCL})

link_directories(${MESOS_ROOT}/build/3rdparty/zookeeper-3.4.8/src/zookeeper-3.4.8-build
				${MESOS_ROOT}/build/3rdparty/libprocess/src
				${MESOS_ROOT}/build/3rdparty/protobuf-3.5.0/src/protobuf-3.5.0-build
				${MESOS_ROOT}/build/3rdparty/glog-0.4.0/src/glog-0.4.0-build
				${MESOS_ROOT}/build/3rdparty/http_parser-2.6.2/src/http_parser-2.6.2-build
				${MESOS_ROOT}/build/src
				${MESOS_ROOT}/build/3rdparty/zlib-1.2.8/src/zlib-1.2.8-lib/lib
				${MESOS_ROOT}/build/3rdparty/bzip2-1.0.6/src/bzip2-1.0.6-lib )

if (WIN32)
	add_definitions(-D__WINDOWS__ -DNOGDI -DNOMINMAX -D_UNICODE -DUNICODE -DGOOGLE_GLOG_DLL_DECL= -DLIBARCHIVE_STATIC -DHAVE_LIBZ -DENABLE_NVML -D_SCL_SECURE_NO_WARNINGS -D_CRT_SECURE_NO_WARNINGS -D_CRT_NONSTDC_NO_WARNINGS -DCURL_STATICLIB -DLIBSASL_EXPORTS=1 -DZOOKEEPER_VERSION="3.4.8" -DUSE_STATIC_LIB)
endif ()

if(MSVC)
  set(CMAKE_EXE_LINKER_FLAGS
      "${CMAKE_EXE_LINKER_FLAGS} /NODEFAULTLIB")
endif()
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
add_library(journaldlogger SHARED  ${JOURNALD_SRC})

target_link_libraries(journaldlogger process
									zookeeper
									mesos-protobufs
						debug		libprotobufd 
						optimized 	libprotobuf 
						debug		glogd
						optimized	glog
									Ws2_32
									Userenv
									http_parser
									bzip2
									zlibstatic
									Mswsock)

add_executable(mesos-journald-logger journald.hpp journald.cpp)
if (NOT WIN32)
	find_package(PkgConfig REQUIRED) 

	pkg_check_modules(SYSTEMD_JOURNALD REQUIRED IMPORTED_TARGET libsystemd)
	# ? MESOS_LDFLAGS="-L${mesos}/lib64 -lmesos"
	target_link_libraries(mesos-journald-logger ${SYSTEMD_JOURNALD} mesos)
else ()
	target_link_libraries(mesos-journald-logger process
									zookeeper
									mesos-protobufs
						debug		libprotobufd 
						optimized 	libprotobuf 
						debug		glogd
						optimized	glog
									Ws2_32
									Userenv
									http_parser
									bzip2
									zlibstatic
									Mswsock)
endif ()






