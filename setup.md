#Pittmesh DNS Server Setup

##OpenWRT router setup
To setup a router to use a Pittmesh DNS server these conditions must
be met.

* Must be a DHCP server.

To configure a Openwrt router to use configre a dhcp client to use the
Pittmesh DNS servers insert a configuration line with

You must add a dhcp option to get DHCP clients on Pittmesh to use
the correct DNS server.

In openwrt you can add the DHCP option by inserting the following code 
into `/etc/config/dhcp` at the end of the `config dnsmasq` configuration
block similar to the following.

    list 'dhcp_option' '6,10.10.220.20,10.123.237.147'

* The first part `list` tells uci what format the next option is
* `dhcp_option` is the option fed to dnsmasq
* `6,10.10.220.20,10.123.237.147` is the dhcp option to tell the DHCP
clients to look for a DNS server and the IP addresses for the DNS servers.
