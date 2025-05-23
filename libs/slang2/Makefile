#
# Copyright (C) 2006-2011 Openwrt.org
# Copyright (C) 2011 SMBPhone Inc.
# Copyright (C) 2019, 2023 Jeffery To
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=slang
PKG_VERSION:=2.3.3
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=https://www.jedsoft.org/releases/slang \
	https://www.jedsoft.org/releases/slang/old
PKG_HASH:=f9145054ae131973c61208ea82486d5dd10e3c5cdad23b7c4a0617743c8f5a18

PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=COPYING
PKG_MAINTAINER:=Jeffery To <jeffery.to@gmail.com>

PKG_BUILD_PARALLEL:=0
PKG_CONFIG_DEPENDS:= \
	CONFIG_BUILD_NLS \
	CONFIG_PACKAGE_libslang2-mod-onig \
	CONFIG_PACKAGE_libslang2-mod-png \
	CONFIG_PACKAGE_libslang2-mod-zlib

SLANG_MODULES:= base64 chksum csv fcntl fork histogram iconv json onig \
	png rand select slsmg socket stats sysconf termios varray zlib

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

define Package/libslang2/Default
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=S-Lang Programmer's Library
  URL:=https://www.jedsoft.org/slang/
endef

define Package/libslang2/Default/description
Multi-platform programmer's library providing facilities for interactive
applications. Includes such things as display/screen management,
keyboard input, keymaps, etc. Includes the embeddable S-Lang
interpreter.
endef

define Package/libslang2
$(call Package/libslang2/Default)
  DEPENDS:=+terminfo
endef

define Package/libslang2/description
$(call Package/libslang2/Default/description)
endef

define Package/libslang2-modules
$(call Package/libslang2/Default)
  TITLE+= (all modules)
  DEPENDS:=@PACKAGE_libslang2 $(foreach mod,$(SLANG_MODULES),+libslang2-mod-$(mod))
endef

define Package/libslang2-modules/description
$(call Package/libslang2/Default/description)

This package installs all of S-Lang's bundled modules.
endef

define Package/libslang2-mod/Default
$(call Package/libslang2/Default)
  DEPENDS:=@PACKAGE_libslang2
endef

define Package/libslang2-mod-iconv
$(call Package/libslang2-mod/Default)
  TITLE+= (iconv module)
  DEPENDS+=$(ICONV_DEPENDS)
endef

define Package/libslang2-mod-onig
$(call Package/libslang2-mod/Default)
  TITLE+= (onig module)
  DEPENDS+=+PACKAGE_libslang2-mod-onig:oniguruma
endef

define Package/libslang2-mod-png
$(call Package/libslang2-mod/Default)
  TITLE+= (png module)
  DEPENDS+=+PACKAGE_libslang2-mod-png:libpng
endef

define Package/libslang2-mod-zlib
$(call Package/libslang2-mod/Default)
  TITLE+= (zlib module)
  DEPENDS+=+PACKAGE_libslang2-mod-zlib:zlib
endef

define Package/slsh
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=S-Lang shell
  URL:=https://www.jedsoft.org/slang/slsh.html
  DEPENDS:=+libslang2 +libslang2-modules
endef

define Package/slsh/description
This is a shell which is mostly just a wrapper around the S-Lang
Interpreter, which is part of the S-Lang Library.
endef

TARGET_CFLAGS+= $(FPIC)

CONFIGURE_ARGS+= \
	--enable-largefile \
	--enable-warnings \
	--with-readline=slang \
	--with-terminfo=default \
	--without-pcre \
	--without-x \
	$(if $(CONFIG_BUILD_NLS),--with-iconv="$(ICONV_PREFIX)",--without-iconv) \
	$(if $(CONFIG_PACKAGE_libslang2-mod-onig),--with-onig="$(STAGING_DIR)/opt",--without-onig) \
	$(if $(CONFIG_PACKAGE_libslang2-mod-png),--with-png="$(STAGING_DIR)/opt",--without-png) \
	$(if $(CONFIG_PACKAGE_libslang2-mod-zlib),--with-z="$(STAGING_DIR)/opt",--without-z)

define Build/Compile
	$(call Build/Compile/Default,)
	$(call Build/Compile/Default,static)
endef

# Default install last, so that the dynamic slsh is installed
define Build/Install
	$(call Build/Install/Default,install-static)
	$(call Build/Install/Default,)
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include
	$(CP) $(PKG_INSTALL_DIR)/opt/include/*.h $(1)/opt/include/

	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libslang.{a,so*} $(1)/opt/lib/

	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/slang.pc $(1)/opt/lib/pkgconfig/
endef

define Package/libslang2/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libslang.so* $(1)/opt/lib/
endef

Package/libslang2-modules/install:=:

define Package/slsh/install
	$(INSTALL_DIR) $(1)/opt/etc
	$(CP) $(PKG_INSTALL_DIR)/opt/etc/slsh.rc $(1)/opt/etc/

	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/slsh $(1)/opt/bin/

	$(INSTALL_DIR) $(1)/opt/share
	$(CP) $(PKG_INSTALL_DIR)/opt/share/slsh/ $(1)/opt/share/

	# Entware specific: fix exec path in scripts
	$(SED) 's,^#!.*slsh,#!/opt/bin/slsh,' $(1)/opt/share/slsh/scripts/*
endef

define BuildModule
  ifndef Package/libslang2-mod-$(1)
    define Package/libslang2-mod-$(1)
    $$(call Package/libslang2-mod/Default)
      TITLE+= ($(1) module)
    endef
  endif

  define Package/libslang2-mod-$(1)/description
$$(call Package/libslang2/Default/description)

This package contains the $(1) module.
  endef

  define Package/libslang2-mod-$(1)/install
	$$(INSTALL_DIR) $$(1)/opt/lib/slang/v2/modules
	$$(CP) $$(PKG_INSTALL_DIR)/opt/lib/slang/v2/modules/$(1)-module.so $$(1)/opt/lib/slang/v2/modules/
  endef

  $$(eval $$(call BuildPackage,libslang2-mod-$(1)))
endef

$(foreach mod,$(SLANG_MODULES),$(eval $(call BuildModule,$(mod))))

$(eval $(call BuildPackage,libslang2))
$(eval $(call BuildPackage,libslang2-modules))
$(eval $(call BuildPackage,slsh))
