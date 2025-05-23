#
# Copyright (C) 2006-2011 OpenWrt.org
# Copyright (C) 2015-2016, 2018-2023 Jeffery To
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=python-twisted
PKG_VERSION:=23.10.0
PKG_RELEASE:=1

PYPI_NAME:=Twisted
PYPI_SOURCE_NAME:=twisted
PKG_HASH:=987847a0790a2c597197613686e2784fd54167df3a55d0fb17c8412305d76ce5

PKG_BUILD_DEPENDS:=libtirpc

PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=Jeffery To <jeffery.to@gmail.com>
PKG_CPE_ID:=cpe:/a:twistedmatrix:twisted

PKG_BUILD_DEPENDS:=python-hatchling/host python-hatch-fancy-pypi-readme/host python-incremental/host

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-twisted
  SECTION:=lang
  CATEGORY:=Languages
  SUBMENU:=Python
  TITLE:=Asynchronous networking framework
  URL:=https://twistedmatrix.com/
  DEPENDS:= \
      +python3 \
      +python3-appdirs \
      +python3-attrs \
      +python3-automat \
      +python3-bcrypt \
      +python3-constantly \
      +python3-cryptography \
      +python3-hyperlink \
      +python3-idna \
      +python3-incremental \
      +python3-pyopenssl \
      +python3-service-identity \
      +python3-typing-extensions \
      +python3-zope-interface
endef

define Package/python3-twisted/description
Twisted is a networking engine written in Python, supporting numerous
protocols. It contains a web server, numerous chat clients, chat
servers, mail servers, and more.
endef

define Py3Package/python3-twisted/filespec
+|$(PYTHON3_PKG_DIR)
-|$(PYTHON3_PKG_DIR)/twisted/conch/scripts/tkconch.py
endef

define Py3Package/python3-twisted/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) $(PKG_INSTALL_DIR)/opt/bin/* $(1)/opt/bin/
	for bin in $(1)/opt/bin/*; do \
		mv $$$$bin $$$${bin}3 ; \
		$(LN) $$$${bin##*/}3 $$$$bin ; \
	done
endef

$(eval $(call Py3Package,python3-twisted))
$(eval $(call BuildPackage,python3-twisted))
$(eval $(call BuildPackage,python3-twisted-src))
