#
# Copyright (C) 2006-2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=autossh
PKG_VERSION:=1.4g
PKG_RELEASE:=5

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tgz
PKG_SOURCE_URL:=https://www.harding.motd.ca/autossh/
PKG_HASH:=5fc3cee3361ca1615af862364c480593171d0c54ec156de79fc421e31ae21277
PKG_LICENSE:=0BSD

include $(INCLUDE_DIR)/package.mk

define Package/autossh
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Autossh client
  URL:=https://www.harding.motd.ca/autossh/
  MAINTAINER:=Christian Beier <cb@shoutrlabs.com>
  SUBMENU:=SSH
endef

TARGET_LDFLAGS += $(if $(or $(CONFIG_GLIBC_USE_VERSION_2_33),$(CONFIG_GLIBC_USE_VERSION_2_34),$(CONFIG_USE_MUSL)),,-lnsl)

CONFIGURE_VARS += \
	ac_cv_path_ssh=/opt/bin/ssh

define Package/autossh/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/autossh $(1)/opt/sbin/
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S41autossh $(1)/opt/etc/init.d/
	$(INSTALL_DIR) $(1)/opt/etc/config
	$(INSTALL_DATA) ./files/autossh.conf $(1)/opt/etc/config
#	$(INSTALL_DIR) $(1)/etc/hotplug.d/iface
#	$(INSTALL_DATA) ./files/autossh.hotplug $(1)/etc/hotplug.d/iface/20-autossh
endef

define Package/autossh/conffiles
/opt/etc/config/autossh.conf
endef

$(eval $(call BuildPackage,autossh))
