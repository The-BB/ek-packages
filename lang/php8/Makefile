#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=php
PKG_VERSION:=8.4.10
PKG_RELEASE:=1

PKG_MAINTAINER:=Michael Heimpold <mhei@heimpold.de>
PKG_LICENSE:=PHP-3.01
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:php:php

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://www.php.net/distributions/
PKG_HASH:=14983a9ef8800e6bc2d920739fd386054402f7976ca9cd7f711509496f0d2632

PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

PHP8_MODULES= \
	bcmath \
	calendar ctype curl \
	dom \
	exif \
	fileinfo filter ftp \
	gettext gd gmp \
	iconv intl \
	ldap \
	mbstring mysqli mysqlnd \
	opcache openssl \
	pcntl pdo pdo-mysql pdo-pgsql pdo-sqlite pgsql phar \
	session shmop simplexml snmp soap sockets sodium sqlite3 sysvmsg sysvsem sysvshm \
	tokenizer \
	xml xmlreader xmlwriter \
	zip

PKG_CONFIG_DEPENDS:= \
	$(patsubst %,CONFIG_PACKAGE_php8-mod-%,$(PHP8_MODULES)) \
	CONFIG_PHP8_LIBXML CONFIG_PHP8_SYSTEMTZDATA CONFIG_PACKAGE_apache-mod-php8

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

define Package/php8/Default
  SUBMENU:=PHP8
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=PHP8 Hypertext preprocessor
  URL:=http://www.php.net/
  DEPENDS:=php8
endef

define Package/php8/Default/description
  PHP is a widely-used general-purpose scripting language that is especially
  suited for Web development and can be embedded into HTML.
endef

define Package/php8/config
	config PHP8_LIBXML
		bool "PHP8 LIBXML support"
		depends on PACKAGE_php8-cli || PACKAGE_php8-cgi

	config PHP8_SYSTEMTZDATA
		bool "Use system timezone data instead of php's built-in database"
		depends on PACKAGE_php8-cli || PACKAGE_php8-cgi || PACKAGE_apache-mod-php8
		default y
		help
			Enabling this feature automatically selects the zoneinfo-core package
			which contains data for UTC timezone. To use other timezones you have
			to install the corresponding zoneinfo-... package(s).
endef

define Package/php8
  $(call Package/php8/Default)

  DEPENDS:=+libpcre2 +zlib \
           +PHP8_LIBXML:libxml2 \
           +PHP8_SYSTEMTZDATA:zoneinfo-core
  CONFLICTS:=php7
endef

define Package/php8/description
  $(call Package/php8/Default/description)
  This package contains only the PHP config file. You must actually choose
  your PHP flavour (cli, cgi or fastcgi).

  Please note, that installing php5 and php8 in parallel on the same target
  is not supported in OpenWrt/LEDE/Entware.
endef

define Package/php8-cli
  $(call Package/php8/Default)
  DEPENDS+= +PACKAGE_php8-mod-intl:libstdcpp +riscv64:libatomic
  TITLE+= (CLI)
endef

define Package/php8-cli/description
  $(call Package/php8/Default/description)
  This package contains the CLI version of the PHP8 interpreter.
endef

define Package/php8-cgi
  $(call Package/php8/Default)
  DEPENDS+= +PACKAGE_php8-mod-intl:libstdcpp +riscv64:libatomic
  TITLE+= (CGI & FastCGI)
endef

define Package/php8-cgi/description
  $(call Package/php8/Default/description)
  This package contains the CGI version of the PHP8 interpreter.
endef

define Package/php8-fastcgi
  $(call Package/php8/Default)
  DEPENDS+= +php8-cgi
  TITLE:=FastCGI startup script
endef

define Package/php8-fastcgi/description
  As FastCGI support is now a core feature the php8-fastcgi package now depends
  on the php8-cgi package, containing just the startup script.
endef

define Package/php8-fpm
  $(call Package/php8/Default)
  DEPENDS+= +PACKAGE_php8-mod-intl:libstdcpp +riscv64:libatomic
  TITLE+= (FPM)
endef

define Package/php8-fpm/description
  $(call Package/php8/Default/description)
  This package contains the FastCGI Process Manager of the PHP8 interpreter.
endef

define Package/php8-mod-intl/config
  config PHP8_FULLICUDATA
	bool "Add dependency to full ICU Data"
	depends on PACKAGE_php8-mod-intl
	default n
endef

define Package/php8-mod-intl/description
  Note that this package depends in ICU library which is built without data
  by default. This is to satisfy programs build and run dependencies but to
  keep the installed footprint small on the target system(s).
  However, the data is required to make the ICU library useful - and thus
  directly affects PHPs ICU extension, too - so consider to also
  select/install package 'icu-full-data'.
endef

define Package/apache-mod-php8
  $(call Package/php8/Default)
  SUBMENU:=Web Servers/Proxies
  SECTION:=net
  CATEGORY:=Network
  DEPENDS+=PACKAGE_apache-mod-php8:apache \
	   +PACKAGE_php8-mod-intl:libstdcpp \
	   +riscv64:libatomic \
	   +libpcre2 +zlib
  TITLE:=PHP8 module for Apache Web Server
endef

define Package/apache-mod-php8/description
  $(call Package/php8/Default/description)
  This package contains the PHP module for the Apache Web Server.
endef

# not everything groks --disable-nls
DISABLE_NLS:=

CONFIGURE_ARGS+= \
	--enable-cli \
	--enable-cgi \
	--enable-fpm \
	--enable-shared \
	--disable-static \
	--with-pic \
	--disable-rpath \
	--disable-debug \
	--disable-phpdbg \
	--without-pear \
	\
	--with-config-file-path=/opt/etc \
	--with-config-file-scan-dir=/opt/etc/php8 \
	--disable-short-tags \
	\
	--without-valgrind \
	--with-external-pcre \
	--with-zlib="$(STAGING_DIR)/opt"

ifeq ($(CONFIG_LIBC_USE_GLIBC),y)
TARGET_LDFLAGS += -ldl

# XXX Entware specific: fixed issue #693
TARGET_LDFLAGS += -lresolv
endif
ifeq ($(CONFIG_USE_MUSL),y)
TARGET_CFLAGS += -D_LARGEFILE64_SOURCE
endif
ifneq ($(findstring riscv64,$(CONFIG_ARCH)),)
TARGET_LDFLAGS += -latomic
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-bcmath),)
  CONFIGURE_ARGS+= --enable-bcmath=shared
else
  CONFIGURE_ARGS+= --disable-bcmath
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-calendar),)
  CONFIGURE_ARGS+= --enable-calendar=shared
else
  CONFIGURE_ARGS+= --disable-calendar
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-ctype),)
  CONFIGURE_ARGS+= --enable-ctype=shared
else
  CONFIGURE_ARGS+= --disable-ctype
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-curl),)
  CONFIGURE_ARGS+= --with-curl=shared
else
  CONFIGURE_ARGS+= --without-curl
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-dom),)
  CONFIGURE_ARGS+= --enable-dom=shared
else
  CONFIGURE_ARGS+= --disable-dom
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-exif),)
  CONFIGURE_ARGS+= --enable-exif=shared
else
  CONFIGURE_ARGS+= --disable-exif
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-fileinfo),)
  CONFIGURE_ARGS+= --enable-fileinfo=shared,"$(STAGING_DIR)/opt"
else
  CONFIGURE_ARGS+= --disable-fileinfo
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-filter),)
  CONFIGURE_ARGS+= --enable-filter=shared,"$(STAGING_DIR)/opt"
else
  CONFIGURE_ARGS+= --disable-filter
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-ftp),)
  CONFIGURE_ARGS+= --enable-ftp=shared
else
  CONFIGURE_ARGS+= --disable-ftp
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-gd),)
  CONFIGURE_ARGS+= \
	--enable-gd=shared,"$(STAGING_DIR)/opt" \
	--with-external-gd
else
  CONFIGURE_ARGS+= --disable-gd
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-gettext),)
  CONFIGURE_ARGS+= --with-gettext=shared,"$(STAGING_DIR)/opt/lib/libintl-full"
else
  CONFIGURE_ARGS+= --without-gettext
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-gmp),)
  CONFIGURE_ARGS+= --with-gmp=shared,"$(STAGING_DIR)/opt"
else
  CONFIGURE_ARGS+= --without-gmp
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-iconv),)
  ifeq ($(CONFIG_BUILD_NLS),y)
    CONFIGURE_VARS+= iconv_impl_name="gnu_libiconv"
    CONFIGURE_ARGS+= --with-iconv=shared,"$(ICONV_PREFIX)"
  else
    CONFIGURE_VARS+= ac_cv_func_iconv=yes
    CONFIGURE_ARGS+= --with-iconv=shared
  endif
else
  CONFIGURE_ARGS+= --without-iconv
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-intl),)
  CONFIGURE_ARGS+= --enable-intl=shared
else
  CONFIGURE_ARGS+= --disable-intl
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-ldap),)
  CONFIGURE_ARGS+= \
	--with-ldap=shared,"$(STAGING_DIR)/opt" \
	--with-ldap-sasl
else
  CONFIGURE_ARGS+= --without-ldap
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-mbstring),)
  CONFIGURE_ARGS+= \
	--enable-mbstring=shared \
	--enable-mbregex
else
  CONFIGURE_ARGS+= --disable-mbstring
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-mysqli),)
  CONFIGURE_ARGS+= --with-mysqli=shared
else
  CONFIGURE_ARGS+= --without-mysqli
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-mysqlnd),)
  CONFIGURE_ARGS+= --enable-mysqlnd=shared
else
  CONFIGURE_ARGS+= --disable-mysqlnd
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-opcache),)
  CONFIGURE_ARGS+= --enable-opcache=shared
else
  CONFIGURE_ARGS+= --disable-opcache
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-openssl)$(CONFIG_PACKAGE_php8-mod-ftp)$(CONFIG_PACKAGE_php8-mod-snmp),)
  CONFIGURE_ARGS+= \
	--with-openssl=shared \
	--with-kerberos=no \
	--with-openssl-dir="$(STAGING_DIR)/opt"
else
  CONFIGURE_ARGS+= --without-openssl
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-pcntl),)
  CONFIGURE_ARGS+= --enable-pcntl=shared
else
  CONFIGURE_ARGS+= --disable-pcntl
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-pdo),)
  CONFIGURE_ARGS+= --enable-pdo=shared
  ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-pdo-mysql),)
    CONFIGURE_ARGS+= --with-pdo-mysql=shared
  else
    CONFIGURE_ARGS+= --without-pdo-mysql
  endif
  ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-pdo-pgsql),)
    CONFIGURE_ARGS+= --with-pdo-pgsql=shared,"$(STAGING_DIR)/opt"
  else
    CONFIGURE_ARGS+= --without-pdo-pgsql
  endif
  ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-pdo-sqlite),)
    CONFIGURE_ARGS+= --with-pdo-sqlite=shared
  else
    CONFIGURE_ARGS+= --without-pdo-sqlite
  endif
else
  CONFIGURE_ARGS+= --disable-pdo
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-pgsql),)
  CONFIGURE_ARGS+= --with-pgsql=shared,"$(STAGING_DIR)/opt"
else
  CONFIGURE_ARGS+= --without-pgsql
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-phar),)
  CONFIGURE_ARGS+= --enable-phar=shared
else
  CONFIGURE_ARGS+= --disable-phar
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-session),)
  CONFIGURE_ARGS+= --enable-session=shared
else
  CONFIGURE_ARGS+= --disable-session
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-shmop),)
  CONFIGURE_ARGS+= --enable-shmop=shared
else
  CONFIGURE_ARGS+= --disable-shmop
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-simplexml),)
  CONFIGURE_ARGS+= --enable-simplexml=shared
else
  CONFIGURE_ARGS+= --disable-simplexml
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-snmp),)
  CONFIGURE_ARGS+= --with-snmp=shared,"$(STAGING_DIR)/opt"
  CONFIGURE_VARS+= \
    ac_cv_have_decl_usmHMAC192SHA256AuthProtocol=no \
    ac_cv_have_decl_usmHMAC384SHA512AuthProtocol=no
else
  CONFIGURE_ARGS+= --without-snmp
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-soap),)
  CONFIGURE_ARGS+= --enable-soap=shared
else
  CONFIGURE_ARGS+= --disable-soap
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-sockets),)
  CONFIGURE_ARGS+= --enable-sockets=shared
else
  CONFIGURE_ARGS+= --disable-sockets
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-sodium),)
  CONFIGURE_ARGS+= --with-sodium=shared,"$(STAGING_DIR)/opt"
else
  CONFIGURE_ARGS+= --without-sodium
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-sqlite3),)
  CONFIGURE_ARGS+= --with-sqlite3=shared
else
  CONFIGURE_ARGS+= --without-sqlite3
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-sysvmsg),)
  CONFIGURE_ARGS+= --enable-sysvmsg=shared
else
  CONFIGURE_ARGS+= --disable-sysvmsg
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-sysvsem),)
  CONFIGURE_ARGS+= --enable-sysvsem=shared
else
  CONFIGURE_ARGS+= --disable-sysvsem
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-sysvshm),)
  CONFIGURE_ARGS+= --enable-sysvshm=shared
else
  CONFIGURE_ARGS+= --disable-sysvshm
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-tokenizer),)
  CONFIGURE_ARGS+= --enable-tokenizer=shared
else
  CONFIGURE_ARGS+= --disable-tokenizer
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-xml),)
  CONFIGURE_ARGS+= --enable-xml=shared,"$(STAGING_DIR)/opt"
  ifneq ($(CONFIG_PHP8_LIBXML),y)
    CONFIGURE_ARGS+= --with-expat
  endif
else
  CONFIGURE_ARGS+= --disable-xml
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-xmlreader),)
  CONFIGURE_ARGS+= --enable-xmlreader=shared,"$(STAGING_DIR)/opt"
else
  CONFIGURE_ARGS+= --disable-xmlreader
endif

ifneq ($(SDK)$(CONFIG_PACKAGE_php8-mod-xmlwriter),)
  CONFIGURE_ARGS+= --enable-xmlwriter=shared,"$(STAGING_DIR)/opt"
else
  CONFIGURE_ARGS+= --disable-xmlwriter
endif

ifneq ($(CONFIG_PACKAGE_php8-mod-zip),)
  CONFIGURE_ARGS+= --with-zip=shared,"$(STAGING_DIR)/opt"
else
  CONFIGURE_ARGS+= --without-zip
endif

ifneq ($(SDK)$(CONFIG_PHP8_LIBXML),)
  CONFIGURE_ARGS+= --with-libxml
else
  CONFIGURE_ARGS+= --without-libxml
endif

ifneq ($(CONFIG_PHP8_SYSTEMTZDATA),)
  CONFIGURE_ARGS+= --with-system-tzdata
else
  CONFIGURE_ARGS+= --without-system-tzdata
endif

ifneq ($(CONFIG_PACKAGE_apache-mod-php8),)
  CONFIGURE_ARGS+= --with-apxs2=$(STAGING_DIR)/opt/bin/apxs
endif

CONFIGURE_VARS+= \
	ac_cv_c_bigendian_php=$(if $(CONFIG_BIG_ENDIAN),yes,no) \
	php_cv_cc_rpath="no" \
	ac_cv_php_xml2_config_path="$(STAGING_DIR)/host/bin/xml2-config" \
	ac_cv_u8t_decompose=yes \
	ac_cv_have_pcre2_jit=no

define Package/php8/conffiles
/opt/etc/php.ini
/opt/etc/php8/
endef

define Package/php8/install
	$(INSTALL_DIR) $(1)/opt/etc
	$(INSTALL_DATA) ./files/php.ini $(1)/opt/etc/
endef

define Package/php8-cli/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) $(PKG_BUILD_DIR)/sapi/cli/php $(1)/opt/bin/php8-cli
	ln -sf php8-cli $(1)/opt/bin/php-cli
endef

define Package/php8-cgi/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) $(PKG_BUILD_DIR)/sapi/cgi/php-cgi $(1)/opt/bin/php8-cgi
	ln -sf php8-cgi $(1)/opt/bin/php-cgi
	ln -sf php8-cgi $(1)/opt/bin/php8-fcgi
	ln -sf php8-fcgi $(1)/opt/bin/php-fcgi
endef

define Package/php8-fastcgi/install
	$(INSTALL_DIR) $(1)/opt/etc/php8
	$(INSTALL_DATA) ./files/php8-fastcgi.conf $(1)/opt/etc/php8/php8-fastcgi

	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S79php8-fastcgi $(1)/opt/etc/init.d
endef

define Package/php8-fastcgi/conffiles
/opt/etc/php8/php8-fastcgi
endef

define Package/php8-fpm/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/sapi/fpm/php-fpm $(1)/opt/bin/php8-fpm

	$(INSTALL_DIR) $(1)/opt/etc
	$(INSTALL_DATA) ./files/php8-fpm.conf $(1)/opt/etc/php8-fpm.conf

#	$(INSTALL_DIR) $(1)/etc/config
#	$(INSTALL_DATA) ./files/php8-fpm.config $(1)/etc/config/php8-fpm

	$(INSTALL_DIR) $(1)/opt/etc/php8-fpm.d
	$(INSTALL_DATA) ./files/php8-fpm-www.conf $(1)/opt/etc/php8-fpm.d/www.conf

	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S79php8-fpm $(1)/opt/etc/init.d
endef

define Package/php8-fpm/conffiles
/opt/etc/php8-fpm.conf
/opt/etc/php8-fpm.d/
endef

define Package/apache-mod-php8/install
	$(INSTALL_DIR) $(1)/opt/lib/apache2
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/libs/libphp.so $(1)/opt/lib/apache2
endef

define Build/Prepare
	$(call Build/Prepare/Default)
	$(if $(QUILT),,( cd $(PKG_BUILD_DIR); touch configure.ac; ./buildconf --force ))
endef

define Build/Configure
	$(if $(QUILT),( cd $(PKG_BUILD_DIR); touch configure.ac; ./buildconf --force ))
	$(call Build/Configure/Default)
endef

define Build/InstallDev
	rm -rf $(PKG_BUILD_DIR)/staging
	make -C $(PKG_BUILD_DIR) install INSTALL_ROOT=$(PKG_BUILD_DIR)/staging
	rm -rf $(PKG_BUILD_DIR)/staging/opt/{share,man,sbin}
	rm -f $(PKG_BUILD_DIR)/staging/opt/bin/{php,php-cgi,php-cli}
	mv $(PKG_BUILD_DIR)/staging/opt/bin/phpize $(PKG_BUILD_DIR)/staging/opt/bin/phpize8
	mv $(PKG_BUILD_DIR)/staging/opt/bin/php-config $(PKG_BUILD_DIR)/staging/opt/bin/php8-config
	mv $(PKG_BUILD_DIR)/staging/opt/include/php $(PKG_BUILD_DIR)/staging/opt/include/php8
	mv $(PKG_BUILD_DIR)/staging/opt/lib/php $(PKG_BUILD_DIR)/staging/opt/lib/php8

	$(CP) $(PKG_BUILD_DIR)/staging/opt $(STAGING_DIR)/

	sed -i -e "s#prefix='/opt'#prefix='$(STAGING_DIR)/opt'#" $(STAGING_DIR)/opt/bin/phpize8
	sed -i -e "s#exec_prefix=\"\`eval echo /opt\`\"#exec_prefix='$(STAGING_DIR)/opt'#" $(STAGING_DIR)/opt/bin/phpize8
	sed -i -e "s#/include\`/php\"#/include\`/php8\"#" $(STAGING_DIR)/opt/bin/phpize8
	sed -i -e "s#/lib/php\`/build\"#/lib/php8\`/build\"#" $(STAGING_DIR)/opt/bin/phpize8

	sed -i -e "s#prefix=\"/opt\"#prefix=\"$(STAGING_DIR)/opt\"#" $(STAGING_DIR)/opt/bin/php8-config
	sed -i -e "s#/include/php\"#/include/php8\"#" $(STAGING_DIR)/opt/bin/php8-config
endef

define BuildModule

  define Package/php8-mod-$(1)
    $(call Package/php8/Default)

    DEPENDS+=+riscv64:libatomic

    ifneq ($(3),)
      DEPENDS+=$(3)
    endif

    TITLE:=$(2) shared module
  endef

  define Package/php8-mod-$(1)/install
	$(INSTALL_DIR) $$(1)/opt/lib/php8
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/modules/$(subst -,_,$(1)).so $$(1)/opt/lib/php8/
	$(INSTALL_DIR) $$(1)/opt/etc/php8
      ifeq ($(5),zend)
	echo "zend_extension=/opt/lib/php8/$(subst -,_,$(1)).so" > $$(1)/opt/etc/php8/$(if $(4),$(4),20)_$(subst -,_,$(1)).ini
      else
	echo "extension=$(subst -,_,$(1)).so" > $$(1)/opt/etc/php8/$(if $(4),$(4),20)_$(subst -,_,$(1)).ini
    endif
  endef

  $$(eval $$(call BuildPackage,php8-mod-$(1)))

endef

$(eval $(call BuildPackage,php8))
$(eval $(call BuildPackage,php8-cgi))
$(eval $(call BuildPackage,php8-cli))
$(eval $(call BuildPackage,php8-fastcgi))
$(eval $(call BuildPackage,php8-fpm))
$(eval $(call BuildPackage,apache-mod-php8))

#$(eval $(call BuildModule,NAME,TITLE[,PKG DEPENDS]))
$(eval $(call BuildModule,bcmath,Bcmath))
$(eval $(call BuildModule,calendar,Calendar))
$(eval $(call BuildModule,ctype,Ctype))
$(eval $(call BuildModule,curl,cURL,+PACKAGE_php8-mod-curl:libcurl))
$(eval $(call BuildModule,dom,DOM,+@PHP8_LIBXML +PACKAGE_php8-mod-dom:libxml2))
$(eval $(call BuildModule,exif,EXIF))
$(eval $(call BuildModule,fileinfo,Fileinfo))
$(eval $(call BuildModule,filter,Filter))
$(eval $(call BuildModule,ftp,FTP,+PACKAGE_php8-mod-ftp:libopenssl))
$(eval $(call BuildModule,gd,GD graphics,+PACKAGE_php8-mod-gd:libgd-full))
$(eval $(call BuildModule,gettext,Gettext,+PACKAGE_php8-mod-gettext:libintl-full))
$(eval $(call BuildModule,gmp,GMP,+PACKAGE_php8-mod-gmp:libgmp))
$(eval $(call BuildModule,iconv,iConv,$(ICONV_DEPENDS)))
$(eval $(call BuildModule,intl,Internationalization Functions,+PACKAGE_php8-mod-intl:icu +PHP8_FULLICUDATA:icu-full-data))
$(eval $(call BuildModule,ldap,LDAP,+PACKAGE_php8-mod-ldap:libopenldap +PACKAGE_php8-mod-ldap:libsasl2))
$(eval $(call BuildModule,mbstring,MBString,+PACKAGE_php8-mod-mbstring:oniguruma))
$(eval $(call BuildModule,mysqli,MySQL Improved Extension,+PACKAGE_php8-mod-mysqli:php8-mod-mysqlnd,30))
$(eval $(call BuildModule,mysqlnd,MySQL Native Driver,+PACKAGE_php8-mod-openssl:php8-mod-openssl))
$(eval $(call BuildModule,opcache,OPcache,,,zend))
$(eval $(call BuildModule,openssl,OpenSSL,+PACKAGE_php8-mod-openssl:libopenssl,15))
$(eval $(call BuildModule,pcntl,PCNTL))
$(eval $(call BuildModule,pdo,PHP Data Objects))
$(eval $(call BuildModule,pdo-mysql,PDO driver for MySQL,+php8-mod-pdo +PACKAGE_php8-mod-pdo-mysql:php8-mod-mysqlnd))
$(eval $(call BuildModule,pdo-pgsql,PDO driver for PostgreSQL,+php8-mod-pdo +PACKAGE_php8-mod-pdo-pgsql:libpq))
$(eval $(call BuildModule,pdo-sqlite,PDO driver for SQLite 3.x,+php8-mod-pdo +PACKAGE_php8-mod-pdo-sqlite:libsqlite3 +PACKAGE_php8-mod-pdo-sqlite:librt))
$(eval $(call BuildModule,pgsql,PostgreSQL,+PACKAGE_php8-mod-pgsql:libpq))
$(eval $(call BuildModule,phar,Phar Archives))
$(eval $(call BuildModule,session,Session))
$(eval $(call BuildModule,shmop,Shared Memory))
$(eval $(call BuildModule,simplexml,SimpleXML,+@PHP8_LIBXML +PACKAGE_php8-mod-simplexml:libxml2))
$(eval $(call BuildModule,snmp,SNMP,+PACKAGE_php8-mod-snmp:libnetsnmp +PACKAGE_php8-mod-snmp:libopenssl))
$(eval $(call BuildModule,soap,SOAP,+@PHP8_LIBXML +PACKAGE_php8-mod-soap:libxml2 +php8-mod-session))
$(eval $(call BuildModule,sockets,Sockets))
$(eval $(call BuildModule,sodium,Sodium,+PACKAGE_php8-mod-sodium:libsodium,30))
$(eval $(call BuildModule,sqlite3,SQLite3,+PACKAGE_php8-mod-sqlite3:libsqlite3))
$(eval $(call BuildModule,sysvmsg,System V messages))
$(eval $(call BuildModule,sysvsem,System V shared memory))
$(eval $(call BuildModule,sysvshm,System V semaphore))
$(eval $(call BuildModule,tokenizer,Tokenizer))
$(eval $(call BuildModule,xml,XML,+PHP8_LIBXML:libxml2 +!PHP8_LIBXML:libexpat))
$(eval $(call BuildModule,xmlreader,XMLReader,+@PHP8_LIBXML +PACKAGE_php8-mod-dom:php8-mod-dom +PACKAGE_php8-mod-xmlreader:libxml2))
$(eval $(call BuildModule,xmlwriter,XMLWriter,+@PHP8_LIBXML +PACKAGE_php8-mod-xmlwriter:libxml2))
$(eval $(call BuildModule,zip,ZIP,+PACKAGE_php8-mod-zip:libzip))
