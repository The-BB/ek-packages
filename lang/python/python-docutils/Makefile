include $(TOPDIR)/rules.mk

PKG_NAME:=python-docutils
PKG_VERSION:=0.20.1
PKG_RELEASE:=1

PYPI_NAME:=docutils
PKG_HASH:=f08a4e276c3a1583a86dce3e34aba3fe04d02bba2dd51ed16106244e8a923e3b

PKG_MAINTAINER:=Daniel Danzberger <daniel@dd-wrt.com>
PKG_LICENSE:=Public-Domain BSD-2-License Python-2.0.1 GPL-3.0-or-later
PKG_LICENSE_FILES:=COPYING.txt licenses/BSD-2-Clause.txt licenses/python-2-1-1.txt licenses/gpl-3-0.txt

include ../pypi.mk
include $(INCLUDE_DIR)/package.mk
include ../python3-package.mk

define Package/python3-docutils
  SUBMENU:=Python
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Documentation Utilities
  URL:=https://docutils.sourceforge.io/
  DEPENDS:=+python3
endef

define Package/python3-docutils/description
Docutils is a modular system for processing documentation into useful
formats, such as HTML, XML, and LaTeX. For input Docutils supports
reStructuredText, an easy-to-read, what-you-see-is-what-you-get
plaintext markup syntax.
endef

# XXX Entware specific: skip bite-code
BINS:= \
	rst2html rst2html4 rst2html5 rst2latex rst2man rst2odt \
	rst2odt_prepstyles rst2pseudoxml rst2s5 rst2xetex rst2xml \
	rstpep2html

define Py3Package/python3-docutils/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) $(PKG_INSTALL_DIR)/opt/bin/docutils $(1)/opt/bin
	for bin in $(BINS); do \
		$(CP) $(PKG_INSTALL_DIR)/opt/bin/$$$$bin.py \
			$(1)/opt/bin/$$$${bin}; \
	done
endef

$(eval $(call Py3Package,python3-docutils))
$(eval $(call BuildPackage,python3-docutils))
$(eval $(call BuildPackage,python3-docutils-src))
