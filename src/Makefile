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
# Simple makefile for the MSP432
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <Put a description of the supported targets here>
#
# Platform Overrides:
#      CPU - ARM Cortex Architecture (cortex-m0plus, cortex-m4)
#	   ARCH - ARM Architecture (arm, thumb)
#	   SPECS - Specs file to give the linker (nosys.specs, nano.specs)
#
#
#------------------------------------------------------------------------------
#‘src’ folder : contains five source files (*.c)
#Makefile - The makefile you are to edit for the assignment,sources.mk - The source file you are to edit for the assignment
#main.c - Main file you are to work with, memory.c - File that interacts with memory through an IO abstraction interface
#interrupts_msp432p401r_gcc.c - MSP432 specific C-file for interrupts,startup_msp432p401r_gcc.c - MSP432 specific C-file for startup

#‘include’ folder : contains the three directories of supporting header files
#common - Contains common headers for both platform targets, msp432 - Contains MSP432 platform headers,CMSIS - Contains ARM architecture specific headers

#include sources.mk

# Platform Overrides
PLATFORM ?= HOST

TARGET = c1m2


# Architectures Specific Flags
CPU = cortex-m0plus
ARCH = thumb
SPECS = nosys.specs

# Common Flags, General Flags (Both Platforms)
CFLAGS = -Wall -Werror -g -O0 -std=c99


# Platform Specific Flags
ifeq ($(PLATFORM), MSP432)
	CC = arm-none-eabi-gcc
    LD = arm-none-eabi-ld
    LINKER_FILE = -T msp432p401r.lds
    CPPFLAGS = -DMSP432
    ARCH_FLAGS =  -mcpu=cortex-m4 -mthumb -march=armv7e-m -mfloat-abi=hard -mfpu=fpv4-sp-d16 --specs=nosys.specs
    CFLAGS += $(ARCH_FLAGS) $(CPPFLAGS)
    LDFLAGS = -Wl,-Map=$(TARGET).map $(LINKER_FILE)
    SOURCES = main.c memory.c startup_msp432p401r_gcc.c interrupts_msp432p401r_gcc.c system_msp432p401r.c
    INCLUDES = -I./include/common -Imsp432
    SIZE_TOOL = arm-none-eabi-size
else
    CC = gcc
    LD = gcc
    CPPFLAGS = -DHOST
    LDFLAGS = -Wl,-Map=c1m2.map,--cref
    CFLAGS += $(CPPFLAGS)
    SOURCES = main.c memory.c
    INCLUDES = -I./include/common -I./include/CMSIS
    SIZE_TOOL = size
endif

# Object files
OBJECTS = $(SOURCES:.c=.o)

# Dependency files
DEPS = $(SOURCES:.c=.d)


# Rule to build the final executable
.PHONY: all build compile-all clean


all: build

$(TARGET).out: $(OBJECTS)
	$(CC) $(OBJECTS) $(CFLAGS) $(LDFLAGS) -o $@
	$(CC)-size $@


# Compile all object files (but do not link), Needs to have .PHONY protection.
compile-all: $(OBJECTS)

# Compile all object files and link into a final executable, Needs to have .PHONY protection.
build: $(OBJECTS)
	$(CC) $(CFLAGS) $(OBJECTS) $(LDFLAGS) -o c1m2.out
	$(SIZE_TOOL) c1m2.out


# Generate the preprocessed output of all c-program implementation files (use the –E flag)
%.i: %.c
	$(CC) $(CFLAGS) $(CPPFLAGS) $(INCLUDES) -E $< -o $@


# Generate assembly output of c-program implementation files and the final output executable (Use the –S flag and the objdump utility).
%.asm: %.c
	$(CC) $(CFLAGS) $(CPPFLAGS) $(INCLUDES) -S $< -o $@


# Compile object files, for all c-source files (but do not link) by specifying the object file you want to compile.
%.o: %.c
	$(CC) $(CFLAGS) $(CPPFLAGS) $(INCLUDES) -MMD -MP -MF $(@:.o=.d) -c $< -o $@

# Clean build files, This should remove all compiled objects, preprocessed outputs, assembly outputs, executable files and build output files.
# Needs to have .PHONY protection, This includes but is not limited to .map, .out, .o, .asm, .i etc.

clean:
	rm -f $(OBJECTS) $(OBJECTS:.o=.d) $(OBJECTS:.o=.asm) $(TARGET).out $(TARGET).map





