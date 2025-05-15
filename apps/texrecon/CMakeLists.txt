file (GLOB HEADERS "*.h")
file (GLOB SOURCES "[^_]*.cpp")

set(BIN texrecon)

if(NOT DEFINED ELIBS_ROOT_DIR)
    set(ELIBS_ROOT_DIR ${CMAKE_SOURCE_DIR})
endif()

add_executable(${BIN} ${SOURCES})
set_property(TARGET ${BIN} PROPERTY INTERPROCEDURAL_OPTIMIZATION True)
add_dependencies(${BIN} ${ELIBS_ROOT_DIR}/ext_mve)
target_link_libraries(${BIN} tex ${TBB_LIBRARIES} -lmve -lmve_util ${EIGEN_TARGET})
install(TARGETS ${BIN} RUNTIME DESTINATION bin)
