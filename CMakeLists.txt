cmake_minimum_required(VERSION 3.15)

project(WINRT_TEST VERSION 1.0.0 LANGUAGES CXX)

find_program(NUGET NAMES nuget)
if(NOT NUGET)
	message("-- NuGet not found on system sreaching local files")
	find_program(NUGET ${CMAKE_SOURCE_DIR}/packages/nuget.exe)
	if(NOT NUGET)
		message("-- NuGet not found in local files. Downloading nuget please wait...")
		file(DOWNLOAD "https://dist.nuget.org/win-x86-commandline/latest/nuget.exe" "${CMAKE_SOURCE_DIR}/packages/nuget.exe" SHOW_PROGRESS STATUS status LOG log)
		find_program(NUGET ${CMAKE_SOURCE_DIR}/packages/nuget.exe REQUIRED)
	endif(NOT NUGET)
endif(NOT NUGET)

exec_program(${NUGET} ARGS install "Microsoft.Windows.CppWinRT" -ExcludeVersion -OutputDirectory ${CMAKE_BINARY_DIR}/packages)

add_executable(${PROJECT_NAME})

target_sources(${PROJECT_NAME} PUBLIC ${CMAKE_SOURCE_DIR}/src/main.cpp)

set_target_properties(${PROJECT_NAME} PROPERTIES VS_PROJECT_IMPORT ${CMAKE_BINARY_DIR}/packages/Microsoft.Windows.CppWinRT/build/native/Microsoft.Windows.CppWinRT.props)

target_link_libraries(${PROJECT_NAME} PRIVATE ${CMAKE_BINARY_DIR}/packages/Microsoft.Windows.CppWinRT/build/native/Microsoft.Windows.CppWinRT.targets)

target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_23)
