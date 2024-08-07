# Guide to Flash Eagle Board :)
#### Made with <3 by Basil

## 1. Steps to flash Jetson Linux 32.7.5 (latest) (Jetpack 4.6.5 DP) on EagleBoard EMMC:

**Board spec: https://tannatechbiz.com/nvidia-jetson-nano-developer-kit-b01.html**

(Note: You can also use https://developer.nvidia.com/sdk-manager to confirm it.)

File 1: Driver Package BSP

File 2: Sample Root FS

File 3: tegra210-p3448-0002-p3449-0000-b00.dtb

- Once it is confirmed that it is in force recovery mode, use the steps provided below to

flash the image into the EMMC.

- Download File 1 & File 2 from : [https://developer.nvidia.com/embedded/linux-tegra-r3275](https://developer.nvidia.com/embedded/linux-tegra-r3275)
- Download File 3 form: [https://drive.google.com/file/d/1vd2TleSPh8SxgAKAZABFSNPYdEnsSxhO/view?usp=sharing](https://drive.google.com/file/d/1vd2TleSPh8SxgAKAZABFSNPYdEnsSxhO/view?usp=sharing)

**Do the following operations:**

1. Make sure the device is powered off
2. Jump the pins 9 and 10 (RST and GND) with the jumper
3. Plug in the micro-USB cable with the board and to the host machine firmly.
4. The board will start to boot into force recovery mode.
5. Confirm that the board is in force recovery mode by issuing the command lsusb on

the host machine.

1. The Jetson module is in Force Recovery mode if you see this message:

> " Bus <bbb> Device <ddd>: ID 0955: <nnnn> Nvidia Corp."
> 

Follow the commands :

```bash
1. tar xf jetson-210_Linux_R32.7.2_aarch64.tbz2
2. cd Linux_for_Tegra/rootfs/
3. sudo tar xpf /(path of downloaded sample-root-file)/
Tegra_Linux_Sample-Root-Filesystem_R32.7.2_aarch64.tbz2
4. cd ..
5. sudo ./apply_binaries.sh
6. cp tegra210-p3448-0002-p3449-0000-b00.dtb Linux_for_Tegra/kernel/dtb/
7. sudo ./flash.sh jetson-nano-emmc mmcblk0p1
```

1. Look at the install logs keenly for any dependencies.

Docs reference: 

[https://docs.nvidia.com/jetson/archives/l4t-archived/l4t-3275/index.html#page/Tegra Linux Driver Package Development Guide/quick_start.html#wwpID0EOHA](https://docs.nvidia.com/jetson/archives/l4t-archived/l4t-3275/index.html#page/Tegra%20Linux%20Driver%20Package%20Development%20Guide/quick_start.html#wwpID0EOHA)

9. Once it is done, the board will reboot automatically.

10. Now use a display and boot into the new EMMC image.

## 2. Setting Up the Eagle Board to use:

Boot up the Eagle Board and do the initial setup.

After the first boot, Opne up Terminal and follow these commands.

```bash
# To list the device name and other details;
$ sudo fdisk -l
# To Enter into the fdisk utility using the device name.
$ sudo fdisk /dev/mmcblk1             [ Here,'/dev/mmcblk1' is the Device name ]
```

Note: Here, I’m partitioning /home & /var i.e, giving both the partitions 15 Gigs of space.

> Here are the commands to perform certain actions on the fdisk utility :)
'p' : to print the partitions made
'n' : to create a new partition
'w' : to write the changes made &
'q' : to quit without saving changes.
> 

```bash
# Crete /var partition;
$ n
$ p
$ 1
$ default value
$ +15G
# If it prompts that "Do you want to remove the signature?' 
$ y
# write the partitions made into the disk
$ w
```

Use ‘p’ to print the partition you have made just now.

the partition 1 is  p1 after the device name. Here the partition 1 = /dev/mmcblk1p1

```bash
# Crete /home partition;
$ n
$ p
$ 2
$ default value
# Here default value is used so that it takes the whoel reamining space
$ default value        
# If it prompts that "Do you want to remove the signature?' 
$ y
# Write the partitions made into the disk
$ w
```

The partition 2 is p2 after the device name. Here the partition 2 = /dev/mmcblk1p2

```bash
# Format the partitions 1 & 2;
$ sudo mkfs.ext4 /dev/mmcblk1p1
$ sudo mkfs.ext4 /dev/mmcblk1p2
```

### 1. Initially mounting the /home partition which is /dev/mmcblk1p2

Note be careful on executing these commands:

```bash
# To mount the /home partition
$ sudo mount /dev/mmcblk1p2 /mnt
# To copy all the files from /home to /mnt
$ sudo rsync -av --progress /home/* /mnt/.
# Afetr the copying process is done, delete everything inside the /home dir.
$ rm -rf /home/*
$ sudo umount /mnt
$ sudo mount /dev/mmcblk1p2 /home
# Till now we have made the partitions temporary, now we will make it permanent.
$ sudo vi /etc/fstab 
```

Now inside the vi editor and enter,

/dev/mmcblk1p2           /home     ext4          defaults    0  1

Note: Only use tab not spaces!

Now, reboot the system.

### 2. Then mounting the /var partition which is /dev/mmcblk1p1

Note be careful on executing these commands:

```bash
# To mount the /var partition
$ sudo mount /dev/mmcblk1p1 /mnt
# To copy all the files from /var to /mnt
$ sudo rsync -av --progress /var/* /mnt/.
# Afetr the copying process is done, delete everything inside the /var dir.
$ rm -rf /var/*
$ sudo umount /mnt
$ sudo mount /dev/mmcblk1p2 /var
# Till now we have made the partitions temporary, now we will make it permanent.
$ sudo vi /etc/fstab 
```

Now inside the vi editor and enter,

/dev/mmcblk1p1           /var     ext4          defaults    0  1

Note: Only use tab not spaces!

Now, reboot the system again.

Congrats now your Eagle Baord is good as new :D
