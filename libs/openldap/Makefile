#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=openldap
PKG_VERSION:=2.6.10
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tgz
PKG_SOURCE_URL:=https://mirror.eu.oneandone.net/software/openldap/openldap-release/ \
	https://www.openldap.org/software/download/OpenLDAP/openldap-release/
PKG_HASH:=c065f04aad42737aebd60b2fe4939704ac844266bc0aeaa1609f0cad987be516
PKG_LICENSE:=OLDAP-2.8
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:openldap:openldap

PKG_FIXUP:=autoreconf

# Fixes the following:
# libtool: Version mismatch error.  This is libtool 2.4.2, but the
# libtool: definition of this LT_INIT comes from libtool 2.4.6.
# libtool: You should recreate aclocal.m4 with macros from libtool 2.4.2
# libtool: and run autoconf again.
PKG_REMOVE_FILES:=build/ltversion.m4

PKG_CONFIG_DEPENDS := \
        CONFIG_OPENLDAP_DEBUG \
        CONFIG_OPENLDAP_CRYPT \
        CONFIG_OPENLDAP_MONITOR \
        CONFIG_OPENLDAP_DB47 \
        CONFIG_OPENLDAP_ICU

include $(INCLUDE_DIR)/package.mk

define Package/libopenldap/Default
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=OpenLDAP
  TITLE:=LDAP directory suite
  URL:=https://www.openldap.org/
  MAINTAINER:=W. Michael Petullo <mike@flyn.org>
endef

define Package/libopenldap
  $(call Package/libopenldap/Default)
  MENU:=1
  DEPENDS:=+libopenssl +libsasl2 +libpthread +libuuid +OPENLDAP_DB47:libdb47 +OPENLDAP_ICU:icu
  TITLE+= (libraries)
endef

define Package/libopenldap/config
  source "$(SOURCE)/Config.in"
endef

define Package/openldap/config
  source "$(SOURCE)/Config.in"
endef

define Package/libopenldap/description
OpenLDAP Software is an open source implementation of the Lightweight Directory Access Protocol (LDAP). This package contains the shared LDAP client libraries, needed by other programs.
endef

define Package/libopenldap/conffiles
/opt/etc/openldap/ldap.conf
endef

define Package/openldap-utils
  $(call Package/libopenldap/Default)
  DEPENDS:=+libopenldap
  TITLE+= (utilities)
endef

define Package/openldap-utils/description
This package contains client programs required to access LDAP servers.
endef

define Package/openldap-server
  $(call Package/libopenldap/Default)
  DEPENDS:=+libopenldap +libuuid
  TITLE+= (server)
endef

define Package/openldap-server/description
This package contains server programs required to provide LDAP services.
endef

define Package/openldap-server/conffiles
/opt/etc/openldap/slapd.conf
endef

TARGET_CFLAGS += $(FPIC) -lpthread \
	-DURANDOM_DEVICE=\\\"/dev/urandom\\\"

CONFIGURE_ARGS += \
	--enable-dynamic \
	--enable-syslog \
	--with-cyrus-sasl \
	--with-threads \
	--with-tls \
	--with-yielding-select="yes" \
	--enable-null \
	--disable-relay


ifdef CONFIG_OPENLDAP_CRYPT
	CONFIGURE_ARGS+= --enable-crypt
else
	CONFIGURE_ARGS+= --disable-crypt
endif

ifdef CONFIG_OPENLDAP_MONITOR
	CONFIGURE_ARGS+= --enable-monitor
else
	CONFIGURE_ARGS+= --disable-monitor
endif

ifdef CONFIG_OPENLDAP_DEBUG
	CONFIGURE_ARGS+= --enable-debug
else
	CONFIGURE_ARGS+= --disable-debug
endif

ifdef CONFIG_OPENLDAP_DB47
	CONFIGURE_ARGS+= \
		--enable-bdb \
		--enable-hdb
else
	CONFIGURE_ARGS+= \
		--disable-bdb \
		--disable-hdb
endif

ifndef CONFIG_OPENLDAP_ICU
	CONFIGURE_VARS += \
		ol_cv_lib_icu="no"
endif

define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		HOSTCC="$(HOSTCC)" \
		depend all install
	cd $(PKG_BUILD_DIR)/libraries/liblmdb && $(MAKE) $(CONFIGURE_VARS)
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/include/{lber,ldap}*.h $(1)/opt/include/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/lib{lber,ldap}*.{a,so*} $(1)/opt/lib/
endef

define Package/libopenldap/install
	$(INSTALL_DIR) $(1)/opt/etc/openldap $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/etc/openldap/ldap.conf $(1)/opt/etc/openldap/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/lib{lber,ldap}*.so.* $(1)/opt/lib/
endef

define Package/openldap-utils/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) $(PKG_INSTALL_DIR)/opt/bin/ldap* $(1)/opt/bin/
endef

define Package/openldap-server/install
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S58slapd $(1)/opt/etc/init.d
	$(INSTALL_DIR) $(1)/opt/etc/openldap/schema
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/opt/etc/openldap/schema/* $(1)/opt/etc/openldap/schema/
	$(INSTALL_CONF) $(PKG_INSTALL_DIR)/opt/etc/openldap/slapd.conf $(1)/opt/etc/openldap/
	$(INSTALL_DIR) $(1)/opt/sbin
	# NB: OpenLDAP installs slapd into libexecdir, not sbindir
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/lib/slapd $(1)/opt/sbin/
	$(eval SLAPTOOLS := slapadd slapcat slapdn slapindex slappasswd slaptest slapauth slapacl slapschema)
	for i in $(SLAPTOOLS); do \
		$(LN) ./slapd $(1)/opt/sbin/$$$$i; \
	done
endef

$(eval $(call BuildPackage,libopenldap))
$(eval $(call BuildPackage,openldap-utils))
$(eval $(call BuildPackage,openldap-server))
