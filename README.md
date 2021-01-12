# veth-example

## Environment
* `lsb_release -a`
```
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 18.04.5 LTS
Release:    18.04
Codename:   bionic
```

## Example
* `ip netns add test`
* `ip netns`
```
test
```
* `ip link add veth0 type veth peer name veth1 netns test`: create a pair and then assign `veth1` into namespace `test`
* `ip addr show veth0`: show it in the global namespace
```
130: veth0@if2: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 8e:4c:e4:f8:df:aa brd ff:ff:ff:ff:ff:ff link-netnsid 15
```
* `ip netns exec test ip addr`: run `ip addr` under the `test` network namespace.
```
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: veth1@if130: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether ca:83:85:f5:36:fe brd ff:ff:ff:ff:ff:ff link-netnsid 0
```
* `ip addr add 10.1.1.1/24 dev veth0 && ip addr show veth0`
```
130: veth0@if2: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 8e:4c:e4:f8:df:aa brd ff:ff:ff:ff:ff:ff link-netnsid 15
    inet 10.1.1.1/24 scope global veth0
       valid_lft forever preferred_lft forever
```
* `ip netns exec test ip addr add 10.1.1.2/24 dev veth1 && ip netns exec test ip addr show veth1`
```
2: veth1@if130: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether ca:83:85:f5:36:fe brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.1.2/24 scope global veth1
       valid_lft forever preferred_lft forever
```
* `ip link set veth0 up`
* `ip netns exec test ip link set veth1 up`
* `ping -I 10.1.1.1 10.1.1.2`
```
PING 10.1.1.2 (10.1.1.2) from 10.1.1.1 : 56(84) bytes of data.
64 bytes from 10.1.1.2: icmp_seq=1 ttl=64 time=0.060 ms
64 bytes from 10.1.1.2: icmp_seq=2 ttl=64 time=0.033 ms
64 bytes from 10.1.1.2: icmp_seq=3 ttl=64 time=0.033 ms
```

## Troubleshooting
* `ip netns del test`: delete the namespace `test`
* ` ip link delete veth0`: delete the interface `veth0`

## References
* <https://developers.redhat.com/blog/2018/10/22/introduction-to-linux-interfaces-for-virtual-networking/#bridge>: This article provided many beautiful illustrations.
* <https://wiki.archlinux.org/index.php/Network_bridge>: Arch Linux documentation is always useful and clear.
* <https://blog.scottlowe.org/2013/09/04/introducing-linux-network-namespaces/>: A gentle introduction.
* <https://unix.stackexchange.com/questions/511783/for-veth-pair-ping-does-not-recognize-interface-name-and-tc-qdisc-netem-does-no>
* [Why assign MAC and IP addresses on Bridge interface](https://unix.stackexchange.com/questions/319979/why-assign-mac-and-ip-addresses-on-bridge-interface)
* [Containers and networking](https://wvi.cz/diyC/networking/)
