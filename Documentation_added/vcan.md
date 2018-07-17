Please, for the more complete understanding how to set the socketCAN feature, read the following net pointer:

	https://en.wikipedia.org/wiki/SocketCAN

How to dynamically set a vcan net interface in normal Debian or any other Linux distro as feature:

    root@magdalena:/proc# cd /lib/modules/
    root@magdalena:/lib/modules# ls -al
    drwxr-xr-x  4 root root 4096 Jul  4 14:47 4.9.0-6-amd64
    root@magdalena:/lib/modules# cd 4.9.0-6-amd64/
    root@magdalena:/lib/modules/4.9.0-6-amd64# ls -al
    root@magdalena:/lib/modules/4.9.0-6-amd64# cd kernel
    root@magdalena:/lib/modules/4.9.0-6-amd64/kernel# ls -al
    root@magdalena:/lib/modules/4.9.0-6-amd64/kernel# cd ..
    root@magdalena:/lib/modules/4.9.0-6-amd64# find . -name can*
    ./kernel/drivers/net/can
    ./kernel/drivers/net/can/can-dev.ko
    ./kernel/net/can
    ./kernel/net/can/can-bcm.ko
    ./kernel/net/can/can-raw.ko
    ./kernel/net/can/can-gw.ko
    ./kernel/net/can/can.ko
    root@magdalena:/lib/modules/4.9.0-6-amd64# lsmod | grep can
    can_raw                20480  0
    can                    49152  1 can_raw
    root@magdalena:/lib/modules/4.9.0-6-amd64# modprobe can
    root@magdalena:/lib/modules/4.9.0-6-amd64# lsmod | grep can
    can_raw                20480  0
    can                    49152  1 can_raw
    root@magdalena:/lib/modules/4.9.0-6-amd64# modprobe can-bcm
    root@magdalena:/lib/modules/4.9.0-6-amd64# modprobe can-dev
    root@magdalena:/lib/modules/4.9.0-6-amd64# modprobe can-gw
    root@magdalena:/lib/modules/4.9.0-6-amd64# modprobe vcan
    root@magdalena:/lib/modules/4.9.0-6-amd64# lsmod | grep can
    vcan                   16384  0
    can_gw                 20480  0
    can_dev                24576  0
    can_bcm                24576  0
    can_raw                20480  0
    can                    49152  3 can_raw,can_bcm,can_gw
    root@magdalena:/lib/modules/4.9.0-6-amd64# ip link add name vcan0 type vcan
    root@magdalena:/lib/modules/4.9.0-6-amd64# ip link set dev vcan0 up
    root@magdalena:/lib/modules/4.9.0-6-amd64# ifconfig
    enp0s31f6: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
	   inet 172.16.71.51  netmask 255.255.224.0  broadcast 172.16.95.255
	   inet6 fe80::8eec:4bff:fe63:2154  prefixlen 64  scopeid 0x20<link>
	   ether 8c:ec:4b:63:21:54  txqueuelen 1000  (Ethernet)
	   device interrupt 20  memory 0xf7280000-f72a0000  

    enp4s0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
	   ether 68:05:ca:82:9a:77  txqueuelen 1000  (Ethernet)
	   device interrupt 18  memory 0xf70c0000-f70e0000  

    enx001060315757: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
	   ether 00:10:60:31:57:57  txqueuelen 1000  (Ethernet)

    lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
	   inet 127.0.0.1  netmask 255.0.0.0
	   inet6 ::1  prefixlen 128  scopeid 0x10<host>

    vcan0: flags=193<UP,RUNNING,NOARP>  mtu 16
	   unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 1  (UNSPEC)
	   RX packets 0  bytes 0 (0.0 B)
	   RX errors 0  dropped 0  overruns 0  frame 0
	   TX packets 0  bytes 0 (0.0 B)
	   TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    root@magdalena:/lib/modules/4.9.0-6-amd64# sudo ip link set dev vcan0 down
    root@magdalena:/lib/modules/4.9.0-6-amd64# ifconfig -a

    ...[snap]...

    vcan0: flags=128<NOARP>  mtu 16
	   unspec 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00  txqueuelen 1  (UNSPEC)
	   RX packets 0  bytes 0 (0.0 B)
	   RX errors 0  dropped 0  overruns 0  frame 0
	   TX packets 0  bytes 0 (0.0 B)
	   TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    root@magdalena:/lib/modules/4.9.0-6-amd64# sudo ip link set dev vcan0 up    
    
The following represents the script, setting socketCAN on Linux kernel:

	lsmod | grep can
	modprobe can
	modprobe can_raw
	modprobe can-bcm
	modprobe can-dev
	modprobe can-gw
	modprobe vcan
	lsmod | grep can
	ip link add name vcan0 type vcan
	ip link set dev vcan0 up
	ifconfig
