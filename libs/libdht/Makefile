# Entware specific: link libdht statically. It is needed for old kernels.

include $(TOPDIR)/rules.mk

PKG_NAME:=libdht
PKG_RELEASE:=2

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/jech/dht
PKG_SOURCE_DATE:=2022-04-27
PKG_SOURCE_VERSION:=111230894416d400c9a1e038a033586bfeaafc93
PKG_MIRROR_HASH:=05805aaaa2c8a5f1eb5534a2f3a55c7727a1fa15f8e05e7bf6f117aaa91ce65c

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE

PKG_MAINTAINER:=Daniel Golle <daniel@makrotopia.org>

include $(INCLUDE_DIR)/package.mk

define Package/libdht
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=Kademlia Distributed Hash Table (DHT) library
  BUILDONLY:=1
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include/dht $(1)/opt/lib
	$(CP) $(PKG_BUILD_DIR)/dht.h $(1)/opt/include/dht
	$(CP) $(PKG_BUILD_DIR)/libdht.a $(1)/opt/lib/
endef

$(eval $(call BuildPackage,libdht))
