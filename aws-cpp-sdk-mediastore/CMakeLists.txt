add_project(aws-cpp-sdk-mediastore "C++ SDK for the AWS mediastore service" aws-cpp-sdk-core)

file(GLOB AWS_MEDIASTORE_HEADERS
    "include/aws/mediastore/*.h"
)

file(GLOB AWS_MEDIASTORE_MODEL_HEADERS
    "include/aws/mediastore/model/*.h"
)

file(GLOB AWS_MEDIASTORE_SOURCE
    "source/*.cpp"
)

file(GLOB AWS_MEDIASTORE_MODEL_SOURCE
    "source/model/*.cpp"
)

file(GLOB MEDIASTORE_UNIFIED_HEADERS
    ${AWS_MEDIASTORE_HEADERS}
    ${AWS_MEDIASTORE_MODEL_HEADERS}
)

file(GLOB MEDIASTORE_UNITY_SRC
    ${AWS_MEDIASTORE_SOURCE}
    ${AWS_MEDIASTORE_MODEL_SOURCE}
)

if(ENABLE_UNITY_BUILD)
    enable_unity_build("MEDIASTORE" MEDIASTORE_UNITY_SRC)
endif()

file(GLOB MEDIASTORE_SRC
    ${MEDIASTORE_UNIFIED_HEADERS}
    ${MEDIASTORE_UNITY_SRC}
)

if(WIN32)
    #if we are compiling for visual studio, create a sane directory tree.
    if(MSVC)
        source_group("Header Files\\aws\\mediastore" FILES ${AWS_MEDIASTORE_HEADERS})
        source_group("Header Files\\aws\\mediastore\\model" FILES ${AWS_MEDIASTORE_MODEL_HEADERS})
        source_group("Source Files" FILES ${AWS_MEDIASTORE_SOURCE})
        source_group("Source Files\\model" FILES ${AWS_MEDIASTORE_MODEL_SOURCE})
    endif(MSVC)
endif()

set(MEDIASTORE_INCLUDES
    "${CMAKE_CURRENT_SOURCE_DIR}/include/"
)

add_library(${PROJECT_NAME} ${LIBTYPE} ${MEDIASTORE_SRC})
add_library(AWS::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

set_compiler_flags(${PROJECT_NAME})
set_compiler_warnings(${PROJECT_NAME})

if(USE_WINDOWS_DLL_SEMANTICS AND BUILD_SHARED_LIBS)
    target_compile_definitions(${PROJECT_NAME} PRIVATE "AWS_MEDIASTORE_EXPORTS")
endif()

target_include_directories(${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>)

target_link_libraries(${PROJECT_NAME} ${PROJECT_LIBS})


setup_install()

install (FILES ${AWS_MEDIASTORE_HEADERS} DESTINATION ${INCLUDE_DIRECTORY}/aws/mediastore)
install (FILES ${AWS_MEDIASTORE_MODEL_HEADERS} DESTINATION ${INCLUDE_DIRECTORY}/aws/mediastore/model)

if(PLATFORM_WINDOWS AND MSVC)
    install (FILES nuget/${PROJECT_NAME}.autopkg DESTINATION nuget)
endif()

do_packaging()


