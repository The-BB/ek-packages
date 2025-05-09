#
# Copyright (C) 2009-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=krb5
PKG_VERSION:=1.21.3
PKG_RELEASE:=1

PKG_MAINTAINER:=W. Michael Petullo <mike@flyn.org>

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=NOTICE
PKG_CPE_ID:=cpe:/a:mit:kerberos_5

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://web.mit.edu/kerberos/dist/krb5/1.21
PKG_HASH:=b7a4cd5ead67fb08b980b21abd150ff7217e85ea320c9ed0c6dadd304840ad35

PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1
PKG_BUILD_FLAGS:=no-mips16

include $(INCLUDE_DIR)/package.mk

MAKE_PATH:=src

define Package/krb5/Default
	SECTION:=net
	CATEGORY:=Network
	TITLE:=Kerberos
	URL:=https://web.mit.edu/kerberos/
endef

define Package/krb5-libs
	SECTION:=net
	CATEGORY:=Network
	TITLE:=Kerberos
	DEPENDS:=+libncurses +libss +libcomerr
	TITLE:=Kerberos 5 Shared Libraries
	URL:=http://web.mit.edu/kerberos/
endef

define Package/krb5-server
	$(call Package/krb5/Default)
	DEPENDS:=+krb5-libs +libpthread
	TITLE:=Kerberos 5 Server
endef

define Package/krb5-server-extras
	$(call Package/krb5/Default)
	DEPENDS:=+krb5-libs +libpthread
	TITLE:=Kerberos 5 Admin Server
endef

define Package/krb5-client
	$(call Package/krb5/Default)
	DEPENDS:=+krb5-libs
	TITLE:=Kerberos 5 Client
endef

define Package/krb5/description
  Kerberos is a network authentication protocol. 
  It is designed to provide strong authentication for client/server applications by using secret-key cryptography.
endef

CONFIGURE_PATH = ./src

CONFIGURE_VARS += \
	cross_compiling=yes \
	krb5_cv_attr_constructor_destructor=yes,yes \
	krb5_cv_sys_rcdir=/opt/tmp \
	ac_cv_func_regcomp=yes \
	ac_cv_printf_positional=yes \
	ac_cv_file__etc_environment=no \
	ac_cv_file__etc_TIMEZONE=no \
	ac_cv_header_keyutils_h=no \
	ac_cv_lib_keyutils_add_key=no \
	DEFKTNAME='FILE:/opt/etc/krb5.keytab'

CONFIGURE_ARGS += \
	--localstatedir=/opt/etc \
	--with-system-ss \
	--with-system-et \
	--without-system-verto \
	--without-tcl \
	--without-tls-impl \
	--without-libedit \
	--without-readline \
	--disable-rpath \
	--disable-pkinit \
	--with-size-optimizations \
	--with-crypto-impl=builtin

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include
	$(CP) $(PKG_INSTALL_DIR)/opt/include $(1)/opt/
#	$(INSTALL_DIR) $(1)/usr
	$(CP) $(PKG_INSTALL_DIR)/opt/lib $(1)/opt/
	# needed for samba4, to detect system-krb5
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/krb5-config $(1)/opt/bin
	$(SED) 's,^\(prefix\|exec_prefix\)=.*,\1=$(STAGING_DIR)/opt,g' $(1)/opt/bin/krb5-config
	$(INSTALL_DIR) $(2)/bin
	$(LN) ../../opt/bin/krb5-config $(2)/bin/krb5-config
endef

define Package/krb5-libs/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(INSTALL_DIR) $(1)/opt/lib/krb5
	$(INSTALL_DIR) $(1)/opt/lib/krb5/plugins
	$(INSTALL_DIR) $(1)/opt/lib/krb5/plugins/kdb
	$(INSTALL_DIR) $(1)/opt/lib/krb5/plugins/libkrb5
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/krb5/plugins/kdb/db2.so $(1)/opt/lib/krb5/plugins/kdb
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/*.so* $(1)/opt/lib
endef

define Package/krb5-client/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/kdestroy $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/kinit $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/klist $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/kpasswd $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/ksu $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/kvno $(1)/opt/bin
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/krb5-send-pr $(1)/opt/sbin
endef

# Removed some server-side software to reduce package size. This should be
# put in a separate package if needed.
define Package/krb5-server/install
#	$(INSTALL_DIR) $(1)/etc/init.d
#	$(INSTALL_BIN) ./files/krb5kdc $(1)/etc/init.d/krb5kdc
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/kadmin.local $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/kdb5_util $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/krb5kdc $(1)/opt/sbin
endef

define Package/krb5-server-extras/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/kadmind $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/kprop $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/kpropd $(1)/opt/sbin
endef

#define Package/krb5-server/postinst
#mkdir -p $${IPKG_INSTROOT}/etc/krb5kdc
#touch $${IPKG_INSTROOT}/etc/krb5kdc/kadm5.acl
#endef

$(eval $(call BuildPackage,krb5-libs))
$(eval $(call BuildPackage,krb5-server))
$(eval $(call BuildPackage,krb5-server-extras))
$(eval $(call BuildPackage,krb5-client))
