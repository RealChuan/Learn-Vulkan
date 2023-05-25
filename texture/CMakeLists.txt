add_executable(texture main.cc)
target_include_directories(texture PRIVATE ${VULKAN_HPP_INCLUDE_DIRS}
                                           ${Stb_INCLUDE_DIR})
target_link_libraries(texture PRIVATE glfw glm::glm Vulkan::Vulkan)

file(GLOB_RECURSE GLSL_SOURCE_FILES "shaders/*.frag" "shaders/*.vert")

foreach(GLSL ${GLSL_SOURCE_FILES})
  get_filename_component(FILE_NAME ${GLSL} NAME)
  set(SPIRV "${PROJECT_BINARY_DIR}/shaders/texture/${FILE_NAME}.spv")
  add_custom_command(
    OUTPUT ${SPIRV}
    COMMAND ${CMAKE_COMMAND} -E make_directory "${PROJECT_BINARY_DIR}/texture/"
    COMMAND glslc ${GLSL} -o ${SPIRV}
    DEPENDS ${GLSL})
  list(APPEND SPIRV_BINARY_FILES ${SPIRV})
endforeach(GLSL)

add_custom_target(TXShaders DEPENDS ${SPIRV_BINARY_FILES})

add_dependencies(texture TXShaders)

add_custom_command(
  TARGET texture
  POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E make_directory
          "$<TARGET_FILE_DIR:texture>/texture_dir/"
  COMMAND
    ${CMAKE_COMMAND} -E copy_directory "${PROJECT_BINARY_DIR}/shaders/texture"
    "$<TARGET_FILE_DIR:texture>/texture_dir")

add_custom_command(
  TARGET texture
  POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E copy "${PROJECT_SOURCE_DIR}/texture/texture.jpg"
          "$<TARGET_FILE_DIR:texture>/texture_dir/texture.jpg")

add_executable(texture_loop texture_loop.cc)
target_include_directories(texture_loop PRIVATE ${VULKAN_HPP_INCLUDE_DIRS}
                                                ${Stb_INCLUDE_DIR})
target_link_libraries(texture_loop PRIVATE glfw glm::glm Vulkan::Vulkan)
