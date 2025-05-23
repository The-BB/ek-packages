# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=getdns
PKG_VERSION:=1.7.3
PKG_RELEASE:=2

PKG_LICENSE:=BSD-3-Clause
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://getdnsapi.net/dist/
PKG_HASH:=f1404ca250f02e37a118aa00cf0ec2cbe11896e060c6d369c6761baea7d55a2c

CMAKE_INSTALL:=1

PKG_CONFIG_DEPENDS:= \
	CONFIG_GETDNS_BUILD_LIBEVENT2 \
	CONFIG_GETDNS_ENABLE_STUB_ONLY \
	CONFIG_GETDNS_ENABLE_IDN_LIBIDN2

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/getdns/Default
	TITLE:=getdns
	URL:=https://getdnsapi.net/
endef

define Package/getdns
	$(call Package/getdns/Default)
	SECTION:=libs
	CATEGORY:=Libraries
	TITLE+= (library)
	DEPENDS+= +libopenssl +GETDNS_BUILD_LIBEVENT2:libevent2 +!GETDNS_ENABLE_STUB_ONLY:libunbound +GETDNS_ENABLE_IDN_LIBIDN2:libidn2
	MENU:=1
endef

define Package/getdns/description
	This package contains the getdns library (libgetdns).
	This package also contains the "getdns_query" command line wrapper for getdns exposing the features of this implementation (both in the official API and the additional API functions).
endef

define Package/getdns/config
	source "$(SOURCE)/Config.in"
endef

# CMAKE options for GetDNS are described in this document:
# https://getdnsapi.net/quick-start/cmake-quick-start/
CMAKE_OPTIONS += -DBUILD_LIBEV=OFF
CMAKE_OPTIONS += -DBUILD_LIBUV=OFF

CMAKE_OPTIONS += -DBUILD_LIBEVENT2=$(if $(CONFIG_GETDNS_BUILD_LIBEVENT2),ON,OFF)
CMAKE_OPTIONS += -DENABLE_STUB_ONLY=$(if $(CONFIG_GETDNS_ENABLE_STUB_ONLY),ON,OFF)
CMAKE_OPTIONS += -DUSE_LIBIDN2=$(if $(CONFIG_GETDNS_ENABLE_IDN_LIBIDN2),ON,OFF)

# getdns will use libbsd for the functions inet_pton, inet_ntop, strlcpy if
# present, otherwise it will use builtin code for these functions. In order to
# force the use of the built in code and remove the libbsd dependency disable
# the test for libbsd.
CMAKE_OPTIONS += -DBSD_LIBRARY=OFF

# Disable static linking to ensure that utility programs such as getdns_query
# don't end up as large statically linked binaries.
CMAKE_OPTIONS += -DENABLE_STATIC=OFF
CMAKE_OPTIONS += -DENABLE_SHARED=ON  # This is the default

define Package/getdns/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libgetdns.so.* $(1)/opt/lib/
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/getdns_query $(1)/opt/sbin/getdns_query
endef

$(eval $(call BuildPackage,getdns))
