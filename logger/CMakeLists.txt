add_executable(co2logger main.cpp)

option(DOWNLOAD_DEPENDENCIES "download dependencies" ON)

if(DOWNLOAD_DEPENDENCIES)
	include(FetchContent)
	FetchContent_Declare(
		json
		URL https://github.com/nlohmann/json/releases/download/v3.10.4/include.zip
		URL_HASH SHA256=62c585468054e2d8e7c2759c0d990fd339d13be988577699366fe195162d16cb 
	  )
	FetchContent_MakeAvailable(json)
	target_include_directories(co2logger PRIVATE ${json_SOURCE_DIR}/include)
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
