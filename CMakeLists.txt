cmake_minimum_required(VERSION 2.8)

project(tristripper)
set(MAJOR_VERSION "1")
set(MINOR_VERSION "10")

option(TRISTRIPPER_BUILD_TEST "Build test program (not fully tested on all platforms)." "0")
option(TRISTRIPPER_BUILD_SHARED_LIB "Build tristripper as a shared library (overrides BUILD_SHARED_LIBS)." "0")

if(UNIX)
  add_definitions("-DUNIX")
endif()

###############################################
# library settings
###############################################

include_directories("include")

if(TRISTRIPPER_BUILD_SHARED_LIB)
  set(BUILD_SHARED_LIBS ${TRISTRIPPER_BUILD_SHARED_LIB})
endif()

add_library(tristripper
  src/connectivity_graph.cpp
  src/policy.cpp
  src/tri_stripper.cpp
)

set_target_properties(tristripper
  PROPERTIES
  SOVERSION "${MAJOR_VERSION}"
  VERSION "${MAJOR_VERSION}.${MINOR_VERSION}"
)

install(TARGETS tristripper DESTINATION lib LIBRARY)

install(FILES
  include/public_types.h
  include/tri_stripper.h
  DESTINATION include)

install(FILES
  include/detail/cache_simulator.h
  include/detail/connectivity_graph.h
  include/detail/graph_array.h
  include/detail/heap_array.h
  include/detail/policy.h
  include/detail/types.h
  DESTINATION include/detail
)

###############################################
# test program settings
###############################################
if(TRISTRIPPER_BUILD_TEST)

  find_package(OpenGL REQUIRED)
  find_package(GLUT REQUIRED)

  file(COPY "${CMAKE_CURRENT_SOURCE_DIR}/test/models" DESTINATION "${CMAKE_CURRENT_BINARY_DIR}")

  include_directories(
    "test"
    ${OPENGL_INCLUDE_DIR}
    ${GLUT_INCLUDE_DIR})

  add_executable(test
    test/gl_mesh.cpp
    test/gl_model.cpp
    test/gl_renderer.cpp
    test/high_res_timer.cpp
    test/main.cpp
    test/varrays_normalizer.cpp
  )

  target_link_libraries(test
    tristripper
    ${OPENGL_LIBRARIES}
    ${GLUT_LIBRARIES}
  )

endif()
