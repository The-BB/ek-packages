#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=hwloc
PKG_VERSION:=2.12.1
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=https://download.open-mpi.org/release/$(PKG_NAME)/v2.12
PKG_HASH:=38a90328bb86259f9bb2fe1dc57fd841e111d1e6358012bef23dfd95d21dc66b

PKG_MAINTAINER:=W. Michael Petullo <mike@flyn.org>
PKG_LICENSE:=BSD-3-Clause
PKG_LICENSE_FILES:=COPYING

PKG_INSTALL:=1
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/hwloc/Default
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE:=Portable Hardware Locality
  URL:=https://www.open-mpi.org/projects/hwloc/
endef

define Package/hwloc/Default/description
  The Portable Hardware Locality (hwloc) software package provides a
  portable abstraction (across OS, versions, architectures, ...) of the
  hierarchical topology of modern architectures, including NUMA memory
  nodes, sockets, shared caches, cores and simultaneous multithreading. It
  also gathers various system attributes such as cache and memory
  information as well as the locality of I/O devices such as network
  interfaces, InfiniBand HCAs or GPUs.
endef

define Package/hwloc-utils
$(call Package/hwloc/Default)
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE+= utilities
  DEPENDS+= +libhwloc +libncurses
endef

define Package/hwloc-utils/description
$(call Package/hwloc/Default/description)
  This package contains the hwloc utilities.
endef

define Package/libhwloc
$(call Package/hwloc/Default)
  SECTION:=libs
  CATEGORY:=Libraries
  TITLE+= libraries
  DEPENDS+=+libpciaccess
endef

define Package/libhwloc/description
$(call Package/hwloc/Default/description)
  This package contains the hwloc libraries.
endef

CONFIGURE_ARGS += \
	--disable-libxml2 \
	--disable-opencl \
	--disable-libudev

define Build/InstallDev
	$(INSTALL_DIR) $(STAGING_DIR)/opt/include
	$(CP) $(PKG_INSTALL_DIR)/opt/include/hwloc.h $(STAGING_DIR)/opt/include/
	$(INSTALL_DIR) $(STAGING_DIR)/opt/include/hwloc
	$(CP) $(PKG_INSTALL_DIR)/opt/include/hwloc/*.h $(STAGING_DIR)/opt/include/hwloc/
	$(INSTALL_DIR) $(STAGING_DIR)/opt/include/hwloc/autogen
	$(CP) $(PKG_INSTALL_DIR)/opt/include/hwloc/autogen/*.h $(STAGING_DIR)/opt/include/hwloc/autogen/
	$(INSTALL_DIR) $(STAGING_DIR)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/lib* $(STAGING_DIR)/opt/lib/
	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(INSTALL_DATA) \
		$(PKG_INSTALL_DIR)/opt/lib/pkgconfig/*.pc \
		$(1)/opt/lib/pkgconfig
endef

define Package/hwloc-utils/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) $(PKG_INSTALL_DIR)/opt/bin/* $(1)/opt/bin/
endef

define Package/libhwloc/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/*.so* $(1)/opt/lib/
endef

$(eval $(call BuildPackage,hwloc-utils))
$(eval $(call BuildPackage,libhwloc))
