include $(TOPDIR)/rules.mk

PKG_NAME:=xupnpd
PKG_RELEASE:=2

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/clark15b/xupnpd
PKG_SOURCE_DATE:=2021-04-08
PKG_SOURCE_VERSION:=2bc1e741e0efe04cb3150430ff25410093618b4f
PKG_MIRROR_HASH:=00ba72ed3d394220cc564319d29b79c20e482d904e81aa7563b17349e918f94f

PKG_MAINTAINER:=Álvaro Fernández Rojas <noltari@gmail.com>
PKG_LICENSE:=GPLv2
PKG_LICENSE_FILES:=LICENSE

include $(INCLUDE_DIR)/package.mk

LUA_FLAGS:=-llua -lssl -lcrypto

ifneq ($(CONFIG_USE_MUSL),)
  TARGET_CFLAGS += -D_LARGEFILE64_SOURCE
endif

define Build/Compile
	(cd $(PKG_BUILD_DIR)/src; $(TARGET_CC) -v $(LUA_FLAGS) $(TARGET_CFLAGS) -fno-exceptions -fno-rtti -DWITH_URANDOM $(TARGET_CPPFLAGS) $(TARGET_LDFLAGS) -lm -ldl -lcrypt -o xupnpd *.c *.cpp)
endef

define Package/xupnpd
  SECTION:=multimedia
  CATEGORY:=Multimedia
  DEPENDS:= +liblua +libopenssl #+USE_GLIBC:libcrypt-compat
  TITLE:=eXtensible UPnP agent
  URL:=http://xupnpd.org/
endef

define Package/xupnpd/conffiles
/opt/share/xupnpd/xupnpd.lua
/opt/share/xupnpd/config
/opt/share/xupnpd/playlists
endef

define Package/xupnpd/description
xupnpd - eXtensible UPnP agent
This program is a light DLNA Media Server which provides ContentDirectory:1 service for sharing IPTV unicast streams over local area network (with udpxy for multicast to HTTP unicast conversion).
The program shares UTF8-encoded M3U playlists with links over local area network as content of the directory.
You can watch HDTV broadcasts (multicast or unicast) and listen Internet Radio in IP network without transcoding and PC.
endef

define Package/xupnpd/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/src/xupnpd $(1)/opt/bin
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S94xupnpd $(1)/opt/etc/init.d
	$(INSTALL_DIR) $(1)/opt/share/xupnpd
	$(CP) $(PKG_BUILD_DIR)/src/*.lua $(1)/opt/share/xupnpd
	$(INSTALL_DIR) $(1)/opt/share/xupnpd/ui
	$(CP) $(PKG_BUILD_DIR)/src/ui/* $(1)/opt/share/xupnpd/ui
	$(INSTALL_DIR) $(1)/opt/share/xupnpd/www
	$(CP) $(PKG_BUILD_DIR)/src/www/* $(1)/opt/share/xupnpd/www
	$(INSTALL_DIR) $(1)/opt/share/xupnpd/playlists
	$(CP) $(PKG_BUILD_DIR)/src/playlists/* $(1)/opt/share/xupnpd/playlists
	$(INSTALL_DIR) $(1)/opt/share/xupnpd/plugins
	$(CP) $(PKG_BUILD_DIR)/src/plugins/* $(1)/opt/share/xupnpd/plugins
	$(INSTALL_DIR) $(1)/opt/share/xupnpd/config
	$(INSTALL_DIR) $(1)/opt/share/xupnpd/localmedia
	$(INSTALL_DIR) $(1)/opt/share/xupnpd/profiles
	$(CP) $(PKG_BUILD_DIR)/src/profiles/* $(1)/opt/share/xupnpd/profiles
endef

$(eval $(call BuildPackage,xupnpd))
