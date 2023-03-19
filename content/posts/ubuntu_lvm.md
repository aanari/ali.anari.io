---
title: "Expand Hard Disk with Ubuntu LVM"
date: 2020-11-06
draft: false
description: "Resizing commands"
tags: [linux]
---

## Overview

So you're running an [Ubuntu](https://ubuntu.com) server in a virtual machine, and now you need to add 20 GB of disk space to root (`/`). There are quite a few ways to do this, and fortunately you're running the [Logical Volume Manager (LVM)](https://wiki.ubuntu.com/Lvm) in Ubuntu, so the process isn't too bad.

## Script

```bash
sudo growpart /dev/sda 3
pvdisplay
lvdisplay
pvresize /dev/sda3
lvextend -l+100%FREE /dev/ubuntu-vg/ubuntu-lv
resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
```

After you make the additional space available in your hypervisor, reboot your Ubuntu server so that it can see the new free space. You can also avoid this restart by asking the kernel to rescan the disk with `'echo 1 > /sys/class/block/sda/device/rescan'`.
