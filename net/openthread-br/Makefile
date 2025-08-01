# SPDX-FileCopyrightText: 2022-2023 Stijn Tintel <stijn@linux-ipv6.be>
# SPDX-License-Identifier: GPL-2.0-only

include $(TOPDIR)/rules.mk

PKG_NAME:=openthread-br
PKG_SOURCE_DATE:=2024-11-20
PKG_SOURCE_VERSION:=bd7bc77742920eba3b3801a410c3eba3372c98bb
PKG_RELEASE:=2

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL=https://github.com/openthread/ot-br-posix.git
PKG_MIRROR_HASH:=ca4352e527e38de26c8c4242ced5899e6fd1e4fea57d0dc4b2a6bd433e6b14b6

PKG_MAINTAINER:=Stijn Tintel <stijn@linux-ipv6.be>
PKG_LICENSE:=BSD-3-Clause
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/cmake.mk

define Package/luci-app-openthread
  CATEGORY:=LuCI
  SECTION:=luci
  SUBMENU:=3. Applications
  TITLE:=LuCI Support for OpenThread Border Router
  DEPENDS:=+luci-base
endef

define Package/openthread-br
  CATEGORY:=Network
  SECTION:=net
  TITLE:=OpenThread Border Router
  DEPENDS:= \
	+kmod-tun \
	+libblobmsg-json \
	+libjson-c \
	+libncurses \
	+libnetfilter-queue \
	+libreadline \
	+libstdcpp \
	+libubox \
	+libubus \
	+mdnsd \
	+mdnsresponder
endef

define Package/openthread-br/description
  A Thread border router for POSIX-based platforms.
endef

define Package/openthread-br/conffiles
/var/lib/thread
endef

CMAKE_OPTIONS += \
	-DOT_BORDER_ROUTER=ON \
	-DOT_CHANNEL_MANAGER=ON \
	-DOT_CHANNEL_MONITOR=ON \
	-DOT_FIREWALL=OFF \
	-DOT_NAT64_BORDER_ROUTING=OFF \
	-DOT_TARGET_OPENWRT=ON \
	-DOTBR_BACKBONE_ROUTER=ON \
	-DOTBR_BORDER_ROUTING=ON \
	-DOTBR_DNSSD_DISCOVERY_PROXY=ON \
	-DOTBR_DUA_ROUTING=ON \
	-DOTBR_MDNS=mDNSResponder \
	-DOTBR_NAT64=OFF \
	-DOTBR_OPENWRT=ON \
	-DOTBR_REST=ON \
	-DOTBR_SRP_ADVERTISING_PROXY=ON \
	-DOTBR_SRP_SERVER_AUTO_ENABLE=ON \
	-DOTBR_TREL=ON

TARGET_CFLAGS += -DOPENTHREAD_POSIX_CONFIG_DAEMON_SOCKET_BASENAME=\\\"/var/run/openthread-%s\\\"

define Package/luci-app-openthread/install
	$(INSTALL_DIR) \
		$(1)/usr/lib/lua/luci/controller/admin \
		$(1)/usr/lib/lua/luci/view/admin_thread \
		$(1)/www/luci-static/resources
	$(INSTALL_DATA) \
		$(PKG_BUILD_DIR)/src/openwrt/controller/thread.lua \
		$(1)/usr/lib/lua/luci/controller/admin
	$(INSTALL_DATA) \
		$(PKG_BUILD_DIR)/src/openwrt/view/admin_thread/* \
		$(1)/usr/lib/lua/luci/view/admin_thread
	$(INSTALL_DATA) \
		$(PKG_BUILD_DIR)/src/openwrt/handle_error.js \
		$(1)/www/luci-static/resources
endef

define Package/openthread-br/install
	$(INSTALL_DIR) \
		$(1)/etc/init.d \
		$(1)/lib/netifd/proto \
		$(1)/usr/sbin
	$(INSTALL_BIN) ./files/openthread-proto.sh $(1)/lib/netifd/proto/openthread.sh
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/* $(1)/usr/sbin
endef


#$(eval $(call BuildPackage,luci-app-openthread))
#$(eval $(call BuildPackage,openthread-br))
