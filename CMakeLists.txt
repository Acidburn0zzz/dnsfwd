cmake_minimum_required (VERSION 2.8.11)
project(dnsovc)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall")

option(USE_SYSTEMD "Compile with systemd integration" OFF)

add_executable(dnsfwd
  src/dnsfwd.cpp
  src/client.cpp
  src/server.cpp
  src/service.cpp
  src/config.cpp
  )
target_link_libraries(dnsfwd boost_system boost_program_options pthread)

if(USE_SYSTEMD)
  add_definitions(-DUSE_SYSTEMD)
  target_link_libraries(dnsfwd boost_system systemd)
  configure_file(systemd/dnsfwd.socket dnsfwd.socket)
  configure_file(systemd/dnsfwd.service dnsfwd.service)
endif()

install(TARGETS dnsfwd DESTINATION bin)
if(USE_SYSTEMD)
  install(FILES dnsfwd.service DESTINATION lib/systemd/system)
  install(FILES dnsfwd.socket DESTINATION lib/systemd/system)
endif()
