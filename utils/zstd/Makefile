include $(TOPDIR)/rules.mk

PKG_NAME:=zstd
PKG_VERSION:=1.5.7
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/facebook/zstd/releases/download/v$(PKG_VERSION)
PKG_HASH:=eb33e51f49a15e023950cd7825ca74a4a2b43db8354825ac24fc1b7ee09e6fa3

PKG_MAINTAINER:=Aleksey Vasilenko <aleksey.vasilenko@gmail.com>
PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:facebook:zstandard

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/meson.mk

MESON_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)/build/meson/openwrt-build

define Package/zstd/Default
  SUBMENU:=Compression
  URL:=https://github.com/facebook/zstd
endef

define Package/libzstd
$(call Package/zstd/Default)
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=zstd library.
  MENU:=1
endef

define Package/libzstd/description
  Zstandard - Fast real-time compression algorithm.
  This package provides libzstd library.
endef

define Package/libzstd/config
	config ZSTD_OPTIMIZE_O3
		bool "Use all optimizations (-O3)"
		depends on PACKAGE_libzstd
		default y
		help
			This enables additional optmizations using the -O3 compilation flag.
endef

define Package/zstd
$(call Package/zstd/Default)
  SECTION:=utils
  CATEGORY:=Utilities
  DEPENDS:=+libzstd
  TITLE:=Fast real-time compression algorithm.
endef

define Package/zstd/description
  Zstandard - Fast real-time compression algorithm.
  This package provides the zstd binaries.
endef

ifeq ($(CONFIG_ZSTD_OPTIMIZE_O3),y)
TARGET_CFLAGS:= $(filter-out -O%,$(TARGET_CFLAGS)) -O3
endif

MESON_ARGS += \
	-Dlegacy_level=7 \
	-Ddebug_level=0 \
	-Dzlib=disabled \
	-Dlzma=disabled \
	-Dlz4=disabled \
	-Db_lto=true

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include
	$(CP) $(PKG_INSTALL_DIR)/opt/include/*.h $(1)/opt/include
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libzstd.so* $(1)/opt/lib
	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/libzstd.pc $(1)/opt/lib/pkgconfig
endef

define Package/libzstd/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libzstd.so* $(1)/opt/lib
endef

define Package/zstd/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) $(PKG_INSTALL_DIR)/opt/bin/{unzstd,zstd,zstdcat,zstdgrep,zstdless,zstdmt} $(1)/opt/bin
endef

$(eval $(call BuildPackage,libzstd))
$(eval $(call BuildPackage,zstd))
