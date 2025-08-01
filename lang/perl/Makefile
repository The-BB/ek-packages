#
# Copyright (C) 2006-2018 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# Entware specific: base.config
# perl-dev package (with headers only) for native perl modules building added

include $(TOPDIR)/rules.mk

include perlver.mk

PKG_NAME:=perl
PKG_VERSION:=$(PERL_VERSION)
PKG_RELEASE:=4

PKG_SOURCE_URL:=https://www.cpan.org/src/5.0
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_HASH:=c740348f357396327a9795d3e8323bafd0fe8a5c7835fc1cbaba0cc8dfe7161f

PKG_LICENSE:=GPL-1.0-or-later Artistic-1.0-Perl
PKG_LICENSE_FILES:=Copying Artistic README
PKG_MAINTAINER:=Marcel Denia <naoir@gmx.net>, Philip Prindeville <philipp@redfish-solutions.com>
PKG_CPE_ID:=cpe:/a:perl:perl

# Build settings
PKG_BUILD_DIR:=$(BUILD_DIR)/perl/$(PKG_NAME)-$(PKG_VERSION)
HOST_BUILD_DIR:=$(BUILD_DIR_HOST)/perl/$(PKG_NAME)-$(PKG_VERSION)
PKG_INSTALL:=1
PKG_BUILD_DEPENDS:=perl/host
PKG_BUILD_PARALLEL:=1
HOST_BUILD_PARALLEL:=1

# Variables used during configuration/build
HOST_PERL_PREFIX:=$(STAGING_DIR_HOSTPKG)/usr

ifneq ($(CONFIG_USE_MUSL),)
  TARGET_CFLAGS += -D_LARGEFILE64_SOURCE
endif

# Filter -g3, it will break Compress-Raw-Zlib
TARGET_CFLAGS_PERL:=$(patsubst -g3,-g,$(TARGET_CFLAGS))
TARGET_CPPFLAGS_PERL:=$(patsubst -g3,-g,$(TARGET_CPPFLAGS))

# A list of disabled tests
# ExtUtils tests are disabled for now as we don't support building
# native extensions on the target machine at the moment
PERL_DISABLEDTESTS:=cpan/ExtUtils-Constant cpan/ExtUtils-MakeMaker
# We're on Linux, so don't even package them
PERL_DISABLEDTESTS+=cpan/Win32API-File cpan/Win32 ext/VMS-DCLsym ext/VMS-Filespec ext/VMS-Stdio ext/Win32CORE os2/
# NDBM and ODBM not supported
PERL_DISABLEDTESTS+=ext/NDBM_File ext/ODBM_File


include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/host-build.mk
include perlmod.mk

define Package/perl
  SUBMENU:=Perl
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=The Perl intepreter
  URL:=http://www.perl.com/
  DEPENDS:= +USE_GLIBC:libbsd +PERL_THREADS:libpthread @!arc #+USE_GLIBC:libcrypt-compat
endef

define Package/perl/description
  Perl is a stable, cross platform programming language.
  It is used for mission critical projects in the public and private sectors
  and is widely used to program web applications of all needs.
endef

define Package/perl/config
	source "$(SOURCE)/Config.in"
endef

# Static host perl
define Host/Configure
	( cd $(HOST_BUILD_DIR); ./Configure -der -Uusedl -Duserelocatableinc -Dprefix=$(HOST_PERL_PREFIX) $(if $(CONFIG_PERL_THREADS),-Dusethreads,))
endef

define Host/Install
	( cd $(HOST_BUILD_DIR); ./miniperl installperl )
	$(INSTALL_DIR) $(HOST_PERL_PREFIX)/bin/
	$(CP) $(HOST_BUILD_DIR)/generate_uudmap $(HOST_PERL_PREFIX)/bin/
	
	# Link any possibly installed static extension in
	$(MAKE) -C $(HOST_BUILD_DIR)/relink clean || true
	( cd $(HOST_BUILD_DIR)/relink && $(HOST_PERL_PREFIX)/bin/perl Makefile.PL )
	$(call perlmod/host/relink,$(HOST_BUILD_DIR)/relink)
endef

# Target perl
define Build/Configure
	# We don't want to pass -Ilib to host perl in the target build (as lib
	# contains the target libraries, and files may currently be written
	# while being imported in parallel builds). We do however need the
	# target versions of the Config modules at the beginning of the include
	# path for the build scripts' use.
	#
	# Create an alternative lib_build directory that will be added to the
	# include path instead of lib (using hostperl-wrapper), containing only
	# the config modules.
	$(INSTALL_DIR) $(PKG_BUILD_DIR)/lib_build
	ln -sf ../lib/Config.pm ../lib/Config_heavy.pl ../lib/Config_git.pl $(PKG_BUILD_DIR)/lib_build/

	install -m0755 files/hostperl-wrapper $(PKG_BUILD_DIR)/hostperl-wrapper
	sed -i "s'@HOST_PERL@'$(HOST_PERL_PREFIX)/bin/perl'g" $(PKG_BUILD_DIR)/hostperl-wrapper

	$(PERL_CMD) files/perlconfig.pl -Dowrt:target_cc='$(TARGET_CC)' \
	                                -Dowrt:gccversion=$(CONFIG_GCC_VERSION) \
	                                -Dowrt:target_cross='$(TARGET_CROSS)' \
	                                -Dowrt:cflags='$(TARGET_CFLAGS_PERL) $(TARGET_CPPFLAGS_PERL)' \
	                                -Dowrt:ldflags='$(TARGET_LDFLAGS) $(if $(CONFIG_GCC_LIBSSP),-lssp)' \
	                                -Dowrt:libc=$(subst uClibc,uclibc,$(CONFIG_LIBC)) \
	                                -Dowrt:ipv6=$(if $($(CONFIG_IPV6)),define,undef) \
	                                -Dowrt:threads=$(if $(CONFIG_PERL_THREADS),yes,no) \
	                                -Dowrt:staging_dir='$(STAGING_DIR)' \
	                                -Dowrt:host_perl_prefix='$(HOST_PERL_PREFIX)' \
	                                -Dsysroot='$(TOOLCHAIN_ROOT_DIR)' \
	                                files/version.config \
	                                files/base.config \
	                                files/$(patsubst i386,i486,$(ARCH)).config \
	                                files/architecture.config \
	                                files/signal.config \
	                                files/threads.config \
	                                files/libc.config \
	                                files/misc.config \
	                                > $(PKG_BUILD_DIR)/config.sh
	(cd $(PKG_BUILD_DIR) && ./Configure -S)
	install -m 0644 $(PKG_BUILD_DIR)/config.h $(PKG_BUILD_DIR)/xconfig.h
endef

define Build/Compile
	# make depend is required to avoid race conditions: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=996953
	+$(MAKE) -C $(PKG_BUILD_DIR) depend
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR)
endef

ifeq ($(CONFIG_arc),)
define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/lib/perl5/$(PERL_VERSION)
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/perl5/$(PERL_VERSION) $(1)/opt/lib/perl5/
endef
endif

define Package/perl/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/perl$(PKG_VERSION) $(1)/opt/bin
	ln -nsf perl$(PKG_VERSION) $(1)/opt/bin/perl
	
	$(INSTALL_DIR) $(1)/opt/lib/perl5/$(PERL_VERSION)/CORE
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/perl5/$(PERL_VERSION)/CORE/libperl.so $(1)/opt/lib/perl5/$(PERL_VERSION)/CORE/
endef


$(eval $(call RequireCommand,rsync, \
	$(PKG_NAME) requires rsync installed on the host-system. \
))

$(eval $(call BuildPackage,perl))
$(eval $(call HostBuild))

-include perlbase.mk

# A helper package that includes all sort of supplementary files for tests
define Package/perl-tests-common
$(call Package/perlbase-template)
TITLE:=Common test support files
DEPENDS:=@PERL_TESTS
endef

define Package/perl-tests-common/install
	$(INSTALL_DIR) $(1)/$(PERL_TESTSDIR)
	$(INSTALL_DIR) $(1)/$(PERL_TESTSDIR)/Porting
	$(INSTALL_DIR) $(1)/$(PERL_TESTSDIR)/regen
	$(INSTALL_DIR) $(1)/$(PERL_TESTSDIR)/lib
	$(INSTALL_DIR) $(1)/opt/lib/perl5/$(PERL_VERSION)/XS
	$(INSTALL_DIR) $(1)/opt/lib/perl5/$(PERL_VERSION)/auto/XS
	$(INSTALL_DIR) $(1)/opt/lib/perl5/$(PERL_VERSION)/unicore
	
	$(CP) $(PKG_BUILD_DIR)/t $(1)/$(PERL_TESTSDIR)
	$(CP) $(PKG_BUILD_DIR)/Porting $(1)/$(PERL_TESTSDIR)
	$(CP) $(PKG_BUILD_DIR)/regen $(1)/$(PERL_TESTSDIR)
	$(CP) $(PKG_BUILD_DIR)/MANIFEST $(1)/$(PERL_TESTSDIR)
	$(CP) $(PKG_BUILD_DIR)/TestInit.pm $(1)/$(PERL_TESTSDIR)
	$(CP) $(PKG_BUILD_DIR)/vutil.c $(1)/$(PERL_TESTSDIR)
	$(CP) $(PKG_BUILD_DIR)/vxs.inc $(1)/$(PERL_TESTSDIR)
	$(CP) $(PKG_BUILD_DIR)/lib/XS $(1)/opt/lib/perl5/$(PERL_VERSION)/
	$(CP) $(PKG_BUILD_DIR)/lib/auto/XS $(1)/opt/lib/perl5/$(PERL_VERSION)/auto
	$(CP) $(PKG_BUILD_DIR)/lib/vmsish.pm $(1)/opt/lib/perl5/$(PERL_VERSION)/
	$(CP) $(PKG_BUILD_DIR)/lib/vmsish.t $(1)/$(PERL_TESTSDIR)/lib
	$(CP) $(PKG_BUILD_DIR)/lib/Internals.t $(1)/$(PERL_TESTSDIR)/lib
	$(CP) $(PKG_BUILD_DIR)/lib/unicore/TestProp.pl $(1)/opt/lib/perl5/$(PERL_VERSION)/unicore
	$(CP) files/perl-run_tests.sh $(1)/$(PERL_TESTSDIR)/run_tests.sh
	sed \
		-e 's!%%PERL_DISABLEDTESTS%%!$(PERL_DISABLEDTESTS)!' \
		-e 's!%%PERL_VERSION%%!$(PERL_VERSION)!' \
		-i $(1)/$(PERL_TESTSDIR)/run_tests.sh
	$(CP) $(PKG_BUILD_DIR)/config_h.SH $(1)/$(PERL_TESTSDIR)
	$(CP) $(PKG_BUILD_DIR)/perl.h $(1)/$(PERL_TESTSDIR)
endef

$(eval $(call BuildPackage,perl-tests-common))

define Package/perl-dev
$(call Package/perlbase-template)
TITLE:=Perl development files
DEPENDS:=+perl
endef

define Package/perl-dev/install
	$(INSTALL_DIR) $(1)/opt/lib/perl5/$(PERL_VERSION)/CORE
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/perl5/$(PERL_VERSION)/CORE/*.h $(1)/opt/lib/perl5/$(PERL_VERSION)/CORE/
endef

$(eval $(call BuildPackage,perl-dev))
