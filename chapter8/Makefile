#ARCH dependent
XEN_TARGET_ARCH = x86_64
ifeq ($(XEN_TARGET_ARCH),x86_32)
ARCH_CFLAGS  := -m32 -march=i686
ARCH_LDFLAGS := -m elf_i386 -T loader.lds
ARCH_ASFLAGS := -m32
ARCH_OBJS := bootstrap.x86_32.o
endif
ifeq ($(XEN_TARGET_ARCH),x86_64)
ARCH_CFLAGS := -m64
ARCH_ASFLAGS := -m64
ARCH_LDFLAGS := -m elf_x86_64 -T loader-x86_64.lds
ARCH_OBJS := bootstrap.x86_64.o
endif

#CPPFLAGS += -I../xen/xen/include/ -I../xen/xen/include/asm
CPPFLAGS += -Iinclude -Iinclude/x86 -DCONFIG_X86_PAE -D__XEN_INTERFACE_VERSION__=0x00030203 $(ARCH_CPPFLAGS)
LDFLAGS  += -nostdlib -g $(ARCH_LDFLAGS)
CFLAGS   += -Wall -std=c99 -g $(ARCH_CFLAGS) -fno-stack-protector
ASFLAGS   = -D__ASSEMBLY__ $(ARCH_ASFLAGS)

.PHONY: all clean run

all: testkernel

testkernel: $(ARCH_OBJS) kernel.o console.o traps.o xenstore.o
	$(LD) $(LDFLAGS) $^ -o testkernel

clean:
	rm -f *.o 
	rm -f testkernel

run: testkernel
	@sudo xm create -p domain_config
	@sudo ./chmod_xenstore.sh
	@sudo xm unpause Simplest_Kernel
	@echo "Unpaused domain\n\n"
	@sudo xm console Simplest_Kernel
