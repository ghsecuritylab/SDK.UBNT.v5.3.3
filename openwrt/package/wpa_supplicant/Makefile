#
# Copyright (C) 2008-2009 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# $Id: Makefile 15885 2009-05-17 01:43:57Z nico $

include $(TOPDIR)/rules.mk

PKG_NAME:=wpa_supplicant
PKG_VERSION:=0.6.10
PKG_RELEASE:=1.1
PKG_MD5SUM:=8985b1030d753e5f74f8acfc34e591bb

PKG_SOURCE_URL:=http://hostap.epitest.fi/releases
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz

PKG_BUILD_DEPENDS:=+kmod-ath-11n

include $(INCLUDE_DIR)/package.mk

define Package/wpa-supplicant
  SECTION:=net
  CATEGORY:=Network
  TITLE:=WPA Supplicant
  DEPENDS:=$(if $(CONFIG_WPA_SUPPLICANT_OPENSSL),+libopenssl)
  URL:=http://hostap.epitest.fi/wpa_supplicant/
endef

define Package/wpa-supplicant/Description
 WPA Supplicant
endef

define Package/wpa-supplicant/config
	source "$(SOURCE)/Config.in"
endef

define Package/wpa-cli
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=wpa-supplicant
  TITLE:=WPA Supplicant command line interface
endef

define Package/wpa-cli/Description
 WPA Supplicant control utility 
endef

CONFIG=$(firstword $(wildcard ./files/config.$(BOARD) ./files/config.$(ARCH) ./config))
TARGET_CFLAGS +=  $(if $(CONFIG_WPA_SUPPLICANT_NO_TIMESTAMP_CHECK),-DNO_TIMESTAMP_CHECK)
TARGET_CFLAGS += -I$(STAGING_DIR)/usr/include

define Build/Configure
	cp $(CONFIG) $(PKG_BUILD_DIR)/wpa_supplicant/.config
	$(if $(CONFIG_WPA_SUPPLICANT_OPENSSL),$(SED) 's|^CONFIG_TLS.*|CONFIG_TLS=openssl|g' $(PKG_BUILD_DIR)/wpa_supplicant/.config)
endef

define Build/Compile
	rm -rf $(PKG_INSTALL_DIR)
	mkdir -p $(PKG_INSTALL_DIR)
	CFLAGS="$(TARGET_CFLAGS)" \
	$(MAKE) -C $(PKG_BUILD_DIR)/wpa_supplicant \
		CC="$(TARGET_CC)" \
		KERNEL=$(LINUX_DIR) \
		CPPFLAGS="$(TARGET_CPPFLAGS)" \
		LDFLAGS="$(TARGET_LDFLAGS)" \
		all
endef

define Package/wpa-supplicant/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(CP) \
		$(PKG_BUILD_DIR)/wpa_supplicant/wpa_supplicant \
		$(1)/usr/sbin/
	$(INSTALL_DIR) $(1)/lib/wifi
	$(INSTALL_DATA) ./files/wpa_supplicant.sh $(1)/lib/wifi/wpa_supplicant.sh
endef

define Package/wpa-cli/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(CP) $(PKG_BUILD_DIR)/wpa_supplicant/wpa_cli $(1)/usr/sbin/
endef

$(eval $(call BuildPackage,wpa-supplicant))
$(eval $(call BuildPackage,wpa-cli))
