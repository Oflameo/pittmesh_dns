#Pittmesh DNS Server Setup

##Initial DNS Server Provisioning
To use the Pittmesh DNS provision playbooks the target system has to be
* Debian 8 or newer
* Has access to Debian repositories
* Host must be listed in ansible inventory file
* Target host key must be trusted
* Ansible 2.0 or later on provisioning host

An example ansible playbook command you would run to is the following.

    ansible-playbook -e "user=root hosts=pmd0" basepkg.yml

If there is a trust error whe using that command try logging into the target 
and trying again after disconnecting. If you don't know how to to write an 
ansible inventory file [click here](https://docs.ansible.com/ansible/intro_inventory.html).

To get all of the software installed needed to run a Pittmesh DNS server
can be provisioned by running playbooks `basepkg.yml` and `specpkg.yml`.

The DNS service isn't enabled or started by default. If you are using
systemd you can login and use the following to get the DNS service enabled
and started. The following commands must be ran with elevated permissions.

    systemctl enable bind9
    systemctl start bind9

After the DNS server is provisioned it will update itself every hour on the
55 minute by executing `pmd-update.yml` with ansible via cron by pulling
down the target git repository, writing `template/bind/db.pittmesh.j2` to 
`/etc/bind/db.pittmesh` and passing it through a jinja2 filter and reloading
the bind9 DNS service. The jinja template is used to generate the serial
number for the bind9 resource records for Pittmesh DNS so that the records
are updated whenever they are changed. If the serial number isn't changed
DNS resource record updates will fail. The serial number is the epoch of 
the last time `pmd-update.yml` was run after there was a change to 
`template/bind/db.pittmesh.j2` in the git repository.

##Getting Resource Records added
To get resource records for your services on Pittmesh you can go about it
in two ways. You could clone the repository, edit 
`template/bind/db.pittmesh.j2` and send a pull request. You can also open 
an incident on Github for your the for your resources.
You need to include at minimal your desired hostname and a valid Pittmesh IP
address to get your host added.

The standard domain extension for all Pittmesh services are `.pittmesh`.
Currently we don't have a Policy on subdomains.

##OpenWrt Router Setup
To setup a router to use a Pittmesh DNS server these conditions must
be met.

* Must be a DHCP server

To configure a OpenWrt router to use configure a dhcp client to use the
Pittmesh DNS servers insert a configuration line with

You must add a dhcp option to get DHCP clients on Pittmesh to use
the correct DNS server.

In OpenWrt you can add the DHCP option by inserting the following code 
into `/etc/config/dhcp` at the end of the `config dnsmasq` configuration
block similar to the following.

    list 'dhcp_option' '6,10.10.220.20,10.123.237.147'

* The first part `list` tells uci what format the next option is
* `dhcp_option` is the option fed to dnsmasq
* `6,10.10.220.20,10.123.237.147` is the dhcp option to tell the DHCP
clients to look for a DNS server and the IP addresses for the DNS servers.

To configure your OpenWrt router to use the Pittmesh DNS servers replace the 
`list server` options with the entries for the Pittmesh DNS servers.

    list server '10.10.220.20'
    list server '10.123.237.147'

##Pittmesh Client Node Setup
Clients simply need to get an IP address from the router and DNS will just work.

##Sources
https://docs.ansible.com/ansible/playbooks.html

https://docs.ansible.com/ansible/intro_inventory.html

http://jinja.pocoo.org/

https://wiki.debian.org/Bind9

http://www.bind9.net/arm99.pdf

https://wiki.openwrt.org/doc/howto/dhcp.dnsmasq
