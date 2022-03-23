include $(TOPDIR)/rules.mk

PKG_NAME:=qca-nss-cfi
PKG_RELEASE:=$(AUTORELEASE)

PKG_SOURCE_URL:=https://source.codeaurora.org/quic/qsdk/oss/lklm/nss-cfi
PKG_SOURCE_PROTO:=git
PKG_SOURCE_DATE:=2021-03-20
PKG_SOURCE_VERSION:=73f2e5f5bf93cc244036ce5641faf9f859692cdf
PKG_MIRROR_HASH:=9eb9e1c62ec3979ac9bebfd48ad8258cd29431dcf9514a3da86c605b56f2c445

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/kernel.mk
include $(INCLUDE_DIR)/package.mk

ifneq (, $(findstring $(CONFIG_TARGET_BOARD), "ipq807x" "ipq60xx"))
#4.4/5.4 + ipq807x/ipq60xx/ipq50xx
  CFI_OCF_DIR:=ocf/v2.0
  CFI_CRYPTOAPI_DIR:=cryptoapi/v2.0
else
#4.4 Kernel + ipq806x
  CFI_CRYPTOAPI_DIR:=cryptoapi/v1.1
  CFI_OCF_DIR:=ocf/v1.0
  CFI_IPSEC_DIR:=ipsec/v1.0
endif

define KernelPackage/qca-nss-cfi-cryptoapi
  SECTION:=kernel
  CATEGORY:=Kernel modules
  SUBMENU:=Cryptographic API modules
  DEPENDS:= @(TARGET_ipq806x||TARGET_ipq807x||TARGET_ipq60xx) +kmod-qca-nss-crypto +kmod-crypto-authenc
  TITLE:=Kernel driver for NSS cfi
  FILES:=$(PKG_BUILD_DIR)/$(CFI_CRYPTOAPI_DIR)/qca-nss-cfi-cryptoapi.ko
  AUTOLOAD:=$(call AutoLoad,59,qca-nss-cfi-cryptoapi)
endef

define Build/InstallDev/qca-nss-cfi
	$(INSTALL_DIR) $(1)/usr/include/qca-nss-cfi
	$(CP) $(PKG_BUILD_DIR)/$(CFI_CRYPTOAPI_DIR)/../exports/* $(1)/usr/include/qca-nss-cfi
	$(CP) $(PKG_BUILD_DIR)/include/* $(1)/usr/include/qca-nss-cfi
endef

define Build/InstallDev
	$(call Build/InstallDev/qca-nss-cfi,$(1))
endef

define KernelPackage/qca-nss-cfi/Description
This package contains a NSS cfi driver for QCA chipset
endef

EXTRA_CFLAGS+= \
	-DCONFIG_NSS_DEBUG_LEVEL=4 \
	-I$(LINUX_DIR)/crypto/ocf \
	-I$(STAGING_DIR)/usr/include/qca-nss-crypto \
	-I$(STAGING_DIR)/usr/include/crypto \
	-I$(STAGING_DIR)/usr/include/qca-nss-drv

ifneq (, $(findstring $(CONFIG_TARGET_BOARD), "ipq807x" "ipq60xx"))
EXTRA_CFLAGS+= -I$(STAGING_DIR)/usr/include/qca-nss-clients
endif

# Build individual packages if selected
ifneq ($(CONFIG_PACKAGE_kmod-qca-nss-cfi-cryptoapi),)
MAKE_OPTS+=cryptoapi=y
endif

define Build/Compile
	+$(MAKE) -C "$(LINUX_DIR)" $(strip $(MAKE_OPTS)) \
		CROSS_COMPILE="$(TARGET_CROSS)" \
		ARCH="$(LINUX_KARCH)" \
		M="$(PKG_BUILD_DIR)" \
		EXTRA_CFLAGS="$(EXTRA_CFLAGS)" \
		CC="$(TARGET_CC)" \
		CFI_CRYPTOAPI_DIR=$(CFI_CRYPTOAPI_DIR) \
		CFI_OCF_DIR=$(CFI_OCF_DIR) \
		CFI_IPSEC_DIR=$(CFI_IPSEC_DIR) \
		SoC=$(CONFIG_TARGET_BOARD) \
		$(KERNEL_MAKE_FLAGS) \
		$(PKG_JOBS) \
		modules
endef

$(eval $(call KernelPackage,qca-nss-cfi-cryptoapi))
