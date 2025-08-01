#
# Copyright (C) 2007-2014 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=rsync
PKG_VERSION:=3.4.1
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://download.samba.org/pub/$(PKG_NAME)/src
PKG_HASH:=2924bcb3a1ed8b551fc101f740b9f0fe0a202b115027647cf69850d65fd88c52

PKG_MAINTAINER:=Maxim Storchak <m.storchak@gmail.com>
PKG_LICENSE:=GPL-3.0-or-later
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:rsync:rsync

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

define Package/rsync
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=File Transfer
  TITLE:=an open source utility that provides fast incremental file transfer
  DEPENDS:=+libpopt +zlib +RSYNC_xattr:libattr +RSYNC_acl:libacl +RSYNC_zstd:libzstd +RSYNC_xxhash:libxxhash +RSYNC_lz4:liblz4 $(ICONV_DEPENDS)
  URL:=https://rsync.samba.org/
  MENU:=1
endef

define Package/rsync/config
	source "$(SOURCE)/Config.in"
endef

TARGET_CFLAGS += $(if $(CONFIG_IPV6),-DINET6,)

CONFIGURE_ARGS += \
	--without-included-popt \
	--without-included-zlib \
	--disable-debug \
	--disable-asm \
	--disable-locale \
	--disable-md2man \
	--disable-openssl \
	--disable-simd \
	--disable-roll-simd \
	--$(if $(CONFIG_BUILD_NLS),en,dis)able-iconv \
	--$(if $(CONFIG_BUILD_NLS),en,dis)able-iconv-open \
	--$(if $(CONFIG_RSYNC_zstd),en,dis)able-zstd \
	--$(if $(CONFIG_RSYNC_lz4),en,dis)able-lz4 \
	--$(if $(CONFIG_RSYNC_xattr),en,dis)able-xattr-support \
	--$(if $(CONFIG_RSYNC_acl),en,dis)able-acl-support \
	--$(if $(CONFIG_RSYNC_xxhash),en,dis)able-xxhash \
	$(if $(CONFIG_IPV6),,--disable-ipv6) \
	--with-rsyncd-conf=/opt/etc/rsyncd.conf

define Package/rsyncd
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=File Transfer
  TITLE:=Rsync daemon
  DEPENDS:=+rsync
  USERID:=rsyncd=976:rsyncd=976
  URL:=https://rsync.samba.org/
endef

define Package/rrsync
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=File Transfer
  TITLE:=Restricted rsync script
  DEPENDS:=+rsync +perlbase-file +perl @(PACKAGE_openssh-server||PACKAGE_openssh-server-pam)
  URL:=https://www.samba.org/ftp/unpacked/rsync/support/rrsync
endef

define Package/rsync/description
 rsync is a program that allows files to be copied to and from remote machines
 in much the same way as rcp. It has many more options than rcp, and uses the
 rsync remote-update protocol to greatly speed up file transfers when the
 destination file already exists.

 The rsync remote-update protocol allows rsync to transfer just the differences
 between two sets of files across the network link.
endef

define Package/rsync/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/$(PKG_NAME) $(1)/opt/bin/
endef

define Package/rsyncd/description
 rsyncd is a configuration file and initscript to utilize rsync as a daemon. It
 uses the same binary as rsync.
endef

define Package/rsyncd/conffiles
/opt/etc/rsyncd.conf
endef

define Package/rsyncd/install
	$(INSTALL_DIR) $(1)/opt/etc
	$(INSTALL_DATA) ./files/rsyncd.conf $(1)/opt/etc/
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S50rsyncd $(1)/opt/etc/init.d/
#	$(INSTALL_DIR) $(1)/etc/capabilities
#	$(INSTALL_DATA) ./files/rsyncd.json $(1)/etc/capabilities
endef

define Package/rrsync/description
  rrsync is a script which wraps around rsync to restrict its permission to a
  particular subdirectory via ~/.ssh/authorized_keys and/or to read-only
  or write-only mode
endef

define Package/rrsync/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/support/rrsync $(1)/opt/bin
endef

$(eval $(call BuildPackage,rsync))
$(eval $(call BuildPackage,rsyncd))
$(eval $(call BuildPackage,rrsync))
