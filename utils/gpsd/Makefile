#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=gpsd
PKG_VERSION:=3.26.1
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@SAVANNAH/$(PKG_NAME)
PKG_HASH:=45c0d4779324bd59a47cfcb7ac57180d2dbdf418603d398a079392dabf1f740c

PKG_MAINTAINER:=Pushpal Sidhu <psidhu.devel@gmail.com>
PKG_LICENSE:=BSD-3-Clause
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:gpsd_project:gpsd

PKG_BUILD_DEPENDS:=scons/host

include $(INCLUDE_DIR)/package.mk
include ../../devel/scons/scons.mk

define Package/gpsd/Default
  DEPENDS+= +libusb-1.0
  URL:=https://gpsd.gitlab.io/gpsd/
endef

define Package/gpsd/Default/description
  gpsd is a userland daemon acting as a translator between GPS and AIS receivers
  and their clients. gpsd listens on port 2947 for clients requesting
  position/time/velocity information.  The receivers are expected to generate
  position information in a well-known format -- as NMEA-0183 sentences, SiRF
  binary, Rockwell binary, Garmin binary format, or other vendor binary
  protocols.  gpsd takes this information from the GPS and translates it into
  something uniform and easier to understand for clients.
endef

define Package/gpsd
  $(call Package/gpsd/Default)
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=An interface daemon for GPS receivers
  DEPENDS+= +gpsd-utils
endef

#define Package/gpsd/conffiles
#/etc/config/gpsd
#endef

define Package/gpsd/description
  $(call Package/gpsd/Default/description)
  This package contains the GPS daemon.
endef

define Package/gpsd-clients
  $(call Package/gpsd/Default)
  SECTION:=utils
  CATEGORY:=Utilities
  DEPENDS+= +libncurses
  TITLE:=GPS tools and clients
endef

define Package/gpsd-clients/description
  $(call Package/gpsd/Default/description)
  This package contains auxiliary tools and example clients for monitoring and
  testing the GPS daemon.
endef

define Package/gpsd-utils
  $(call Package/gpsd/Default)
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=GPS daemon utils
endef

define Package/gpsd-utils/description
  $(call Package/gpsd/Default/description)
  This package contains utilities for interacting with GPS daemon.
endef

define Package/libgps
  $(call Package/gpsd/Default)
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=C service library for communicating with the GPS daemon
  ABI_VERSION:=31
endef

define Package/libgps/description
  $(call Package/gpsd/Default/description)
  This package contains the libgps library.
endef

SCONS_VARS += \
	CFLAGS="$(TARGET_CFLAGS) $(EXTRA_CFLAGS) -L$(STAGING_DIR)/opt/lib" \
	LINKFLAGS="$(TARGET_LDFLAGS)"

SCONS_OPTIONS += \
	target_platform=linux \
	dbus_export=no \
	tsip=no \
	fv18=no \
	tripmate=no \
	earthmate=no \
	itrax=no \
	navcom=no \
	ubx=no \
	evermore=no \
	ntrip=no \
	libgpsmm=no \
	libQgpsmm=no \
	bluez=no \
	nostrip=yes \
	python=no \
	implicit_link=no \
	chrpath=no \
	manbuild=no \
	sysroot="$(TOOLCHAIN_ROOT_DIR)" \
	target="$(TARGET_CROSS:-=)"

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include
	$(CP) $(PKG_INSTALL_DIR)/opt/include/gps.h $(1)/opt/include/
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libgps*.so* $(1)/opt/lib/
	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/libgps.pc $(1)/opt/lib/pkgconfig
	$(SED) 's,/opt/include,$$$${prefix}/include,g' $(1)/opt/lib/pkgconfig/libgps.pc
	$(SED) 's,/opt/lib,$$$${exec_prefix}/lib,g' $(1)/opt/lib/pkgconfig/libgps.pc
endef

define Package/gpsd/install
#	$(INSTALL_DIR) $(1)/etc/config
#	$(INSTALL_CONF) ./files/gpsd.config $(1)/etc/config/gpsd
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S62gpsd $(1)/opt/etc/init.d/
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/gpsd $(1)/opt/sbin/

#	$(INSTALL_DIR) $(1)/etc/gpsd
#	$(INSTALL_BIN) ./files/etc/gpsd/device-hook $(1)/etc/gpsd/

#	$(INSTALL_DIR) $(1)/etc/uci-defaults
#	$(INSTALL_DATA) ./files/etc/uci-defaults/* $(1)/etc/uci-defaults/
endef

define Package/gpsd-clients/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) \
		$(PKG_INSTALL_DIR)/opt/bin/cgps \
		$(PKG_INSTALL_DIR)/opt/bin/gps{ctl,decode,mon,pipe} \
		$(PKG_INSTALL_DIR)/opt/bin/gpxlogger \
		$(PKG_INSTALL_DIR)/opt/bin/lcdgps \
		$(1)/opt/bin/
endef

define Package/gpsd-utils/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/gpsdctl $(1)/opt/sbin/
endef

define Package/libgps/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libgps.so.* $(1)/opt/lib/
endef

$(eval $(call BuildPackage,gpsd))
$(eval $(call BuildPackage,gpsd-clients))
$(eval $(call BuildPackage,gpsd-utils))
$(eval $(call BuildPackage,libgps))
