
ARCH = $(shell basename `pwd`)
_ARCH = $(shell echo $(ARCH) | tr -d [0-9])
ASMS = $(wildcard *.s)
OBJS = $(ASMS:.s=.o)

CROSS_COMPILE ?= $(_ARCH)-linux-gnu-
QEMU_USER ?= qemu-$(ARCH)

AS = $(CROSS_COMPILE)as -mabi=64
LD = $(CROSS_COMPILE)ld -m elf64ltsmip

all: $(OBJS)
	# Need to register mips64el to /proc/sys/fs/binfmt_misc/register
	# Based on /usr/share/binfmts/
	# See the binfmt target, this must be run in host system, not guest
	@$(QEMU_USER) $(basename $^)

%.o: %.s
	$(AS) -o $@ $<
	$(LD) -o $(basename $@) $@

clean: $(OBJS)
	rm -rf *.o $(basename $^)

binfmt:
	# ref: http://magikh0e.ihtb.org/pubHardwareHacking/AnalyzingReversingAndEmulatingFirmware.html
	# ref: https://github.com/qemu/qemu/blob/master/scripts/qemu-binfmt-conf.sh
	sudo bash -c "echo ':mips64el:M::\x7fELF\x02\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x08\x00:\xff\xff\xff\xff\xff\xff\xff\x00\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff\xff:/usr/bin/qemu-mips64el:' > /proc/sys/fs/binfmt_misc/register"
