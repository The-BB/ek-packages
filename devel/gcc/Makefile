#
# Copyright (C) 2002-2003 Erik Andersen <andersen@uclibc.org>
# Copyright (C) 2004 Manuel Novoa III <mjn3@uclibc.org>
# Copyright (C) 2005-2006 Felix Fietkau <nbd@nbd.name>
# Copyright (C) 2006-2014 OpenWrt.org
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU
# General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 59 Temple Place, Suite 330, Boston, MA 02111-1307 USA
#
# XXX Entware specific: keep GCC-8.4.0; drop GCC-10.3.0;
#	add (?autotool?) --enable-dependency-tracking (libatomic)

include $(TOPDIR)/rules.mk

PKG_NAME:=gcc
GCC_VERSION:=$(call qstrip,$(CONFIG_GCC_VERSION))
PKG_VERSION:=$(firstword $(subst +, ,$(GCC_VERSION)))
GCC_MAJOR_VERSION:=$(word 1,$(subst ., ,$(PKG_VERSION)))
PKG_RELEASE:=8
GCC_DIR:=$(PKG_NAME)-$(PKG_VERSION)

PKG_SOURCE_URL:=@GNU/gcc/gcc-$(PKG_VERSION)
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_INSTALL:=1
PKG_FIXUP:=libtool
PKG_BUILD_PARALLEL:=1

PKG_CPE_ID:=cpe:/a:gnu:gcc

ifeq ($(PKG_VERSION),8.4.0)
	PKG_HASH:=e30a6e52d10e1f27ed55104ad233c30bd1e99cfb5ff98ab022dc941edd1b2dd4
endif

ifeq ($(PKG_VERSION),11.3.0)
	PKG_HASH:=b47cf2818691f5b1e21df2bb38c795fac2cfbd640ede2d0a5e1c89e338a3ac39
endif

ifeq ($(PKG_VERSION),12.3.0)
	PKG_HASH:=949a5d4f99e786421a93b532b22ffab5578de7321369975b91aec97adfda8c3b
endif

ifeq ($(PKG_VERSION),13.3.0)
	PKG_HASH:=0845e9621c9543a13f484e94584a49ffc0129970e9914624235fc1d061a0c083
endif

ifeq ($(PKG_VERSION),14.3.0)
	PKG_HASH:=e0dc77297625631ac8e50fa92fffefe899a4eb702592da5c32ef04e2293aca3a
endif

PATCH_DIR:=patches-$(GCC_MAJOR_VERSION).x

include $(INCLUDE_DIR)/package.mk

define Package/gcc
  SECTION:=devel
  CATEGORY:=Development
  TITLE:=gcc
  MAINTAINER:=W. Michael Petullo <mike@flyn.org>
  DEPENDS:= +binutils +libstdcpp +libzstd @!arc
  MENU:=1
endef

define Package/gcc/config
  source "$(SOURCE)/Config.in"
endef

ifeq ($(CONFIG_INCLUDE_STATIC_LIBC),y)
	COPY_STATIC_LIBC=cp -a $(TOOLCHAIN_ROOT_DIR)/lib/libc.a $(1)/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)
endif

ifeq ($(CONFIG_INCLUDE_STATIC_LIBPTHREAD),y)
	COPY_STATIC_LIBPTHREAD=cp -a $(TOOLCHAIN_ROOT_DIR)/lib/libpthread.a $(1)/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)
endif

ifeq ($(CONFIG_INCLUDE_STATIC_LIBSTDC),y)
	COPY_STATIC_LIBSTDC=cp -a $(TOOLCHAIN_ROOT_DIR)/lib/libstdc++.a $(1)/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)
endif

### FIXME %G %L <= 8.x; %G >= 10.x
ifeq ($(CONFIG_INCLUDE_STATIC_LINK_SPEC),y)
  ifeq ($(CONFIG_GCC_USE_VERSION_8),y)
	INSTALL_STATIC_SPEC=g++ -dumpspecs |sed s/--start-group}\ %G\ %L\ /--start-group}\ %G\ %L\ -lstdc++\ -lgcc_pic\ / >/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/specs
  else
	INSTALL_STATIC_SPEC=g++ -dumpspecs |sed s/--start-group}\ %G\ %{!nolibc:%L}\ /--start-group}\ %G\ %{!nolibc:%L}\ -lstdc++\ -lgcc_pic\ / >/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/specs
  endif
	RPATH_LINK_STATIC_SPEC=sed -i 's| %{L\*} | %{L*} -rpath-link /opt/lib |' /opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/specs
	REMOVE_STATIC_SPEC=rm /opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/specs
endif

TARGET_LANGUAGES:="c,c++"
BUGURL=https://dev.openwrt.org/
PKGVERSION=OpenWrt GCC $(PKG_VERSION)
TARGET_CPPFLAGS += -D_GLIBCXX_INCLUDE_NEXT_C_HEADERS

# not using sstrip here as this messes up the .so's somehow
STRIP:=$(firstword $(TOOLCHAIN_BIN_DIRS))/$(TARGET_CROSS)strip
RSTRIP:= \
	NM="$(firstword $(TOOLCHAIN_BIN_DIRS))/$(TARGET_CROSS)nm" \
	STRIP="$(STRIP)" \
	STRIP_KMOD="$(STRIP) --strip-debug" \
	$(SCRIPT_DIR)/rstrip.sh

ifneq ($(CONFIG_SOFT_FLOAT),y)
	ifeq ($(CONFIG_arm),y)
		ARM_FLOAT_OPTION:= --with-float=hard
	endif
endif

GMPSRC=gmp-6.1.0

define Download/gmp
  URL:=ftp://gcc.gnu.org/pub/gcc/infrastructure/
  FILE:=$(GMPSRC).tar.bz2
  HASH:=498449a994efeba527885c10405993427995d3f86b8768d8cdf8d9dd7c6b73e8
endef
$(eval $(call Download,gmp))

MPCSRC=mpc-1.0.3

define Download/mpc
  URL:=ftp://gcc.gnu.org/pub/gcc/infrastructure/
  FILE:=$(MPCSRC).tar.gz
  HASH:=617decc6ea09889fb08ede330917a00b16809b8db88c29c31bfbb49cbf88ecc3
endef
$(eval $(call Download,mpc))

MPFRSRC=mpfr-3.1.4

define Download/mpfr
  URL:=ftp://gcc.gnu.org/pub/gcc/infrastructure/
  FILE:=$(MPFRSRC).tar.bz2
  HASH:=d3103a80cdad2407ed581f3618c4bed04e0c92d1cf771a65ead662cc397f7775
endef
$(eval $(call Download,mpfr))

define Build/Prepare
	$(PKG_UNPACK)
#	we have to download and unpack additional stuff before patching
	tar -C $(PKG_BUILD_DIR) -xvjf $(DL_DIR)/$(GMPSRC).tar.bz2
	ln -sf $(PKG_BUILD_DIR)/$(GMPSRC) $(PKG_BUILD_DIR)/gmp
	tar -C $(PKG_BUILD_DIR) -xvzf $(DL_DIR)/$(MPCSRC).tar.gz
	ln -sf $(PKG_BUILD_DIR)/$(MPCSRC) $(PKG_BUILD_DIR)/mpc
	tar -C $(PKG_BUILD_DIR) -xvjf $(DL_DIR)/$(MPFRSRC).tar.bz2
	ln -sf $(PKG_BUILD_DIR)/$(MPFRSRC) $(PKG_BUILD_DIR)/mpfr
	$(Build/Patch)
#	poor man's fix for `none-openwrt-linux' not recognized when building with musl
	cp $(PKG_BUILD_DIR)/config.sub $(PKG_BUILD_DIR)/mpfr/
	cp $(PKG_BUILD_DIR)/config.sub $(PKG_BUILD_DIR)/gmp/
	chmod u+w $(PKG_BUILD_DIR)/mpc/config.sub
	cp $(PKG_BUILD_DIR)/config.sub $(PKG_BUILD_DIR)/mpc/
endef

CONFIGURE_ARGS += CXXFLAGS_FOR_TARGET="-g -O2 -D_GLIBCXX_INCLUDE_NEXT_C_HEADERS"

define Build/Configure
	(cd $(PKG_BUILD_DIR); rm -f config.cache; \
		SHELL="$(BASH)" \
		$(TARGET_CONFIGURE_OPTS) \
		$(PKG_BUILD_DIR)/configure \
			$(CONFIGURE_ARGS) \
			--build=$(GNU_HOST_NAME) \
			--host=$(REAL_GNU_TARGET_NAME) \
			--target=$(REAL_GNU_TARGET_NAME) \
			--enable-languages=$(TARGET_LANGUAGES) \
			--with-bugurl=$(BUGURL) \
			--with-pkgversion="$(PKGVERSION)" \
			--enable-dependency-tracking \
			--enable-shared \
			$(if $(CONFIG_LIBC_USE_GLIBC),--enable,--disable)-__cxa_atexit \
			--with-default-libstdcxx-abi=gcc4-compatible \
			--enable-target-optspace \
			--with-gnu-ld \
			--disable-nls \
			--disable-libsanitizer \
			--disable-libvtv \
			--disable-libcilkrts \
			--disable-libmudflap \
			--disable-libmpx \
			--disable-multilib \
			--disable-libgomp \
			--disable-libquadmath \
			--disable-libssp \
			--disable-decimal-float \
			--disable-libstdcxx-pch \
			--with-host-libstdcxx=-lstdc++ \
			--prefix=/opt \
			--libexecdir=/opt/lib \
			--with-local-prefix=/opt \
			--with-stage1-ldflags="-lstdc++ -Wl,-rpath=/opt/lib -Wl,--dynamic-linker=/opt/lib/$(DYNLINKER)" \
			$(ARM_FLOAT_OPTION) \
			$(SOFT_FLOAT_CONFIG_OPTION) \
			$(call qstrip,$(CONFIG_EXTRA_GCC_CONFIG_OPTIONS)) \
	);
endef

define Build/Compile
	export SHELL="$(BASH)"; $(MAKE_VARS) $(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
			DESTDIR="$(PKG_INSTALL_DIR)" $(MAKE_ARGS) all
	export SHELL="$(BASH)"; $(MAKE_VARS) $(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
			DESTDIR="$(PKG_INSTALL_DIR)" $(MAKE_ARGS) install
endef

ENVCFLAGS:="$(TARGET_OPTIMIZATION) $(EXTRA_OPTIMIZATION)
ifeq ($(CONFIG_SOFT_FLOAT),y)
    ifeq ($(CONFIG_arm),y)
	ENVCFLAGS+= -mfloat-abi=soft
    else
	ENVCFLAGS+= -msoft-float
    endif
endif
ENVCFLAGS+="

ENVLDFLAGS:="-Wl,-rpath=/opt/lib -Wl,--dynamic-linker=/opt/lib/$(DYNLINKER) -L/opt/lib, -lstdc++"

define Package/gcc/install
	$(INSTALL_DIR) $(1)/opt/bin $(1)/opt/lib $(1)/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)
	cp -ar $(PKG_INSTALL_DIR)/opt/include $(1)/opt
	# FIXME c++
	cp -ar $(PKG_INSTALL_DIR)/opt/include/c++/$(PKG_VERSION)/$(REAL_GNU_TARGET_NAME)/{bits,ext} \
		$(1)/opt/include/c++/$(PKG_VERSION)/
	cp -a $(PKG_INSTALL_DIR)/opt/bin/{$(REAL_GNU_TARGET_NAME)-{g++,gcc,gcc-ar},cpp,gcov} $(1)/opt/bin
	ln -s $(REAL_GNU_TARGET_NAME)-g++ $(1)/opt/bin/c++
	ln -s $(REAL_GNU_TARGET_NAME)-g++ $(1)/opt/bin/g++
	ln -s $(REAL_GNU_TARGET_NAME)-g++ $(1)/opt/bin/$(REAL_GNU_TARGET_NAME)-c++
	ln -s $(REAL_GNU_TARGET_NAME)-gcc $(1)/opt/bin/gcc
	ln -s $(REAL_GNU_TARGET_NAME)-gcc $(1)/opt/bin/cc
	ln -s $(REAL_GNU_TARGET_NAME)-gcc $(1)/opt/bin/$(REAL_GNU_TARGET_NAME)-gcc-$(PKG_VERSION)
	ln -s $(REAL_GNU_TARGET_NAME)-gcc-ar $(1)/opt/bin/gcc-ar
	cp -ar $(PKG_INSTALL_DIR)/opt/lib/gcc $(1)/opt/lib
	cp -ar $(TOOLCHAIN_ROOT_DIR)/include $(1)/opt
	cp -a $(TOOLCHAIN_ROOT_DIR)/lib/*.{o,so*} $(1)/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)
	cp -a $(TOOLCHAIN_ROOT_DIR)/lib/*nonshared*.a  $(1)/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)
	cp -a $(TOOLCHAIN_ROOT_DIR)/lib/libm.a  $(1)/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)
	$(COPY_STATIC_LIBC)
	$(COPY_STATIC_LIBPTHREAD)
	$(COPY_STATIC_LIBSTDC)
	rm -f $(1)/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/libgo*
	rm -f $(1)/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/libcc1*
	# XXX rm "..install.cmd" ".install"
	$(FIND) $(1)/opt/include -type f -name '*install*' -delete
	# XXX fix path
	$(SED) 's,/usr/include,/opt/include,' \
		$(1)/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/install-tools/mkheaders.conf
	# XXX cleanup *.la/*.py
	$(SED) 's,^dependency_libs=.*,dependency_libs='\'' -L/opt/lib -lm'\'',' \
		$(1)/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/liblto_plugin.la
	$(SED) 's,$(TOOLCHAIN_ROOT_DIR)/$(REAL_GNU_TARGET_NAME),/opt,;s,$(TOOLCHAIN_ROOT_DIR),/opt,' \
			$(1)/opt/lib/$(PKG_NAME)/$(REAL_GNU_TARGET_NAME)/$(PKG_VERSION)/*.py
	$(INSTALL_DIR) $(1)/opt/etc/profile.d
	echo 'export CFLAGS=$(ENVCFLAGS)' > $(1)/opt/etc/profile.d/gcc.sh
	echo 'export CXXFLAGS=$(ENVCFLAGS)' >> $(1)/opt/etc/profile.d/gcc.sh
	echo 'export LDFLAGS=$(ENVLDFLAGS)' >> $(1)/opt/etc/profile.d/gcc.sh
endef

#ifeq ($(CONFIG_INCLUDE_STATIC_LINK_SPEC),y)
define Package/gcc/postinst
#!/bin/sh

$(INSTALL_STATIC_SPEC)
$(RPATH_LINK_STATIC_SPEC)

echo "There are no *-dev packages in Entware(with few exceptions)!"
echo "Please install headers as described in the wiki:"
echo "https://github.com/Entware/Entware/wiki"
endef

ifeq ($(CONFIG_INCLUDE_STATIC_LINK_SPEC),y)
define Package/gcc/postrm
#!/bin/sh

$(REMOVE_STATIC_SPEC)
endef
endif

$(eval $(call BuildPackage,gcc))
