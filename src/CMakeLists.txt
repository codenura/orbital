add_executable(${EXECUTEABLE_NAME}
    main.cpp
    Lexer.cpp
    Parser.cpp
)

# Link Runtime As Statically
if(MSVC)
    set_property(TARGET ${EXECUTEABLE_NAME} PROPERTY
        MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>"
    )
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    target_link_options(${EXECUTEABLE_NAME} PRIVATE -static-libgcc -static-libstdc++)
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    target_link_options(${EXECUTEABLE_NAME} PRIVATE -static-libgcc -static-libstdc++)
endif()

target_link_libraries(${EXECUTEABLE_NAME} PRIVATE
    orbtlio
    ${DL_LIBRARY}
)

if(WIN32)
    target_link_libraries(${EXECUTEABLE_NAME} PRIVATE unofficial::pdcurses::pdcurses)
else()
    target_link_libraries(${EXECUTEABLE_NAME} PRIVATE ${CURSES_LIBRARIES})
endif()

target_include_directories(${EXECUTEABLE_NAME} PRIVATE
    ${CMAKE_SOURCE_DIR}/include
)

set_target_properties(${EXECUTEABLE_NAME} PROPERTIES
    CXX_STANDARD 20
    CXX_STANDARD_REQUIRED ON
)

if(BUILD_TESTING)
    file(GLOB SAMPLE_FILES "${CMAKE_SOURCE_DIR}/sample/*.obt")
    foreach(SAMPLE_FILE ${SAMPLE_FILES})
        get_filename_component(SAMPLE_NAME ${SAMPLE_FILE} NAME_WE)
        add_test(NAME "sample_${SAMPLE_NAME}"
            COMMAND ${EXECUTEABLE_NAME} ${SAMPLE_FILE}
            WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
    endforeach()
endif()