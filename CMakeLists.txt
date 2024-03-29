cmake_minimum_required(VERSION 3.10)
SET(CMAKE_CXX_STANDARD 17)
enable_language(CXX)

#if(NOT CMAKE_BUILD_TYPE)
set(CMAKE_BUILD_TYPE Release)
#endif()

set(CMAKE_CXX_FLAGS "-Wall -Wextra -Wdeprecated")  # For macOS
#set(CMAKE_CXX_FLAGS "-W4 -std:c++17 -EHsc")  # For windows
set(CMAKE_CXX_FLAGS_DEBUG "-g")
set(CMAKE_CXX_FLAGS_RELEASE "-O3") # for mac. Comment out for windows

# ======= DEPENDENCY: GLFW =========
set(GLFW_VULKAN_STATIC ON CACHE BOOL "" FORCE)  # mac. Doesnt work on windows.
#set(GLFW_VULKAN_STATIC OFF CACHE BOOL "" FORCE)  # Windows or mac. Mac works with both, makes no difference as far as I can tell.
set(BUILD_SHARED_LIBS OFF CACHE BOOL "" FORCE)
add_subdirectory(vendor/glfw)

# BOOST
FIND_PACKAGE(Boost 1.71.0 COMPONENTS system thread REQUIRED)
find_package(Vulkan REQUIRED)

# ======= DEPENDENCY: GLM ===========
add_subdirectory(vendor/glm)

#add_subdirectory(vendor/tinygltf)

# ======= DEPENDENCY: SPDLOG ========
add_subdirectory(vendor/spdlog)

# =========== Static library building =============
project(HephaestusStatic VERSION 0.0.4 LANGUAGES CXX)
add_library(HephaestusStatic STATIC include/hp/hp.hpp src/hp/profiling.cpp include/hp/profiling.hpp include/hp/config.hpp src/hp/logging.cpp include/hp/logging.hpp src/hp/vk/window.cpp include/hp/vk/window.hpp src/hp/vk/vk.cpp include/hp/vk/vk.hpp src/hp/vk/shaders.cpp src/hp/vk/window.cpp include/hp/vk/window.hpp src/hp/multithreading.cpp include/hp/multithreading.hpp src/hp/vk/buffers.cpp)
target_link_libraries(HephaestusStatic PUBLIC glm)
target_include_directories(HephaestusStatic PUBLIC src include vendor/glfw/include vendor/glm vendor/spdlog/include vendor ${Boost_INCLUDE_DIR} vendor/vma/src)
target_link_libraries(HephaestusStatic PUBLIC glfw)

#target_link_libraries(HephaestusStatic PUBLIC $ENV{VULKAN_SDK}/Lib32/vulkan-1.lib) # for 32 bit machines or x64 architecture
#target_link_directories(HephaestusStatic PUBLIC $ENV{VULKAN_SDK}/Lib32)
#target_link_directories(HephaestusStatic PUBLIC ${Boost_LIBRARY_DIRS})
#target_include_directories(HephaestusStatic PUBLIC $ENV{VULKAN_SDK}/Include)

target_link_libraries(HephaestusStatic PUBLIC ${Vulkan_LIBRARIES})
target_include_directories(HephaestusStatic PUBLIC ${Vulkan_INCLUDE_DIRS})
target_link_directories(HephaestusStatic PUBLIC $ENV{VULKAN_SDK}/lib)
#target_link_libraries(HephaestusStatic PUBLIC $ENV{VULKAN_SDK}/lib/libVkLayer_khronos_validation.so)
#target_link_directories(HephaestusStatic PUBLIC ${Boost_LIBRARY_DIRS})
target_link_libraries(HephaestusStatic PUBLIC ${Boost_LIBRARIES})

# ====== SHARED LIBRARY BUILDING ========
project(HephaestusShared VERSION 0.0.4 LANGUAGES CXX)
add_library(HephaestusShared SHARED include/hp/hp.hpp src/hp/profiling.cpp include/hp/profiling.hpp include/hp/config.hpp src/hp/logging.cpp include/hp/logging.hpp src/hp/vk/window.cpp include/hp/vk/window.hpp src/hp/vk/vk.cpp include/hp/vk/vk.hpp src/hp/vk/shaders.cpp src/hp/vk/window.cpp include/hp/vk/window.hpp src/hp/multithreading.cpp include/hp/multithreading.hpp src/hp/vk/buffers.cpp)
target_link_libraries(HephaestusShared PUBLIC glm)
target_include_directories(HephaestusShared PUBLIC src include vendor/glfw/include vendor/glm vendor/spdlog/include vendor ${Boost_INCLUDE_DIR} vendor/vma/src)
target_link_libraries(HephaestusShared PUBLIC glfw)

# Mac Specific code. Comment out if on windows.
target_link_libraries(HephaestusShared PUBLIC ${Boost_LIBRARIES})
target_link_libraries(HephaestusShared PUBLIC ${Vulkan_LIBRARIES})
target_include_directories(HephaestusShared PUBLIC ${Vulkan_INCLUDE_DIRS})
target_link_directories(HephaestusShared PUBLIC $ENV{VULKAN_SDK}/lib)
#target_link_libraries(HephaestusShared PUBLIC $ENV{VULKAN_SDK}/lib/libVkLayer_khronos_validation.so)

# Windows specific code. Comment out if on mac
#target_link_libraries(HephaestusShared PUBLIC $ENV{VULKAN_SDK}/Lib32/vulkan-1.lib) # for 32 bit machines or x64 architecture
#target_link_directories(HephaestusShared PUBLIC $ENV{VULKAN_SDK}/Lib32)
#target_link_directories(HephaestusShared PUBLIC ${Boost_LIBRARY_DIRS})
#target_include_directories(HephaestusShared PUBLIC $ENV{VULKAN_SDK}/Include)

# ====== SANDBOX BUILDING ========
add_subdirectory(examples)

