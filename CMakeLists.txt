cmake_minimum_required(VERSION 3.0)
project(jinglepings-server)
find_package(OpenCV 4 REQUIRED)
find_package(Threads REQUIRED)

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "-Wall -Wextra")
set(CMAKE_CXX_FLAGS_DEBUG "-g")
set(CMAKE_CXX_FLAGS_RELEASE "-O3")

set(SOURCE src/main.cpp
        src/draw/JingleBuffer.cpp
        src/draw/JingleBuffer.h
        src/controller/JingleController.cpp
        src/controller/JingleController.h
        src/socket/Socket.cpp
        src/socket/Socket.h
        src/tui/Tui.cpp
        src/tui/Tui.h)

add_compile_definitions(JINGLE_FRAME_WIDTH=1920 JINGLE_FRAME_HEIGHT=1080)
add_compile_definitions(JINGLE_INTERFACE="lo" JINGLE_PING_THREADS=1 JINGLE_RTMP_URL="rtmp://37.60.237.32:1935/live/jinglepings")
add_compile_definitions(JINGLE_BLACKLIST_FILE="blacklist.txt")

add_executable(${PROJECT_NAME} ${SOURCE})
target_link_libraries(jinglepings-server ${OpenCV_LIBS} ${CMAKE_THREAD_LIBS_INIT} stdc++fs)
