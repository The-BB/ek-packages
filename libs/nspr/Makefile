#
# Copyright (C) 2019 Lucian Cristian
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk

PKG_NAME:=nspr
PKG_VERSION:=4.36
PKG_RELEASE:=1
PKG_MAINTAINER:=Lucian Cristian <lucian.cristian@gmail.com>
PKG_LICENSE:=MPL-2.0
PKG_CPE_ID:=cpe:/a:mozilla:netscape_portable_runtime

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:= \
    https://download.cdn.mozilla.net/pub/$(PKG_NAME)/releases/v$(PKG_VERSION)/src/ \
    https://archive.mozilla.org/pub/$(PKG_NAME)/releases/v$(PKG_VERSION)/src/
PKG_HASH:=55dec317f1401cd2e5dba844d340b930ab7547f818179a4002bce62e6f1c6895

PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1
PKG_FIXUP:=autoreconf
PKG_BUILD_FLAGS:=lto

include $(INCLUDE_DIR)/package.mk

CONFIGURE_PATH = ./nspr
MAKE_PATH = ./nspr
PKG_AUTOMAKE_PATHS = $(PKG_BUILD_DIR)/nspr
LBITS = $(shell $(TARGET_CC) -dM -E - </dev/null | grep -q "__LP64__" && echo 64 || echo 32)

ifeq ($(LBITS),64)
    conf=--enable-64bit
endif

export MUSL=$(if $(CONFIG_LIBC_USE_GLIBC),0,1)
TARGET_LDFLAGS += -Wl,--gc-sections,--as-needed $(FPIC)
ifneq ($(CONFIG_USE_MUSL),)
  TARGET_CFLAGS += -D_LARGEFILE64_SOURCE
endif

CONFIGURE_ARGS += \
    --build=$(GNU_HOST_NAME) \
    --host=$(GNU_HOST_NAME) \
    --target=$(REAL_GNU_TARGET_NAME) \
    --disable-debug \
    --with-pthreads \
    $(if $(CONFIG_IPV6),--enable-ipv6,--disable-ipv6) \
    $(conf)

define Build/Compile
	CROSS_COMPILE=1 CFLAGS="-DXP_UNIX $(HOST_CFLAGS)" LDFLAGS="" CC="$(HOSTCC)" \
	    $(MAKE) -C $(PKG_BUILD_DIR)/nspr/config
	$(call Build/Compile/Default)
endef

define Package/nspr
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=Netscape Portable Runtime (NSPR)
  URL:=https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSPR
  DEPENDS:=+libpthread
endef

define Package/nspr/description
  Netscape Portable Runtime (NSPR) provides a platform-neutral API for system
  level and libc-like functions. The API is used in the Mozilla clients, many
  of Red Hat's and Oracle's server applications, and other software offerings.
endef

define Build/InstallDev
	$(INSTALL_DIR) \
	    $(1)/opt/include \
	    $(1)/opt/lib/ \
	    $(1)/opt/lib/pkgconfig/ \
	    $(1)/opt/share/aclocal/

	$(CP) $(PKG_INSTALL_DIR)/opt/include/nspr $(1)/opt/include/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/*.so* $(1)/opt/lib/
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/* $(1)/opt/lib/pkgconfig/
	$(INSTALL_DATA) $(PKG_INSTALL_DIR)/opt/share/aclocal/* $(1)/opt/share/aclocal/
endef

define Package/nspr/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/*.so $(1)/opt/lib/
endef

$(eval $(call BuildPackage,nspr))
