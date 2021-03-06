set(tests
    active_set_algorithm
    least_squares_solver
    )

add_custom_target(test_build)
foreach(test_name ${tests})
    add_executable(${test_name}
        ${test_name}.cpp)
    add_test(test_${test_name} ${test_name})
    add_dependencies(test_build ${test_name})
endforeach()

if (${CMAKE_BUILD_TYPE} STREQUAL "Coverage")

    add_custom_target(coverage_build
        COMMAND ${CMAKE_CTEST_COMMAND}
        COMMAND lcov --capture --directory . --output-file coverage.info
        COMMAND lcov --summary coverage.info
        WORKING_DIRECTORY ${CMAKE_BUILD_DIR}
        DEPENDS test_build
        )

    add_custom_target(coverage_html
        COMMAND genhtml coverage.info --output-directory out
        COMMAND x-www-browser out/index.html
        WORKING_DIRECTORY ${CMAKE_BUILD_DIR}
        DEPENDS coverage_build
        )

    set(coverage_deps
        coverage_build)

    if (COV_HTML)
        list(APPEND coverage_deps coverage_html)
    endif()

    add_custom_target(coverage
        DEPENDS ${coverage_deps}
        )
endif()

# vim: set et fenc=utf-8 ft=cmake ff=unix sts=0 sw=4 ts=4 :
