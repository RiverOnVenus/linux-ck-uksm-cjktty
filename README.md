# Linux-ck-uksm-cjktty

![GitHub](https://img.shields.io/github/license/RiverOnVenus/linux-ck-uksm-cjktty) [![PKGBUILD CI](https://github.com/RiverOnVenus/linux-ck-uksm-cjktty/actions/workflows/main.yml/badge.svg)](https://github.com/RiverOnVenus/linux-ck-uksm-cjktty/actions/workflows/main.yml) ![GitHub all releases](https://img.shields.io/github/downloads/RiverOnVenus/linux-ck-uksm-cjktty/total)

The Linux-ck-uksm-cjktty kernel and modules with the ck1 patchset and uksm patch and cjktty patch etc.. featuring MuQSS CPU scheduler and default bbr2. Built on the [Linux-ck](https://aur.archlinux.org/packages/linux-ck/) maintained by [graysky](https://github.com/graysky2).

- Con Kolivas' [ck patchset](http://ck.kolivas.org/patches/) including a CPU scheduler named MuQSS (*Multiple Queue Skiplist Scheduler*, pronounced *mux*) which replaces Brain Fuck Scheduler (BFS), his previous work. Many Arch Linux users choose this kernel for its excellent desktop interactivity and responsiveness under any load situation. It is designed for desktop/laptop use but not for servers. It provides low latency environment and works well for 16 CPUs or fewer.
- [UKSM patch](https://github.com/dolohow/uksm) is an improvement upon KSM. Some basic data structures and routines are borrowed from ksm.c .
- [CJKTTY patch](https://github.com/zhmars/cjktty-patches) supports displaying CJK Unified Ideographs on Linux tty.
- [BBR](https://github.com/google/bbr) is a congestion control algorithm proposed by Google.

# Build and install

- [AUR](https://aur.archlinux.org/packages/linux-ck-uksm-cjktty/)
- [Release page](https://github.com/RiverOnVenus/linux-ck-uksm-cjktty/releases)

# Sysctl configuration improving performance

```
# See https://wiki.archlinux.org/title/Sysctl for more information.

# Networking
net.core.netdev_max_backlog = 16384
net.core.somaxconn = 8192
net.core.rmem_default = 1048576
net.core.rmem_max = 16777216
net.core.wmem_default = 1048576
net.core.wmem_max = 16777216
net.core.optmem_max = 65536
net.ipv4.tcp_rmem = 4096 1048576 2097152
net.ipv4.tcp_wmem = 4096 65536 16777216
net.ipv4.udp_rmem_min = 8192
net.ipv4.udp_wmem_min = 8192
net.ipv4.tcp_fastopen = 3
net.ipv4.tcp_max_syn_backlog = 8192
net.ipv4.tcp_max_tw_buckets = 2000000
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_fin_timeout = 10
net.ipv4.tcp_mtu_probing = 1
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_rfc1337 = 1
net.ipv4.tcp_keepalive_time = 120
net.ipv4.tcp_keepalive_intvl = 10
net.ipv4.tcp_keepalive_probes = 6
net.ipv4.conf.default.log_martians = 1
net.ipv4.conf.all.log_martians = 1
net.core.default_qdisc = cake

# Virtual memory
vm.vfs_cache_pressure = 50
vm.dirty_background_ratio = 5
vm.dirty_ratio = 10

# For Solid State Drives
vm.swappiness = 100
# See https://chrisdown.name/2018/01/02/in-defence-of-swap.html
```

# Changing I/O scheduler

To change the active I/O scheduler to *bfq* for device *sda*, use:

```
# echo bfq > /sys/block/sda/queue/scheduler
```

Or create file `/etc/udev/rules.d/60-ioschedulers.rules`:

```
# set scheduler for NVMe
ACTION=="add|change", KERNEL=="nvme[0-9]n[0-9]", ATTR{queue/scheduler}="bfq"
# set scheduler for SSD and eMMC
ACTION=="add|change", KERNEL=="sd[a-z]|mmcblk[0-9]*", ATTR{queue/rotational}=="0", ATTR{queue/scheduler}="bfq"
# set scheduler for rotating disks
ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/rotational}=="1", ATTR{queue/scheduler}="bfq"
```

**Reboot or force [udev#Loading new rules](https://wiki.archlinux.org/title/Udev#Loading_new_rules)**:

If rules fail to reload automatically, use:

```
# udevadm control --reload
```

To manually force *udev* to trigger your rules, use:

```
# udevadm trigger
```

# More about CK and MuQSS

- [Linux-ck](https://wiki.archlinux.org/title/Linux-ck)
- [LKML announcement](https://lkml.org/lkml/2016/10/29/4)
- [Con Kolivas' Blog](https://ck-hack.blogspot.it/)
