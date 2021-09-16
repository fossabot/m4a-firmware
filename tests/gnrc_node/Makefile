# name of your application
APPLICATION = gnrc_node

# If no BOARD is found in the environment, use this default:
BOARD ?= m4a-mb

# This has to be the absolute path to the RIOT base directory:
RIOTBASE ?= $(CURDIR)/../../RIOT

# Path where external boards can be found
EXTERNAL_BOARD_DIRS ?= $(CURDIR)/../../boards

# Default to using ethos for providing the uplink when not on native
UPLINK ?= ethos


# Check if the selected Uplink is valid
ifeq (,$(filter ethos slip cdc-ecm wifi ethernet,$(UPLINK)))
  $(error Supported uplinks are `ethos`, `slip`, `cdc-ecm`, `ethernet` and `wifi`)
endif

# Include packages that pull up and auto-init the link layer.
# NOTE: 6LoWPAN will be included if IEEE802.15.4 devices are present
USEMODULE += netdev_default
USEMODULE += auto_init_gnrc_netif
# Activate ICMPv6 error messages
USEMODULE += gnrc_icmpv6_error
# Specify the mandatory networking modules for IPv6 and UDP
USEMODULE += gnrc_ipv6_router_default
USEMODULE += gnrc_udp
# Add a routing protocol
USEMODULE += gnrc_rpl
USEMODULE += auto_init_gnrc_rpl
# This application dumps received packets to STDIO using the pktdump module
USEMODULE += gnrc_pktdump
# Additional networking modules that can be dropped if not needed
USEMODULE += gnrc_icmpv6_echo
USEMODULE += netutils
# Add also the shell, some shell commands
USEMODULE += shell
USEMODULE += shell_commands
USEMODULE += ps
USEMODULE += netstats_l2
USEMODULE += netstats_ipv6
USEMODULE += netstats_rpl

# Optionally include DNS support. This includes resolution of names at an
# upstream DNS server and the handling of RDNSS options in Router Advertisements
# to auto-configure that upstream DNS server.
# USEMODULE += sock_dns              # include DNS client
# USEMODULE += gnrc_ipv6_nib_dns     # include RDNSS option handling

# Comment this out to disable code in RIOT that does safety checking
# which is not needed in a production environment but helps in the
# development process:
DEVELHELP ?= 1

# When using a regular network uplink we should use DHCPv6
ifneq (,$(filter cdc-ecm wifi ethernet,$(UPLINK)))
  USE_DHCPV6 ?= 1
else
  USE_DHCPV6 ?= 0
endif

ifeq (1,$(USE_DHCPV6))
  # include DHCPv6 client for 6LoWPAN border router
  USEMODULE += gnrc_dhcpv6_client_6lbr
else
  # include UHCP client
  USEMODULE += gnrc_uhcpc
endif

# Ethos/native TAP interface and UHCP prefix can be configured from make command
TAP ?= tap0

ifeq (1,$(USE_DHCPV6))
  # with DHCPv6 the 64-bit delegated prefixes are generated from a shorter
  # configured prefix.
  IPV6_PREFIX ?= 2001:db8::/32
else
  # UHCP advertises the prefix as is.
  IPV6_PREFIX ?= 2001:db8::/64
endif
# MODULE DEPENDENT CONFIGURATION IMPORT
# =====================================
# use ethos (ethernet over serial) or SLIP (serial-line IP) for network
# communication and stdio over UART, but not on native, as native has a tap
# interface towards the host.
ifeq (slip,$(UPLINK))
  # SLIP baudrate and UART device can be configured from make command
  SLIP_BAUDRATE ?= 115200
  include $(CURDIR)/Makefile.slip.conf
else ifeq (ethos,$(UPLINK))
  # ethos baudrate can be configured from make command
  ETHOS_BAUDRATE ?= 115200
  include $(CURDIR)/Makefile.ethos.conf
else ifeq (cdc-ecm,$(UPLINK))
  include $(CURDIR)/Makefile.cdc-ecm.conf
endif


.PHONY: host-tools

host-tools:
	$(Q)env -u CC -u CFLAGS $(MAKE) -C $(RIOTTOOLS)

# define native specific targets to only run UHCP daemon when required
ifneq (,$(filter native,$(BOARD)))
ifneq (1,$(USE_DHCPV6))
.PHONY: uhcpd-daemon

uhcpd-daemon: host-tools
	$(RIOTTOOLS)/uhcpd/bin/uhcpd $(TAP) $(IPV6_PREFIX) &
endif
endif

ifeq (slip,$(UPLINK))
sliptty:
	$(Q)env -u CC -u CFLAGS $(MAKE) -C $(RIOTTOOLS)/sliptty
endif


# Uncomment this to join RPL DODAGs even if DIOs do not contain
# DODAG Configuration Options (see the doc for more info)
# CFLAGS += -DCONFIG_GNRC_RPL_DODAG_CONF_OPTIONAL_ON_JOIN

# Change this to 0 show compiler invocation lines by default:
QUIET ?= 1

# Set a custom channel if needed
# trying to use CFLAGS instead a mk file as before
include $(RIOTBASE)/Makefile.include
CFLAGS += -DCONFIG_IEEE802154_DEFAULT_CHANNEL=22
CFLAGS += -DCONFIG_IEEE802154_DEFAULT_SUBGHZ_CHANNEL=0
# include $(RIOTMAKE)/default-radio-settings.inc.mk
