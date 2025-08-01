#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=lm-sensors
PKG_VERSION:=3.6.2
PKG_RELEASE:=2

PKG_VERSION_SUBST=$(subst .,-,$(PKG_VERSION))
PKG_SOURCE_URL:=https://codeload.github.com/hramrach/lm-sensors/tar.gz/V$(PKG_VERSION_SUBST)?
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_HASH:=c6a0587e565778a40d88891928bf8943f27d353f382d5b745a997d635978a8f0
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION_SUBST)

PKG_MAINTAINER:=Jo-Philipp Wich <jo@mein.io>
PKG_LICENSE:=GPL-2.0-or-later LGPL-2.1-or-later
PKG_CPE_ID:=cpe:/a:lm_sensors:lm_sensors

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/lm-sensors/Default
  DEPENDS:=+sysfsutils
  URL:=https://hwmon.wiki.kernel.org/lm_sensors
endef

define Package/lm-sensors
  $(call Package/lm-sensors/Default)
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=lm-sensors
  DEPENDS+=+libsensors
endef

define Package/lm-sensors-detect
  $(call Package/lm-sensors/Default)
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=lm-sensors-detect
  DEPENDS+=+lm-sensors \
	+PACKAGE_lm-sensors-detect:perl \
	+PACKAGE_lm-sensors-detect:perlbase-essential \
	+PACKAGE_lm-sensors-detect:perlbase-fcntl \
	+PACKAGE_lm-sensors-detect:perlbase-file \
	+PACKAGE_lm-sensors-detect:perlbase-xsloader
endef

define Package/libsensors
  $(call Package/lm-sensors/Default)
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=libsensors
  ABI_VERSION:=5
endef

define Package/lm-sensors/description
 utility to read hardware sensor data
endef

define Package/lm-sensors-detect/description
 script to autodetect sensor hardware
endef

define Package/libsensors/description
 lm-sensors libraries
endef

define Package/lm-sensors/conffiles
/opt/etc/sensors.conf
/opt/etc/sensors3.conf
/opt/etc/sensors.d
endef

define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		$(TARGET_CONFIGURE_OPTS) \
		KERNELVERSION="$(LINUX_VERSION)" \
		LINUX="$(LINUX_DIR)" \
		CC="$(TARGET_CC)" \
		CFLAGS="$(TARGET_CFLAGS)" \
		CPPFLAGS="$(TARGET_CPPFLAGS)" \
		STAGING_DIR="$(STAGING_DIR)" \
		PREFIX="/opt" \
		MACHINE="$(ARCH)" \
		EXLDFLAGS="" \
		user
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include/sensors
	$(CP) \
		$(PKG_BUILD_DIR)/lib/sensors.h \
		$(1)/opt/include/sensors
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) \
		$(PKG_BUILD_DIR)/lib/libsensors.{a,so*} \
		$(1)/opt/lib
endef

define Package/lm-sensors/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/prog/sensors/sensors $(1)/opt/sbin
	$(INSTALL_DIR) $(1)/opt/etc/sensors.d
	$(INSTALL_CONF) ./files/custom.conf $(1)/opt/etc/sensors.d/custom.conf
#	$(INSTALL_DIR) $(1)/etc/init.d
#	$(INSTALL_BIN) ./files/lm-sensors.init $(1)/etc/init.d/lm-sensors
endef

define Package/lm-sensors-detect/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/prog/detect/sensors-detect $(1)/opt/sbin
endef

define Package/libsensors/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_BUILD_DIR)/lib/libsensors.so* $(1)/opt/lib
endef

$(eval $(call BuildPackage,libsensors))
$(eval $(call BuildPackage,lm-sensors))
$(eval $(call BuildPackage,lm-sensors-detect))
