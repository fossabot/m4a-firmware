APPLICATION = usb_cdc_ecm_border_router
BOARD ?= m4a-mb

RIOTBASE ?= $(CURDIR)/../../RIOT

# Path where external boards can be found
EXTERNAL_BOARD_DIRS ?= $(CURDIR)/../../boards

GNRC_NETIF_NUMOF := 3

USEMODULE += netdev_default
USEMODULE += auto_init_gnrc_netif
USEMODULE += gnrc_icmpv6_error
USEMODULE += gnrc_sixlowpan_border_router_default
USEMODULE += gnrc_udp

USEMODULE += gnrc_rpl
USEMODULE += auto_init_gnrc_rpl

USEMODULE += gnrc_icmpv6_echo

USEMODULE += shell
USEMODULE += shell_commands
# Optional, but might be useful
USEMODULE += ps
USEMODULE += netstats_l2
USEMODULE += netstats_ipv6
USEMODULE += netstats_rpl

USEMODULE += usbus_cdc_ecm
USEMODULE += auto_init_usbus

USB_VID ?= 1234 # Replace with your vendor ID
USB_PID ?= 2345 # Replace with your product ID

CFLAGS += -DUSB_CONFIG_VID=0x$(USB_VID) -DUSB_CONFIG_PID=0x$(USB_PID)

DEVELHELP ?= 1
# Disable warnings when a parameter is not used
CFLAGS += -Wno-unused-parameter
CFLAGS += -Wno-unused-variable
CFLAGS += -DCONFIG_GNRC_RPL_DEFAULT_NETIF=7
#
CFLAGS += -DCONFIG_GNRC_IPV6_NIB_NUMOF=25
CFLAGS += -DCONFIG_GNRC_IPV6_NIB_OFFL_NUMOF=25
#
CFLAGS += -DCONFIG_GNRC_IPV6_NIB_DEFAULT_ROUTER_NUMOF=2
CFLAGS += -DCONFIG_IEEE802154_DEFAULT_CHANNEL=22
CFLAGS += -DCONFIG_IEEE802154_DEFAULT_SUBGHZ_CHANNEL=0
include $(RIOTBASE)/Makefile.include
