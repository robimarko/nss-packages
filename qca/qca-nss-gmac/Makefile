include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=qca-nss-gmac
PKG_RELEASE:=1

PKG_SOURCE_URL:=https://source.codeaurora.org/quic/qsdk/oss/lklm/nss-gmac
PKG_SOURCE_PROTO:=git
PKG_SOURCE_DATE:=2021-04-22
PKG_SOURCE_VERSION:=171767947467662f2407d0cfff26dfb136c3fb4a
PKG_MIRROR_HASH:=76eaee7210abfe817518590bdfc54ad051b6bb7851a203fb74dc4887b42ad71f

include $(INCLUDE_DIR)/package.mk

define KernelPackage/qca-nss-gmac
  SECTION:=kernel
  CATEGORY:=Kernel modules
  SUBMENU:=Network Devices
  DEPENDS:=@TARGET_ipq806x
  TITLE:=Kernel driver for NSS gmac
  FILES:=$(PKG_BUILD_DIR)/ipq806x/qca-nss-gmac.ko
  AUTOLOAD:=$(call AutoLoad,31,qca-nss-gmac)
endef

define KernelPackage/qca-nss-gmac/Description
This package contains a NSS driver for QCA chipset
endef

define Build/InstallDev
	mkdir -p $(1)/usr/include/qca-nss-gmac
	$(CP) $(PKG_BUILD_DIR)/ipq806x/exports/* $(1)/usr/include/qca-nss-gmac/
endef

EXTRA_CFLAGS+= \
	-DCONFIG_NSS_DEBUG_LEVEL=4 \
	-I$(PKG_BUILD_DIR)/nss_hal/include \
	-I$(PKG_BUILD_DIR)/nss_hal/$(BOARD)

define Build/Compile
	$(MAKE) $(PKG_JOBS) -C "$(LINUX_DIR)" \
		$(KERNEL_MAKE_FLAGS) \
		$(PKG_MAKE_FLAGS) \
		M="$(PKG_BUILD_DIR)" \
		EXTRA_CFLAGS="$(EXTRA_CFLAGS)" \
		modules
endef

$(eval $(call KernelPackage,qca-nss-gmac))
