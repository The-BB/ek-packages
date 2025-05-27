# This Makefile is free software, SPDX codes: GPL-2.0-or-later OR MIT

include $(TOPDIR)/rules.mk

PKG_NAME:=cJSON
PKG_VERSION:=1.7.18
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/DaveGamble/cJSON/tar.gz/v$(PKG_VERSION)?
PKG_HASH:=3aa806844a03442c00769b83e99970be70fbef03735ff898f4811dd03b9f5ee5

PKG_MAINTAINER:=Karl Palsson <karlp@etactica.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:cjson_project:cjson

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/cJSON
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=Ultralightweight JSON parser in ANSI C
  URL:=https://github.com/DaveGamble/cJSON
endef

define Package/cJSON/description
 Ultralightweight JSON parser in ANSI C.
cJSON aims to be the dumbest possible parser that you can get your
job done with. It's a single file of C, and a single header file.
endef

CMAKE_OPTIONS += -DENABLE_CJSON_TEST=OFF

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include/cjson
	$(CP) $(PKG_INSTALL_DIR)/opt/include/cjson/cJSON.h $(1)/opt/include/cjson
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libcjson.so* $(1)/opt/lib/
	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/libcjson.pc $(1)/opt/lib/pkgconfig
	$(SED) 's,/opt,$(STAGING_DIR)/opt,g' $(1)/opt/lib/pkgconfig/libcjson.pc
endef

define Package/cJSON/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libcjson.so* $(1)/opt/lib/
endef

$(eval $(call BuildPackage,cJSON))
