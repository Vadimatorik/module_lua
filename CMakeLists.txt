# Минимально необходимая для сборки версия cmake.
CMAKE_MINIMUM_REQUIRED (VERSION 3.13.2)

# Имя библиотеки.
project (LUA)

if (NOT DEFINED ${PROJECT_NAME}_OPTIMIZATION_FLAGS)
    message (FATAL_ERROR
             "The value of the variable <<${PROJECT_NAME}_OPTIMIZATION_FLAGS>> is not specified!")
endif ()

if (NOT DEFINED ${PROJECT_NAME}_COMPILER_FLAGS)
    message (FATAL_ERROR
             "The value of the variable <<${PROJECT_NAME}_COMPILER_FLAGS>> is not specified!")
endif ()

include_directories(
    ${${PROJECT_NAME}_CFG_H_PATH}
    ${CMAKE_CURRENT_SOURCE_DIR}/lua
)

# Компиляции полежат все <<.c>> файлы из директории src.
file (GLOB ${PROJECT_NAME}_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/lua/*.c")

# Собираем библиотеку из .obj файлов, полученных из файлов, добавленных выше.
add_library (${PROJECT_NAME} STATIC ${${PROJECT_NAME}_SOURCES})

# Компиляция файлов происходит по флагам из корневого CMakeList.
target_compile_options (${PROJECT_NAME} PRIVATE
                        "${${PROJECT_NAME}_COMPILER_FLAGS}${${PROJECT_NAME}_OPTIMIZATION_FLAGS}")

# Показ флагов компиляции.
add_custom_command (TARGET ${PROJECT_NAME} POST_BUILD
                    COMMENT
                    "${PROJECT_NAME} flags: ${${PROJECT_NAME}_COMPILER_FLAGS}${${PROJECT_NAME}_OPTIMIZATION_FLAGS}")

# Вывод коммита.
add_custom_command (TARGET ${PROJECT_NAME} POST_BUILD
                    COMMENT "${PROJECT_NAME} commit:"
                    COMMAND git rev-parse HEAD)

# Вывод размера библиотеки.
add_custom_command (TARGET ${PROJECT_NAME} POST_BUILD
                    DEPENDS ${PROJECT_NAME}.a
                    COMMENT "${PROJECT_NAME} size information:"
                    COMMAND cd ${PROJECT_BINARY_DIR}
                    COMMAND ${ARM_SIZE} lib${PROJECT_NAME}.a -t)