Instructions for LAB example

Prepared by: Gregor Semrl, gsemrl@nil.com
Date: 21.11.2019

This is a simple lab LSA example.

Install all packages to the corresponding nodes:

1) rfs node (resource facing node)

- ospf-rfs

2) cfs node (customer facing node)

-   ospf-ned: based on ospf-rfs YANG service model, but with modification: you have to replace leafrefs with strings
-   lsa-dispatch-map: YANG model with data about rfs nodes and managed devices on a particular node

    Example: 

        admin@ncs# show running-config lsa-dispatch-map 
        lsa-dispatch-map lsa-rfs-node
        managed-device CE11
        !
        managed-device CE12
        !
        
-   osfp: CFS services that uses lsa-dispatch-map and ospf-ned to deliver configuration data to CFS node


In addition to steps above you also need to:

- enable LSA in ncs.conf (enable large scale) 
- enable northbound netconf ssh communication
- define appropriate authgroup (in this case, LSA)
- register RFS device in CFS node:

    devices device lsa-rfs-node
    address   10.100.1.20
    port      2022
    ssh host-key ssh-rsa
    key-data "......"
    !
    authgroup LSA
    device-type netconf ned-id lsa-netconf
    state admin-state unlocked

- sync device after fetching ssh keys (sync-from)

Additional info - compiling a CFS NED:

ncs-make-package --no-netsim --netconf-ned (location of YANG service model of RFS) --build cfs-ned-interface

