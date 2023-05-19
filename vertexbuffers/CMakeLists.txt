add_executable(vertexbuffers main.cc)
target_include_directories(vertexbuffers PRIVATE ${VULKAN_HPP_INCLUDE_DIRS})
target_link_libraries(vertexbuffers PRIVATE glfw glm::glm Vulkan::Vulkan)

file(GLOB_RECURSE GLSL_SOURCE_FILES
    "shaders/*.frag"
    "shaders/*.vert"
    )

foreach(GLSL ${GLSL_SOURCE_FILES})
  get_filename_component(FILE_NAME ${GLSL} NAME)
  set(SPIRV "${PROJECT_BINARY_DIR}/shaders/vertexbuffers/${FILE_NAME}.spv")
  add_custom_command(
    OUTPUT ${SPIRV}
    COMMAND ${CMAKE_COMMAND} -E make_directory "${PROJECT_BINARY_DIR}/vertexbuffers/"
    COMMAND glslc ${GLSL} -o ${SPIRV}
    DEPENDS ${GLSL})
  list(APPEND SPIRV_BINARY_FILES ${SPIRV})
endforeach(GLSL)

add_custom_target(
    VShaders 
    DEPENDS ${SPIRV_BINARY_FILES}
    )

add_dependencies(vertexbuffers VShaders)

add_custom_command(TARGET vertexbuffers POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E make_directory "$<TARGET_FILE_DIR:vertexbuffers>/vertexbuffers_dir/"
    COMMAND ${CMAKE_COMMAND} -E copy_directory
        "${PROJECT_BINARY_DIR}/shaders/vertexbuffers"
        "$<TARGET_FILE_DIR:vertexbuffers>/vertexbuffers_dir"
        )

        