file (GLOB HEADERS "*.h")
file (GLOB SOURCES "[^_]*.cc")

add_library(mve STATIC ${SOURCES} ${HEADERS})

if (JPEG_FOUND)
    target_include_directories(mve PRIVATE ${JPEG_INCLUDE_DIR})
endif()

if (PNG_FOUND)
    target_include_directories(mve PRIVATE ${PNG_INCLUDE_DIR})
endif()

if (TIFF_FOUND)
    target_include_directories(mve PRIVATE ${TIFF_INCLUDE_DIR})
endif()

message(STATUS "JPEG_FOUND: ${JPEG_FOUND}")
message(STATUS "PNG_FOUND: ${PNG_FOUND}")
message(STATUS "TIFF_FOUND: ${TIFF_FOUND}")
message(STATUS "JPEG_INCLUDE_DIR: ${JPEG_INCLUDE_DIR}")
message(STATUS "PNG_INCLUDE_DIR: ${PNG_INCLUDE_DIR}")
message(STATUS "TIFF_INCLUDE_DIR: ${TIFF_INCLUDE_DIR}")
message(STATUS "JPEG_LIBRARIES: ${JPEG_LIBRARIES}")
message(STATUS "PNG_LIBRARIES: ${PNG_LIBRARIES}")
message(STATUS "TIFF_LIBRARIES: ${TIFF_LIBRARIES}")

target_link_libraries(mve
    PUBLIC
        mve_math
        mve_util
    PRIVATE
        ${JPEG_LIBRARIES}
        ${PNG_LIBRARIES}
        ${TIFF_LIBRARIES}
)
