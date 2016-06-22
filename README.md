## Centralized Firewall Control script

Centralized firewall control provides a centralized way to manage the firewall on multiple servers or loadbalancers running iptables. This way you can quickly allow/block/del/search abuse ranges etc. with one command on several servers.
It accesses those servers through ssh. 

Tested on Debian Jessie, but should work on any distro.

## Prerequisites

To use the 'precheck' and 'findip' functions, you need the 'netaddr' python module installed.

Debian: apt-get install python3-netaddr

Since this script connects to the given servers with ssh, such access must be present before it can be used.

## Settings

Copy the example config from cfc.cfg-example to cfc.cfg the first time.

The following settings can be set in the config file:

* action: sets the action when adding a rule, default: DROP
* checkaggrbin: path to the checkaggr.py script, default: ./checkaggr.py
* date: set the date format for the firewall comments, default: $(date +%d%m%Y) -> 22062016
* fwchain: name of the firewall chain to add/del/search, default: INPUT
* masklimit: max size of the ip ranges that can be added, default: /21
* precheck: check if the ip that is about to be added is already in the firewall or part of a larger added range, might be a bit slow on large firewalls (~25 sec. for searching 500 ip ranges per server), default: true
* protected: ip ranges that are excluded from the 'add' function, usually the ranges owned by the local network, default: "172.16.0.0/12 10.0.0.0/8 192.168.0.0/16"
* pythonbin: location of the used Python binary, default: /usr/bin/python3
* servers: servers that the firewalls run on that will be controlled through ssh, default: "lvs01.example.com lvs02.example.com lvs03.example.com lvs04.example.com"

## Usage

When entering IPs/ranges with the following commands, do so in CIDR notation, this gets validated and won't accept anything else.

add:

	cfc.sh add n.n.n.n/NN '<optional comment>'

Adds the given IP(range) to the firewalls with the configured action for all traffic from that source. Makes a comment by default with the current date, you can add an optional comment using single quotes to add a reason or owner of that range as an example. It can also be searched on that comment later on.

del:

	cfc.sh del n.n.n.n/NN

Deletes the given IP(range)/rule from the firewalls

find:

	cfc.sh find <string>

Searches the firewalls for the given string (case in-sensitive), this can be (part of) an IP / range / comment

findip:

	cfc.sh findip n.n.n.n/NN

Searches the firewalls if the given IP(range) is already part of an added rule, might be a bit slow on large firewalls (~25 sec. for searching 500 ip ranges per server)

last:

	cfc.sh last <nr_of_most_recent_rules>

Shows the last <n> entries added to the firewalls
