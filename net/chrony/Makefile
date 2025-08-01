#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# Entware specific: CONFIGURE_VARS+=CPPFLAGS="-DNDEBUG -I$(STAGING_DIR)/opt/include"
# do detect libcap

include $(TOPDIR)/rules.mk

PKG_NAME:=chrony
PKG_VERSION:=4.7
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://chrony-project.org/releases/
PKG_HASH:=c0de41a8c051e5d32b101b5f7014b98ca978b18e592f30ce6840b6d4602d947b

PKG_MAINTAINER:=Miroslav Lichvar <mlichvar0@gmail.com>
PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:tuxfamily:chrony

PKG_BUILD_DEPENDS:=pps-tools
PKG_BUILD_FLAGS:=lto

include $(INCLUDE_DIR)/package.mk

define Package/chrony/Default
  SUBMENU:=Time Synchronization
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+libcap +libpthread
  USERID:=chrony=323:chrony=323
  TITLE:=A versatile NTP client and server
  URL:=http://chrony.tuxfamily.org/
  PROVIDES:=nts
endef

define Package/chrony
$(call Package/chrony/Default)
  TITLE+= (without NTS)
  VARIANT:=normal
  CONFLICTS:=chrony-nts
endef

define Package/chrony-nts
$(call Package/chrony/Default)
  TITLE+= (with NTS)
  DEPENDS+= +PACKAGE_chrony-nts:libgnutls +PACKAGE_chrony-nts:ca-bundle
  VARIANT:=with-nts
endef

define Package/chrony/description
	An NTP client and server designed to perform well in a wide range
	of conditions. It can synchronize the system clock with NTP servers,
	reference clocks, and manual input using wristwatch and keyboard.
	This package does NOT include NTS support.
endef

define Package/chrony-nts/description
	An NTP client and server designed to perform well in a wide range
	of conditions. It can synchronize the system clock with NTP servers,
	reference clocks, and manual input using wristwatch and keyboard.
	This package includes NTS support.
endef

define Package/chrony/conffiles
/opt/etc/chrony/chrony.conf
endef

Package/chrony-nts/conffiles = $(Package/chrony/conffiles)

CONFIGURE_ARGS+= \
	--host-machine=$(shell echo $(GNU_TARGET_NAME) | sed -e 's/-.*//') \
	--host-release="" \
	--host-system=Linux \
	--sysconfdir=/opt/etc/chrony \
	--prefix=/opt \
	--chronyrundir=/opt/var/run/chrony \
	$(if $(findstring normal,$(BUILD_VARIANT)),--disable-nts) \
	--disable-readline \
	--disable-rtc \
	$(if $(findstring normal,$(BUILD_VARIANT)),--disable-sechash) \
	--with-hwclockfile=/opt/sbin/hwclock \
	--with-pidfile=/opt/var/run/chrony/chronyd.pid \
	--with-sendmail=/opt/sbin/sendmail \
	--with-user=chrony

CONFIGURE_VARS+=CPPFLAGS="-DNDEBUG -I$(STAGING_DIR)/opt/include"

define Package/chrony/install
	$(INSTALL_DIR) $(1)/opt/bin/
	$(INSTALL_DIR) $(1)/opt/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/chronyd $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/chronyc $(1)/opt/bin
#	$(INSTALL_BIN) ./files/chrony.ntp-hotplug $(1)/usr/sbin/chrony-hotplug
	$(INSTALL_DIR) $(1)/opt/etc/init.d
#	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_DIR) $(1)/opt/etc/chrony
#	$(INSTALL_DIR) $(1)/etc/hotplug.d/iface
#	$(INSTALL_BIN) ./files/chrony.hotplug $(1)/etc/hotplug.d/iface/20-chrony
	$(INSTALL_BIN) ./files/S06chronyd $(1)/opt/etc/init.d
#	$(INSTALL_CONF) ./files/chrony.config $(1)/etc/config/chrony
#	$(INSTALL_CONF) ./files/chrony.conf $(1)/etc/chrony/chrony.conf
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/examples/chrony.conf.* $(1)/opt/etc/chrony/
endef

Package/chrony-nts/install= $(Package/chrony/install)

$(eval $(call BuildPackage,chrony))
$(eval $(call BuildPackage,chrony-nts))
