include $(TOPDIR)/rules.mk

PKG_NAME:=sf_smac
PKG_RELEASE=1

include $(INCLUDE_DIR)/kernel.mk
include $(INCLUDE_DIR)/package.mk

define KernelPackage/sf_smac
  SUBMENU:=Wireless Drivers
  TITLE:=SIFLOWER sf16a18 wireless driver
  DEPENDS:=+kmod-mac80211 +@DRIVER_11N_SUPPORT +@DRIVER_11AC_SUPPORT
  FILES:=\
	$(PKG_BUILD_DIR)/sf16a18_rf.ko \
	$(PKG_BUILD_DIR)/sf16a18_smac.ko \
	$(PKG_BUILD_DIR)/startcore.ko
ifdef CONFIG_PACKAGE_SFUMAC_FMAC
ifndef CONFIG_FLASH_OPTIMIZE
	FILES += $(PKG_BUILD_DIR)/sf16a18_fmac.ko
endif
endif
endef

define KernelPackage/sf_smac/config
	if PACKAGE_kmod-sf_smac
		config PACKAGE_SFSMAC_DBGINFO_ALLOCS
			bool "enable dbginfo alloc memory"
			default "y"

		config PACKAGE_SFSMAC_LB_LA_ENABLE
			bool "enable LA function for low band"
			default "n"

		config PACKAGE_SFSMAC_HB_LA_ENABLE
			bool "enable LA function for high band"
			depends on !PACKAGE_SFSMAC_LB_LA_ENABLE
			default "n"

		config PACKAGE_SFUMAC_WIFI_TEST_SCRIPTS
			bool "enable test shell scripts"
			default "y"

		config PACKAGE_SFUMAC_WIFI_ATE_TOOLS
			bool "enable wifi ate tool"
			default "y"

		config PACKAGE_SFUMAC_LB_EXT_PA_SUPPORT
			bool "enable support lb ext pa detect"
			default "y"

		config PACKAGE_SFUMAC_HB_EXT_PA_SUPPORT
			bool "enable support hb ext pa detect"
			default "y"

		menu "softmac/fullmac Configuration"
			choice
				prompt "decide if use fullmac when bootup"
				default PACKAGE_SFUMAC_SMAC

			config PACKAGE_SFUMAC_SMAC
				bool "use softmac"

			config PACKAGE_SFUMAC_FMAC
				bool "use fullmac"
			endchoice
		endmenu
	endif
endef

EXTRA_KCONFIG = \
	CONFIG_SF16A18_WIFI_MAC_HOST_OFFLOAD=m	\
	CONFIG_SF16A18_WIFI_SOFT_MAC=m	\
	CONFIG_SF16A18_WIFI_RF=m

ifndef CONFIG_FLASH_OPTIMIZE
	EXTRA_KCONFIG += CONFIG_SF16A18_WIFI_FULL_MAC=m
endif

ifdef CONFIG_TARGET_siflower_sf16a18_mpw0
SF_ARCH=mpw0
endif
ifdef CONFIG_TARGET_siflower_sf16a18_mpw1
SF_ARCH=mpw1
#for mpw1,rf is not avaiable,so we use fpga macro to setup mac only
EXTRA_KCONFIG += CONFIG_SF16A18_WIFI_MPW1_FPGA=y
endif
ifdef CONFIG_TARGET_siflower_sf19a28_mpw1
SF_ARCH=a28mpw0
endif

SF_UMAC_TYPE?=smac
ifdef CONFIG_PACKAGE_SFUMAC_SMAC
SF_UMAC_TYPE=smac
endif
ifdef CONFIG_PACKAGE_SFUMAC_FMAC
SF_UMAC_TYPE=fmac
endif

ifdef CONFIG_TARGET_siflower_sf16a18_fullmask
SF_ARCH=fullmask
endif

define KernelPackage/sf_smac/description
 Kernel module to sf16a18 smac && rf.
endef

NOSTDINC_FLAGS = \
	-I$(PKG_BUILD_DIR) \
	-I$(STAGING_DIR)/usr/include/mac80211-backport/uapi \
	-I$(STAGING_DIR)/usr/include/mac80211-backport \
	-I$(STAGING_DIR)/usr/include/mac80211/uapi \
	-I$(STAGING_DIR)/usr/include/mac80211 \
	-include backport/autoconf.h \
	-include backport/backport.h

ifdef CONFIG_PACKAGE_MAC80211_MESH
  NOSTDINC_FLAGS += -DCONFIG_MAC80211_MESH
endif

ifdef CONFIG_PACKAGE_MAC80211_DEBUGFS
  NOSTDINC_FLAGS += -DCONFIG_MAC80211_DEBUGFS
endif

ifndef CONFIG_PACKAGE_SFSMAC_DBGINFO_ALLOCS
EXTRA_KCONFIG += CONFIG_DBG_DUMP=n
endif

ifdef CONFIG_PACKAGE_SFSMAC_LB_LA_ENABLE
NOSTDINC_FLAGS += -DCONFIG_SF16A18_WIFI_LA_ENABLE
endif

ifdef CONFIG_PACKAGE_SFSMAC_HB_LA_ENABLE
NOSTDINC_FLAGS += -DCONFIG_SF16A18_WIFI_LA_ENABLE
endif

ifdef CONFIG_PACKAGE_SFUMAC_LB_EXT_PA_SUPPORT
NOSTDINC_FLAGS += -DCONFIG_SF16A18_WIFI_LB_EXT_PA_ENABLE
endif

ifdef CONFIG_PACKAGE_SFUMAC_HB_EXT_PA_SUPPORT
NOSTDINC_FLAGS += -DCONFIG_SF16A18_WIFI_HB_EXT_PA_ENABLE
endif

ifdef CONFIG_PACKAGE_SFUMAC_WIFI_ATE_TOOLS
EXTRA_KCONFIG += CONFIG_SF16A18_WIFI_ATE_TOOLS=y
NOSTDINC_FLAGS += -DCONFIG_SF16A18_WIFI_ATE_TOOLS
endif

NOSTDINC_FLAGS += -DCONFIG_SF16A18_WIFI_RF

ifeq ($(SF_ARCH), fullmask)
#use m_SFDSP as default
CONFIG_SF16A18_LMAC_USE_M_SFDSP=y
NOSTDINC_FLAGS += -DCONFIG_SF16A18_LMAC_USE_M_SFDSP
else
ifeq ($(SF_ARCH), a28mpw0)
CONFIG_SF16A18_LMAC_USE_M_SFDSP=y
NOSTDINC_FLAGS += -DCONFIG_SF16A18_LMAC_USE_M_SFDSP
NOSTDINC_FLAGS += -DCONFIG_MULTI_NSS
NOSTDINC_FLAGS += -DCONFIG_NSS_2
NOSTDINC_FLAGS += -DCONFIG_SFA28_MPW0
EXTRA_KCONFIG += CONFIG_SFA28_MPW0=y
else
#if use interaptiv,you must set CONFIG_NR_CPUS to 2
CONFIG_SF16A18_LMAC_USE_INTERPTIV=y
NOSTDINC_FLAGS += -DCONFIG_SF16A18_LMAC_USE_INTERPTIV
endif
endif

#if driver is final release
#EXTRA_KCONFIG += CONFIG_SF16A18_RELEASE=y
ifdef CONFIG_FLASH_OPTIMIZE
EXTRA_KCONFIG += CONFIG_RWNX_TINY_DRIVER=y
EXTRA_KCONFIG += CONFIG_SF16A18_STARTCORE_USE_PIC=y
endif
ifdef CONFIG_MEMORY_OPTIMIZE
#use 32 tx queue count to balance the memory and the speed
#EXTRA_KCONFIG += CONFIG_SF16A18_TXQ_MAX_CNT=32
EXTRA_KCONFIG += CONFIG_SF16A18_WIFI_LITE_MEMORY=y
endif
ifdef CONFIG_POWER_OPTIMIZE
#that must be sync with lmac binaries if defined,here defined 8 to save power
EXTRA_KCONFIG += CONFIG_SF16A18_COOLING_TEMP=y
endif
ifeq ($(CONFIG_NEW_LEDS),y)
EXTRA_KCONFIG += CONFIG_SF16A18_WIFI_LED=y
endif

ifdef CONFIG_SFAX8_FACTORY_READ
NOSTDINC_FLAGS += -DCONFIG_SFAX8_FACTORY_READ
endif

define Build/Prepare
	mkdir -p $(PKG_BUILD_DIR)
	$(CP) ./src/* $(PKG_BUILD_DIR)/
	$(CP) -r ./config $(PKG_BUILD_DIR)/
	$(CP) -r ./test $(PKG_BUILD_DIR)/
endef

define Build/Compile
endef

define KernelPackage/sf_smac/install
	$(INSTALL_DIR) $(1)/lib/firmware
	cp \
		$(PKG_BUILD_DIR)/config/rwnx_aetnensis.ini \
		$(PKG_BUILD_DIR)/config/rwnx_settings.ini \
		$(PKG_BUILD_DIR)/config/tx_adjust_gain_table.bin \
		$(PKG_BUILD_DIR)/config/$(SF_ARCH)/sf_rf_expa_config.ini \
		$(PKG_BUILD_DIR)/config/$(SF_ARCH)/agcram.bin \
		$(PKG_BUILD_DIR)/config/$(SF_ARCH)/ldpcram.bin \
		$(PKG_BUILD_DIR)/config/$(SF_ARCH)/rf_pmem.bin \
		$(PKG_BUILD_DIR)/config/$(SF_ARCH)/rf_default_reg.bin \
		$(1)/lib/firmware

ifdef CONFIG_TARGET_siflower_sf19a28_mpw1
	cp $(PKG_BUILD_DIR)/config/$(SF_ARCH)/rf_trx_path.ini $(1)/lib/firmware
endif

ifneq ($(SF_ARCH), a28mpw0)
	cp $(PKG_BUILD_DIR)/config/$(SF_ARCH)/rf_xdma_reg.bin $(1)/lib/firmware
	cp $(PKG_BUILD_DIR)/config/sfwifi.sh $(1)/lib/sfwifi.sh
else
	cp $(PKG_BUILD_DIR)/config/sfwifi-a28.sh $(1)/lib/sfwifi.sh
endif
	$(INSTALL_DIR) $(1)/bin
	cp $(PKG_BUILD_DIR)/config/sfwifi $(1)/bin/
	cp $(PKG_BUILD_DIR)/config/reload_wifi.sh $(1)/bin/
ifeq ($(CONFIG_SF16A18_LMAC_USE_M_SFDSP), y)
ifdef CONFIG_FLASH_OPTIMIZE
	cp $(PKG_BUILD_DIR)/config/$(SF_ARCH)/flash-opt/sf1688_smac.bin $(1)/lib/firmware/
else
	cp $(PKG_BUILD_DIR)/config/$(SF_ARCH)/sf1688_lb_smac.bin $(1)/lib/firmware/
	cp $(PKG_BUILD_DIR)/config/$(SF_ARCH)/sf1688_hb_smac.bin $(1)/lib/firmware/
ifdef CONFIG_PACKAGE_SFUMAC_FMAC
	cp $(PKG_BUILD_DIR)/config/$(SF_ARCH)/sf1688_lb_fmac.bin $(1)/lib/firmware/
	cp $(PKG_BUILD_DIR)/config/$(SF_ARCH)/sf1688_hb_fmac.bin $(1)/lib/firmware/
endif
endif
else
	cp $(PKG_BUILD_DIR)/config/$(SF_ARCH)/idle_task.bin $(1)/lib/firmware/
ifdef CONFIG_PACKAGE_SFUMAC_FMAC
ifndef CONFIG_FLASH_OPTIMIZE
	cp $(PKG_BUILD_DIR)/config/$(SF_ARCH)/sf1688_fmac.bin $(1)/lib/firmware/
endif
endif
	cp $(PKG_BUILD_DIR)/config/$(SF_ARCH)/sf1688_smac.bin $(1)/lib/firmware/
endif

	$(INSTALL_DIR) $(1)/lib/preinit
	cp $(PKG_BUILD_DIR)/config/99_rf_misc $(1)/lib/preinit/

ifdef CONFIG_PACKAGE_SFUMAC_FMAC
	echo 'boot_hook_add preinit_main insmod_cfg80211' >> $(1)/lib/preinit/99_rf_misc
endif
ifdef CONFIG_PACKAGE_SFUMAC_SMAC
	echo 'boot_hook_add preinit_main insmod_mac80211' >> $(1)/lib/preinit/99_rf_misc
endif
ifndef CONFIG_PACKAGE_SFSMAC_HB_LA_ENABLE
	echo 'boot_hook_add preinit_main insmod_$(SF_UMAC_TYPE)_lb' >> $(1)/lib/preinit/99_rf_misc
endif
ifndef CONFIG_PACKAGE_SFSMAC_LB_LA_ENABLE
	echo 'boot_hook_add preinit_main insmod_$(SF_UMAC_TYPE)_hb' >> $(1)/lib/preinit/99_rf_misc
endif
ifndef CONFIG_FLASH_OPTIMIZE
	$(INSTALL_DIR) $(1)/sbin
	cp $(PKG_BUILD_DIR)/config/rf_recali.sh $(1)/sbin/
	cp $(PKG_BUILD_DIR)/config/$(SF_ARCH)/rf_check/* $(1)/sbin/
	$(INSTALL_DIR) $(1)/bin
endif

ifdef CONFIG_PACKAGE_SFUMAC_WIFI_TEST_SCRIPTS
	cp -r $(PKG_BUILD_DIR)/test/. $(1)/bin/
	cp -r $(PKG_BUILD_DIR)/test/rf_test/. $(1)/bin/
endif
endef

$(eval $(call KernelPackage,sf_smac))
