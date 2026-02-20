cmake_minimum_required(VERSION 3.20)

add_subdirectory(libs)

project(Photogrammetry)

set(CMAKE_CXX_STANDARD 17)

# OpenMP позволит распараллеливать циклы на все ядра процессора простыми директивами
find_package(OpenMP)
if (OpenMP_CXX_FOUND)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} ${OpenMP_EXE_LINKER_FLAGS}")
else()
    message(FATAL_ERROR "OpenMP not found! You need OpenMP for speedup on multicore CPUs!")
endif()

find_package(OpenCV 4.11.0 REQUIRED)
#      Linux:
# sudo .github/scripts/install_opencv_linux.sh
# укажите для CMake путь к OpenCV:
# в CLion это делается через File->Settings->Build, Execution, Deployment->CMake->CMake options: -DOpenCV_DIR=/opt/opencv4.11.0/lib/cmake/opencv4
# там же сразу стоит добавить в дополнению к Debug сборке - сборку с включенными оптимизациями и отладочными символами - RelWithDebInfo
#
#     Windows: (но рекомендуется использовать Linux)
# Скачайте этот скрипт и следуйте инструкции - https://gist.github.com/PolarNick239/18036d942ae4b9aad9f1da756ce6c845#file-buildocv-sh-L10-L33

set(SOURCES
        src/phg/sift/sift.cpp
        src/phg/sift/sift.h
        )

set(LIBRARIES
        libutils
        ${OpenCV_LIBS}
        )

add_library(${PROJECT_NAME} ${SOURCES})
target_link_libraries(${PROJECT_NAME} ${LIBRARIES})
target_include_directories(${PROJECT_NAME} PUBLIC ${PROJECT_SOURCE_DIR}/src)

set(TEST_SOURCES
        tests/utils/test_utils.cpp
        tests/utils/test_utils.h
        )

set(TEST_LIBRARIES
        gtest
        gtest_main
        )

add_executable(test_sift tests/test_sift.cpp ${TEST_SOURCES})
target_link_libraries(test_sift ${PROJECT_NAME} ${LIBRARIES} ${TEST_LIBRARIES})
