### VirtualCAN example using socketCAN framework

Please, read README.md to understand general cannelloni feature set.

Before setting the VirtualCAN testing of the virtual socketCAN framework:

Two machines:1 (Debian VM) and 2 (Debian host) need to be connected:
```
Debian VM with IP: 172.16.71.55 <<=== Virtual ETH/virtual MAC I/F ===>> Debian host with IP: 172.16.71.51
     virtual interface vcan0                                                 virtual interface vcan0
```
Machine 2 (Debian host with IP address: 172.16.71.51) will be connected to the virtual
CAN Bus that is attached to Machine 1 (Debian VM with IP address: 172.16.71.55).

Prepare vcan on the Machine 1 (Debian VM with IP address: 172.16.71.55):
```
sudo modprobe vcan
sudo ip link add name vcan0 type vcan
sudo ip link set dev vcan0 up
```

Start canneloni on the Machine 1 (Debian VM with IP address: 172.16.71.55):
```
cannelloni -I vcan0 -R 172.16.71.51 -r 20000 -l 20000
```

Please, do note that vcan0 is the dedicated interface to the Machine 1, has
nothing to do with vcan0 of the Machine 2! Also, do note that the IP address
172.16.71.51 is the IP of the Debian host.

cannelloni will now listen on port 20000 and has Machine 2 configured as its remote.

Prepare vcan on Machine 2:
```
sudo modprobe vcan
sudo ip link add name vcan0 type vcan
sudo ip link set dev vcan0 up
```

When operating with `vcan` interfaces always keep in mind that they
easily surpass the possible data rate of any physical CAN interface.
An application that just sends whenever the bus is ready would simply
send with many Mbit/s.
The receiving end, a physical CAN interface with a net. data rate of
<= 1 Mbit/s would not be able to keep up.
It is therefore a good idea to rate limit a `vcan` interface to
prevent packet loss.
```
sudo tc qdisc add dev vcan0 root tbf rate 300kbit latency 100ms burst 1000
```

This command will rate limit `vcan0` to 300 kbit/s.
Try to match the rate limit with your physical interface on the remote.
Keep also in mind that this also increases the overall latency!

Now start cannelloni on Machine 2 (Debian host):
```
cannelloni -I vcan0 -R 172.16.71.55 -r 20000 -l 20000
```

Please, do note that vcan0 is the dedicated interface to the Machine 2, has
nothing to do with vcan0 of the Machine 1! Also, do note that the IP address
172.16.71.55 is the IP of the Debian VM.

The tunnel is now complete and can be used on both machines.
Simply try it by using `candump` and/or `cangen`:

`candump` and `cangen` tools to be found here:
https://github.com/linux-can/can-utils

Description how to use these tools to be found here:
http://sgframework.readthedocs.io/en/latest/cantutorial.html

If something does not work, try the debug switch `-d cut` to find out
what is wrong.
