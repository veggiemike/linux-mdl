# SPDX-License-Identifier: GPL-2.0
drbd-y := drbd_bitmap.o drbd_proc.o
drbd-y += drbd_receiver.o drbd_req.o drbd_actlog.o
drbd-y += drbd_main.o drbd_strings.o drbd_nl.o
drbd-y += drbd_interval.o drbd_state.o
drbd-y += drbd_nla.o
drbd-$(CONFIG_DEBUG_FS) += drbd_debugfs.o

obj-$(CONFIG_BLK_DEV_DRBD)     += drbd.o

# NOTE: I'm trying to stay organized as I copy stuff in from
#       drbd/drbd/Kbuild...  just tacking things on at the bottom of
#       the file to keep mainline vs drbd upstream delineated.

# new objs
drbd-y += drbd_buildtag.o
drbd-y += drbd_sender.o
drbd-y += drbd_transport.o
obj-$(CONFIG_BLK_DEV_DRBD) += drbd_transport_tcp.o


# FIXME: lru_cache.o... should we just use the mainline one at
#        $top/lib/lru.cache?  that or update it in the right place
#        with the modified lru_cache.c from drbd? but how do i make
#        sure it gets linked in...?


###############################################################################
# compat checks
###############################################################################


# AUGMENTED_RBTREE_SYMBOLS_EXPORTED
#
# The augmented rbtree helper functions are not exported at least until kernel
# version 2.6.38-rc2.
#ifeq ($(shell grep -e '\<rb_augment_insert\>' \
#		   -e '\<rb_augment_erase_begin\>' \
#		   -e '\<rb_augment_erase_end\>' \
#		   $(objtree)/Module.symvers | wc -l),3)
#override EXTRA_CFLAGS += -DAUGMENTED_RBTREE_SYMBOLS_EXPORTED
#endif
#
# NOTE: That copy-pasted comment doesn't seem entirely true... 5.4
#       does NOT export those symbols.
#
#override EXTRA_CFLAGS += -DAUGMENTED_RBTREE_SYMBOLS_EXPORTED

# IDR_GET_NEXT_EXPORTED
#
#ifeq ($(shell grep -e '\<idr_get_next\>' \
#		   $(objtree)/Module.symvers | wc -l),1)
#override EXTRA_CFLAGS += -DIDR_GET_NEXT_EXPORTED
#else
#compat_objs += drbd-kernel-compat/idr.o
#endif
#
# NOTE: 5.4 looks good
#
override EXTRA_CFLAGS += -DIDR_GET_NEXT_EXPORTED

# BLKDEV_ISSUE_ZEROOUT_EXPORTED
#
#ifeq ($(shell grep -e '\<blkdev_issue_zeroout\>' \
#		   $(objtree)/Module.symvers | wc -l),1)
#override EXTRA_CFLAGS += -DBLKDEV_ISSUE_ZEROOUT_EXPORTED
#else
#compat_objs += drbd-kernel-compat/blkdev_issue_zeroout.o
#endif
#
# NOTE: 5.4 looks good
#
override EXTRA_CFLAGS += -DBLKDEV_ISSUE_ZEROOUT_EXPORTED

# nsecs_to_jiffies
#
#ifneq ($(shell grep -e '\<nsecs_to_jiffies\>' \
#		   $(objtree)/Module.symvers | wc -l),1)
#compat_objs += drbd-kernel-compat/nsecs_to_jiffies.o
#endif
#
# NOTE: 5.4 looks good
#


# drbd_wrappers, which keys off of the above checks, also has some
# unconditional code in it that we need.
compat_objs += drbd_wrappers.o


# DAX_PMEM
#
#ifdef CONFIG_DEV_DAX_PMEM
#ifneq ($(shell grep -e '\<arch_wb_cache_pmem\>' $(objtree)/Module.symvers | wc -l),1)
#override EXTRA_CFLAGS += -DDAX_PMEM_IS_INCOMPLETE
#else
#drbd-y += drbd_dax_pmem.o
#endif
#endif
#
# NOTE: 5.4 has it, and I've got CONFIG_DEV_DAX_PMEM=m
#
drbd-y += drbd_dax_pmem.o


# add in the list of needed compat_objs
drbd-y += $(compat_objs)


# KREF_DEBUG
#
# FIXME: leaving this all commented out for now, although maybe we
#        should plumb this in better.
#
#ifndef DISABLE_KREF_DEBUGGING_HERE
#      override EXTRA_CFLAGS += -DCONFIG_KREF_DEBUG
#      drbd-y += kref_debug.o drbd_kref_debug.o
#endif
