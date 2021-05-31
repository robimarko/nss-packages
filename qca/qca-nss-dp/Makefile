include $(TOPDIR)/rules.mk

PKG_NAME:=qca-nss-dp
PKG_RELEASE:=$(AUTORELEASE)

PKG_SOURCE_URL:=https://source.codeaurora.org/quic/cc-qrdk/oss/lklm/nss-dp
PKG_SOURCE_PROTO:=git
PKG_SOURCE_DATE:=2020-06-19
PKG_SOURCE_VERSION:=245f2bdbf67b1fee3c83bb6ef26bef6c772315f4
PKG_MIRROR_HASH:=e0054fe1bbeb22a562a6e3d36e1cbafdc244559fde391722796ad40e269b451b

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/kernel.mk
include $(INCLUDE_DIR)/package.mk

define KernelPackage/qca-nss-dp
  SECTION:=kernel
  CATEGORY:=Kernel modules
  SUBMENU:=Network Devices
  DEPENDS:=@(TARGET_ipq807x||TARGET_ipq60xx) +kmod-qca-ssdk-nohnat
  TITLE:=Kernel driver for NSS data plane
  FILES:=$(PKG_BUILD_DIR)/qca-nss-dp.ko
  AUTOLOAD:=$(call AutoLoad,31,qca-nss-dp)
endef

define KernelPackage/qca-nss-dp/Description
This package contains a NSS data plane driver for QCA chipset
endef

define Build/InstallDev
	mkdir -p $(1)/usr/include/qca-nss-dp
	$(CP) $(PKG_BUILD_DIR)/exports/* $(1)/usr/include/qca-nss-dp/
endef

EXTRA_CFLAGS+= \
	-I$(STAGING_DIR)/usr/include/qca-ssdk

NSS_DP_HAL_DIR:=$(PKG_BUILD_DIR)/hal
define Build/Configure
	$(LN) $(NSS_DP_HAL_DIR)/arch/$(CONFIG_TARGET_BOARD)/nss_$(CONFIG_TARGET_BOARD).h \
		$(PKG_BUILD_DIR)/exports/nss_dp_arch.h
endef

ifeq ($(CONFIG_TARGET_BOARD), "ipq807x")
    SOC="ipq807x_64"
else ifeq ($(CONFIG_TARGET_BOARD), "ipq60xx")
    SOC="ipq60xx_64"
endif

define Build/Compile
	+$(MAKE) -C "$(LINUX_DIR)" \
		CROSS_COMPILE="$(TARGET_CROSS)" \
		ARCH="$(LINUX_KARCH)" \
		M="$(PKG_BUILD_DIR)" \
		EXTRA_CFLAGS="$(EXTRA_CFLAGS)" SoC="$(SOC)" \
		$(KERNEL_MAKE_FLAGS) \
		$(PKG_JOBS) \
		modules
endef

$(eval $(call KernelPackage,qca-nss-dp))
