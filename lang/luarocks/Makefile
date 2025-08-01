#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=luarocks
PKG_VERSION:=2.2.2
PKG_RELEASE:=4

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/keplerproject/luarocks
PKG_SOURCE_VERSION:=v$(PKG_VERSION)
PKG_MIRROR_HASH:=9b2f880e0ff57a0d7e3a9d8d5365c73e6f546891bc39d2c6ef4c3443a8ca4b45

PKG_MAINTAINER:=Amr Hassan <amr.hassan@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=COPYING

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1
PKG_BUILD_DEPENDS:=lua/host
HOST_BUILD_DEPENDS:=$(PKG_BUILD_DEPENDS)
HOST_BUILD_PARALLEL:=1

HOST_BUILD_PREFIX:=$(STAGING_DIR)/host
HOST_BUILD_DIR:=$(BUILD_DIR)/host/$(PKG_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk

define Package/luarocks
  SUBMENU:=Lua
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=luarocks
  URL:=https://github.com/keplerproject/luarocks
  DEPENDS:=+lua +luac +liblua +luasocket +unzip +curl +luasec
endef

define Package/luarocks/description
	LuaRocks is a deployment and management system for Lua modules.
endef

# My custom args, copied and modified from SDK_ROOT/include/package-defaults.mk
CONFIGURE_ARGS = \
    --prefix=$(CONFIGURE_PREFIX) \
    --sysconfdir=/opt/etc \
    --with-lua=$(STAGING_DIR_HOSTPKG)

HOST_CONFIGURE_ARGS= \
    --prefix=$(STAGING_DIR)/host \
    --sysconfdir=$(STAGING_DIR)/host/etc \
    --with-lua=$(STAGING_DIR_HOSTPKG)

CONFIGURE_VARS = \
	LUAROCKS_UNAME_S="Linux" \
	LUAROCKS_UNAME_M="$(ARCH)"

HOST_CONFIGURE_VARS = \
	LUAROCKS_UNAME_S="Linux" \
	LUAROCKS_UNAME_M="$(ARCH)"

define Build/Compile
	$(call Build/Compile/Default,build)
endef

define Host/Compile
  $(call Host/Compile/Default,build)
endef

define Package/luarocks/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/luarocks-5.1 $(1)/opt/bin/luarocks
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/luarocks-admin-5.1 $(1)/opt/bin/luarocks-admin
	$(SED) 's,^#!.*lua,#!/opt/bin/lua,' $(1)/opt/bin/luarocks{,-admin}
	$(CP) $(PKG_INSTALL_DIR)/opt/share $(1)/opt/share
	$(SED) 's,$(STAGING_DIR_HOSTPKG),/opt,g' $(1)/opt/share/lua/5.1/luarocks/site_config.lua
	$(CP) $(PKG_INSTALL_DIR)/opt/etc $(1)/opt/etc
endef

define Host/Install
  $(MAKE) -C $(HOST_BUILD_DIR) install
endef

$(eval $(call BuildPackage,luarocks))

$(eval $(call HostBuild))
