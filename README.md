An openvswtich support Provider Backbone Bridge
===============================================

This is a version of Open vSwitch supported the provider backbone bridge (PBB). 

Provider Backbone Bridges (PBB; known as "mac-in-mac") is a set of architecture and protocols for routing over a provider's network allowing interconnection of multiple Provider Bridge Networks without losing each customer's individually defined VLANs. It was initially created by Nortel before being submitted to the IEEE 802.1 committee for standardization. The final standard was approved by the IEEE in June 2008 as IEEE 802.1ah-2008 and has been integrated into IEEE 802.1Q-2011.

OpenFlow 1.3 added actions not yet implemented by official Open vSwitch:

* push_pbb
* pop_pbb

OpenFlow 1.3 and OpenFlow 1.4 added OXMs for the following fields not yet implemented by official Open vSwitch:

* PBB I-SID.
* PBB UCA.

This is a branch added code for  supporting these actions and fileds based on offical Open vSwtich version 2.12.0.


Usage
-------------------------------

### push

**Push pbb_isid,eth_src,eth_dst**  

Outer src mac is set as 00:00:00:11:11:11, and dsc mac is set as 00:00:00:22:22:22, pbb isid is set as 23. 

```shell
root@OVS# ovs-ofctl add-flow br0 in_port=11,dl_src=22:11:11:11:11:11,dl_dst=22:22:22:22:22:22,actions=push_pbb:0x88e7,set_field:23-\>pbb_isid,set_field:00:00:00:11:11:11-\>eth_src,set_field:00:00:00:22:22:22-\>eth_dst,output:12 
```

**Push pbb without pbb_isid,eth_src,eth_dst**

Outer src mac is set as 22:11:11:11:11:11, and dsc mac is set as 22:22:22:22:22:22, pbb isid is set as 0. 

```shell
root@OVS# ovs-ofctl add-flow br0 in_port=11,dl_src=22:11:11:11:11:11,dl_dst=22:22:22:22:22:22,actions=push_pbb:0x88e7,output:12
```
 
### pop

Pop pbb packets.

```shell
root@OVS# ovs-ofctl add-flow br0 in_port=11,dl_type=0x88e7,dl_src=00:00:00:11:11:11,dl_dst=00:00:00:22:22:22,actions=pop_pbb,output:12
```

### Important Things to Know

* Push pbb should be done with push_vlan,
* When do push pbb, primary src mac, and dst mac will be used if no config of eth_src , eth_dst
* When do pop pbb, primary packets should not include vlan


Build and Install
-------------------------------

Get the code


```shell
root@OVS# git clone https://github.com/lucky2you/openvswitch-pbb.git
root@OVS# git checkout branch-pbb
```

Build the code

```shell
root@OVS# cd branch-pbb
root@OVS# ./boot.sh
root@OVS# mkdir build
root@OVS# cd build
root@OVS# ../configure --prefix=/usr --localstatedir=/var --sysconfdir=/etc --with-linux=/lib/modules/$(uname -r)/build
root@OVS# make
```

Install binary

```shell
root@OVS# make install
root@OVS# make modules_install
```
