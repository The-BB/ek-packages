#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=freetype
PKG_VERSION:=2.13.3
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@SF/freetype
PKG_HASH:=0550350666d427c74daeb85d5ac7bb353acba5f76956395995311a9c6f063289

PKG_MAINTAINER:=Val Kulkov <val.kulkov@gmail.com>
PKG_LICENSE:=FTL GPL-2.0-only MIT ZLIB GPL-3.0-or-later
PKG_LICENSE_FILES:=LICENSE.TXT docs/FTL.TXT docs/GPLv2.TXT src/bdf/README src/pcf/README src/gzip/zlib.h builds/unix/config.sub builds/unix/config.guess
PKG_CPE_ID:=cpe:/a:freetype:freetype

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/meson.mk

define Package/libfreetype
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=A free, high-quality and portable font engine
  URL:=https://www.freetype.org/
  DEPENDS:=+zlib +libpng
endef

define Package/libfreetype/description
 The FreeType project is a team of volunteers who develop free,
 portable and high-quality software solutions for digital typography.
 They specifically target embedded systems and focus on bringing small,
 efficient and ubiquitous products.
endef

MESON_ARGS += \
	-Dzlib=enabled \
	-Dbzip2=disabled \
	-Dpng=enabled \
	-Dharfbuzz=disabled \
	-Dbrotli=disabled \
	-Dmmap=enabled

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include/freetype2
	$(CP) $(PKG_INSTALL_DIR)/opt/include/freetype2/* $(1)/opt/include/freetype2/
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libfreetype.so* $(1)/opt/lib/
	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/freetype2.pc $(1)/opt/lib/pkgconfig/freetype2.pc
endef

define Package/libfreetype/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libfreetype.so.* $(1)/opt/lib/
endef

$(eval $(call BuildPackage,libfreetype))
