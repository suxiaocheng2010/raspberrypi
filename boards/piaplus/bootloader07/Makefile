
ARMGNU ?= arm-none-eabi

COPS = -Wall -O2 -nostdlib -nostartfiles -ffreestanding 

gcc : kernel.img 

all : gcc clang

clean :
	rm -f *.o
	rm -f *.bin
	rm -f *.hex
	rm -f *.elf
	rm -f *.list
	rm -f *.img
	rm -f *.bc
	rm -f *.clang.s


vectors.o : vectors.s
	$(ARMGNU)-as vectors.s -o vectors.o

bootloader07.o : bootloader07.c
	$(ARMGNU)-gcc $(COPS) -c bootloader07.c -o bootloader07.o

periph.o : periph.c 
	$(ARMGNU)-gcc $(COPS) -c periph.c -o periph.o

kernel.img : loader vectors.o periph.o bootloader07.o 
	$(ARMGNU)-ld vectors.o periph.o bootloader07.o -T loader -o bootloader07_rpi1.elf
	$(ARMGNU)-objdump -D bootloader07_rpi1.elf > bootloader07_rpi1.list
	$(ARMGNU)-objcopy bootloader07_rpi1.elf -O ihex bootloader07_rpi1.hex
	$(ARMGNU)-objcopy bootloader07_rpi1.elf -O binary kernel.img

LOPS = -Wall -m32 -emit-llvm
LLCOPS0 = -march=arm 
LLCOPS1 = -march=arm -mcpu=arm1176jzf-s
LLCOPS = $(LLCOPS1)
COPS = -Wall  -O2 -nostdlib -nostartfiles -ffreestanding
OOPSx = -std-compile-opts
OOPS = -std-link-opts

clang : bootloader07.clang.bin

bootloader07.bc : bootloader07.c
	clang $(LOPS) -c bootloader07.c -o bootloader07.bc

periph.bc : periph.c
	clang $(LOPS) -c periph.c -o periph.bc

bootloader07.clang.elf : loader vectors.o bootloader07.bc periph.bc
	llvm-link periph.bc bootloader07.bc -o bootloader07.nopt.bc
	opt $(OOPS) bootloader07.nopt.bc -o bootloader07.opt.bc
	llc $(LLCOPS) bootloader07.opt.bc -o bootloader07.clang.s
	$(ARMGNU)-as bootloader07.clang.s -o bootloader07.clang.o
	$(ARMGNU)-ld -o bootloader07.clang.elf -T loader vectors.o bootloader07.clang.o
	$(ARMGNU)-objdump -D bootloader07.clang.elf > bootloader07.clang.list

bootloader07.clang.bin : bootloader07.clang.elf
	$(ARMGNU)-objcopy bootloader07.clang.elf bootloader07.clang.bin -O binary


