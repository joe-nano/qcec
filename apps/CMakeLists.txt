add_executable(${PROJECT_NAME}_app ${CMAKE_CURRENT_SOURCE_DIR}/app.cpp)
target_compile_features(${PROJECT_NAME}_app PUBLIC cxx_std_14)
set_target_properties(${PROJECT_NAME}_app PROPERTIES CXX_EXTENSIONS OFF)
target_link_libraries(${PROJECT_NAME}_app PRIVATE JKQ::${PROJECT_NAME})
if(MSVC)
	target_compile_options(${PROJECT_NAME}_app PUBLIC "/W4" "$<$<CONFIG:RELEASE>:/O2 /GL>")
	target_link_options(${PROJECT_NAME}_app PUBLIC "$<$<CONFIG:Release>:/LTCG>")
else()
	target_compile_options(${PROJECT_NAME}_app PUBLIC -Wall $<$<CONFIG:DEBUG>:-g3 -Og -Wextra -Wpedantic -pedantic> $<$<CONFIG:RELEASE>:-O3 -mtune=native -march=native -DNDEBUG -g0>)
endif()

add_custom_command(TARGET ${PROJECT_NAME}_app
                   POST_BUILD
                   COMMAND ${CMAKE_COMMAND} -E create_symlink ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_app ${CMAKE_BINARY_DIR}/${PROJECT_NAME}_app
                   COMMENT "Creating symlink for ${PROJECT_NAME}_app"
                   VERBATIM)

include(GNUInstallDirs)
install(TARGETS ${PROJECT_NAME}_app
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})

set_target_properties(${PROJECT_NAME}_app PROPERTIES EXPORT_NAME ${PROJECT_NAME}_app)
