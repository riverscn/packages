include $(TOPDIR)/rules.mk

PKG_NAME:=syncthing
PKG_VERSION:=1.18.2
PKG_RELEASE:=$(AUTORELEASE)

PKG_SOURCE:=syncthing-source-v$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/syncthing/syncthing/releases/download/v$(PKG_VERSION)
PKG_HASH:=afe2ae979da3b4f1af8aeabd7e1704807242913b516e0b6585510821c9d6d4f2

PKG_BUILD_DIR=$(BUILD_DIR)/$(PKG_NAME)-$(PKG_VERSION)/$(PKG_NAME)

PKG_MAINTAINER:=Paul Spooren <mail@aparcar.org>
PKG_LICENSE:=MPL-2.0
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:syncthing:syncthing

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

PKG_CONFIG_DEPENDS:= \
	CONFIG_SYNCTHING_COMPRESS_GOPROXY \
	CONFIG_SYNCTHING_COMPRESS_UPX

GO_PKG:=github.com/syncthing/syncthing/
GO_PKG_BUILD_PKG:=github.com/syncthing/syncthing/cmd/syncthing/
GO_PKG_INSTALL_EXTRA:=^gui/

GO_PKG_LDFLAGS_X:=\
	github.com/syncthing/syncthing/lib/build.Version=v$(PKG_VERSION) \
	github.com/syncthing/syncthing/lib/build.Stamp=$(SOURCE_DATE_EPOCH) \
	github.com/syncthing/syncthing/lib/build.User=openwrt \
	github.com/syncthing/syncthing/lib/build.Host=openwrt \
	github.com/syncthing/syncthing/lib/build.Program=syncthing
GO_PKG_TAGS:=noupgrade

include $(INCLUDE_DIR)/package.mk
include ../../lang/golang/golang-package.mk

define Package/syncthing
  TITLE:=Continuous file synchronization program
  URL:=https://syncthing.net
  DEPENDS:=$(GO_ARCH_DEPENDS)
  SECTION:=utils
  CATEGORY:=Utilities
  USERID:=syncthing=499:syncthing=499
endef

define Package/syncthing/conffiles
/etc/config/syncthing
/etc/syncthing
endef

define Package/syncthing/description
		Syncthing replaces proprietary sync and cloud services with something
		open, trustworthy and decentralized. Your data is your data alone and
		you deserve to choose where it is stored, if it is shared with some
		third party and how it's transmitted over the Internet.
endef

define Package/syncthing/config
config SYNCTHING_COMPRESS_GOPROXY
	bool "Compiling with GOPROXY proxy"
	default n

config SYNCTHING_COMPRESS_UPX
	bool "Compress executable files with UPX"
	depends on !mips64
	default n
endef

ifeq ($(CONFIG_SYNCTHING_COMPRESS_GOPROXY),y)
  export GO111MODULE=on
  export GOPROXY=https://goproxy.bj.bcebos.com
endif

define Build/Compile
	$(call GoPackage/Build/Compile)
ifeq ($(CONFIG_SYNCTHING_COMPRESS_UPX),y)
	$(STAGING_DIR_HOST)/bin/upx --lzma --best $(GO_PKG_BUILD_BIN_DIR)/syncthing
endif
endef

define Package/syncthing/install
	$(call GoPackage/Package/Install/Bin,$(1))

	$(CP) ./files/* $(1)/
endef

$(eval $(call GoBinPackage,syncthing))
$(eval $(call BuildPackage,syncthing))
