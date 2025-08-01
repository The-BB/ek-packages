#
# Copyright (C) 2011 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=dante
PKG_VERSION:=1.4.4
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=http://www.inet.no/dante/files/
PKG_HASH:=1973c7732f1f9f0a4c0ccf2c1ce462c7c25060b25643ea90f9b98f53a813faec

PKG_MAINTAINER:=David Yang <mmyangfl@gmail.com>
PKG_LICENSE:=BSD-4-Clause

PKG_FIXUP:=autoreconf
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1
PKG_BUILD_DEPENDS:=USE_MUSL:libtirpc

include $(INCLUDE_DIR)/package.mk

CONFIGURE_ARGS += \
	--without-upnp \
	--without-pam \
	--disable-libwrap \
	--with-socks-conf=/opt/etc/socks.conf \
	--with-sockd-conf=/opt/etc/sockd.conf \
	--with-pidfile=/opt/var/run/sockd.pid

CONFIGURE_VARS += \
	ac_cv_search_pam_start="" \
	ac_cv_func_sched_setscheduler=no

TARGET_CFLAGS += $(if $(CONFIG_USE_MUSL),-I"$(STAGING_DIR)/opt/include/tirpc")

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include
	$(CP) $(PKG_INSTALL_DIR)/opt/include/* $(1)/opt/include/
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/*.{a,so*,la} $(1)/opt/lib/
endef


define Package/dante/default
  TITLE:=Dante SOCKS
  URL:=http://www.inet.no/dante/
#  DEPENDS:= +USE_GLIBC:libcrypt-compat
endef

define Package/dante/default/description
Dante is a circuit-level firewall/proxy that can be used to provide convenient
and secure network connectivity, requiring only that the server Dante runs on
has external network connectivity. Dante is used daily by Fortune 100 companies
and large international organizations, either as a standard SOCKS server or as
a "reverse proxy".
endef

define Package/libsocks
  $(call Package/dante/default)
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE+= Library
  ABI_VERSION:=0
endef

define Package/libsocks/description
$(call Package/dante/default/description)
This package provides the shared libsocks library.
endef

define Package/libsocks/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libsocks.so.* $(1)/opt/lib/
endef


define Package/sockd
  $(call Package/dante/default)
  SUBMENU:=Web Servers/Proxies
  SECTION:=net
  CATEGORY:=Network
  TITLE+= Daemon
endef

define Package/sockd/description
$(call Package/dante/default/description)
This package provides the Dante sockd daemon.
endef

define Package/sockd/conffiles
/opt/etc/sockd.conf
endef

define Package/sockd/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(CP) $(PKG_INSTALL_DIR)/opt/sbin/sockd $(1)/opt/sbin/

	$(INSTALL_DIR) $(1)/opt/etc
	$(CP) $(PKG_BUILD_DIR)/example/sockd.conf $(1)/opt/etc/

	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S90sockd $(1)/opt/etc/init.d/
endef


define Package/socksify
  $(call Package/dante/default)
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Web Servers/Proxies
  TITLE+= Client
endef

define Package/socksify/description
$(call Package/dante/default/description)
This package provides the Dante socksify client.
endef

define Package/socksify/conffiles
/opt/etc/socks.conf
endef

define Package/socksify/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) $(PKG_INSTALL_DIR)/opt/bin/socksify $(1)/opt/bin/

ifeq ($(CONFIG_USE_GLIBC),y)
	$(SED) 's|libdl.so|libdl.so.2|g' $(1)/opt/bin/socksify
endif

	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libdsocks.so* $(1)/opt/lib/

	$(INSTALL_DIR) $(1)/opt/etc
	$(CP) $(PKG_BUILD_DIR)/example/socks.conf $(1)/opt/etc/
endef


$(eval $(call BuildPackage,libsocks))
$(eval $(call BuildPackage,sockd))
$(eval $(call BuildPackage,socksify))
