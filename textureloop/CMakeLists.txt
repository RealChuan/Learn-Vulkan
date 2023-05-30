add_executable(textureloop main.cc)
target_include_directories(textureloop PRIVATE ${VULKAN_HPP_INCLUDE_DIRS}
                                               ${Stb_INCLUDE_DIR})
target_link_libraries(textureloop PRIVATE glfw glm::glm Vulkan::Vulkan)
