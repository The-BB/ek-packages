#
# Copyright (C) 2005-2011 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=libgpg-error
PKG_VERSION:=1.55
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=https://mirrors.dotsrc.org/gcrypt/libgpg-error \
		http://ring.ksc.gr.jp/archives/net/gnupg/libgpg-error \
		https://www.gnupg.org/ftp/gcrypt/libgpg-error
PKG_HASH:=95b178148863f07d45df0cea67e880a79b9ef71f5d230baddc0071128516ef78

PKG_MAINTAINER:=W. Michael Petullo <mike@flyn.org>
PKG_LICENSE:=LGPL-2.1-or-later
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:gnupg:libgpg-error

PKG_FIXUP:=autoreconf
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/libgpg-error
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=GnuPG error handling helper library
  URL:=https://www.gnupg.org/related_software/libgpg-error/
endef

define Package/libgpg-error/description
	An helper library for common error codes and descriptions.
	This is a library that defines common error values for all GnuPG
	components. Among these are GPG, GPGSM, GPGME, GPG-Agent, libgcrypt,
	Libksba, DirMngr, Pinentry, SmartCard Daemon and possibly more in the
	future.
endef

CONFIGURE_ARGS += \
	--enable-shared \
	--enable-static \
	--enable-install-gpg-error-config \
	--disable-doc \
	--disable-languages \
	--disable-rpath \
	--disable-tests

define Build/InstallDev
	$(INSTALL_DIR) $(2)/bin $(1)/opt/bin
	$(INSTALL_BIN) \
		$(PKG_INSTALL_DIR)/opt/bin/gpg-error-config \
		$(2)/bin/
	$(SED) \
		's,^\(prefix\|exec_prefix\)=.*,\1=$(STAGING_DIR)/opt,g' \
		$(2)/bin/gpg-error-config
	$(LN) $(STAGING_DIR)/host/bin/gpg-error-config $(1)/opt/bin/gpg-error-config

	$(INSTALL_BIN) \
		$(PKG_INSTALL_DIR)/opt/bin/gpgrt-config \
		$(2)/bin/
	$(SED) \
		's,^\(prefix\|exec_prefix\)=.*,\1=$(STAGING_DIR)/opt,g' \
		$(2)/bin/gpgrt-config
	$(LN) $(STAGING_DIR)/host/bin/gpgrt-config $(1)/opt/bin/gpgrt-config

	$(INSTALL_DIR) $(1)/opt/include
	$(INSTALL_DATA) \
		$(PKG_INSTALL_DIR)/opt/include/gpg-error.h \
		$(1)/opt/include/

	$(INSTALL_DATA) \
		$(PKG_INSTALL_DIR)/opt/include/gpgrt.h \
		$(1)/opt/include/

	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/libgpg-error.{la,a,so*} \
		$(1)/opt/lib/

	$(INSTALL_DIR) $(1)/opt/share/aclocal
	$(INSTALL_DATA) \
		$(PKG_INSTALL_DIR)/opt/share/aclocal/gpg-error.m4 \
		$(1)/opt/share/aclocal/

	$(INSTALL_DATA) \
		$(PKG_INSTALL_DIR)/opt/share/aclocal/gpgrt.m4 \
		$(1)/opt/share/aclocal/

	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/pkgconfig/gpg-error.pc \
		$(1)/opt/lib/pkgconfig
endef

define Package/libgpg-error/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/libgpg-error.so.* \
		$(1)/opt/lib/
endef

$(eval $(call BuildPackage,libgpg-error))
