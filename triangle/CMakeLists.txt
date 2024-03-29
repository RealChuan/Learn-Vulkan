add_executable(triangle main.cc)
target_include_directories(triangle PRIVATE ${VULKAN_HPP_INCLUDE_DIRS})
target_link_libraries(triangle PRIVATE glfw glm::glm Vulkan::Vulkan)

file(GLOB_RECURSE GLSL_SOURCE_FILES "shaders/*.frag" "shaders/*.vert")

foreach(GLSL ${GLSL_SOURCE_FILES})
  get_filename_component(FILE_NAME ${GLSL} NAME)
  set(SPIRV "${PROJECT_BINARY_DIR}/shaders/triangle/${FILE_NAME}.spv")
  add_custom_command(
    OUTPUT ${SPIRV}
    COMMAND ${CMAKE_COMMAND} -E make_directory
            "${PROJECT_BINARY_DIR}/shaders/triangle/"
    COMMAND glslc ${GLSL} -o ${SPIRV}
    DEPENDS ${GLSL})
  list(APPEND SPIRV_BINARY_FILES ${SPIRV})
endforeach(GLSL)

add_custom_target(TShaders DEPENDS ${SPIRV_BINARY_FILES})

add_dependencies(triangle TShaders)

add_custom_command(
  TARGET triangle
  POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E make_directory
          "$<TARGET_FILE_DIR:triangle>/triangle_dir/"
  COMMAND
    ${CMAKE_COMMAND} -E copy_directory "${PROJECT_BINARY_DIR}/shaders/triangle"
    "$<TARGET_FILE_DIR:triangle>/triangle_dir")
