cmake_minimum_required(VERSION 3.10)
set(PROJECT_NAME "isar_community_flutter_libs")
project(${PROJECT_NAME} LANGUAGES CXX)

# This value is used when generating builds using this plugin, so it must
# not be changed
set(PLUGIN_NAME "isar_community_flutter_libs_plugin")

# Download libisar.so if not present (e.g. when using the package as a git dependency)
set(ISAR_LIB "${CMAKE_CURRENT_SOURCE_DIR}/libisar.so")
if(NOT EXISTS "${ISAR_LIB}")
  set(ISAR_BINARIES_TAG "binaries-libmdbx-0.14.3")
  if(CMAKE_SYSTEM_PROCESSOR MATCHES "aarch64|arm64")
    set(ISAR_ARCH "arm64")
  else()
    set(ISAR_ARCH "x64")
  endif()
  set(ISAR_DOWNLOAD_URL "https://github.com/frmoraess/isar-community/releases/download/${ISAR_BINARIES_TAG}/libisar_linux_${ISAR_ARCH}.so")
  message(STATUS "Downloading libisar.so from ${ISAR_DOWNLOAD_URL}")
  file(DOWNLOAD "${ISAR_DOWNLOAD_URL}" "${ISAR_LIB}" SHOW_PROGRESS STATUS DOWNLOAD_STATUS)
  list(GET DOWNLOAD_STATUS 0 STATUS_CODE)
  if(NOT STATUS_CODE EQUAL 0)
    message(FATAL_ERROR "Failed to download libisar.so: ${DOWNLOAD_STATUS}")
  endif()
endif()

add_library(${PLUGIN_NAME} SHARED
  "isar_community_flutter_libs_plugin.cc"
)
apply_standard_settings(${PLUGIN_NAME})
set_target_properties(${PLUGIN_NAME} PROPERTIES
  CXX_VISIBILITY_PRESET hidden)
target_compile_definitions(${PLUGIN_NAME} PRIVATE FLUTTER_PLUGIN_IMPL)
target_include_directories(${PLUGIN_NAME} INTERFACE
  "${CMAKE_CURRENT_SOURCE_DIR}/include")
target_link_libraries(${PLUGIN_NAME} PRIVATE flutter)
target_link_libraries(${PLUGIN_NAME} PRIVATE PkgConfig::GTK)

set(isar_community_flutter_libs_bundled_libraries
  "${ISAR_LIB}"
  PARENT_SCOPE
)
