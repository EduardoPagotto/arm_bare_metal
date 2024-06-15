cmake_minimum_required(VERSION 3.28)

PROJECT(CROSS_ARM VERSION 1.0.0 LANGUAGES C ASM)
# enable_language(ASM)
# enable_language(C)

get_property(languages GLOBAL PROPERTY ENABLED_LANGUAGES)

#SET(CMAKE_ASM_FLAGS              "-x assembler-with-cpp -g -mcpu=arm926ej-s " CACHE INTERNAL "asm compiler flags")
SET(CMAKE_ASM_FLAGS              "-g -mcpu=arm926ej-s " CACHE INTERNAL "asm compiler flags")

SET(CMAKE_C_FLAGS                "-std=c17 -Wall -mcpu=arm926ej-s -D USE_UART " CACHE INTERNAL "c compiler flags")
SET(CMAKE_C_FLAGS_DEBUG          "-g")
SET(CMAKE_C_FLAGS_MINSIZEREL     "-Os -DNDEBUG")
SET(CMAKE_C_FLAGS_RELEASE        "-O4 -DNDEBUG")
SET(CMAKE_C_FLAGS_RELWITHDEBINFO "-O2 -g")

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/bin)

set(EXECUTABLE_NAME "arm_app")

message(STATUS "**************************************")
message(STATUS "Toolchain file in: \"${CMAKE_TOOLCHAIN_FILE}\"")
message(STATUS "CMAKE_C_FLAGS: \"${CMAKE_C_FLAGS}\"")
message(STATUS "CMAKE_ASM_FLAGS: \"${CMAKE_ASM_FLAGS}\"")
message(STATUS "languages: \"${languages}\"")
message(STATUS "**************************************")

set(sources_c ${CMAKE_SOURCE_DIR}/test.c)
set(sources_a ${CMAKE_SOURCE_DIR}/startup.s)   

#set(CMAKE_ASM_COMPILE_OBJECT "<CMAKE_ASM_COMPILER> -o <OBJECT> <SOURCE>")
#set_property(SOURCE startup.s APPEND PROPERTY COMPILE_OPTIONS "-x" "assembler-with-cpp")

set(libs_def -T${CMAKE_SOURCE_DIR}/test.ld)

add_executable (${EXECUTABLE_NAME}.elf ${sources_c} ${sources_a})
target_link_libraries(${EXECUTABLE_NAME}.elf ${libs_def})

#INSTALL(FILES ${${EXECUTABLE_NAME}_BINARY_DIR}/${EXECUTABLE_NAME} DESTINATION bin)

# Generate the binary file
add_custom_target (${EXECUTABLE_NAME}.bin ALL ${CMAKE_OBJCOPY} -Obinary "${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}/${EXECUTABLE_NAME}.elf"
                                                                        "${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}/${EXECUTABLE_NAME}.bin" 
                                                                        DEPENDS 
                                                                        ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}/${EXECUTABLE_NAME}.elf)

# Generate the hex file
add_custom_target (${CMAKE_PROJECT_NAME}.hex ALL ${CMAKE_OBJCOPY} -Oihex "${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.elf" 
                                                                          "${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.hex" 
                                                                          DEPENDS 
                                                                          ${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.elf)

# Echo the size Infomation
# add_custom_target (size ALL arm-none-eabi-size "${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.elf" 
#                                                DEPENDS
#                                                ${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.elf)

# # Make flash to the board by st-link
# add_custom_target (flash COMMAND st-flash write ${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.bin 0x8000000)

# # Make clean-all
add_custom_target (clean-all COMMAND rm -rf ${CMAKE_BINARY_DIR}/*)