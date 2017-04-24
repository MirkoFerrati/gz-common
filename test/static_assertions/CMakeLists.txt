set(static_assert_tests
  plugin_no_base_class
  plugin_not_fully_qualified_class
  plugin_not_fully_qualified_base
  plugin_wrong_base_class
  plugin_class_repeated
  plugin_not_global_ns
  )

# Make tests that try to build these files
foreach (test ${static_assert_tests})
  add_executable(${test} ${test}.cc)
  set_target_properties(${test} PROPERTIES
    EXCLUDE_FROM_ALL TRUE
    EXCLUDE_FROM_DEFAULT_BUILD TRUE)
  add_test(NAME STATIC_ASSERT_${test}
      COMMAND ${CMAKE_COMMAND} --build . --target ${test} --config $<CONFIGURATION>
      WORKING_DIRECTORY ${CMAKE_BINARY_DIR})
endforeach()

# Tests pass if the output contains the expected error

set_tests_properties(STATIC_ASSERT_plugin_no_base_class PROPERTIES
  PASS_REGULAR_EXPRESSION "A must inherit from NotBase")

set_tests_properties(STATIC_ASSERT_plugin_class_repeated PROPERTIES
  PASS_REGULAR_EXPRESSION "Class and Base class must be different")

 # TODO Fix FQN name check
set_tests_properties(STATIC_ASSERT_plugin_not_fully_qualified_class PROPERTIES WILL_FAIL TRUE)
set_tests_properties(STATIC_ASSERT_plugin_not_fully_qualified_base PROPERTIES WILL_FAIL TRUE)
# set_tests_properties(STATIC_ASSERT_plugin_not_fully_qualified_class PROPERTIES
#  PASS_REGULAR_EXPRESSION "A must be fully qualified like ::ns::MyClass")
#
# set_tests_properties(STATIC_ASSERT_plugin_not_fully_qualified_base PROPERTIES
#   PASS_REGULAR_EXPRESSION "Base must be fully qualified like ::ns::BaseClass")

set_tests_properties(STATIC_ASSERT_plugin_not_global_ns PROPERTIES
  PASS_REGULAR_EXPRESSION "IGN_macro_must_be_used_in_global_namespace")

set_tests_properties(STATIC_ASSERT_plugin_wrong_base_class PROPERTIES
  PASS_REGULAR_EXPRESSION "ns::A must inherit from ns::WrongBase")