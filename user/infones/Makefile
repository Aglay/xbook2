# makefile for test

#The tools name
NASM 		= nasm
CC			= gcc
LD 			= ld
DD 			= dd
OBJDUMP		= objdump

#The file path
DIR_LIBARY = ../../libary

# libarys
DIR_XBOOKLIB = $(DIR_LIBARY)/xbooklib
DIR_SGI = $(DIR_LIBARY)/sgi

BIN = a.out
BIN_DIS = out.disasm
BIN_OFF = 4400 
BIN_CNTS = 650

#img dir
IMG_DIR = ../../develop/image/

DISK_IMG = c.img
DISK_VHD = c.vhd
DISK_VMDK = c-flat.vmdk

HD_IMG = $(IMG_DIR)$(DISK_IMG)
HD_VHD = $(IMG_DIR)$(DISK_VHD)
HD_VMDK = $(IMG_DIR)$(DISK_VMDK)

#flags
ASM_FLAGS	= -f elf \
			-I $(DIR_XBOOKLIB)/include 

C_FLAGS		= -c -fno-builtin -Wall -Wunused -m32 -std=c99\
			-I $(DIR_XBOOKLIB)/include \
			-I $(DIR_SGI)/include \
			-I core 

LDFLAGS		= -m elf_i386 -e _start -Ttext 0x00001000

OBJS =  infoNES_System_Book.o \

LIB_FILE := $(DIR_SGI)/sgi-0.0.1.a \
			$(DIR_XBOOKLIB)/xbooklib-0.0.1.a \
			

#First read here
.PHONY: all dis

all: compile link disk 

#Compile files
compile: $(OBJS)

link: $(BIN)
$(BIN): $(OBJS) $(LIB_FILE)
	$(LD) $(LDFLAGS) -o $(BIN) $(OBJS) $(LIB_FILE)


dis: $(BIN)
	$(OBJDUMP) -M intel -D $(BIN) > $(BIN_DIS)

disk: img vhd vmdk

img:
	$(DD) if=$(BIN) of=$(HD_IMG) bs=512 seek=$(BIN_OFF) count=$(BIN_CNTS) conv=notrunc
	
vhd:
	$(DD) if=$(BIN) of=$(HD_VHD) bs=512 seek=$(BIN_OFF) count=$(BIN_CNTS) conv=notrunc
	
vmdk:
	$(DD) if=$(BIN) of=$(HD_VMDK) bs=512 seek=$(BIN_OFF) count=$(BIN_CNTS) conv=notrunc

qemu: compile link img
	
vbox: compile link vhd

vmware: compile link vmdk

#Clean temporary files
clean:
	-rm *.o
	-rm $(BIN)

%.o: %.asm
	$(NASM) $(ASM_FLAGS) -o $@ $<
	
%.o: %.c
	$(CC) $(C_FLAGS) -o $@ $<
