TOOLCHAIN = /opt/gcc-arm-none-eabi-10.3-2021.10
CC := $(TOOLCHAIN)/bin/arm-none-eabi-gcc
AS := $(TOOLCHAIN)/bin/arm-none-eabi-as
LD := $(TOOLCHAIN)/bin/arm-none-eabi-ld 
OBJCOPY := $(TOOLCHAIN)/bin/arm-none-eabi-objcopy
LDSCRIPT := test.ld
#bin := test.bin

all: startup.o test.o
	$(LD) -T $(LDSCRIPT) test.o startup.o -o test.elf
	$(OBJCOPY) -O binary test.elf test.bin

startup.o : startup.s 
	$(AS) -mcpu=arm926ej-s -g startup.s -o startup.o

test.o : test.c
	$(CC) -c -mcpu=arm926ej-s -g test.c -o test.o

clean:
	rm -f *.o *.elf *.bin