#
# Copyright (C) 2006-2025 OpenWrt.org
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

PKG_SOURCE_VERSION:=1b88185409692ae49d2abf2142975e1390ac2392
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/svpcom/wfb-ng/archive/refs/tags/v$(PKG_VERSION).tar.gz?
PKG_HASH:=70ee72f3c9c2eb71a8a014705ca6e46cdc911dc04a65a95adc2ce914312d26a3

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)
PKG_BUILD_PARALLEL:=1
PKG_MAINTAINER:=Ruixi Zhou <zhouruixi@gmail.com>

include $(INCLUDE_DIR)/package.mk

# Добавляем флаги, чтобы компилятор видел заголовочные файлы библиотек
TARGET_CFLAGS += -I$(STAGING_DIR)/usr/include
TARGET_LDFLAGS += -L$(STAGING_DIR)/usr/lib

define Build/Compile
	$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR)/$(MAKE_PATH) \
		$(MAKE_FLAGS) \
		CC="$(TARGET_CC)" \
		CXX="$(TARGET_CXX)" \
		CFLAGS="$(TARGET_CFLAGS)" \
		LDFLAGS="$(TARGET_LDFLAGS)" \
		VERSION=$(PKG_VERSION) COMMIT=$(PKG_SOURCE_VERSION) \
		all_bin
endef

define Package/wfb-ng/default
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+libsodium +libstdcpp
  TITLE:=Next generation of long-range packet radio link
  URL:=https://github.com/svpcom/wfb-ng
  SUBMENU:=Wireless
endef

define Package/wfb-rx
  $(call Package/wfb-ng/default)
  DEPENDS:=+libpcap +libsodium +libstdcpp
  TITLE+= wfb_rx
endef

define Package/wfb-tx
  $(call Package/wfb-ng/default)
  TITLE+= wfb_tx
endef

define Package/wfb-tx-cmd
  $(call Package/wfb-ng/default)
  DEPENDS:=+wfb-tx
  TITLE+= wfb_tx_cmd
endef

# ИСПРАВЛЕНО: Добавлена зависимость +libevent2
define Package/wfb-tun
  $(call Package/wfb-ng/default)
  DEPENDS:=+libsodium +libstdcpp +kmod-tun +libevent2
  TITLE+= wfb_tun
endef

define Package/wfb-keygen
  $(call Package/wfb-ng/default)
  TITLE+= wfb_keygen
endef

define Package/wfb-key
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Wireless
  TITLE+= wfb key used by FPVue
endef

# Основной пакет для наземки
define Package/wfb-gs
  $(call Package/wfb-ng/default)
  DEPENDS:=+libpcap +libsodium +libstdcpp +wfb-rx +wfb-key +wfb-tun +wfb-tx-cmd
  TITLE+= groundstations (scripts and configs)
endef

# --- Блок CLI закомментирован для экономии места (требует Python) ---
# define Package/wfb-cli
#   $(call Package/wfb-ng/default)
#   DEPENDS:=+python3-light +python3-msgpack
#   TITLE+= wfb_cli (Status Dashboard)
# endef

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

define Package/wfb-tun/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/wfb_tun $(1)/usr/bin/
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
	$(INSTALL_DIR) $(1)/etc/uci-defaults
	$(INSTALL_DATA) ./files/wfb-gs_usb0.default.uci $(1)/etc/uci-defaults/90_wfb-gs_usb0
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

# --- Команда установки CLI также закомментирована ---
# define Package/wfb-cli/install
# 	$(INSTALL_DIR) $(1)/usr/bin
# 	$(INSTALL_BIN) $(PKG_BUILD_DIR)/wfb-cli $(1)/usr/bin/
# endef

$(eval $(call BuildPackage,wfb-rx))
$(eval $(call BuildPackage,wfb-tx))
$(eval $(call BuildPackage,wfb-tx-cmd))
$(eval $(call BuildPackage,wfb-tun))
$(eval $(call BuildPackage,wfb-keygen))
$(eval $(call BuildPackage,wfb-key))
$(eval $(call BuildPackage,wfb-gs))
# $(eval $(call BuildPackage,wfb-cli))
