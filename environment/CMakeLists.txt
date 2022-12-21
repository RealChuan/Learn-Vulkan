add_executable(enviroment main.cc)
target_include_directories(enviroment PRIVATE ${VULKAN_HPP_INCLUDE_DIRS})
target_link_libraries(enviroment PRIVATE glfw glm::glm Vulkan::Vulkan)
