include $(TOPDIR)/rules.mk

PKG_NAME:=luajit2
PKG_SOURCE_DATE:=2025-05-29
PKG_VERSION:=2.1.$(subst -,.,$(PKG_SOURCE_DATE))
PKG_RELEASE:=1

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/openresty/luajit2
PKG_SOURCE_VERSION:=afc74313c6f919f713e2a25003cf0066852bb24a
PKG_MIRROR_HASH:=cd3048e818c14e0f7b904de0bb3767d0e2e9c556096ed06401ad79ccb7defcda

PKG_MAINTAINER:=Javier Marcet <javier@marcet.info>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=COPYRIGHT

PKG_BUILD_FLAGS:=no-mips16

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/host-build.mk

# HAS_LUAJIT_ARCH config is defined in luajit and is used to define
# arch deoendency for luajit. Since luajit2 is an improved version of
# luajit, they share the same arch dependency. Refer there to update
# dependency for them.
define Package/luajit2
 SECTION:=lang
 CATEGORY:=Languages
 SUBMENU:=Lua
 TITLE:=LuaJIT from OpenResty
 URL:=https://www.luajit.org
 DEPENDS:=@HAS_LUAJIT_ARCH
 PROVIDES:=luajit
endef

define Package/luajit2/description
 OpenResty's maintained branch of LuaJIT, a Just-In-Time (JIT) compiler for the Lua programming language
endef

ifeq ($(HOST_ARCH),$(filter $(HOST_ARCH), x86_64 mips64))
  ifeq ($(CONFIG_ARCH_64BIT),)
    HOST_BITS := -m32
  endif
endif

define Build/Compile
	$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		HOST_CC="$(HOSTCC) $(HOST_CFLAGS) $(HOST_BITS)" \
		CROSS="$(TARGET_CROSS)" \
		DESTDIR=$(PKG_INSTALL_DIR) \
		PREFIX=/opt \
		TARGET_SYS=Linux \
		TARGET_CFLAGS="$(TARGET_CFLAGS)" \
		BUILDMODE=dynamic
	$(RM) -rf $(PKG_INSTALL_DIR)
	mkdir -p $(PKG_INSTALL_DIR)
	$(MAKE) -C $(PKG_BUILD_DIR) \
		DESTDIR=$(PKG_INSTALL_DIR) \
		PREFIX=/opt \
		TARGET_SYS=Linux \
		install
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include/luajit-2.1
	$(CP) $(PKG_INSTALL_DIR)/opt/include/luajit-2.1/*.{h,hpp} $(1)/opt/include/luajit-2.1
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/*so* $(1)/opt/lib/
	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/luajit.pc $(1)/opt/lib/pkgconfig/
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) $(PKG_INSTALL_DIR)/opt/bin/luajit-* $(1)/opt/bin/$(PKG_NAME)
endef

define Package/luajit2/install
	$(INSTALL_DIR) $(1)/opt/lib/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/*.so* $(1)/opt/lib/
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) $(PKG_INSTALL_DIR)/opt/bin/luajit-* $(1)/opt/bin/$(PKG_NAME)
endef

define Host/Compile
	$(MAKE) $(HOST_JOBS) -C $(HOST_BUILD_DIR) \
		DPREFIX=$(STAGING_DIR_HOSTPKG) \
		TARGET_CFLAGS="$(HOST_CFLAGS)" \
		TARGET_LDFLAGS="$(HOST_LDFLAGS)"
endef

define Host/Install
	$(MAKE) $(HOST_JOBS) -C $(HOST_BUILD_DIR) \
		DPREFIX=$(STAGING_DIR_HOSTPKG) \
		install
	$(CP) $(STAGING_DIR_HOSTPKG)/bin/luajit-* $(STAGING_DIR_HOSTPKG)/bin/$(PKG_NAME)
endef

$(eval $(call HostBuild,luajit2))
$(eval $(call BuildPackage,luajit2))
