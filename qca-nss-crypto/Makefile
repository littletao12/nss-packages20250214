include $(TOPDIR)/rules.mk

PKG_NAME:=qca-nss-crypto
PKG_RELEASE:=3

PKG_SOURCE_URL:=https://git.codelinaro.org/clo/qsdk/oss/lklm/nss-crypto.git
PKG_SOURCE_PROTO:=git
PKG_SOURCE_DATE:=2022-12-15
PKG_SOURCE_VERSION:=3c5a574
PKG_MIRROR_HASH:=a195ba22016d91cd1711fe8f5167d65cfbd03feee5a9089929cb0d2180bf4047
QSDK_VERSION:=12.5
PKG_VERSION:=$(QSDK_VERSION).$(subst -,.,$(PKG_SOURCE_DATE))~$(PKG_SOURCE_VERSION)

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/kernel.mk
include $(INCLUDE_DIR)/package.mk

# v1.0 is for Akronite
# v2.0 is for Hawkeye/Cypress/Maple
ifneq (, $(findstring $(CONFIG_TARGET_SUBTARGET), "ipq807x" "ipq60xx" "ipq50xx"))
NSS_CRYPTO_DIR:=v2.0
else
NSS_CRYPTO_DIR:=v1.0
endif

define KernelPackage/qca-nss-crypto
	SECTION:=kernel
	CATEGORY:=Kernel modules
	SUBMENU:=Cryptographic API modules
	DEPENDS:=@TARGET_qualcommax +kmod-qca-nss-drv +nss-eip-firmware +@NSS_DRV_CRYPTO_ENABLE
	TITLE:=Kernel driver for NSS crypto driver
	FILES:=$(PKG_BUILD_DIR)/$(NSS_CRYPTO_DIR)/src/qca-nss-crypto.ko \
	$(PKG_BUILD_DIR)/$(NSS_CRYPTO_DIR)/tool/qca-nss-crypto-tool.ko
	AUTOLOAD:=$(call AutoProbe,qca-nss-crypto)
endef

define KernelPackage/qca-nss-crypto/Description
This package contains a NSS crypto driver for QCA chipset
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include/qca-nss-crypto
	$(CP) $(PKG_BUILD_DIR)/$(NSS_CRYPTO_DIR)/include/* $(1)/usr/include/qca-nss-crypto
endef

EXTRA_CFLAGS+= \
	-DCONFIG_NSS_DEBUG_LEVEL=4 \
	-I$(STAGING_DIR)/usr/include/qca-nss-crypto \
	-I$(STAGING_DIR)/usr/include/qca-nss-drv \
	-I$(PKG_BUILD_DIR)/$(NSS_CRYPTO_DIR)/include \
	-I$(PKG_BUILD_DIR)/$(NSS_CRYPTO_DIR)/src \
	-Wno-missing-declarations -Wno-missing-prototypes

ifeq ($(CONFIG_TARGET_BOARD), "qualcommax")
	SOC:=$(CONFIG_TARGET_SUBTARGET)
endif

define Build/Compile
	+$(MAKE) -C "$(LINUX_DIR)" \
		CC="$(TARGET_CC)" \
		CROSS_COMPILE="$(TARGET_CROSS)" \
		ARCH="$(LINUX_KARCH)" \
		M="$(PKG_BUILD_DIR)" \
		EXTRA_CFLAGS="$(EXTRA_CFLAGS)" \
		NSS_CRYPTO_DIR=$(NSS_CRYPTO_DIR) \
		SoC=$(SOC) \
		$(KERNEL_MAKE_FLAGS) \
		$(PKG_JOBS) \
		modules
endef

$(eval $(call KernelPackage,qca-nss-crypto))
