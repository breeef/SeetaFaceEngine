cmake_minimum_required(VERSION 2.8.4)

project(seeta_fa_lib)

# Build options
option(BUILD_EXAMPLES  "Set to ON to build examples"  ON)

# Use C++11
#set(CMAKE_CXX_STANDARD 11)
#set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
message(STATUS "C++11 support has been enabled by default.")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O2")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -msse4.1")

include_directories(include)

set(src_files 
    src/cfan.cpp
    src/face_alignment.cpp
    src/sift.cpp
    )

add_library(seeta_fa_lib SHARED ${src_files})
set(fa_required_libs seeta_fa_lib)

install(TARGETS seeta_fa_lib
        RUNTIME DESTINATION bin/\${CMAKE_INSTALL_CONFIG_NAME}
        LIBRARY DESTINATION lib/\${CMAKE_INSTALL_CONFIG_NAME}
        ARCHIVE DESTINATION lib/static/\${CMAKE_INSTALL_CONFIG_NAME})
install(DIRECTORY include DESTINATION include/FaceAlignment)


if (BUILD_EXAMPLES)
    include_directories(include/../../FaceDetection/include)
    message(STATUS "Build with examples.")
    find_package(OpenCV)
    if (NOT OpenCV_FOUND)
        message(WARNING "OpenCV not found. Test will not be built.")
    else()
        include_directories(${OpenCV_INCLUDE_DIRS} build)
	link_directories(build)
        list(APPEND fa_required_libs ${OpenCV_LIBS} seeta_facedet_lib)
        add_executable(test_face_alignment src/test/test_face_alignment.cpp)
        target_link_libraries(test_face_alignment ${fa_required_libs})

		install(TARGETS test_face_alignment
			RUNTIME DESTINATION bin/\${CMAKE_INSTALL_CONFIG_NAME}
			LIBRARY DESTINATION lib/\${CMAKE_INSTALL_CONFIG_NAME}
			ARCHIVE DESTINATION lib/static/\${CMAKE_INSTALL_CONFIG_NAME})
		install(FILES ${PROJECT_SOURCE_DIR}/src/test/test_face_alignment.cpp DESTINATION sample)
		install(DIRECTORY data DESTINATION .)
		install(DIRECTORY model DESTINATION .)
    endif()
endif()
