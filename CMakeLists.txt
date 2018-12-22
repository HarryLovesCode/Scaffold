cmake_minimum_required(VERSION 3.0)
project(Scaffold)

if (NOT EMSCRIPTEN)
    option(GLFW_BUILD_DOCS OFF)
    option(GLFW_BUILD_EXAMPLES OFF)
    option(GLFW_BUILD_TESTS OFF)
    option(GLEW_USE_STATIC_LIBS ON)

    add_subdirectory(External/glfw)
    add_subdirectory(External/glew/build/cmake)

    include_directories(External/glfw/include/)
    include_directories(External/glew/include/)
endif()

add_subdirectory(External/glm)

if (MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W4")
elseif (EMSCRIPTEN)
    set(CMAKE_EXECUTABLE_SUFFIX ".html")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Wpedantic -std=c++17")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -s USE_GLFW=3 -s WASM=1 -s ALLOW_MEMORY_GROWTH=1")
else()
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Wpedantic -std=c++17")
endif()

include_directories(External/glm/)
include_directories(External/stb/)

file(GLOB PROJECT_HEADERS Source/*.hpp)
file(GLOB PROJECT_SOURCES Source/*.cpp)
file(GLOB PROJECT_SHADERS Shader/*.comp
                          Shader/*.frag
                          Shader/*.geom
                          Shader/*.vert)
file(GLOB PROJECT_CONFIGS CMakeLists.txt
                          README.md
                         .gitattributes
                         .gitignore
                         .gitmodules)

source_group("Headers" FILES ${PROJECT_HEADERS})
source_group("Shaders" FILES ${PROJECT_SHADERS})
source_group("Sources" FILES ${PROJECT_SOURCES})
source_group("Vendors" FILES ${VENDORS_SOURCES})

add_definitions(-DPROJECT_SOURCE_DIR=\"${PROJECT_SOURCE_DIR}\")

if (NOT EMSCRIPTEN)
    add_definitions(-DGLEW_USE_STATIC_LIBS=ON -DGLFW_INCLUDE_NONE)
endif()

add_executable(Scaffold ${PROJECT_SOURCES} ${PROJECT_HEADERS} ${PROJECT_SHADERS} ${PROJECT_CONFIGS} ${VENDORS_SOURCES})

if (NOT EMSCRIPTEN)
    target_link_libraries(Scaffold glfw glew_s ${GLEW_LIBRARIES} ${GLFW_LIBRARIES})
endif()