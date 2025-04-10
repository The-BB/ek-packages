#
# Copyright (C) 2006-2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# Entware-specific: --disable-nls

include $(TOPDIR)/rules.mk

PKG_NAME:=alsa-utils
PKG_VERSION:=1.2.11
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=https://www.alsa-project.org/files/pub/utils/ \
		https://distfiles.gentoo.org/distfiles/
PKG_HASH:=9ac6ca3a883f151e568dcf979b8d2e5cbecc51b819bb0e6bb8a2e9b34cc428a7

PKG_MAINTAINER:=Ted Hess <thess@kitschensync.net>
PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=COPYING

PKG_FIXUP:=autoreconf
PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/alsa-utils
  SECTION:=sound
  CATEGORY:=Sound
  DEPENDS:=+alsa-lib +alsa-ucm-conf +libncursesw +libpthread
  TITLE:=ALSA (Advanced Linux Sound Architecture) utilities
  URL:=https://www.alsa-project.org/
endef

define Package/alsa-utils-seq
  SECTION:=sound
  CATEGORY:=Sound
  DEPENDS:=+alsa-lib +libpthread
  TITLE:=ALSA sequencer utilities
  URL:=https://www.alsa-project.org/
endef

define Package/alsa-utils-tests
  SECTION:=sound
  CATEGORY:=Sound
  TITLE:=ALSA utilities test data (adds ~1.3M to image)
  DEPENDS:=+alsa-lib +libpthread
  URL:=https://www.alsa-project.org/
endef

CONFIGURE_ARGS += \
	--disable-rpath \
	--disable-alsatest \
	--disable-bat \
	--disable-xmlto \
	--disable-rst2man \
	--with-curses=ncursesw \
	--disable-nls

CONFIGURE_VARS += \
	ac_cv_header_samplerate_h=no

TARGET_LDFLAGS += -latopology

define Package/alsa-utils/install
	$(INSTALL_DIR) $(1)/opt/{s,}bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/amixer $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/alsamixer $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/aplay $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/arecord $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/alsactl $(1)/opt/sbin/

	$(INSTALL_DIR) $(1)/opt/share/alsa/init
	$(INSTALL_DATA) \
	    $(PKG_INSTALL_DIR)/opt/share/alsa/init/* \
	    $(1)/opt/share/alsa/init/

	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S04alsactl $(1)/opt/etc/init.d/
	$(INSTALL_DIR) $(1)/opt/var/{lock,run}
	$(INSTALL_DIR) $(1)/opt/var/lib/alsa
endef

define Package/alsa-utils-seq/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/aconnect $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/amidi $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/aplaymidi $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/arecordmidi $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/iecset $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/alsaloop $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/alsatplg $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/alsaucm $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/aseqdump $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/aseqnet $(1)/opt/bin/
endef

define Package/alsa-utils-tests/install
	$(INSTALL_DIR) $(1)/opt/{s,}bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/speaker-test $(1)/opt/bin/
	$(INSTALL_DIR) $(1)/opt/share/sounds/alsa
	$(INSTALL_DATA) \
	    $(PKG_INSTALL_DIR)/opt/share/sounds/alsa/* \
	    $(1)/opt/share/sounds/alsa/
endef

$(eval $(call BuildPackage,alsa-utils))
$(eval $(call BuildPackage,alsa-utils-seq))
$(eval $(call BuildPackage,alsa-utils-tests))
