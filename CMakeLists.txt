cmake_minimum_required(VERSION 3.16)
project(SuperJupiterMachineState VERSION 0.1.1 LANGUAGES CXX)

set(_superjupiter_machine_state_top_level OFF)
if(CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
  set(_superjupiter_machine_state_top_level ON)
endif()
option(SUPERJUPITER_MACHINE_STATE_BUILD_TESTS "Build MachineState tests" ${_superjupiter_machine_state_top_level})
option(SUPERJUPITER_MACHINE_STATE_BUILD_EXAMPLES "Build MachineState host example" ${_superjupiter_machine_state_top_level})
option(SUPERJUPITER_MACHINE_STATE_INSTALL "Generate MachineState install rules" ${_superjupiter_machine_state_top_level})

add_library(SuperJupiterMachineState INTERFACE)
add_library(SuperJupiter::MachineState ALIAS SuperJupiterMachineState)
set_target_properties(SuperJupiterMachineState PROPERTIES EXPORT_NAME MachineState)
target_include_directories(SuperJupiterMachineState INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
  $<INSTALL_INTERFACE:include>)
target_compile_features(SuperJupiterMachineState INTERFACE cxx_std_11)

if(SUPERJUPITER_MACHINE_STATE_BUILD_TESTS)
  enable_testing()
  add_executable(superjupiter-machine-state-review tests/review_regressions.cpp)
  target_link_libraries(superjupiter-machine-state-review PRIVATE SuperJupiter::MachineState)
  add_test(NAME superjupiter-machine-state-review COMMAND superjupiter-machine-state-review)
  add_executable(superjupiter-machine-state-tests tests/test.cpp)
  target_link_libraries(superjupiter-machine-state-tests PRIVATE SuperJupiter::MachineState)
  add_test(NAME superjupiter-machine-state-tests COMMAND superjupiter-machine-state-tests)
  add_executable(superjupiter-machine-state-namespace-test tests/namespace_isolation.cpp)
  target_link_libraries(superjupiter-machine-state-namespace-test PRIVATE SuperJupiter::MachineState)
  add_test(NAME superjupiter-machine-state-namespace-test COMMAND superjupiter-machine-state-namespace-test)
endif()
if(SUPERJUPITER_MACHINE_STATE_BUILD_EXAMPLES)
  add_executable(superjupiter-machine-state-replay tools/replay.cpp)
  target_link_libraries(superjupiter-machine-state-replay PRIVATE SuperJupiter::MachineState)
endif()
if(SUPERJUPITER_MACHINE_STATE_INSTALL)
  include(CMakePackageConfigHelpers)
  set(_superjupiter_machine_state_package_dir lib/cmake/SuperJupiterMachineState)
  install(TARGETS SuperJupiterMachineState EXPORT SuperJupiterMachineStateTargets)
  install(FILES src/SuperJupiterMachineState.h DESTINATION include)
  install(EXPORT SuperJupiterMachineStateTargets NAMESPACE SuperJupiter::
    DESTINATION ${_superjupiter_machine_state_package_dir})
  configure_package_config_file(cmake/SuperJupiterMachineStateConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterMachineStateConfig.cmake
    INSTALL_DESTINATION ${_superjupiter_machine_state_package_dir})
  write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterMachineStateConfigVersion.cmake
    VERSION ${PROJECT_VERSION} COMPATIBILITY SameMinorVersion ARCH_INDEPENDENT)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterMachineStateConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterMachineStateConfigVersion.cmake
    DESTINATION ${_superjupiter_machine_state_package_dir})
  install(FILES LICENSE DESTINATION share/licenses/SuperJupiterMachineState)
endif()
