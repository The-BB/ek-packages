#
# Copyright (C) 2007-2019 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=glib2
PKG_VERSION:=2.82.0
PKG_RELEASE:=1

PKG_SOURCE:=glib-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@GNOME/glib/$(basename $(PKG_VERSION))
PKG_HASH:=f4c82ada51366bddace49d7ba54b33b4e4d6067afa3008e4847f41cb9b5c38d3

PKG_MAINTAINER:=Peter Wagner <tripolar@gmx.at>
PKG_LICENSE:=LGPL-2.1-or-later
PKG_LICENSE_FILES:=COPYING
PKG_CPE_ID:=cpe:/a:gnome:glib

PKG_BUILD_DIR:=$(BUILD_DIR)/glib-$(PKG_VERSION)
PKG_CONFIG_DEPENDS:=CONFIG_BUILD_NLS
PKG_FORTIFY_SOURCE:=0
PKG_BUILD_FLAGS:=gc-sections

HOST_BUILD_DIR:=$(BUILD_DIR_HOST)/glib-$(PKG_VERSION)
HOST_BUILD_DEPENDS:=pcre2/host libffi/host libiconv-full/host

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk
include $(INCLUDE_DIR)/meson.mk

define Package/glib2
  SECTION:=libs
  CATEGORY:=Libraries
  DEPENDS:=$(ICONV_DEPENDS) $(INTL_DEPENDS) +zlib +libpthread +libffi +libattr +libpcre2
  TITLE:=glib 2.0
  URL:=http://www.gtk.org/
endef

define Package/glib2/description
  The GLib library of C routines
endef

COMP_ARGS:=

# default feature=auto see meson_options.txt
COMP_ARGS+=-Dglib_debug=disabled

# default feature=auto see meson_options.txt
COMP_ARGS+=-Dlibmount=disabled

# default feature=auto see meson_options.txt
COMP_ARGS+=-Dselinux=disabled

# default boolean=true see meson_options.txt
COMP_ARGS+=-Dglib_assert=false

# default boolean=true see meson_options.txt
COMP_ARGS+=-Dtests=false

# default feature=auto see meson_options.txt
COMP_ARGS+=-Dintrospection=disabled

# set runtime dir to /var/run
COMP_ARGS+=-Druntime_dir=/opt/var/run

MESON_HOST_ARGS += $(COMP_ARGS) -Dxattr=false -Dnls=disabled
MESON_ARGS += $(COMP_ARGS) -Dxattr=true -Db_lto=true -Dnls=$(if $(CONFIG_BUILD_NLS),en,dis)abled

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/bin/* \
		$(1)/opt/bin/

	$(INSTALL_DIR) $(1)/opt/include
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/include/glib-2.0 \
		$(1)/opt/include/
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/glib-2.0/include/*.h \
		$(1)/opt/include/glib-2.0/
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/include/gio-unix-2.0 \
		$(1)/opt/include/

	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/glib-2.0 \
		$(1)/opt/lib/

	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/*.{so*,a} \
		$(1)/opt/lib/

	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(INSTALL_DATA) \
		$(PKG_INSTALL_DIR)/opt/lib/pkgconfig/*.pc \
		$(1)/opt/lib/pkgconfig
	$(foreach BIN,glib_genmarshal glib_mkenums,
		$(SED) 's/^$(BIN)=$$$${bindir}\/\(.*\)/$(BIN)=$$$${prefix_hostpkg}\/bin\/\1/' $(1)/opt/lib/pkgconfig/glib-2.0.pc
	)
	$(foreach BIN,glib_compile_resources glib_compile_schemas gdbus_codegen,
		$(SED) 's/^$(BIN)=$$$${bindir}\/\(.*\)/$(BIN)=$$$${prefix_hostpkg}\/bin\/\1/' $(1)/opt/lib/pkgconfig/gio-2.0.pc
	)

	$(INSTALL_DIR) $(2)/share/aclocal/
	$(INSTALL_DATA) \
		$(PKG_INSTALL_DIR)/opt/share/aclocal/*.m4  \
		$(2)/share/aclocal/

	$(INSTALL_DIR) $(1)/opt/share/glib-2.0
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/share/glib-2.0/codegen \
		$(1)/opt/share/glib-2.0/
endef

define Package/glib2/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/*.so* \
		$(1)/opt/lib/
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,glib2))
