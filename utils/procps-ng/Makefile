#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=procps-ng
PKG_VERSION:=4.0.5
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@SF/procps-ng
PKG_HASH:=c2e6d193cc78f84cd6ddb72aaf6d5c6a9162f0470e5992092057f5ff518562fa

PKG_MAINTAINER:=
PKG_LICENSE:=GPL-2.0-or-later
PKG_LICENSE_FILES:=COPYING COPYING.LIB
PKG_CPE_ID:=cpe:/a:procps-ng_project:procps-ng

PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1
PKG_FIXUP:=autoreconf

include $(INCLUDE_DIR)/package.mk

CONFIGURE_ARGS += --enable-skill

# Entware specifics: pidfd_open syscall since Linux 5.3
CONFIGURE_ARGS += --disable-pidwait

# most of these have alternatives provided by busybox applets
PROCPS_APPLETS := \
    free kill pgrep pkill pmap ps pwdx skill slabtop \
    snice sysctl tload top uptime vmstat w watch

# procps-ng will be configured with "--bindir=/usr/bin --sbindir=/usr/sbin" and
# as such executables will be installed there by default, but some of them need
# to reside in locations such as /bin and /sbin to be in accordance with
# busybox alternatives which is also other distributions like debian and centos
# are doing
PROCPS_APPLETS_SRC_DIR_SBIN:=sysctl
procps-applets-src-dir=$(if $(filter $(PROCPS_APPLETS_SRC_DIR_SBIN),$(1)),/opt/sbin,/opt/bin)

PROCPS_APPLETS_DIR_BIN:=kill ps watch
PROCPS_APPLETS_DIR_SBIN:=sysctl
procps-applets-dir=$(if $(filter $(PROCPS_APPLETS_DIR_BIN),$(1)),/opt/bin,$(if $(filter $(PROCPS_APPLETS_DIR_SBIN),$(1)),/opt/sbin,/opt/bin))

define Package/procps-ng/Default
  SECTION:=utils
  CATEGORY:=Utilities
  DEPENDS:=+libncurses
  TITLE:=procps-ng utilities
  URL:=https://gitlab.com/procps-ng/procps
endef

define Build/Prepare
	$(call Build/Prepare/Default)
	echo "$(PKG_VERSION)" > "$(PKG_BUILD_DIR)/.tarball-version"
endef

define Package/procps-ng
  $(call Package/procps-ng/Default)
  MENU:=1
endef

define Package/procps-ng/description
  procps is a set of command line and full-screen utilities that provide information out of the pseudo-filesystem
  most commonly located at /proc. This filesystem provides a simple interface to the kernel data structures.
  The programs of procps generally concentrate on the structures that describe the processess running on the system.

  NOTE: some utilities provided by procps-ng packages (ps, uptime, kill, ...) are installed as busybox applets, by default.
  Conflicting applets should be removed from the build to avoid file conflicts.
endef

define GenPlugin
 define Package/procps-ng-$(1)
   $(call Package/procps-ng/Default)
   DEPENDS:=procps-ng
   TITLE:=Applet $(1) from the procps-ng package
   ALTERNATIVES:=200:$(2)/$(1):/opt/libexec/$(1)-procps-ng
 endef

 define Package/procps-ng-$(1)/description
  Installs the applet $(1).
 endef
endef

$(foreach a,$(PROCPS_APPLETS),$(eval $(call GenPlugin,$(a),$(call procps-applets-dir,$(a)))))

MAKE_FLAGS += \
	CFLAGS="$(TARGET_CFLAGS)" \
	CPPFLAGS="$(TARGET_CPPFLAGS)" \
	LDFLAGS="$(TARGET_LDFLAGS)" \

define Package/procps-ng/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libproc2.so* $(1)/opt/lib/
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/include/* $(1)/opt/include/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/* $(1)/opt/lib/
endef

define BuildPlugin
  define Package/procps-ng-$(1)/install
	$(INSTALL_DIR) $$(1)/opt/libexec
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)$(2)/$(1) $$(1)/opt/libexec/$(1)-procps-ng
  endef

  $$(eval $$(call BuildPackage,procps-ng-$(1)))
endef

$(foreach a,$(PROCPS_APPLETS),$(eval $(call BuildPlugin,$(a),$(call procps-applets-src-dir,$(a)))))
$(eval $(call BuildPackage,procps-ng))
