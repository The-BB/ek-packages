#
# Copyright (C) 2010-2016 OpenWrt.org
# Copyright (C) 2009-2016 Thomas Heil <heil@terminal-consulting.de>
# Copyright (C) 2018 Christian Lachner <gladiac@gmail.com>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=haproxy
PKG_VERSION:=3.0.10
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://www.haproxy.org/download/3.0/src
PKG_HASH:=d1508670b6fd5839c669a0a916842f0d3d3d0b578bb351a2a74a1de3d929ce26

PKG_MAINTAINER:=Thomas Heil <heil@terminal-consulting.de>, \
		Christian Lachner <gladiac@gmail.com>
PKG_LICENSE:=GPL-2.0-only
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:haproxy:haproxy

include $(INCLUDE_DIR)/package.mk

define Package/haproxy/Default
  SUBMENU:=Web Servers/Proxies
  SECTION:=net
  CATEGORY:=Network
  TITLE:=TCP/HTTP Load Balancer
  URL:=https://www.haproxy.org/
  DEPENDS:= +libpcre2 +libltdl +zlib +libpthread +liblua5.4 +libatomic #+USE_GLIBC:libcrypt-compat
endef

define Package/haproxy/conffiles
/opt/etc/haproxy.cfg
endef

Package/haproxy-nossl/conffiles = $(Package/haproxy/conffiles)

define Package/haproxy/Default/description
 Open source Reliable, High Performance TCP/HTTP Load Balancer.
endef

define Package/haproxy
  $(call Package/haproxy/Default)
  TITLE+=with SSL support
  DEPENDS+= +libopenssl +libncurses +libreadline
  VARIANT:=ssl
endef

define Package/haproxy/description
$(call Package/haproxy/Default/description)
 This package is built with SSL and LUA support.
endef

define Package/haproxy-nossl
  $(call Package/haproxy/Default)
  TITLE+=without SSL support
  VARIANT:=nossl
  CONFLICTS:=haproxy
endef

define Package/haproxy-nossl/description
  $(call Package/haproxy/Default/description)
 This package is built without SSL support.
endef

TARGET=linux-glibc
ENABLE_LUA:=y

ifeq ($(CONFIG_USE_UCLIBC),y)
	ADDON+=USE_BACKTRACE=
	ADDON+=USE_LIBCRYPT=
endif

ifeq ($(CONFIG_USE_MUSL),y)
	TARGET=linux-musl
endif

ifeq ($(BUILD_VARIANT),ssl)
	ADDON+=USE_OPENSSL=1
	ADDON+=ADDLIB="-lcrypto -lm"
	ADDON+=USE_QUIC=1
	ADDON+=USE_QUIC_OPENSSL_COMPAT=1
endif

define Build/Compile
	$(MAKE) TARGET=$(TARGET) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		CC="$(TARGET_CC)" \
		PCREDIR="$(STAGING_DIR)/opt/" \
		USE_LUA=1 LUA_LIB_NAME="lua5.4" LUA_INC="$(STAGING_DIR)/opt/include/lua5.4" LUA_LIB="$(STAGING_DIR)/opt/lib" \
		SMALL_OPTS="-DBUFSIZE=16384 -DMAXREWRITE=1030 -DSYSTEM_MAXCONN=165530" \
		USE_ZLIB=1 USE_PCRE2=1 USE_PCRE2_JIT=1 USE_PTHREAD_PSHARED=1 USE_LIBATOMIC=1 USE_PROMEX=1 \
		VERSION="$(PKG_VERSION)" SUBVERS="-$(PKG_RELEASE)" \
		VERDATE="$(shell date -d @$(SOURCE_DATE_EPOCH) '+%Y/%m/%d')" IGNOREGIT=1 \
		$(ADDON) \
		CFLAGS="$(TARGET_CFLAGS) -fno-strict-aliasing -Wdeclaration-after-statement -Wno-unused-label -Wno-sign-compare -Wno-unused-parameter -Wno-clobbered -Wno-missing-field-initializers -Wno-cast-function-type -Wno-address-of-packed-member -Wtype-limits -Wshift-negative-value -Wshift-overflow=2 -Wduplicated-cond -Wnull-dereference -fwrapv -fasynchronous-unwind-tables -Wno-null-dereference" \
		LD="$(TARGET_CC)" \
		LDFLAGS="$(TARGET_LDFLAGS)"

	$(MAKE_VARS) $(MAKE) -C $(PKG_BUILD_DIR) \
		PREFIX="/opt" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		LD="$(TARGET_CC)" \
		LDFLAGS="$(TARGET_LDFLAGS)" \
		$(MAKE_FLAGS) \
		install

	$(MAKE_VARS) $(MAKE) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		CC="$(TARGET_CC)" \
		CFLAGS="$(TARGET_CFLAGS) -Wno-address-of-packed-member" \
		LDFLAGS="$(TARGET_LDFLAGS)" \
		admin/halog/halog
endef

define Package/haproxy/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/haproxy $(1)/opt/sbin/
	$(INSTALL_DIR) $(1)/opt/etc
	$(INSTALL_CONF) ./files/haproxy.cfg $(1)/opt/etc/
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S99haproxy $(1)/opt/etc/init.d/
endef

Package/haproxy-nossl/install = $(Package/haproxy/install)

define Package/halog
  $(call Package/haproxy)
  TITLE+=halog
  DEPENDS:=haproxy
endef

define Package/halog/description
  HAProxy Log Analyzer
endef

define Package/halog/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/admin/halog/halog $(1)/opt/bin/
endef

$(eval $(call BuildPackage,haproxy))
$(eval $(call BuildPackage,halog))
$(eval $(call BuildPackage,haproxy-nossl))
