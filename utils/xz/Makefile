#
# Copyright (C) 2013-2015 OpenWrt.org
# Copyright (C) 2017 Daniel Engberg <daniel.engberg.lists@pyret.net>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=xz
PKG_VERSION:=5.6.2
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=@SF/lzmautils
PKG_HASH:=e12aa03cbd200597bd4ce11d97be2d09a6e6d39a9311ce72c91ac7deacde3171

PKG_MAINTAINER:=
PKG_LICENSE:=Public-Domain LGPL-2.1-or-later 0BSD
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:tukaani:xz

PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1
PKG_BUILD_FLAGS:=lto

include $(INCLUDE_DIR)/package.mk

define Package/xz/Default
  SUBMENU:=Compression
  SECTION:=utils
  CATEGORY:=Utilities
  URL:=https://tukaani.org/xz
endef

define Package/xz-utils
$(call Package/xz/Default)
  TITLE:=XZ Utils (meta)
  MENU:=1
endef

define Package/liblzma
$(call Package/xz/Default)
  SECTION:=libs
  CATEGORY:=Libraries
  DEPENDS:=+libpthread
  TITLE:=liblzma library from XZ Utils
endef

# $(1): package name & command in /opt/bin/
# $(2): package dependencies
# $(3): symbolic links to $(1) in /opt/bin/
define BuildSubPackage

  define Package/$(1)
  $(call Package/xz/Default)
    DEPENDS:=xz-utils $(2)
    TITLE:=$(1) utility from XZ Utils
    $(if $(3),ALTERNATIVES:=$(foreach f,$(1) $(3),300:/opt/bin/$(f):/opt/libexec/$(1)-lzmautils))
  endef

  define Package/$(1)/description
   Contains: $(1) $(3)
  endef

  define Package/$(1)/install
	$(INSTALL_DIR) $$(1)$(if $(3),/opt/libexec,/opt/bin)
	$(CP) $(PKG_INSTALL_DIR)/opt/bin/$(1) $$(1)$(if $(3),/opt/libexec/$(1)-lzmautils,/opt/bin/$(1))
  endef

  $$(eval $$(call BuildPackage,$(1)))
endef

TARGET_LDFLAGS += -Wl,--gc-sections

CONFIGURE_ARGS += \
	--enable-small \
	--enable-assume-ram=4 \
	--disable-assembler \
	--disable-debug \
	--disable-doc \
	--disable-rpath \
	--disable-symbol-versions \
	--disable-werror \
	--with-pic

CONFIGURE_VARS += \
	gl_cv_posix_shell=/bin/sh

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/include/lzma{,.h} \
		$(1)/opt/include/
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/liblzma.{a,so*} \
		$(1)/opt/lib/
	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/pkgconfig/liblzma.pc \
		$(1)/opt/lib/pkgconfig/
endef

define Package/xz-utils/install
	true
endef

define Package/liblzma/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/liblzma.so.* $(1)/opt/lib/
endef


$(eval $(call BuildPackage,xz-utils))
$(eval $(call BuildPackage,liblzma))
$(eval $(call BuildSubPackage,lzmadec, +liblzma,))
$(eval $(call BuildSubPackage,lzmainfo, +liblzma,))
$(eval $(call BuildSubPackage,xz, +liblzma, lzcat lzma unlzma unxz xzcat))
$(eval $(call BuildSubPackage,xzdec, +liblzma,))
$(eval $(call BuildSubPackage,xzdiff, +xz, lzcmp lzdiff xzcmp))
$(eval $(call BuildSubPackage,xzgrep, +xz, lzegrep lzfgrep lzgrep xzegrep xzfgrep))
$(eval $(call BuildSubPackage,xzless, +xz, lzless))
$(eval $(call BuildSubPackage,xzmore, +xz, lzmore))
