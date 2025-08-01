# SPDX-License-Identifier: GPL-2.0-only
#
# Copyright (C) 2009-2013 OpenWrt.org
#

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=xtables-addons
PKG_VERSION:=3.27
PKG_RELEASE:=3
PKG_HASH:=e47ea8febe73c12ecab09d2c93578c5dc72d76f17fdf673397758f519cce6828

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://inai.de/files/xtables-addons/
PKG_BUILD_DEPENDS:=iptables

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

PKG_MAINTAINER:=Jo-Philipp Wich <jo@mein.io>
PKG_LICENSE:=GPL-2.0
PKG_LICENSE_FILES:=COPYING

PKG_FIXUP:=autoreconf
PKG_ASLR_PIE:=0

include $(INCLUDE_DIR)/package.mk

define Package/xtables-addons
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Firewall
  TITLE:=Extensions not distributed in the main Xtables
  URL:=https://inai.de/projects/xtables-addons/
endef

# uses GNU configure

CONFIGURE_ARGS+= \
	--with-kbuild="$(LINUX_DIR)" \
	--with-xtlibdir="/usr/lib/iptables"

define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		$(KERNEL_MAKE_FLAGS) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		DEPMOD="/bin/true" \
		all
endef

define Build/Install
	$(MAKE) -C $(PKG_BUILD_DIR) \
		$(KERNEL_MAKE_FLAGS) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		DEPMOD="/bin/true" \
		install
endef

# 1: extension/module suffix used in package name
# 2: extension/module display name used in package title/description
# 3: list of extensions to package
# 4: list of modules to package
# 5: module depends
define BuildTemplate

 ifneq ($(3),)
  define Package/iptables-mod-$(1)
    $$(call Package/xtables-addons)
    CATEGORY:=Network
    TITLE:=$(2) iptables extension
    DEPENDS:=iptables $(if $(4),+kmod-ipt-$(1))
  endef

  define Package/iptables-mod-$(1)/install
	$(INSTALL_DIR) $$(1)/usr/lib/iptables
	for m in $(3); do \
		$(CP) \
			$(PKG_INSTALL_DIR)/usr/lib/iptables/lib$$$$$$$${m}.so \
			$$(1)/usr/lib/iptables/ ; \
	done
  endef

  $$(eval $$(call BuildPackage,iptables-mod-$(1)))
 endif

 ifneq ($(4),)
  define KernelPackage/ipt-$(1)
    SUBMENU:=Netfilter Extensions
    TITLE:=$(2) netfilter module
    DEPENDS:=+kmod-ipt-core $(5)
    FILES:=$(foreach mod,$(4),$(PKG_BUILD_DIR)/extensions/$(mod).$(LINUX_KMOD_SUFFIX))
    AUTOLOAD:=$(call AutoProbe,$(notdir $(4)))
  endef

  $$(eval $$(call KernelPackage,ipt-$(1)))
 endif

endef


define Package/iptaccount
  $(call Package/xtables-addons)
  CATEGORY:=Network
  TITLE:=iptables-mod-account control utility
  DEPENDS:=iptables +iptables-mod-account
endef

define Package/iptaccount/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(INSTALL_DIR) $(1)/usr/sbin
	$(CP) \
		$(PKG_INSTALL_DIR)/usr/lib/libxt_ACCOUNT_cl.so* \
		$(1)/usr/lib/
	$(CP) \
		$(PKG_INSTALL_DIR)/usr/sbin/iptaccount \
		$(1)/usr/sbin/
endef


define Package/iptasn
  $(call Package/xtables-addons)
  CATEGORY:=Network
  TITLE:=iptables-mod-asn support scripts for MaxMind ASN databases
  DEPENDS:=iptables +iptables-mod-asn \
		+perl +perlbase-getopt +perlbase-io +perl-text-csv_xs \
		+perl-net-cidr-lite \
		+wget-ssl +!BUSYBOX_CONFIG_ZCAT:gzip
endef

define Package/iptasn/config
	menu "Select iptasn options"
		config IPTASN_PRESERVE
			bool "Preserve across sysupgrades"
			default n
			help
			  Backup and restore during sysupgrade (requires >7MB)
	endmenu
endef

ifeq ($(CONFIG_IPTASN_PRESERVE),y)
define Package/iptasn/conffiles
/usr/share/xt_asn/
endef
endif

define Package/iptasn/install
	$(INSTALL_DIR) $(1)/usr/lib/xtables-addons
	$(CP) \
		$(PKG_INSTALL_DIR)/usr/lib/xtables-addons/xt_asn_{build,dl} \
		$(1)/usr/lib/xtables-addons/
	$(INSTALL_DIR) $(1)/usr/share/xt_asn
	touch $(1)/usr/share/xt_asn/.keep
endef


define Package/iptgeoip
  $(call Package/xtables-addons)
  CATEGORY:=Network
  TITLE:=iptables-mod-geoip support scripts for MaxMind GeoIP databases
  DEPENDS:=iptables +iptables-mod-geoip \
		+perl +perlbase-getopt +perlbase-io +perl-text-csv_xs \
		+perl-net-cidr-lite \
		+wget-ssl +!BUSYBOX_CONFIG_ZCAT:gzip
endef

define Package/iptgeoip/config
	menu "Select iptgeoip options"
		config IPTGEOIP_PRESERVE
			bool "Preserve across sysupgrades"
			default n
			help
			  Backup and restore during sysupgrade (requires >7MB)
	endmenu
endef

ifeq ($(CONFIG_IPTGEOIP_PRESERVE),y)
define Package/iptgeoip/conffiles
/usr/share/xt_geoip/
endef
endif

define Package/iptgeoip/install
	$(INSTALL_DIR) $(1)/usr/lib/xtables-addons
	$(CP) \
		$(PKG_INSTALL_DIR)/usr/lib/xtables-addons/xt_geoip_{build,dl}{,_maxmind} \
		$(1)/usr/lib/xtables-addons/
	$(INSTALL_DIR) $(1)/usr/bin
	$(CP) \
		$(PKG_INSTALL_DIR)/usr/bin/xt_geoip_query \
		$(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/share/xt_geoip
	touch $(1)/usr/share/xt_geoip/.keep
endef


define KernelPackage/nf-nathelper-rtsp
  SUBMENU:=Netfilter Extensions
  TITLE:=Conntrack and NAT for rtsp
  DEPENDS:=+kmod-nf-conntrack +kmod-nf-nat
  FILES:=$(PKG_BUILD_DIR)/extensions/rtsp/nf_conntrack_rtsp.$(LINUX_KMOD_SUFFIX) \
    $(PKG_BUILD_DIR)/extensions/rtsp/nf_nat_rtsp.$(LINUX_KMOD_SUFFIX)
  AUTOLOAD:=$(call AutoProbe,nf_conntrack_rtsp nf_nat_rtsp)
  PROVIDES:=kmod-ipt-nathelper-rtsp
endef


#$(eval $(call BuildTemplate,SUFFIX,DESCRIPTION,EXTENSION,MODULE,DEPENDS))

#$(eval $(call BuildTemplate,compat-xtables,API compatibilty layer,,compat_xtables,+IPV6:kmod-ip6tables))

#$(eval $(call BuildTemplate,account,ACCOUNT,xt_ACCOUNT,ACCOUNT/xt_ACCOUNT,+kmod-ipt-compat-xtables))
#$(eval $(call BuildTemplate,asn,asn,xt_asn,xt_asn,))
#$(eval $(call BuildTemplate,chaos,CHAOS,xt_CHAOS,xt_CHAOS,+kmod-ipt-compat-xtables +kmod-ipt-delude +kmod-ipt-tarpit))
#$(eval $(call BuildTemplate,condition,Condition,xt_condition,xt_condition,))
#$(eval $(call BuildTemplate,delude,DELUDE,xt_DELUDE,xt_DELUDE,+kmod-ipt-compat-xtables))
#$(eval $(call BuildTemplate,dhcpmac,DHCPMAC,xt_DHCPMAC,xt_DHCPMAC,+kmod-ipt-compat-xtables))
#$(eval $(call BuildTemplate,dnetmap,DNETMAP,xt_DNETMAP,xt_DNETMAP,+kmod-ipt-compat-xtables +kmod-ipt-nat))
#$(eval $(call BuildTemplate,fuzzy,fuzzy,xt_fuzzy,xt_fuzzy,))
#$(eval $(call BuildTemplate,geoip,geoip,xt_geoip,xt_geoip,))
#$(eval $(call BuildTemplate,iface,iface,xt_iface,xt_iface,))
#$(eval $(call BuildTemplate,ipmark,IPMARK,xt_IPMARK,xt_IPMARK,+kmod-ipt-compat-xtables))
#$(eval $(call BuildTemplate,ipp2p,IPP2P,xt_ipp2p,xt_ipp2p,+kmod-ipt-compat-xtables +kmod-lib-textsearch))
#$(eval $(call BuildTemplate,ipv4options,ipv4options,xt_ipv4options,xt_ipv4options,))
#$(eval $(call BuildTemplate,length2,length2,xt_length2,xt_length2,+kmod-ipt-compat-xtables))
#$(eval $(call BuildTemplate,logmark,LOGMARK,xt_LOGMARK,xt_LOGMARK,+kmod-ipt-compat-xtables))
#$(eval $(call BuildTemplate,lscan,lscan,xt_lscan,xt_lscan,))
#$(eval $(call BuildTemplate,lua,Lua PacketScript,xt_LUA,LUA/xt_LUA,+kmod-ipt-conntrack-extra))
#$(eval $(call BuildTemplate,proto,PROTO,xt_PROTO,xt_PROTO,))
#$(eval $(call BuildTemplate,psd,psd,xt_psd,xt_psd,))
#$(eval $(call BuildTemplate,quota2,quota2,xt_quota2,xt_quota2,))
#$(eval $(call BuildTemplate,sysrq,SYSRQ,xt_SYSRQ,xt_SYSRQ,+kmod-ipt-compat-xtables +kmod-crypto-hash))
#$(eval $(call BuildTemplate,tarpit,TARPIT,xt_TARPIT,xt_TARPIT,+kmod-ipt-compat-xtables))

#$(eval $(call BuildPackage,iptaccount))
#$(eval $(call BuildPackage,iptasn))
#$(eval $(call BuildPackage,iptgeoip))
#$(eval $(call KernelPackage,nf-nathelper-rtsp))
