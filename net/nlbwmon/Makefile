include $(TOPDIR)/rules.mk

PKG_NAME:=nlbwmon
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/jow-/nlbwmon.git
PKG_SOURCE_DATE:=2025-06-02
PKG_SOURCE_VERSION:=29236be687927b0c81ee6e2642e7caa8787c4ad3
PKG_MIRROR_HASH:=f8dd6bbf4d0122d2925183b9b19b8d1d3f21f0c129a2546814bbd7e8cc5ef0b9

CMAKE_INSTALL:=1

PKG_MAINTAINER:=Jo-Philipp Wich <jo@mein.io>
PKG_LICENSE:=ISC
PKG_LICENSE_FILES:=COPYING

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

CMAKE_OPTIONS += -DLIBNL_LIBRARY_TINY=ON
TARGET_CFLAGS += -I$(STAGING_DIR)/usr/include/libnl-tiny

define Package/nlbwmon
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+libubox +libnl-tiny +zlib +kmod-nf-conntrack-netlink
  TITLE:=OpenWrt Traffic Usage Monitor
endef

define Package/nlbwmon/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/nlbwmon $(1)/usr/sbin/nlbwmon
	$(LN) nlbwmon $(1)/usr/sbin/nlbw
	$(INSTALL_DIR) $(1)/usr/share/nlbwmon
	$(INSTALL_DATA) $(PKG_BUILD_DIR)/protocols.txt $(1)/usr/share/nlbwmon/protocols
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/nlbwmon.init $(1)/etc/init.d/nlbwmon
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/nlbwmon.config $(1)/etc/config/nlbwmon
	$(INSTALL_DIR) $(1)/etc/hotplug.d/iface
	$(INSTALL_BIN) ./files/nlbwmon.hotplug $(1)/etc/hotplug.d/iface/30-nlbwmon
endef

define Package/nlbwmon/conffiles
/etc/config/nlbwmon
/usr/share/nlbwmon/protocols
endef


#$(eval $(call BuildPackage,nlbwmon))
