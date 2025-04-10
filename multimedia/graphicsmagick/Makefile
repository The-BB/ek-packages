# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=graphicsmagick
PKG_VERSION:=1.3.45
PKG_RELEASE:=1

PKG_BUILD_DIR:=$(BUILD_DIR)/GraphicsMagick-$(PKG_VERSION)
PKG_SOURCE:=GraphicsMagick-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=@SF/graphicsmagick
PKG_HASH:=dcea5167414f7c805557de2d7a47a9b3147bcbf617b91f5f0f4afe5e6543026b

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=Copyright.txt
PKG_MAINTAINER:=Aleksey Vasilenko <aleksey.vasilenko@gmail.com>
PKG_CPE_ID:=cpe:/a:graphicsmagick:graphicsmagick

PKG_BUILD_PARALLEL:=1
DISABLE_NLS:=
PKG_BUILD_DEPENDS:=zlib freetype libpng libjpeg-turbo tiff
PKG_BUILD_FLAGS:=lto

include $(INCLUDE_DIR)/package.mk

PKG_INSTALL:=1

define Package/graphicsmagick/Default
  SECTION:=multimedia
  CATEGORY:=Multimedia
  TITLE:=Graphics manipulation tools
  URL:=http://www.graphicsmagick.org/
  DEPENDS:=graphicsmagick
endef

define Package/graphicsmagick
  $(call Package/graphicsmagick/Default)
  DEPENDS:=+libltdl +libpthread +zlib +libfreetype +libpng +libjpeg +libtiff
endef

define Package/graphicsmagick/description
GraphicsMagick is a free and open-source software suite for displaying,
converting, and editing raster image and vector image files. It is a fork
of ImageMagick.
endef

CONFIGURE_ARGS += \
	--enable-shared \
	--disable-static \
	--enable-dependency-tracking \
	--with-modules \
	--with-threads \
	--without-magick-plus-plus \
	--without-perl \
	--without-bzlib \
	--without-dps \
	--without-fpx \
	--without-gs \
	--without-jbig \
	--without-webp \
	--with-jpeg \
	--without-jp2 \
	--without-lcms2 \
	--without-libzip \
	--without-lzma \
	--with-png \
	--with-tiff \
	--without-trio \
	--with-ttf \
	--without-umem \
	--without-wmf \
	--without-xml \
	--with-zlib \
	--without-zstd \
	--without-x

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/include/* \
		$(1)/opt/include/

	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) \
		$(PKG_INSTALL_DIR)/opt/lib/*.so* \
		$(1)/opt/lib/

	$(INSTALL_DIR) $(1)/opt/lib/pkgconfig
	$(INSTALL_DATA) \
		$(PKG_INSTALL_DIR)/opt/lib/pkgconfig/* \
		$(1)/opt/lib/pkgconfig/
endef

GMlibdir:=opt/lib/GraphicsMagick-$(PKG_VERSION)
define Package/graphicsmagick/install
	$(INSTALL_DIR) $(1)/opt/bin $(1)/$(GMlibdir)
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/*.so* $(1)/opt/lib/
	$(CP) $(PKG_INSTALL_DIR)/opt/bin/* $(1)/opt/bin/
	$(CP) $(PKG_INSTALL_DIR)/$(GMlibdir)/* $(1)/$(GMlibdir)/
	# XXX
	$(SED) 's,$(STAGING_DIR),,g;s,$(TOOLCHAIN_DIR),/opt,g; \
		s,$(STAGING_DIR_HOST),/opt,g; \
		s,-f\(file\|macro\)-prefix-map=$(PKG_BUILD_DIR)=GraphicsMagick-$(PKG_VERSION),,g' \
			$(1)/opt/bin/GraphicsMagick-config
	$(FIND) $(1)/$(GMlibdir) -type f -a -name "*.la" -exec $(SED) \
		's,$(STAGING_DIR),,g;s,$(TOOLCHAIN_DIR),/opt,g' {} +
endef

$(eval $(call BuildPackage,graphicsmagick))
