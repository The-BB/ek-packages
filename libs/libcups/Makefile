#
# Copyright (C) 2006-2012 OpenWrt.org
# Copyright (C) 2017-2018 Luiz Angelo Daros de Luca <luizluca@gmail.com>
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# Entware specific: libcups/install section, cups package with all other stuff
# avahi/dbus enabled

include $(TOPDIR)/rules.mk

PKG_NAME:=cups
PKG_VERSION:=2.2.12
PKG_RELEASE:=3

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION)-source.tar.gz
PKG_SOURCE_URL:=https://github.com/apple/cups/releases/download/v$(PKG_VERSION)/
PKG_HASH:=0f61ab449e4748a24c6ab355b481ff7691247a140d327b2b7526fce34b7f9aa8
PKG_MAINTAINER:=Luiz Angelo Daros de Luca <luizluca@gmail.com>
PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=LICENSE.txt
PKG_CPE_ID:=cpe:/a:apple:cups

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

define Package/libcups/Default
  URL:=http://www.cups.org/
  SUBMENU:=Printing
endef

define Package/libcups
$(call Package/cups/Default)
  SECTION:=libs
  CATEGORY:=Libraries
  DEPENDS:=+zlib +libpthread +libpng +libjpeg +libusb-1.0
  DEPENDS+=+libavahi-client +libavahi-dbus-support $(ICONV_DEPENDS)
  TITLE:=Common UNIX Printing System - Core library
endef

define Package/cups
$(call Package/cups/Default)
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+libcups +libstdcpp
  TITLE:=Common UNIX Printing System
endef

define Package/libcups/description
	Common UNIX Printing System - Core library
endef

define Package/cups/description
	Common UNIX Printing System
endef

CONFIGURE_ARGS+=--with-cups-user="nobody" \
		--with-cups-group="nogroup" \
		--without-perl \
		--without-python \
		--without-php \
		--enable-avahi \
		--enable-shared \
		--enable-libusb \
		--disable-acl \
		--enable-dbus \
		--disable-dnssd \
		--disable-launchd \
		--disable-pam \
		--disable-gnutls \
		--disable-cdsassl \
		--disable-ssl \
		--disable-gssapi \
		--with-cupsd-file-perm="0700" \
		--with-printcap="/opt/etc/printcap" \
		--without-java \
		--without-rcdir \
		--disable-systemd \
		UNAME="Linux" \
		LIBS="-lz -lpng -ljpeg"

define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR) \
		$(TARGET_CONFIGURE_OPTS) \
		DSTROOT="$(PKG_INSTALL_DIR)" \
		STRIP="/bin/true" \
		all install
	$(INSTALL_DIR) $(PKG_INSTALL_DIR)/opt/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/cups-config $(PKG_INSTALL_DIR)/opt/bin
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/cups-config $(1)/opt/bin/
	$(INSTALL_DIR) $(2)/bin
	$(LN) ../../opt/bin/cups-config $(2)/bin
	# XXX fix prefix
	$(SED) 's,^\(prefix\|exec_prefix\)=.*,\1=$(STAGING_DIR)/opt,g' \
		$(1)/opt/bin/cups-config

	$(INSTALL_DIR) $(1)/opt/include
	$(CP) $(PKG_INSTALL_DIR)/opt/include/cups $(1)/opt/include/
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libcups*.so* $(1)/opt/lib/
endef

define Package/cups/conffiles
/opt/etc/cups
endef

define Package/libcups/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib*/libcups*.so* $(1)/opt/lib/
endef

define Package/cups/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/{bin,sbin,etc,share,var} $(1)/opt/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/cups $(1)/opt/lib/
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S60cupsd $(1)/opt/etc/init.d/
	# XXX cleanup
	$(SED) 's,$(TOOLCHAIN_DIR),/opt,g;s,$(STAGING_DIR),,g' $(1)/opt/bin/cups-config
	rm -fr $(1)/opt/share/man
endef

$(eval $(call BuildPackage,libcups))
$(eval $(call BuildPackage,cups))
