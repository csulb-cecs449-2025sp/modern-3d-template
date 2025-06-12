cmake_minimum_required (VERSION 3.8)

# Enable Hot Reload for MSVC compilers if supported.
if (POLICY CMP0141)
  cmake_policy(SET CMP0141 NEW)
  set(CMAKE_MSVC_DEBUG_INFORMATION_FORMAT "$<IF:$<AND:$<C_COMPILER_ID:MSVC>,$<CXX_COMPILER_ID:MSVC>>,$<$<CONFIG:Debug,RelWithDebInfo>:EditAndContinue>,$<$<CONFIG:Debug,RelWithDebInfo>:ProgramDatabase>>")
endif()

project ("ModernOpenGL")


add_executable (ModernOpenGL "src/main.cpp" "include/ShaderProgram.h" "src/ShaderProgram.cpp" )


# Find and link external libraries, like SFML.
# This only works if Vcpkg has been configured correctly.
find_package(SFML COMPONENTS System Window Graphics CONFIG REQUIRED)
target_link_libraries(ModernOpenGL PRIVATE SFML::System SFML::Window SFML::Graphics)

find_package(assimp CONFIG REQUIRED)
target_link_libraries(ModernOpenGL PRIVATE assimp::assimp)

find_package(glad CONFIG REQUIRED)
target_link_libraries(ModernOpenGL PRIVATE glad::glad)

target_include_directories(ModernOpenGL PUBLIC "./include")

if (CMAKE_VERSION VERSION_GREATER 3.12)
  set_property(TARGET ModernOpenGL PROPERTY CXX_STANDARD 20)
endif()

add_custom_target(copyshaders
        COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_BINARY_DIR}/shaders
        COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_SOURCE_DIR}/shaders_source ${CMAKE_CURRENT_BINARY_DIR}/shaders
        COMMENT "copying ${CMAKE_SOURCE_DIR}/shaders_source to ${CMAKE_CURRENT_BINARY_DIR}/shaders"
        WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
)
add_dependencies(ModernOpenGL copyshaders)

add_custom_target(copymodels
        COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_BINARY_DIR}/models
        COMMAND ${CMAKE_COMMAND} -E copy_directory ${CMAKE_SOURCE_DIR}/models ${CMAKE_CURRENT_BINARY_DIR}/models
        COMMENT "copying ${CMAKE_SOURCE_DIR}/models to ${CMAKE_CURRENT_BINARY_DIR}/models"
        WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
)
add_dependencies(ModernOpenGL copymodels)