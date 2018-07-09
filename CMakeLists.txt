cmake_minimum_required(VERSION 3.9)
project(cxxabitest)

set(CMAKE_CXX_STANDARD REQUIRED)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD 14)

# Need Boost.Unit for test
find_package(Boost 1.66 REQUIRED unit_test_framework)

# - Art "shim" library
add_library(art_suite_shim SHARED
  art_suite_shim/cetlib_except/demangle.h
  art_suite_shim/cetlib_except/coded_exception.h
  art_suite_shim/cetlib_except/exception.cc
  art_suite_shim/cetlib_except/demangle.cc
  art_suite_shim/cetlib_except/exception.h
  art_suite_shim/canvas/Utilities/Exception.cc
  art_suite_shim/canvas/Utilities/Exception.h
  art_suite_shim/cetlib/quiet_unit_test.hpp
  )
target_include_directories(art_suite_shim PUBLIC ${PROJECT_SOURCE_DIR}/art_suite_shim)

# - Native abi
add_library(maybeCast_native SHARED maybeCastObj.h maybeCastObj.cc)
target_link_libraries(maybeCast_native PUBLIC art_suite_shim)

add_executable(maybeCastObjNative_t maybeCastObj_t.cc)
target_compile_definitions(maybeCastObjNative_t PRIVATE BOOST_TEST_DYN_LINK BOOST_TEST_MAIN)
target_link_libraries(maybeCastObjNative_t PRIVATE maybeCast_native Boost::unit_test_framework)


# - Root TClass system
find_package(ROOT)
if(ROOT_FOUND)
  add_library(maybeCast_root SHARED maybeCastObj.h maybeCastObj.cc)
  target_compile_definitions(maybeCast_root PRIVATE CANVAS_USE_ROOTCPPABI)
  target_include_directories(maybeCast_root PRIVATE ${ROOT_INCLUDE_DIRS})
  target_link_libraries(maybeCast_root PUBLIC art_suite_shim ROOT::Core)

  add_executable(maybeCastObjRoot_t maybeCastObj_t.cc)
  target_compile_definitions(maybeCastObjRoot_t PRIVATE BOOST_TEST_DYN_LINK BOOST_TEST_MAIN)
  target_link_libraries(maybeCastObjRoot_t PRIVATE maybeCast_root Boost::unit_test_framework)
endif()



