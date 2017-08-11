#
#  This file is part of brzboot
#
#  Copyright (C) 2013 Karel Zak <kzak@redhat.com>
#
#  brzboot is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.
#
#  brzboot is distributed in the hope that it will be useful, but
#  WITHOUT ANY WARRANTY; without even the implied warranty of
#  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU
#  Lesser General Public License for more details.
#
#  You should have received a copy of the GNU Lesser General Public License
#  along with systemd; If not, see <http://www.gnu.org/licenses/>.

ACLOCAL_AMFLAGS = -I m4 ${ACLOCAL_FLAGS}
AM_MAKEFLAGS = --no-print-directory

brzbootlibdir = $(prefix)/lib/brzboot

AM_CPPFLAGS = -include config.h
AM_CFLAGS = \
	-D_GNU_SOURCE \
	-Wall \
	-Wextra \
	-Wmissing-prototypes \
	-Wno-unused-parameter
AM_LDFLAGS =

EXTRA_DIST = autogen.sh README LICENSE
CLEANFILES =

# ------------------------------------------------------------------------------
if HAVE_BLKID
bin_PROGRAMS = brzboot

brzboot_SOURCES = \
	src/setup/setup.c \
	src/setup/efivars.c \
	src/setup/efivars.h

brzboot_CPPFLAGS = \
	$(AM_CPPFLAGS) \
	-DMACHINE_TYPE_NAME=\"$(MACHINE_TYPE_NAME)\" \
	-DBRZBOOTLIBDIR=\"$(brzbootlibdir)\"

brzboot_CFLAGS = \
	$(AM_CFLAGS) \
	$(BLKID_CFLAGS)

brzboot_LDADD = \
	$(BLKID_LIBS)
endif

if ENABLE_MANPAGES
%.8: %.xml
	$(AM_V_GEN)$(XSLTPROC) -o $@ --nonet \
	  --stringparam man.output.quietly 1 \
	  --stringparam man.th.extra1.suppress 1 \
	  --stringparam man.authors.section.enabled 0 \
	  --stringparam man.copyright.section.enabled 0 \
	  http://docbook.sourceforge.net/release/xsl/current/manpages/docbook.xsl $<

dist_man_MANS = man/brzboot.8
endif

EXTRA_DIST += man/brzboot.xml
CLEANFILES += man/brzboot.8

# ------------------------------------------------------------------------------
# EFI compilation -- this part of the build system uses custom make rules and
# bypasses regular automake to provide absolute control on compiler and linker
# flags.
efi_cppflags = \
	$(EFI_CPPFLAGS) \
	-I$(top_builddir) -include config.h \
	-I$(EFI_INC_DIR)/efi \
	-I$(EFI_INC_DIR)/efi/$(ARCH) \
	-DMACHINE_TYPE_NAME=\"$(MACHINE_TYPE_NAME)\"

efi_cflags = \
	$(EFI_CFLAGS) \
	-Wall \
	-Wextra \
	-nostdinc \
	-ggdb -O0 \
	-fpic \
	-fshort-wchar \
	-nostdinc \
	-ffreestanding \
	-fno-strict-aliasing \
	-fno-stack-protector \
	-Wsign-compare \
	-mno-sse \
	-mno-mmx

if ARCH_X86_64
efi_cflags += \
	-mno-red-zone \
	-DEFI_FUNCTION_WRAPPER \
	-DGNU_EFI_USE_MS_ABI
endif

efi_ldflags = \
	$(EFI_LDLAGS) \
	-T $(EFI_LDS_DIR)/elf_$(ARCH)_efi.lds \
	-shared \
	-Bsymbolic \
	-nostdlib \
	-znocombreloc \
	-L $(EFI_LIB_DIR) \
	$(EFI_LDS_DIR)/crt0-efi-$(ARCH).o

# ------------------------------------------------------------------------------
brzboot_headers = \
	src/efi/util.h \
	src/efi/console.h \
	src/efi/graphics.h \
	src/efi/pefile.h

brzboot_sources = \
	src/efi/util.c \
	src/efi/console.c \
	src/efi/graphics.c \
	src/efi/pefile.c \
	src/efi/brzboot.c

brzboot_objects = $(addprefix $(top_builddir)/,$(brzboot_sources:.c=.o))
brzboot_solib = $(top_builddir)/src/efi/brzboot.so
brzboot = brzboot$(MACHINE_TYPE_NAME).efi

brzbootlib_DATA = $(brzboot)
CLEANFILES += $(brzboot_objects) $(brzboot_solib) $(brzboot)
EXTRA_DIST += $(brzboot_sources) $(brzboot_headers)

$(top_builddir)/src/efi/%.o: $(top_srcdir)/src/efi/%.c $(addprefix $(top_srcdir)/,$(brzboot_headers))
	@$(MKDIR_P) $(top_builddir)/src/efi/
	$(AM_V_CC)$(EFI_CC) $(efi_cppflags) $(efi_cflags) -c $< -o $@

$(brzboot_solib): $(brzboot_objects)
	$(AM_V_CCLD)$(LD) $(efi_ldflags) $(brzboot_objects) \
		-o $@ -lefi -lgnuefi $(shell $(CC) -print-libgcc-file-name); \
	nm -D -u $@ | grep ' U ' && exit 1 || :
.DELETE_ON_ERROR: $(gummboot_solib)

$(brzboot): $(brzboot_solib)
	$(AM_V_GEN) objcopy -j .text -j .sdata -j .data -j .dynamic \
	  -j .dynsym -j .rel -j .rela -j .reloc \
	  --target=efi-app-$(ARCH) $< $@

# ------------------------------------------------------------------------------
stub_headers = \
	src/efi/util.h \
	src/efi/pefile.h \
	src/efi/linux.h

stub_sources = \
	src/efi/util.c \
	src/efi/pefile.c \
	src/efi/linux.c \
	src/efi/stub.c

stub_objects = $(addprefix $(top_builddir)/,$(stub_sources:.c=.o))
stub_solib = $(top_builddir)/src/efi/stub.so
stub = linux$(MACHINE_TYPE_NAME).efi.stub

brzbootlib_DATA += $(stub)
CLEANFILES += $(stub_objects) $(stub_solib) $(stub)
EXTRA_DIST += $(stub_sources) $(stub_headers)

$(top_builddir)/src/efi/%.o: $(top_srcdir)/src/efi/%.c $(addprefix $(top_srcdir)/,$(stub_headers))
	@$(MKDIR_P) $(top_builddir)/src/efi/
	$(AM_V_CC)$(EFI_CC) $(efi_cppflags) $(efi_cflags) -c $< -o $@

$(stub_solib): $(stub_objects)
	$(AM_V_CCLD)$(LD) $(efi_ldflags) $(stub_objects) \
		-o $@ -lefi -lgnuefi $(shell $(CC) -print-libgcc-file-name); \
	nm -D -u $@ | grep ' U ' && exit 1 || :
.DELETE_ON_ERROR: $(gummboot_solib)

$(stub): $(stub_solib)
	$(AM_V_GEN) objcopy -j .text -j .sdata -j .data -j .dynamic \
	  -j .dynsym -j .rel -j .rela -j .reloc \
	  --target=efi-app-$(ARCH) $< $@

# ------------------------------------------------------------------------------
CLEANFILES += test-disk.img
EXTRA_DIST += test/test-create-disk.sh

test-disk.img: brzboot$(MACHINE_TYPE_NAME).efi test/test-create-disk.sh
	$(AM_V_GEN)test/test-create-disk.sh

qemu: test-disk.img
	$(QEMU) -machine accel=kvm -m 256 -bios $(QEMU_BIOS) -snapshot test-disk.img

install-tree: all
	rm -rf $(abs_srcdir)/install-tree
	$(MAKE) install DESTDIR=$(abs_srcdir)/install-tree
	tree $(abs_srcdir)/install-tree
