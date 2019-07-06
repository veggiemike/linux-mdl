subdir-m += avl
subdir-m += icp
subdir-m += lua
subdir-m += nvpair
subdir-m += spl
subdir-m += unicode
subdir-m += zcommon
subdir-m += zfs

INSTALL_MOD_DIR ?= extra

ZFS_MODULE_CFLAGS += -std=gnu99 -Wno-declaration-after-statement
ZFS_MODULE_CFLAGS += 
ZFS_MODULE_CFLAGS += -include /scrap/zfs/zfs_config.h
ZFS_MODULE_CFLAGS += -I/scrap/zfs/include/spl
ZFS_MODULE_CFLAGS += -I/scrap/zfs/include

ZFS_MODULE_CPPFLAGS += -D_KERNEL
ZFS_MODULE_CPPFLAGS += -UDEBUG -DNDEBUG

#ZFS_MODULE_CFLAGS += -I/include
#KBUILD_EXTRA_SYMBOLS += 

export ZFS_MODULE_CFLAGS ZFS_MODULE_CPPFLAGS

SUBDIR_TARGETS = icp lua

modules:
	list='$(SUBDIR_TARGETS)'; for targetdir in $$list; do \
		$(MAKE) -C $$targetdir; \
	done
	$(MAKE) -C /scrap/linux-mdl-5.2 M=`pwd`  CONFIG_ZFS=m $@

clean:
	@# Only cleanup the kernel build directories when CONFIG_KERNEL
	@# is defined.  This indicates that kernel modules should be built.
#	$(MAKE) -C /scrap/linux-mdl-5.2 M=`pwd`  $@

	if [ -f NONE ]; then $(RM) NONE; fi
	if [ -f Module.markers ]; then $(RM) Module.markers; fi

	find . -name '*.ur-safe' -type f -print | xargs $(RM)

modules_install:
	@# Install the kernel modules
	$(MAKE) -C /scrap/linux-mdl-5.2 M=`pwd` $@ \
		INSTALL_MOD_PATH=$(DESTDIR)$(INSTALL_MOD_PATH) \
		INSTALL_MOD_DIR=$(INSTALL_MOD_DIR) \
		KERNELRELEASE=5.2.0-rc7-mdl+
	@# Remove extraneous build products when packaging
	kmoddir=$(DESTDIR)$(INSTALL_MOD_PATH)/lib/modules/5.2.0-rc7-mdl+; \
	if [ -n "$(DESTDIR)" ]; then \
		find $$kmoddir -name 'modules.*' | xargs $(RM); \
	fi
	sysmap=$(DESTDIR)$(INSTALL_MOD_PATH)/boot/System.map-5.2.0-rc7-mdl+; \
	if [ -f $$sysmap ]; then \
		depmod -ae -F $$sysmap 5.2.0-rc7-mdl+; \
	fi

modules_uninstall:
	@# Uninstall the kernel modules
	kmoddir=$(DESTDIR)$(INSTALL_MOD_PATH)/lib/modules/5.2.0-rc7-mdl+
	list='$(subdir-m)'; for subdir in $$list; do \
		$(RM) -R $$kmoddir/$(INSTALL_MOD_DIR)/$$subdir; \
	done

distdir:
	list='$(subdir-m)'; for subdir in $$list; do \
		(cd ../module && find $$subdir -name '*.c' -o -name '*.h' -o -name '*.S' |\
		xargs cp --parents -t $$distdir); \
	done

distclean maintainer-clean: clean
install: modules_install
uninstall: modules_uninstall
all: modules
check:
