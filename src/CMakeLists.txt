set(CMAKE_CXX_STANDARD 17)

file(GLOB_RECURSE headers ${OTTO_SOURCE_DIR}/src/*.hpp)
file(GLOB_RECURSE sources ${OTTO_SOURCE_DIR}/src/*.cpp)
list(REMOVE_ITEM sources ${OTTO_SOURCE_DIR}/src/main.cpp)

find_package (Threads)
# Library
add_library(otto ${sources} ${BACKWARD_ENABLE})
target_link_libraries(otto PUBLIC external)
target_link_libraries(otto PUBLIC nanocanvas)
target_link_libraries(otto PUBLIC GSL)
target_link_libraries(otto PUBLIC fmt)
target_link_libraries(otto PUBLIC glfw)
target_link_libraries(otto PUBLIC jack)
target_link_libraries(otto PUBLIC ${CMAKE_THREAD_LIBS_INIT})
target_include_directories(otto PUBLIC ./)

if (NOT OTTO_USE_LIBCXX)
  target_link_libraries(otto PUBLIC atomic)
endif()

target_link_libraries(otto PUBLIC imgui)

# Executable
add_executable(otto_exec ${OTTO_SOURCE_DIR}/src/main.cpp ${BACKWARD_ENABLE})
target_link_libraries(otto_exec PUBLIC otto)
set_target_properties(otto_exec PROPERTIES OUTPUT_NAME otto)

# Docs
if (OTTO_BUILD_DOCS)
  file(WRITE ${OTTO_BINARY_DIR}/.standardese
"[compilation]
commands_dir=${OTTO_BINARY_DIR}

[input]
blacklist_namespace=detail
extract_private=true
")
  standardese_generate(otto CONFIG ${OTTO_BINARY_DIR}/.standardese
    INPUT ${headers})
endif()
