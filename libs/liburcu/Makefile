#
# Copyright (C) 2015 OpenWrt.org
# Copyright (C) 2014-2018 CZ.NIC, z.s.p.o. <knot-dns@labs.nic.cz>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=liburcu
PKG_VERSION:=0.15.3
PKG_RELEASE:=1

PKG_MAINTAINER:=Daniel Salzman <daniel.salzman@nic.cz>
PKG_LICENSE:=LGPL-2.1-or-later GPL-2.0-or-later MIT
PKG_LICENSE_FILES:=lgpl-2.1.txt gpl-2.0.txt lgpl-relicensing.txt

PKG_SOURCE:=userspace-rcu-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=https://lttng.org/files/urcu/
PKG_HASH:=26687ec84e3e114759454c884a08abeaf79dec09b041895ddf4c45ec150acb6d
PKG_BUILD_DIR:=$(BUILD_DIR)/userspace-rcu-$(PKG_VERSION)

PKG_FIXUP:=autoreconf
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

include $(INCLUDE_DIR)/package.mk

define Package/liburcu
	SECTION:=libs
	CATEGORY:=Libraries
	TITLE:=User-space Read-Copy-Update library
	URL:=https://lttng.org/
	DEPENDS:=+libpthread @!arc
endef

define Package/liburcu/description
	Userspace Read-Copy-Update library.
endef

ifeq ($(CONFIG_arc),)
define Build/InstallDev
	$(INSTALL_DIR)						$(1)/opt/include
	$(CP) $(PKG_INSTALL_DIR)/opt/include/urcu*		$(1)/opt/include/
	$(INSTALL_DIR)						$(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/liburcu*.{a,so*}	$(1)/opt/lib/
	$(INSTALL_DIR)						$(1)/opt/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/*.pc		$(1)/opt/lib/pkgconfig/
endef
endif

define Package/liburcu/install
	$(INSTALL_DIR)					$(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/liburcu*.so.*	$(1)/opt/lib/
endef

$(eval $(call BuildPackage,liburcu))
