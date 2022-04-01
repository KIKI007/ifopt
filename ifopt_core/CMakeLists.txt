set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})
if (NOT TARGET Eigen3::Eigen)
  message(STATUS "ifopt: `Eigen3` targets not found. Attempting to find package...")
  find_package(Eigen3 3.3.7 REQUIRED NO_MODULE)
else()
  message(STATUS "ifopt: `Eigen3` targets found.")
endif()

###########
## Build ##
###########
add_library(${LIB_CORE}
    src/problem.cc
    src/composite.cc
    src/leaves.cc
)

target_link_libraries(${LIB_CORE} PUBLIC Eigen3::Eigen)

target_include_directories(${LIB_CORE} 
  PUBLIC 
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/test>
    $<INSTALL_INTERFACE:include>)

# better specify features you need, not c++ version
target_compile_features(${LIB_CORE} 
  PUBLIC 
    cxx_range_for 
    cxx_auto_type
)

#############
## Testing ##
#############
find_package(GTest QUIET)
if (TARGET GTest::GTest) # only build when modern target exists
  add_executable(${LIB_CORE}-test 
    test/composite_test.cc
    test/problem_test.cc
  )  
  target_link_libraries(${LIB_CORE}-test  
    PRIVATE 
      ${LIB_CORE} 
      GTest::GTest GTest::Main
  )    
  add_test(${LIB_CORE}-test  ${LIB_CORE}-test)
  
  install(
    TARGETS ${LIB_CORE}-test
    RUNTIME DESTINATION ${IFOPT_INSTALL_BINDIR}
  )
endif()
