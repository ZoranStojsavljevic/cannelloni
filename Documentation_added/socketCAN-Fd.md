As promised. Here is what I, after some lengthily investigation, found using
Beaglebone Black and Vbox Virtual machine, which passes through host
(my host is pass-through VT-d capable).

VBox VM and BBB HW are connected via private IP network, NOT
influencing host PC at all.

I created two vcan0 interfaces on both ends, and using user space app
Cannelloni: https://github.com/mguentner/cannelloni as binding
application, I use CAN tunneling via ETH phys.

I, actually use socketCAN-Fd framework from the Linux 4.17.2 LTS
kernel, playing with user space can-utils app.

Here is the condensed Beaglebone Black local.conf for such kind of setup:
CONF_VERSION = "1"
PATCHRESOLVE = "noop"
SSTATE_DIR ?= "${TOPDIR}/sstate-cache"
DL_DIR ?= "${TOPDIR}/downloads"
TMPDIR ?= "${TOPDIR}/tmp"
PACKAGE_CLASSES ?= "package_rpm package_deb"
BBMASK = "meta-bbb/recipes-mender"
EXTRA_IMAGE_FEATURES = "debug-tweaks"
CORE_IMAGE_EXTRA_INSTALL_append = "openssh cmake canutils libsocketcan
nfs-utils rt-tests strace procps packagegroup-core-buildessential "
DISTRO_FEATURES_append = " ram"
IMAGE_FSTYPES_append = " cpio.xz"
MACHINE ??= "beaglebone"
DISTRO ??= "poky"
BBMULTICONFIG ?= ""

And, the setup (on both sides) is shown here:
https://stackoverflow.com/questions/36568167/can-fd-support-for-virtual-can-vcan-on-socketcan/51376306#51376306
(signed as _nobody_, aka me).
_______

It works as a charm. Here is what I did, following your advice.
Please, do note that command are generic, and that there is a
environment preparation I do NOT want to go into!

To set socketCAN framework beneath Linux kernel (I am using 4.17.2),
please, as root:

lsmod | grep can
modprobe can
modprobe can_raw
modprobe can-bcm
modprobe can-dev
modprobe can-gw
modprobe vcan
lsmod | grep can

To set the socketCAN-Fd framework, the following should be done (also as root):

ip link add dev vcan0 type vcan
ip link set vcan0 mtu 72
ip link set dev vcan0 up
ifconfig

The can-utils package is required to test the socketCAN-Fd framework.
Also, the following is required:
https://github.com/mguentner/cannelloni

And, everything works like a Swatch! ;-)

On the xmit side: cangen -f vcan0 -v vcan0

2C3##0.25.5A.FF.1E.DC.BD.CB.42.25.5A.FF.1E.DC.BD.CB.42.25.5A.FF.1E.DC.
BD.CB.42.25.5A.FF.1E.DC.BD.CB.42.25.5A.FF.1E.DC.BD.CB.42.25.5A.FF.1E.
DC.BD.CB.42.25.5A.FF.1E.DC.BD.CB.42.25.5A.FF.1E.DC.BD.CB.42

On the receiving side: candump vcan0

vcan0 2C3 [64] 25 5A FF 1E DC BD CB 42 25 5A FF 1E DC BD CB 42 25 5A
FF 1E DC BD CB 42 25 5A FF 1E DC BD CB 42 25 5A FF 1E DC BD CB 42 25
5A FF 1E DC BD CB 42 25 5A FF 1E DC BD CB 42 25 5A FF 1E DC BD CB 42
_______

True socketCAN-Fd framework. :-)
