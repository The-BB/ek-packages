# 
# Copyright (C) 2009 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=luaexpat
PKG_VERSION:=1.5.2
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_VERSION:=$(PKG_VERSION)
PKG_SOURCE_URL:=https://github.com/lunarmodules/luaexpat
PKG_MIRROR_HASH:=d479e41ea28b71ede214754d0cde87e6582022fcdd3981c928b41491062835c8

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENCE
PKG_CPE_ID:=cpe:/a:matthewwild:luaexpat

include $(INCLUDE_DIR)/package.mk

define Package/luaexpat
  SUBMENU:=Lua
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=LuaExpat
  URL:=http://matthewwild.co.uk/projects/luaexpat/
  MAINTAINER:=W. Michael Petullo <mike@flyn.org>
  DEPENDS:=+lua +libexpat
endef

define Package/luaexpat/description
  LuaExpat is a SAX XML parser based on the Expat library.
endef

define Build/Configure
endef

define Build/Compile
	$(CP) files/compat-5.1r5 $(PKG_BUILD_DIR)/compat-5.1r5
	$(MAKE) -C $(PKG_BUILD_DIR) \
	EXPAT_INC="-I$(STAGING_DIR)/opt/include/" \
	LUA_INC="-I$(STAGING_DIR)/opt/include/" \
	LUA_LDIR="$(STAGING_DIR)/opt/lib/" \
	COMPAT_DIR="$(PKG_BUILD_DIR)/compat-5.1r5" \
	LDFLAGS="-shared $(TARGET_LDFLAGS)" \
	CC="$(TARGET_CC) $(TARGET_CFLAGS) $(FPIC) -std=c99" \
	LD="$(TARGET_CROSS)ld -shared"
endef

define Package/luaexpat/install
	$(INSTALL_DIR) $(1)/opt/lib/lua
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/src/lxp.so $(1)/opt/lib/lua/lxp.so
	$(INSTALL_DIR) $(1)/opt/lib/lua/lxp
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/src/lxp/lom.lua $(1)/opt/lib/lua/lxp
endef

$(eval $(call BuildPackage,luaexpat))
