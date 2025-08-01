#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# Entware specific: conffile defined, all files from /opt/lib/sqid added

include $(TOPDIR)/rules.mk

PKG_NAME:=squid
PKG_VERSION:=6.11
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=http://www2.pl.squid-cache.org/Versions/v6/ \
	http://www.squid-cache.org/Versions/v6/
PKG_HASH:=b6e60c08429314287925331b3da6c03b7d3c35b7e03319f78edb8bad6fc68899

PKG_MAINTAINER:=Marko Ratkaj <markoratkaj@gmail.com>
PKG_LICENSE:=GPL-2.0-or-later
PKG_CPE_ID:=cpe:/a:squid-cache:squid

PKG_FIXUP:=autoreconf
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/squid/Default
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Web Servers/Proxies
  URL:=https://www.squid-cache.org/
endef

define Package/squid
  $(call Package/squid/Default)
  MENU:=1
  DEPENDS:= +USE_GLIBC:libbsd +libpthread +librt +libltdl +libstdcpp +libatomic #+USE_GLIBC:libcrypt-compat
  DEPENDS+= +SQUID_use-gnutls:libgnutls +SQUID_use-openssl:libopenssl
  DEPENDS+= +SQUID_with-libcap:libcap
  DEPENDS+= +SQUID_with-nettle:libnettle
  DEPENDS+= +SQUID_with-expat:libexpat
  DEPENDS+= +SQUID_with-libxml2:libxml2
  USERID:=squid=137:squid=137
  TITLE:=full-featured Web proxy cache
endef

define Package/squid/description
  Squid is a caching proxy for the Web supporting HTTP, HTTPS, FTP, and more.
  It reduces bandwidth and improves response times by caching and reusing
  frequently-requested web pages.
endef

define Package/squid/config
  source "$(SOURCE)/Config.in"
endef

define Package/squid/conffiles
/opt/etc/squid/squid.conf
endef

define Package/squid-mod-cachemgr
  $(call Package/squid/Default)
  DEPENDS:=squid
  TITLE:=Web based proxy manager and reporting tool
endef

CONFIGURE_ARGS += \
	BUILDCXX=$(HOSTCXX_NOCACHE) \
	BUILDCXXFLAGS="$(HOST_CXXFLAGS) $(HOST_CPPFLAGS) -std=c++17" \
	--datadir=/opt/share/squid \
	--libexecdir=/opt/lib/squid \
	--sysconfdir=/opt/etc/squid \
	--with-logdir=/opt/var/log/squid \
	--with-pidfile=/opt/var/run/squid.pid \
	--disable-static \
	--enable-delay-pools \
	--enable-cache-digests \
	--enable-linux-netfilter \
	--disable-unlinkd \
	--enable-x-accelerator-vary \
	--disable-auto-locale \
	--enable-epoll \
	--with-filedescriptors=2048 \
	--disable-external-acl-helpers \
	--disable-arch-native \
	--without-mit-krb5 \
	--without-heimdal-krb5 \
	--without-netfilter-conntrack \
	--disable-ident-lookups \
	$(if $(CONFIG_SQUID_auth-basic),--enable,--disable)-auth-basic \
	$(if $(CONFIG_SQUID_auth-digest),--enable,--disable)-auth-digest \
	$(if $(CONFIG_SQUID_auth-ntlm),--enable,--disable)-auth-ntlm \
	$(if $(CONFIG_SQUID_auth-negotiate),--enable,--disable)-auth-negotiate \
	$(if $(CONFIG_SQUID_enable-ipv6),--enable,--disable)-ipv6 \
	$(if $(CONFIG_SQUID_enable-ssl-crtd),--enable-ssl-crtd) \
	$(if $(CONFIG_SQUID_use-gnutls),--with,--without)-gnutls \
	$(if $(CONFIG_SQUID_use-gnutls),--without-openssl) \
	$(if $(CONFIG_SQUID_use-gnutls),,--with-openssl="$(STAGING_DIR)/opt") \
	$(if $(CONFIG_SQUID_enable-icmp),--enable,--disable)-icmp \
	$(if $(CONFIG_SQUID_enable-icap-client),--enable,--disable)-icap-client \
	$(if $(CONFIG_SQUID_enable-snmp),--enable,--disable)-snmp \
	$(if $(CONFIG_SQUID_with-libcap),--with,--without)-cap \
	$(if $(CONFIG_SQUID_with-nettle),--with,--without)-nettle \
	$(if $(CONFIG_SQUID_with-expat),--with,--without)-expat \
	$(if $(CONFIG_SQUID_with-libxml2),--with,--without)-xml2

CONFIGURE_VARS += \
	ac_cv_header_linux_netfilter_ipv4_h=yes \
	ac_cv_epoll_works=yes

TARGET_CFLAGS += -Wno-error
TARGET_CXXFLAGS += -std=c++17
TARGET_LDFLAGS += -latomic

define Package/squid/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/squid $(1)/opt/sbin/

	$(INSTALL_DIR) $(1)/opt/lib/squid
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/lib/squid/* $(1)/opt/lib/squid/

#	$(INSTALL_DIR) $(1)/etc/config
#	$(INSTALL_CONF) ./files/squid.config $(1)/etc/config/squid

	$(INSTALL_DIR) $(1)/opt/etc/squid
	$(CP) $(PKG_INSTALL_DIR)/opt/etc/squid/* $(1)/opt/etc/squid/
	$(INSTALL_CONF) ./files/squid.conf $(1)/opt/etc/squid/

	$(INSTALL_DIR) $(1)/opt/etc/init.d/
	$(INSTALL_BIN) ./files/S22squid $(1)/opt/etc/init.d/

	$(INSTALL_DIR) $(1)/opt/share/squid/icons/
	$(CP) $(PKG_INSTALL_DIR)/opt/share/squid/icons/* $(1)/opt/share/squid/icons/
	$(CP) $(PKG_INSTALL_DIR)/opt/share/squid/mib.txt $(1)/opt/share/squid/

	$(INSTALL_DIR) $(1)/opt/share/squid/errors/templates/
	$(CP) $(PKG_INSTALL_DIR)/opt/share/squid/errors/templates/* $(1)/opt/share/squid/errors/templates/
	# Entware specific: fix exec path in scripts
	$(SED) 's,^#!.*perl,#!/opt/bin/perl,' $(1)/opt/lib/squid/*
endef

define Package/squid-mod-cachemgr/install
	$(INSTALL_DIR) $(1)/opt/share/www/cgi-bin/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/squid/cachemgr.cgi $(1)/opt/share/www/cgi-bin/
endef

$(eval $(call BuildPackage,squid))
$(eval $(call BuildPackage,squid-mod-cachemgr))
