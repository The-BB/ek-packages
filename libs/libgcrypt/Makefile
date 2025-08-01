#
# Copyright (C) 2005-2011 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=libgcrypt
PKG_VERSION:=1.11.1
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=https://www.gnupg.org/ftp/gcrypt/libgcrypt/
PKG_HASH:=24e91c9123a46c54e8371f3a3a2502f1198f2893fbfbf59af95bc1c21499b00e

PKG_MAINTAINER:=W. Michael Petullo <mike@flyn.org>
PKG_CPE_ID:=cpe:/a:gnupg:libgcrypt

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/libgcrypt
  SECTION:=libs
  CATEGORY:=Libraries
  DEPENDS:=+libgpg-error
  TITLE:=GNU crypto library
  URL:=https://www.gnupg.org/related_software/libgcrypt/
  LICENSE:=LGPL-2.1-or-later
  LICENSE_FILES:=COPYING.LIB
endef

define Package/libgcrypt/description
 This is a general purpose cryptographic library based on the code from
 GnuPG. It provides functions for all cryptograhic building blocks:
 symmetric ciphers (AES, DES, Arcfour, CAST5), hash algorithms (MD5, SHA-1,
 RIPE-MD160, SHA-224/256, SHA-384/512), MACs (HMAC for all hash
 algorithms), public key algorithms (RSA, DSA), large integer functions,
 random numbers and a lot of supporting functions. Some algorithms have
 been disabled to reduce size (Blowfish, Twofish, Serpent,
 RC2, SEED, Camellia, CRC, MD4, TIGER-192, Whirlpool, ElGamal, ECC).
endef

CONFIGURE_ARGS += \
	--enable-shared \
	--enable-static \
	--disable-asm \
	--disable-doc \
	--with-gpg-error-prefix="$(STAGING_DIR)/opt"

TARGET_CFLAGS += $(FPIC)

# Entware specifics: add libpthread
TARGET_LDFLAGS += $(if $(CONFIG_USE_GLIBC),-pthread)

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/bin $(2)/bin $(1)/opt/include $(1)/opt/lib/pkgconfig $(1)/opt/share/aclocal
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/libgcrypt-config $(1)/opt/bin/
	$(CP) $(PKG_INSTALL_DIR)/opt/include/gcrypt*.h $(1)/opt/include/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libgcrypt.{la,a,so*} $(1)/opt/lib/
	$(CP) $(PKG_INSTALL_DIR)/opt/share/aclocal/libgcrypt.m4 $(1)/opt/share/aclocal/
	$(SED) 's,^\(prefix\|exec_prefix\)=.*,\1=$(STAGING_DIR)/opt,g' $(1)/opt/bin/libgcrypt-config
	$(LN) ../../opt/bin/libgcrypt-config $(2)/bin/libgcrypt-config
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/*.pc $(1)/opt/lib/pkgconfig
endef

define Package/libgcrypt/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libgcrypt.so.* $(1)/opt/lib/
endef

$(eval $(call BuildPackage,libgcrypt))
