cmake_minimum_required(VERSION 2.8.9)
cmake_policy(SET CMP0048 NEW)

set(core_tests bgptest;core_test;test_entity;test_value;test_value_list;test_capabilities_parser)

find_package(Boost COMPONENTS log_setup log thread date_time system filesystem unit_test_framework REQUIRED)

include_directories(SYSTEM ${Boost_INCLUDE_DIRS})
set(Boost_USE_STATIC_LIBS        OFF)
set(Boost_USE_MULTITHREADED      ON)
set(Boost_USE_STATIC_RUNTIME     OFF)
set(BOOST_ALL_DYN_LINK           ON)   # force dynamic linking for all libraries

set(BOOST_INCLUDE_DIRS $boost_installation_prefix/include)

set(CMAKE_CXX_FLAGS         "${CMAKE_CXX_FLAGS} -Wall -Wextra")
set(CMAKE_CXX_FLAGS_RELEASE "-O2 -DNDEBUG")
set(CMAKE_CXX_FLAGS_DEBUG   "-g3 -O0 -fprofile-arcs -ftest-coverage")

foreach(test_name IN LISTS core_tests)
    add_executable(${test_name} ${test_name}.cpp)
    set_property(TARGET ${test_name} PROPERTY CXX_STANDARD 11)
    set_property(TARGET ${test_name} PROPERTY CXX_STANDARD_REQUIRED ON)
    #target_include_directories(${test_name} PRIVATE ${BOOST_INCLUDE_DIRS})
    #target_compile_definitions(${test_name} PRIVATE "BOOST_TEST_DYN_LINK=1")
    target_link_libraries(${test_name} ydk ${Boost_UNIT_TEST_FRAMEWORK_LIBRARY} ${Boost_LOG_SETUP_LIBRARY}
        ${Boost_LOG_LIBRARY}
        ${BOOST_THREAD_LIBRARY}
        ${BOOST_DATE_TIME_LIBRARY}
        ${Boost_FILESYSTEM_LIBRARY}
        ${Boost_SYSTEM_LIBRARY}
        curl
        xslt
        pcre
        ssh_threads
        xml2
        ssh)
    target_compile_definitions(${test_name} PRIVATE "BOOST_ALL_DYN_LINK=1")
    target_compile_definitions(${test_name} PRIVATE "BOOST_LOG_DYN_LINK=1")
    
    add_test(NAME ${test_name} COMMAND $<TARGET_FILE:${test_name}>)
endforeach(test_name)



configure_file("${PROJECT_SOURCE_DIR}/tests/config.hpp.in" "${PROJECT_SOURCE_DIR}/tests/config.hpp" ESCAPE_QUOTES @ONLY)
