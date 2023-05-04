set(
  TOP_LEVEL_SUBDIRS
  utility
)

set(VT_IO_INSTALL_DESTINATION "include/vt-io")
set(VT_IO_EXTERNAL_DESTINATION "include")

foreach(DIR ${TOP_LEVEL_SUBDIRS})
  install(
    DIRECTORY "vt-io/${DIR}"
    DESTINATION ${VT_IO_INSTALL_DESTINATION}
    FILES_MATCHING PATTERN "*.h"
  )
endforeach()

file(GLOB TOP_HEADERS "vt-io/*.h")

install(
  FILES ${TOP_HEADERS}
  DESTINATION ${VT_IO_INSTALL_DESTINATION}
)

file(
  GLOB
  HEADER_FILES
  ${CMAKE_CURRENT_SOURCE_DIR}/vt-io/*.h
)

file(
  GLOB
  SOURCE_FILES
  ${CMAKE_CURRENT_SOURCE_DIR}/vt-io/*.cc
)

foreach(SUB_DIR ${TOP_LEVEL_SUBDIRS})
  file(
    GLOB
    "${SUB_DIR}_HEADER_FILES"
    RELATIVE
    "${CMAKE_CURRENT_SOURCE_DIR}"
    "${CMAKE_CURRENT_SOURCE_DIR}/vt-io/${SUB_DIR}/*.h"
  )

  file(
    GLOB
    "${SUB_DIR}_SOURCE_FILES"
    RELATIVE
    "${CMAKE_CURRENT_SOURCE_DIR}"
    "${CMAKE_CURRENT_SOURCE_DIR}/vt-io/${SUB_DIR}/*.cc"
  )

  #message("headers=${${SUB_DIR}_HEADER_FILES}")
  #message("source=${${SUB_DIR}_SOURCE_FILES}")

  list(
    APPEND
    HEADER_FILES
    "${${SUB_DIR}_HEADER_FILES}"
  )

  list(
    APPEND
    SOURCE_FILES
    "${${SUB_DIR}_SOURCE_FILES}"
  )
endforeach()

add_library(
  ${VT_IO_LIBRARY}
  STATIC
  ${HEADER_FILES} ${SOURCE_FILES}
)

add_library(${VT_IO_LIBRARY_NS} ALIAS ${VT_IO_LIBRARY})

target_link_libraries(
  ${VT_IO_LIBRARY} PUBLIC ${JSON_LIBRARY}
)

target_link_libraries(
  ${VT_IO_LIBRARY} PUBLIC ${FMT_LIBRARY}
)

target_link_libraries(
  ${VT_IO_LIBRARY} PUBLIC ${BROTLI_LIBRARY}
)

target_include_directories(
  ${VT_IO_LIBRARY} PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
  $<INSTALL_INTERFACE:include>
)

target_include_directories(
  ${VT_IO_LIBRARY} PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  $<INSTALL_INTERFACE:include>
)

install(
  TARGETS                   ${VT_IO_LIBRARY}
  EXPORT                    ${VT_IO_LIBRARY}
  LIBRARY DESTINATION       lib
  ARCHIVE DESTINATION       lib
  RUNTIME DESTINATION       bin
  INCLUDES DESTINATION      ${VT_IO_EXTERNAL_DESTINATION}
)

install(
  EXPORT                    ${VT_IO_LIBRARY}
  DESTINATION               cmake
  FILE                      "vtIOTargets.cmake"
  NAMESPACE                 vt::lib::
  COMPONENT                 runtime
)

install(TARGETS ${FMT_LIBRARY} EXPORT ${VT_IO_LIBRARY})
install(TARGETS ${JSON_LIBRARY} EXPORT ${VT_IO_LIBRARY})
install(TARGETS ${BROTLI_LIBRARY} EXPORT ${VT_IO_LIBRARY})

export(
  TARGETS                   ${VT_IO_LIBRARY}
                            ${FMT_LIBRARY}
                            ${JSON_LIBRARY}
                            ${BROTLI_LIBRARY}
  FILE                      "vtIOTargets.cmake"
  NAMESPACE                 vt::lib::
)
