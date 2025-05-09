#
# Copyright (C) 2006-2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# Entware specific: findutils-find, findutils-xargs merged with findutils; ALTERNATIVES

include $(TOPDIR)/rules.mk

PKG_NAME:=findutils
PKG_VERSION:=4.10.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@GNU/$(PKG_NAME)
PKG_HASH:=1387e0b67ff247d2abde998f90dfbf70c1491391a59ddfecb8ae698789f0a4f5

PKG_LICENSE:=GPL-3.0-or-later
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:gnu:findutils

PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk

define Package/findutils/Default
	TITLE:=GNU Find Utilities
	SECTION:=utils
	CATEGORY:=Utilities
	URL:=https://www.gnu.org/software/findutils/
endef

define Package/findutils/description/Default
Replace busybox versions of findutils with full GNU versions.
This is normally not needed as busybox is smaller and provides
sufficient functionality, but some users may want or need
the full functionality of the GNU tools.
endef

define Package/findutils
	$(call Package/findutils/Default)
	ALTERNATIVES:= \
		300:/opt/bin/find:/opt/libexec/find-gnu \
		300:/opt/bin/xargs:/opt/libexec/xargs-gnu
endef

define Package/findutils/description
$(call Package/findutils/description/Default)
This package installs find and xargs utilities
endef

define Package/findutils-locate
	$(call Package/findutils/Default)
	TITLE+= - locate and updatedb utility
endef

define Package/findutils-locate/description
$(call Package/findutils/description/Default)
This package contains the locate and related updatedb utility
endef

CONFIGURE_ARGS += \
	--localstatedir=/opt/var \
	--without-selinux
CONFIGURE_VARS += ac_cv_path_SORT=sort

define Package/findutils/install
	$(INSTALL_DIR) $(1)/opt/libexec
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/find $(1)/opt/libexec/find-gnu
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/xargs $(1)/opt/libexec/xargs-gnu
endef

define Package/findutils-locate/install
	$(INSTALL_DIR) $(1)/opt/bin $(1)/opt/var
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/locate $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/updatedb $(1)/opt/bin/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib $(1)/opt/
endef

$(eval $(call BuildPackage,findutils))
$(eval $(call BuildPackage,findutils-locate))
