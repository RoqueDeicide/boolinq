# Common variables.
SET (CMAKE_CXX_STANDARD 11)
SET (CMAKE_CXX_STANDARD_REQUIRED ON)
SET (TARGET "${PROJECT_NAME}-test" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Wmissing-include-dirs -Wfloat-equal -Wshadow")
SET (TARGET "${PROJECT_NAME}-test" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wdouble-promotion -Weffc++ -Woverloaded-virtual -Wsign-promo")
SET (TARGET "${PROJECT_NAME}-test" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wvla -Winvalid-pch -Winline -Wredundant-decls -Wcast-align")
SET (TARGET "${PROJECT_NAME}-test" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wcast-qual -Wpointer-arith -Wold-style-cast")
SET (TARGET "${PROJECT_NAME}-test" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fprofile-arcs -ftest-coverage")
SET (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fprofile-arcs -ftest-coverage")


INCLUDE_DIRECTORIES (${PROJECT_SOURCE_DIR}/include/boolinq)
INCLUDE_DIRECTORIES (${CMAKE_BINARY_DIR}/googletest-src/googletest/include)

# Unit tests.
SET (
    BoolinqTest_SOURCES
    ${PROJECT_SOURCE_DIR}/test/AllTest.cpp
    ${PROJECT_SOURCE_DIR}/test/AnyTest.cpp
    ${PROJECT_SOURCE_DIR}/test/AvgTest.cpp
    ${PROJECT_SOURCE_DIR}/test/AppendTest.cpp
    ${PROJECT_SOURCE_DIR}/test/BitsTest.cpp
    ${PROJECT_SOURCE_DIR}/test/BytesTest.cpp
    ${PROJECT_SOURCE_DIR}/test/ConcatTest.cpp
    ${PROJECT_SOURCE_DIR}/test/ContainsTest.cpp
    ${PROJECT_SOURCE_DIR}/test/CountTest.cpp
    ${PROJECT_SOURCE_DIR}/test/DistinctTest.cpp
    ${PROJECT_SOURCE_DIR}/test/DotCallTest.cpp
    ${PROJECT_SOURCE_DIR}/test/ElementAtTest.cpp
    ${PROJECT_SOURCE_DIR}/test/FirstTest.cpp
    ${PROJECT_SOURCE_DIR}/test/ForEachTest.cpp
    ${PROJECT_SOURCE_DIR}/test/GroupByTest.cpp
    ${PROJECT_SOURCE_DIR}/test/CtorTest.cpp
    ${PROJECT_SOURCE_DIR}/test/LastTest.cpp
    ${PROJECT_SOURCE_DIR}/test/LinqTest.cpp
    ${PROJECT_SOURCE_DIR}/test/MaxTest.cpp
    ${PROJECT_SOURCE_DIR}/test/MinTest.cpp
    ${PROJECT_SOURCE_DIR}/test/OrderByTest.cpp
    ${PROJECT_SOURCE_DIR}/test/PrependTest.cpp
    ${PROJECT_SOURCE_DIR}/test/RangeTest.cpp
    ${PROJECT_SOURCE_DIR}/test/ReverseTest.cpp
    ${PROJECT_SOURCE_DIR}/test/SelectTest.cpp
    ${PROJECT_SOURCE_DIR}/test/SelectManyTest.cpp
    ${PROJECT_SOURCE_DIR}/test/SkipTest.cpp
    ${PROJECT_SOURCE_DIR}/test/SkipWhileTest.cpp
    ${PROJECT_SOURCE_DIR}/test/SumTest.cpp
    ${PROJECT_SOURCE_DIR}/test/TakeTest.cpp
    ${PROJECT_SOURCE_DIR}/test/TakeWhileTest.cpp
    ${PROJECT_SOURCE_DIR}/test/ToStdDequeTest.cpp
    ${PROJECT_SOURCE_DIR}/test/ToStdListTest.cpp
    ${PROJECT_SOURCE_DIR}/test/ToStdSetTest.cpp
    ${PROJECT_SOURCE_DIR}/test/ToStdVectorTest.cpp
    ${PROJECT_SOURCE_DIR}/test/UnbitsTest.cpp
    ${PROJECT_SOURCE_DIR}/test/UnbytesTest.cpp
    ${PROJECT_SOURCE_DIR}/test/WhereTest.cpp
)
ADD_EXECUTABLE (
    "${PROJECT_NAME}-test"
    ${BoolinqTest_SOURCES}
)

TARGET_LINK_LIBRARIES (
    "${PROJECT_NAME}-test"
    gtest_main
    gtest
    #gcov
)

#separate target for the complex group by test requiring C++14
ADD_EXECUTABLE (
    "${PROJECT_NAME}-test14"
    ${PROJECT_SOURCE_DIR}/test/GroupByTestComplex.cpp
)

TARGET_COMPILE_FEATURES("${PROJECT_NAME}-test14" PRIVATE cxx_std_14)

TARGET_LINK_LIBRARIES (
    "${PROJECT_NAME}-test14"
    gtest_main
    gtest
)

ENABLE_TESTING ()
ADD_TEST (BoolinqTest "${PROJECT_NAME}-test")
ADD_TEST (BoolinqTest14 "${PROJECT_NAME}-test14")

# Test coverage report.
SET (Coverage_REPORT ${PROJECT_BINARY_DIR}/coverage.info)
SET (Coverage_DIR    ${PROJECT_BINARY_DIR}/coverage)
ADD_CUSTOM_COMMAND (
    OUTPUT  ${Coverage_REPORT}
    COMMAND lcov -q -c -f -b . -d ${PROJECT_BINARY_DIR}/ -o ${Coverage_REPORT}
    COMMAND lcov -e ${Coverage_REPORT} '${PROJECT_SOURCE_DIR}/*' -o ${Coverage_REPORT}
    COMMAND genhtml ${Coverage_REPORT} --legend --demangle-cpp -f -q -o ${Coverage_DIR}
    DEPENDS "${PROJECT_NAME}-test" "${PROJECT_NAME}-test14"
)
ADD_CUSTOM_TARGET (coverage DEPENDS ${Coverage_REPORT})
LIST (APPEND Coverage_DATA "${Coverage_REPORT}")
LIST (APPEND Coverage_DATA "${Coverage_DIR}")
LIST (APPEND Coverage_DATA "${PROJECT_BINARY_DIR}/test/CMakeFiles/boolinq-test.dir")
SET_DIRECTORY_PROPERTIES (PROPERTIES ADDITIONAL_MAKE_CLEAN_FILES "${Coverage_DATA}")
