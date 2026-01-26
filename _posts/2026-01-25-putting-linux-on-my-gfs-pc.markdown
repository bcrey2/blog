---
layout: post
title:  "putting linux on my gf's pc ^^"
date:   2026-01-25 15:13:38 -0700
categories: jekyll update
---

After being left in the lurch by Microsoft in October of last year, my partner decided it was probably time to cut her teeth as a linux user. She agreed to let me install ubuntu and created a file of everything she wanted backed up.

I've installed linux a handful of times, typically using gui tools for tasks like creating installation media. With this installation I tried to use the cli wherever possible in accordance with my goal to one day become a sock-wearing installation speedrunner on r/archlinux

The first issue I ran into was actually creating the backup. The Windows native compression tool doesn't always handle hyphens well, and so I went ahead and downloaded 7-Zip which did the trick. After compression, the backup was >8GB, which meant it couldn't be directly copied into my single-partition FAT32 usb:

```
$ lsblk -f
NAME        FSTYPE FSVER LABEL    UUID                                 FSAVAIL FSUSE% MOUNTPOINTS
sda                                                                                   
└─sda1      vfat   FAT32 USB DISK 816F-7610                              28.9G     0% /run/media/chase/USB DISK
zram0       swap   1     zram0    6a1ef8e7-8340-4b36-8aea-b1a812264b8c                [SWAP]
nvme0n1                                                                               
├─nvme0n1p1 vfat   FAT32          D01D-A61E                             937.6M     8% /boot
├─nvme0n1p2 ext4   1.0            689b63ee-5328-4de3-a138-898fc3091c2a   15.9G    62% /
└─nvme0n1p3 ext4   1.0            61efe04b-c03b-4c99-862d-f243bd621fe7  778.2G     7% /home
```

FAT32 does not allow writes >4GB at a time, so I reformatted the partition with the exFAT filesystem:

```
$ umount /dev/sda1
$ mkfs.exfat /dev/sda1
Creating exFAT filesystem(/dev/sda1, cluster size=32768)

Writing volume boot record: done
Writing backup volume boot record: done
Fat table creation: done
Allocation bitmap creation: done
Upcase table creation: done
Writing root directory entry: done
Synchronizing...

exFAT format complete!
```

Next I went and downloaded ubuntu 24.04.3 LTS `.iso` file. Installation media tools like Rufus had always been a black box to me, but its actually pretty simple to create a live cd using just coreutils. First, I listed the contents of `/dev/disk/by-id` and grabbed the symlink to the usb device. Then, I wrote the contents of the ISO directly to that location:

```
cat ~/Downloads/ubuntu-24.04.3-desktop-amd64.iso | sudo tee /dev/disk/by-id/usb-_USB_DISK_3.0_07215B8AB6B14809-0:0 > /dev/null; sync
```

![](/assets/img/both-usbs.jpg)

