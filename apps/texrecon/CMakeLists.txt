file (GLOB HEADERS "*.h")
file (GLOB SOURCES "[^_]*.cpp")

set(BIN texrecon)

if(NOT DEFINED ELIBS_ROOT_DIR)
    set(ELIBS_ROOT_DIR ${CMAKE_SOURCE_DIR})
endif()

add_executable(${BIN} ${SOURCES})
set_property(TARGET ${BIN} PROPERTY INTERPROCEDURAL_OPTIMIZATION True)
add_dependencies(${BIN} ext_mve)
target_link_directories(
    ${BIN}
    PRIVATE
    ${ELIBS_ROOT_DIR}/elibs/mve/libs/mve
    ${ELIBS_ROOT_DIR}/elibs/mve/libs/util
)
target_link_libraries(${BIN} tex ${TBB_LIBRARIES} -lmve -lmve_util ${EIGEN_TARGET})
install(TARGETS ${BIN} RUNTIME DESTINATION bin)
