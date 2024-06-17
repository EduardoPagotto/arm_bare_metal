cmake_minimum_required(VERSION 3.28)

PROJECT(app_arm)

# SET(CMAKE_ASM_FLAGS              "-x assembler-with-cpp -g -mcpu=arm926ej-s " CACHE INTERNAL "asm compiler flags")
# SET(CMAKE_ASM_FLAGS              "-g -mcpu=arm926ej-s " CACHE INTERNAL "asm compiler flags")
# SET(CMAKE_C_FLAGS                "-std=c17 -Wall -mcpu=arm926ej-s -D USE_UART " CACHE INTERNAL "c compiler flags")
# SET(CMAKE_C_FLAGS                "-std=c17 -Wall -mfloat-abi=soft -D USE_UART " CACHE INTERNAL "c compiler flags")
# SET(CMAKE_C_FLAGS_DEBUG          "-g")
# SET(CMAKE_C_FLAGS_MINSIZEREL     "-Os -DNDEBUG")
# SET(CMAKE_C_FLAGS_RELEASE        "-O4 -DNDEBUG")
# SET(CMAKE_C_FLAGS_RELWITHDEBINFO "-O2 -g")

message(STATUS "**************************************")
message(STATUS "CMAKE_C_FLAGS: \"${CMAKE_C_FLAGS}\"")
message(STATUS "CMAKE_ASM_FLAGS: \"${CMAKE_ASM_FLAGS}\"")
message(STATUS "**************************************")

set(EXECUTABLE ${PROJECT_NAME}.elf)

set(GENERATED_FILES ${CMAKE_SOURCE_DIR}/src/test.c
                    ${CMAKE_SOURCE_DIR}/src/startup.s) 

#SET(ARCH_FLAGS "--target=arm -mcpu=arm926ej-s -mfloat-abi=soft")
#SET(ARCH_FLAGS "--target=arm -mcpu=arm926ej-s -mfloat-abi=soft -mfpu=NEON")
SET(ARCH_FLAGS "--target=armv7a-unknow-none-eabi") # https://clang.llvm.org/docs/CrossCompilation.html <- TARGET TRIPLE SET!!!!!!

set(COMPILE_DEFS ${ARCH_FLAGS}
                 -nostdlib
                 -static
                 -Wall
                 -Wextra
                 -c
                 #-mcmodel=medany
)

set(LINK_DEFS -T${CMAKE_SOURCE_DIR}/src/test.ld
              ${ARCH_FLAGS}
              -nostdlib
              #-L.
              -Wl)


add_executable (${EXECUTABLE} ${GENERATED_FILES})

target_compile_options(${EXECUTABLE} PRIVATE ${COMPILE_DEFS})
target_link_libraries(${EXECUTABLE} ${LINK_DEFS})

# # Generate the binary file
# add_custom_target (${EXECUTABLE_NAME}.bin ALL ${CMAKE_OBJCOPY} -Obinary "${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}/${EXECUTABLE_NAME}.elf"
#                                                                         "${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}/${EXECUTABLE_NAME}.bin" 
#                                                                         DEPENDS 
#                                                                         ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}/${EXECUTABLE_NAME}.elf)

# # Generate the hex file
# add_custom_target (${CMAKE_PROJECT_NAME}.hex ALL ${CMAKE_OBJCOPY} -Oihex "${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.elf" 
#                                                                           "${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.hex" 
#                                                                           DEPENDS 
#                                                                           ${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.elf)

# Echo the size Infomation
# add_custom_target (size ALL arm-none-eabi-size "${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.elf" 
#                                                DEPENDS
#                                                ${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.elf)

# # Make flash to the board by st-link
# add_custom_target (flash COMMAND st-flash write ${EXECUTABLE_OUTPUT_PATH}/${CMAKE_PROJECT_NAME}.bin 0x8000000)
