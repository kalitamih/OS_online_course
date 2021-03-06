CC ?= gcc
LD ?= ld

CFLAGS := -g -m64 -mno-red-zone -mno-mmx -mno-sse -mno-sse2 -ffreestanding \
	-mcmodel=kernel -fno-pic -Wall -Wextra -Werror -pedantic -std=c99 \
	-Wframe-larger-than=1024 -Wstack-usage=1024 \
	-Wno-unknown-warning-option $(if $(DEBUG),-DDEBUG)
LFLAGS := -nostdlib -z max-page-size=0x1000

INC := ./inc
SRC := ./src

C_SOURCES := $(shell find $(SRC)/*.c)
C_OBJECTS := $(C_SOURCES:.c=.o)
C_DEPS := $(C_SOURCES:.c=.d)

S_SOURCES := $(shell find $(SRC)/*.S) intentry.S
S_OBJECTS := $(S_SOURCES:.S=.o)
S_DEPS := $(S_SOURCES:.S=.d)

OBJ := $(C_OBJECTS) $(S_OBJECTS)
DEP := $(C_DEPS) $(S_DEPS)

all: kernel

kernel: $(OBJ) kernel.ld
	$(LD) $(LFLAGS) -T kernel.ld -o $@ $(OBJ)

$(S_OBJECTS): %.o: %.S
	$(CC) -D__ASM_FILE__ -I$(INC) -g -MMD -c $< -o $@

$(C_OBJECTS): %.o: %.c
	$(CC) $(CFLAGS) -I$(INC) -g -MMD -c $< -o $@

intentry.S: genints.py
	python $< > $@

-include $(DEP)

.PHONY: clean
clean:
	rm -f kernel $(OBJ) $(DEP) intentry.S
