# Copyright (C) 2019 Valentin Kivachuk <vk18496@gmail.com>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# Entware specific: add -DHAVE_FEENABLEEXCEPT=FALSE

include $(TOPDIR)/rules.mk

PKG_NAME:=tesseract
PKG_VERSION:=5.4.1
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/tesseract-ocr/tesseract/tar.gz/$(PKG_VERSION)?
PKG_HASH:=c4bc2a81c12a472f445b7c2fb4705a08bd643ef467f51ec84f0e148bd368051b

PKG_MAINTAINER:=Valentin Kivachuk <vk18496@gmail.com>
PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:tesseract_project:tesseract

CMAKE_BINARY_SUBDIR:=openwrt-build

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/tesseract
  MENU:=1
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=Tesseract Open Source OCR Engine
  URL:=https://github.com/tesseract-ocr/tesseract
  DEPENDS:=+libleptonica +libarchive +libpthread +libstdcpp
endef

TARGET_CFLAGS:=$(filter-out -O%,$(TARGET_CFLAGS)) -O3

CMAKE_OPTIONS += \
	-DAUTO_OPTIMIZE=OFF \
	-DBUILD_SHARED_LIBS=ON \
	-DBUILD_TRAINING_TOOLS=OFF \
	-DHAVE_NEON=$(if $(or $(findstring aarch64,$(CONFIG_ARCH)),$(findstring neon,$(CONFIG_CPU_TYPE))),TRUE,FALSE) \
	-DDISABLE_CURL=ON \
	-DHAVE_FEENABLEEXCEPT=FALSE

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include
	$(CP) $(PKG_INSTALL_DIR)/opt/include/tesseract $(1)/opt/include/
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libtesseract.so* $(1)/opt/lib/
	$(INSTALL_DIR) $(1)/opt/lib/cmake/tesseract
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/cmake/tesseract/*.cmake $(1)/opt/lib/cmake/tesseract
	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/tesseract.pc $(1)/opt/lib/pkgconfig/
endef

define Package/tesseract/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) $(PKG_INSTALL_DIR)/opt/bin/* $(1)/opt/bin/
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/lib*.so.* $(1)/opt/lib/
endef

$(eval $(call BuildPackage,tesseract))
