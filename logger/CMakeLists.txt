add_executable(co2logger main.cpp)

option(DOWNLOAD_DEPENDENCIES "download dependencies" ON)

if(DOWNLOAD_DEPENDENCIES)
	include(ExternalProject)
	ExternalProject_Add (json
	  URL https://github.com/nlohmann/json/releases/download/v3.10.2/include.zip
	  PREFIX ${CMAKE_CURRENT_BINARY_DIR}/json
      CONFIGURE_COMMAND ""
      BUILD_COMMAND ""
      INSTALL_COMMAND ""
      LOG_DOWNLOAD ON
      LOG_INSTALL ON
	)
	target_include_directories(co2logger PRIVATE ${CMAKE_CURRENT_BINARY_DIR}/json/src/json/include/)
endif()



target_include_directories(co2logger PRIVATE ../include/cpp-httplib)
target_include_directories(co2logger PRIVATE ../include/co2mon)

target_link_libraries(co2logger hidapi)
target_compile_features(co2logger PRIVATE cxx_std_17)
if(NOT WIN32)
	find_package(Threads REQUIRED)
	target_link_libraries(co2logger Threads::Threads)
endif()

add_custom_command(TARGET co2logger POST_BUILD     
    COMMAND ${CMAKE_COMMAND} -E copy_if_different  
         ${PROJECT_SOURCE_DIR}/misc/configs.json 
        $<TARGET_FILE_DIR:co2logger>)              

        
install(TARGETS co2logger 
        RUNTIME DESTINATION bin)
if(WIN32)
	add_custom_command(TARGET co2logger POST_BUILD 
	    COMMAND ${CMAKE_COMMAND} -E copy_if_different  
	        $<TARGET_FILE_DIR:hidapi>/hidapi.dll    
	        $<TARGET_FILE_DIR:co2logger>)       
	install(FILES ${PROJECT_SOURCE_DIR}/logger/configs.json DESTINATION bin)
else()
	add_custom_command(TARGET co2logger POST_BUILD 
	    COMMAND ${CMAKE_COMMAND} -E copy_if_different  
	        $<TARGET_FILE_DIR:hidapi>/libhidapi.so    
	        $<TARGET_FILE_DIR:co2logger>)       
	install(FILES ${PROJECT_SOURCE_DIR}/misc/configs.json DESTINATION "/etc/co2mon")
	install(FILES ${PROJECT_SOURCE_DIR}/misc/98-co2mon.rules DESTINATION "/etc/udev/rules.d/")
	install(FILES ${PROJECT_SOURCE_DIR}/misc/co2mini.service DESTINATION "/etc/systemd/system/")
endif()
