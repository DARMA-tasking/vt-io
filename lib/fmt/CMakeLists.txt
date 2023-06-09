project(FMT CXX)

function(add_headers VAR)
  set(headers ${${VAR}})
  foreach (header ${ARGN})
    set(headers ${headers} include/fmt-vt/${header})
  endforeach()
  set(${VAR} ${headers} PARENT_SCOPE)
endfunction()

# Define the fmt library, its includes and the needed defines.
add_headers(FMT_HEADERS core.h format.h format-inl.h ostream.h)
set(FMT_SOURCES src/format.cc)

add_library(fmt ${FMT_SOURCES} ${FMT_HEADERS})
add_library(fmt::fmt ALIAS fmt)
set_target_properties(fmt PROPERTIES OUTPUT_NAME fmt-vt)

target_include_directories(fmt SYSTEM PUBLIC
  $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

set(FMT_DEBUG_POSTFIX d CACHE STRING "Debug library postfix.")

set_target_properties(fmt PROPERTIES
  DEBUG_POSTFIX "${FMT_DEBUG_POSTFIX}")

install(
  DIRECTORY                include/fmt-vt
  DESTINATION              include
  CONFIGURATIONS           ${build_type_list}
  FILES_MATCHING PATTERN   "*"
)
