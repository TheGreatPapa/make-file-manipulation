#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# <Put a Description Here>
#
# Use: make [TARGETS] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <FILE>.o - Builds <FILE>.o object file
#      <FILE>.i - Builds <FILE>.i object file
#      <FILE>.asm - Builds <FILE>.asm object file
#
#	   compile-all - Compile all the files without building
#	   build - builds and links all source files
#	   clean - Removes all generated files
#
# Platform Overrides:
#      CPU - ARM Cortex Architecture (cortex-m0plus, cortex-m4)
#      ARCH - ARM Architecture (arm, thumb)
#      SPECS - Specs file to give the linker (nosys.specs, nano.specs)
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides

ifeq ($(PLATFORM),MSP432)
# Compiler Flags and Defines
CC = arm-none-eabi-gcc
LD = arm-none-eabi-ld
LINKER_FILE = ../msp432p401r.lds
LDFLAGS = -Wl,-Map=$(TARGET).map -T $(LINKER_FILE)
SIZE = arm-none-eabi-size
# Architecture Specific Flags
CPU = cortex-m4
ARCH = thumb
MARCH = armv7e-m
FLOATABI = hard
FPU = fpv4-sp-d16
SPECS = nosys.specs

ARM_FLAGS = -mcpu=$(CPU) -m$(ARCH) -march=$(MARCH) -mfloat-abi=$(FLOATABI) -mfpu=$(FPU) --specs=$(SPECS)
OBJDUMP = arm-none-eabi-objdump

endif

ifeq ($(PLATFORM),HOST)
# Compiler Flags and Defines
CC = gcc
LD = ld
SIZE = size
LDFLAGS = -Wl,-Map=$(TARGET).map
OBJDUMP = objdump

endif

#TARGET
TARGET = c1m2

# Compiler Flags and Defines
 
CFLAGS = -Wall -Werror -g -O0 -std=c99 -D$(PLATFORM) $(ARM_FLAGS)
CPPFLAGS = -E

OBJS = $(SOURCES:.c=.o)
PREPRS = $(SOURCES:.c=.i)
ASMS = $(SOURCES:.c=.asm)
DEPS = $(SOURCES:.c=.d)

%.i : %.c
	$(CC) $(INCLUDES) $(CPPFLAGS) $< $(CFLAGS) -o $@

%.asm : %.c
	$(CC) $(INCLUDES) -S $< $(CFLAGS) -o $@

%.o : %.c
	$(CC) $(INCLUDES) -c $< $(CFLAGS) -o $@

%.d : %.c
	$(CC) $(INCLUDES) -M $< $(CFLAGS) -o $@

$(TARGET).asm : $(TARGET).out
	$(OBJDUMP) -d $(TARGET).out >> $@


.PHONY: build
build: $(TARGET).out $(TARGET).asm

.PHONY: compile-all
compile-all: $(OBJS)
	$(CC) $(INCLUDES) $(OBJS) $(CFLAGS) -o $(TARGET).o

$(TARGET).out: $(OBJS) $(DEPS)
	$(CC) $(CFLAGS) $(INCLUDES) $(LDFLAGS) -o $@ $(OBJS)

.PHONY: clean
clean:
	rm -f $(PREPRS) $(ASMS) $(TARGET).out $(TARGET).map $(TARGET).asm
	rm -f *.o *.i *.asm *.d