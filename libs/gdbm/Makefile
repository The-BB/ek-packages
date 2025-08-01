#
# Copyright (C) 2006-2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=gdbm
PKG_VERSION:=1.25
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=@GNU/gdbm
PKG_HASH:=d02db3c5926ed877f8817b81cd1f92f53ef74ca8c6db543fbba0271b34f393ec

PKG_MAINTAINER:=Marcel Denia <naoir@gmx.net>
PKG_LICENSE:=GPL-3.0-or-later
PKG_LICENSE_FILES:=COPYING

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=0

include $(INCLUDE_DIR)/package.mk

define Package/libgdbm
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=GNU database manager
  URL:=https://www.gnu.org/software/gdbm/
endef

define Package/libgdbm/description
  GNU database manager library
  GNU dbm is a set of database routines that use extendible hashing and
  works similar to the standard UNIX dbm routines.
endef

CONFIGURE_ARGS += \
	--enable-libgdbm-compat \
	--enable-shared \
	--without-readline

define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		BINOWN=`id -u` \
		BINGRP=`id -g` \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		SUBDIRS=src \
		all install
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include
	$(CP) $(PKG_INSTALL_DIR)/opt/include/{gdbm,ndbm}.h $(1)/opt/include/
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libgdbm.so* $(1)/opt/lib/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libgdbm_compat.so* $(1)/opt/lib/
endef

define Package/libgdbm/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libgdbm.so.* $(1)/opt/lib/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libgdbm_compat.so* $(1)/opt/lib/
endef

$(eval $(call BuildPackage,libgdbm))
