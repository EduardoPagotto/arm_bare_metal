cmake_minimum_required(VERSION 3.28)

PROJECT(app_arm)

# SET(CMAKE_ASM_FLAGS              "-x assembler-with-cpp -g -mcpu=arm926ej-s " CACHE INTERNAL "asm compiler flags")
# SET(CMAKE_C_FLAGS                "-std=c17 -Wall -mcpu=arm926ej-s -D USE_UART " CACHE INTERNAL "c compiler flags")
# SET(CMAKE_C_FLAGS_DEBUG          "-g")
# SET(CMAKE_C_FLAGS_MINSIZEREL     "-Os -DNDEBUG")
# SET(CMAKE_C_FLAGS_RELEASE        "-O4 -DNDEBUG")
# SET(CMAKE_C_FLAGS_RELWITHDEBINFO "-O2 -g")

set(EXECUTABLE ${PROJECT_NAME}.elf)

set(GENERATED_FILES ${CMAKE_SOURCE_DIR}/src/test.c
                    ${CMAKE_SOURCE_DIR}/src/startup.s) 

SET(ARCH_FLAGS "--target=armv7a-unknow-none-eabi") # https://clang.llvm.org/docs/CrossCompilation.html <- TARGET TRIPLE SET!!!!!!

set(COMPILE_DEFS ${ARCH_FLAGS}
                 -nostdlib
                 -static
                 -Wall
                 -Wextra
                 -c)

set(LINK_DEFS -T${CMAKE_SOURCE_DIR}/src/test.ld
              ${ARCH_FLAGS}
              -nostdlib
              #-L.
              -Wl)

add_executable (${EXECUTABLE} ${GENERATED_FILES})

target_compile_options(${EXECUTABLE} PRIVATE ${COMPILE_DEFS})
target_link_libraries(${EXECUTABLE} ${LINK_DEFS})

# Create aux files
add_custom_command(TARGET ${EXECUTABLE}
        POST_BUILD
        COMMAND ${RISCV_SIZE} ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${EXECUTABLE} # Print executable size
        COMMAND ${RISCV_OBJDUMP} -D ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${EXECUTABLE} > ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}/${PROJECT_NAME}.list
        COMMAND ${RISCV_OBJCOPY} -O ihex ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${EXECUTABLE} ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}/${PROJECT_NAME}.hex
        COMMAND ${RISCV_OBJCOPY} -O binary ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${EXECUTABLE} ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}/${PROJECT_NAME}.bin
        COMMAND ${RISCV_OBJDUMP} -d ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${EXECUTABLE} -l > ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}/${PROJECT_NAME}.s
)

# # Make flash to the board by st-link
# add_custom_target (flash COMMAND st-flash write ${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.bin 0x8000000)
