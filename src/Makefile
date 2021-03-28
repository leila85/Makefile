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
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <Put a description of the supported targets here>
#
# Platform Overrides:
#      <Put a description of the supported Overrides here
#
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides
#Cross-compilation: makefile portable for MSP432 platform or Linux (HOST)
PLATFORM = MSP432

#Target name
TARGET = c1m2


ifeq ($(PLATFORM),HOST)
############## Platform = HOST (linux) ############## 
	CC = gcc
	CFLAGS = -Wall -Werror -O0 -std=c99
	LDFLAGS = -Wl,-Map=$(TARGET).map 
	CPPFLAGS = -DHOST $(INCLUDES)
	SIZE = size

############## Platform = MSP432 (arm) ############## 
else
	LINKER_FILE = msp432p401r.lds
	CPU = cortex-m4
	ARCH = armv7e-m
	SPECS = nosys.specs
	CC = arm-none-eabi-gcc
	LD = arm-none-eabi-ld
	CFLAGS = -mcpu=$(CPU) -m$(ARCH) -mfloat-abi=hard -mfpu=fpv4-sp-d16 -mthumb --specs=$(SPECS) -Wall -Werror -O0 -std=c99
	LDFLAGS = -Wl,-Map=$(TARGET).map -T -L ../$(LINKER_FILE)
	CPPFLAGS = -DMSP432 $(INCLUDES)
	#Architecture specific source codes
	SOURCES =	interrupts_msp432p401r_gcc.c  \
		startup_msp432p401r_gcc.c     		\
		system_msp432p401r.c

	SIZE = arm-none-eabi-size
endif

OBJS = $(SOURCES:.c=.o)
PREPROC = $(SOURCES:.c=.i)
ASM = $(SOURCES:.c=.asm)
DEP = $(SOURCES:.c=.d)

#######Produce object files without linking#######
%.o : %.c
	$(CC) -c $< $(CFLAGS) $(CPPFLAGS) -o $@
#######Save the preprocessor outputs#######
%.i : %.c
	$(CC) -E $< $(CFLAGS) $(CPPFLAGS) -o $@
#######Stop the compiling process and save the output of assembling#######
%.asm : %.c
	$(CC) -S $< $(CFLAGS) $(CPPFLAGS) -o $@

%.d : %.c
	$(CC) -E -M $< $(CPPFLAGS) -o $@


.PHONY: compile-all
compile-all: $(OBJS)

.PHONY: build
build: $(TARGET).out

$(TARGET).out: $(OBJS)
	$(CC) $(OBJS) $(CFLAGS) $(CPPFLAGS) $(LDFLAGS) -o $@
	$(SIZE) $@

.PHONY: clean
clean:
	rm -f $(OBJS) $(PREPROC) $(ASM) $(DEP) $(TARGET).out $(TARGET).map 