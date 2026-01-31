#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=wfb-ng
PKG_VERSION:=25.01.1
PKG_RELEASE:=1
PKG_LICENSE:=GPL-3.0
PKG_LICENSE_FILES:=LICENSE

# Имя файла в папке dl
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz

# Хеш коммита для версии 25.01.1 (взято из твоего git ls-remote)
PKG_SOURCE_VERSION:=1b88185409692ae49d2abf2142975e1390ac2392

# Ссылка (оставляем как запасной вариант, если файла нет в dl)
PKG_SOURCE_URL:=https://github.com/svpcom/wfb-ng/archive/refs/tags/v$(PKG_VERSION).tar.gz?

# Сюда вставь результат команды sha256sum dl/wfb-ng-25.01.1.tar.gz
# Если еще не считал, оставь skip, запусти сборку и скопируй то, что выдаст "Got:"
PKG_HASH:=skip

# ПРАВИЛЬНЫЙ ПУТЬ: в новых версиях папка называется просто wfb-ng-25.01.1
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)

PKG_BUILD_PARALLEL:=1

PKG_MAINTAINER:=Ruixi Zhou <zhouruixi@gmail.com>>

include $(INCLUDE_DIR)/package.mk

define Build/Compile
  $(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR)/$(MAKE_PATH) \
	  $(MAKE_FLAGS) \
	  VERSION=$(PKG_VERSION) COMMIT=$(PKG_SOURCE_VERSION) \
	  all_bin
endef

define Package/wfb-ng/default
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+libsodium +libstdcpp
  TITLE:=Next generation of long-range packet radio link based on raw WiFi radio
  URL:=https://github.com/svpcom/wfb-ng
  SUBMENU:=Wireless
endef

define Package/wfb-ng/description
  Next generation of long-range packet radio link based on raw WiFi radio
endef

define Package/wfb-rx
  $(call Package/$(PKG_NAME)/Default)
  DEPENDS:=+libpcap +libsodium +libstdcpp
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Wireless
  TITLE+= wfb_rx
  URL:=https://github.com/svpcom/wfb-ng
endef

define Package/wfb-gs/description
  $(Package/$(PKG_NAME)/description) RX
endef

define Package/wfb-tx
  $(call Package/$(PKG_NAME)/Default)
  DEPENDS:=+libsodium +libstdcpp
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Wireless
  TITLE+= wfb_tx
  URL:=https://github.com/svpcom/wfb-ng
endef

define Package/wfb-tx/description
  $(Package/$(PKG_NAME)/description) TX
endef

define Package/wfb-tx-cmd
  $(call Package/$(PKG_NAME)/Default)
  DEPENDS:=+wfb-tx
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Wireless
  TITLE+= wfb_tx_cmd
  URL:=https://github.com/svpcom/wfb-ng
endef

define Package/wfb-tx-cmd/description
  $(Package/$(PKG_NAME)/description) tx-cmd
endef

define Package/wfb-keygen
  $(call Package/$(PKG_NAME)/Default)
  DEPENDS:=+libsodium
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Wireless
  TITLE+= wfb_keygen
  URL:=https://github.com/svpcom/wfb-ng
endef

define Package/wfb-keygen/description
  $(Package/$(PKG_NAME)/description) KEYGEN
endef

define Package/wfb-key
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Wireless
  TITLE+= wfb key used by FPVue
  URL:=https://github.com/svpcom/wfb-ng
endef

define Package/wfb-key/description
  $(Package/$(PKG_NAME)/description) wfb key used by FPVue
endef

define Package/wfb-gs
  $(call Package/$(PKG_NAME)/Default)
  DEPENDS:=+libpcap +libsodium +libstdcpp +wfb-rx +wfb-key
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Wireless
  TITLE+= groundstations include wfb_rx and scripts
  URL:=https://github.com/svpcom/wfb-ng
endef

define Package/wfb-gs/description
  $(Package/$(PKG_NAME)/description) groundstations include wfb_rx scripts
endef

define Package/wfb-rx/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/wfb_rx $(1)/usr/bin/
	$(LN) /usr/bin/wfb_rx $(1)/usr/bin/telemetry_rx
endef

define Package/wfb-tx/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/wfb_tx $(1)/usr/bin/
endef

define Package/wfb-tx-cmd/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/wfb_tx_cmd $(1)/usr/bin/
endef

define Package/wfb-keygen/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/wfb_keygen $(1)/usr/bin/
endef

define Package/wfb-key/install
	$(INSTALL_DIR) $(1)/etc/
	$(INSTALL_DATA) $(CURDIR)/files/FPVue.key $(1)/etc/
	$(LN) /etc/FPVue.key $(1)/etc/gs.key
endef

define Package/wfb-gs/install
	$(call Package/wfb-ng/install/wfb-rx,$1)
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_DATA) ./files/wfb-gs_usb0.default.uci $(1)/etc/uci-defaults/90_wfb-gs_usb0
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_DATA) ./files/wfb-gs_wifi.default.uci $(1)/etc/uci-defaults/91_wfb-gs_wifi
	$(INSTALL_DIR) $(1)/usr/bin
	$(LN) /usr/bin/wfb_tx $(1)/usr/bin/telemetry_tx
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) ./files/wfb-gs.init $(1)/etc/init.d/wfb-gs
	$(INSTALL_DIR) $(1)/etc/hotplug.d/net/
	$(INSTALL_DATA) ./files/usb0.hotplug $(1)/etc/hotplug.d/net/51-usb0
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) ./files/wfb-gs.config $(1)/etc/config/wfb-gs
endef

$(eval $(call BuildPackage,wfb-rx))
$(eval $(call BuildPackage,wfb-tx))
$(eval $(call BuildPackage,wfb-tx-cmd))
$(eval $(call BuildPackage,wfb-keygen))
$(eval $(call BuildPackage,wfb-key))
$(eval $(call BuildPackage,wfb-gs))
