<div align="center">
  <p>
    <a align="center" href="" target="_blank">
      <img
        width="850"
        src="https://github.com/myselfbasil/Guide-to-Flash-Eagle-Board/blob/939d86dd2da75769673155760a6ca23911a78f7a/assets/header_img.png"
      >
    </a>
  </p>
</div>
# Guide to Flash Eagle Board :)
#### Made with 🫶🏻 by Basil

## 1. Steps to flash Jetson Linux 32.7.5 (latest) (Jetpack 4.6.5 DP) on EagleBoard EMMC:

**Board spec**: [NVIDIA Jetson Nano Developer Kit B01](https://tannatechbiz.com/nvidia-jetson-nano-developer-kit-b01.html)

(Note: You can also use the [NVIDIA SDK Manager](https://developer.nvidia.com/sdk-manager) to confirm it.)

### Required Files:
1. Driver Package BSP
2. Sample Root FS
3. `tegra210-p3448-0002-p3449-0000-b00.dtb`

Once confirmed that the board is in force recovery mode, follow these steps to flash the image onto the EMMC.

- Download File 1 & File 2 from: [NVIDIA Jetson Linux Tegra R32.7.5](https://developer.nvidia.com/embedded/linux-tegra-r3275)
- Download File 3 from: [Google Drive](https://drive.google.com/file/d/1vd2TleSPh8SxgAKAZABFSNPYdEnsSxhO/view?usp=sharing)

### Flashing Procedure:

1. Ensure the device is powered off.
2. Jump pins 9 and 10 (RST and GND) with a jumper.
3. Connect the micro-USB cable to both the board and the host machine.
4. The board will boot into force recovery mode.
5. Confirm the board is in force recovery mode by running the command `lsusb` on the host machine.
    > The Jetson module is in Force Recovery mode if you see this message:
    > "Bus <bbb> Device <ddd>: ID 0955: <nnnn> Nvidia Corp."

### Commands to flash the image:

```bash
1. tar xf jetson-210_Linux_R32.7.2_aarch64.tbz2
2. cd Linux_for_Tegra/rootfs/
3. sudo tar xpf /<path_of_downloaded_sample-root-file>/Tegra_Linux_Sample-Root-Filesystem_R32.7.2_aarch64.tbz2
4. cd ..
5. sudo ./apply_binaries.sh
6. cp tegra210-p3448-0002-p3449-0000-b00.dtb Linux_for_Tegra/kernel/dtb/
7. sudo ./flash.sh jetson-nano-emmc mmcblk0p1
```

Carefully watch the install logs for any dependency issues.

### Reference Documentation:

- [NVIDIA Jetson Linux Documentation](https://docs.nvidia.com/jetson/archives/l4t-archived/l4t-3275/index.html#page/Tegra%20Linux%20Driver%20Package%20Development%20Guide/quick_start.html)

After the process is complete, the board will reboot automatically.

Connect a display and boot into the new EMMC image.

---

## 2. Setting Up the Eagle Board:

1. Boot up the Eagle Board and complete the initial setup.
2. Open the terminal and run the following commands.

```bash
# To list the device name and other details:
$ sudo fdisk -l

# To enter into the fdisk utility using the device name:
$ sudo fdisk /dev/mmcblk1  # Here, '/dev/mmcblk1' is the device name
```

Note: I'm partitioning `/home` & `/var`, giving both partitions 15 GB of space.

### Fdisk Utility Commands:
- `p`: print the partitions made
- `n`: create a new partition
- `w`: write the changes made
- `q`: quit without saving changes

### Create `/var` Partition:

```bash
$ n
$ p
$ 1
$ <default_value>
$ +15G
# If prompted to "remove the signature":
$ y
# Write the partitions to the disk:
$ w
```

Use `p` to print the partition you just created. Partition 1 = `/dev/mmcblk1p1`.

### Create `/home` Partition:

```bash
$ n
$ p
$ 2
$ <default_value>
# Here, the default value uses the remaining space.
$ <default_value>
# If prompted to "remove the signature":
$ y
# Write the partitions to the disk:
$ w
```

Partition 2 = `/dev/mmcblk1p2`.

### Format the Partitions:

```bash
# Format the partitions:
$ sudo mkfs.ext4 /dev/mmcblk1p1
$ sudo mkfs.ext4 /dev/mmcblk1p2
```

### 1. Mounting the `/home` Partition (`/dev/mmcblk1p2`):

Be careful while executing these commands:

```bash
# To mount the `/home` partition:
$ sudo mount /dev/mmcblk1p2 /mnt

# To copy all the files from `/home` to `/mnt`:
$ sudo rsync -av --progress /home/* /mnt/.

# After the copy is complete, delete everything inside `/home`:
$ rm -rf /home/*

# Unmount `/mnt` and remount `/home`:
$ sudo umount /mnt
$ sudo mount /dev/mmcblk1p2 /home

# Make the partitions permanent by editing `/etc/fstab`:
$ sudo vi /etc/fstab
```

Inside the `vi` editor, add the following line:

```
/dev/mmcblk1p2    /home   ext4   defaults   0  1
```

Note: Use tabs, not spaces!

Now, reboot the system.

### 2. Mounting the `/var` Partition (`/dev/mmcblk1p1`):

Be careful while executing these commands:

```bash
# To mount the `/var` partition:
$ sudo mount /dev/mmcblk1p1 /mnt

# To copy all the files from `/var` to `/mnt`:
$ sudo rsync -av --progress /var/* /mnt/.

# After the copy is complete, delete everything inside `/var`:
$ rm -rf /var/*

# Unmount `/mnt` and remount `/var`:
$ sudo umount /mnt
$ sudo mount /dev/mmcblk1p1 /var

# Make the partitions permanent by editing `/etc/fstab`:
$ sudo vi /etc/fstab
```

Inside the `vi` editor, add the following line:

```
/dev/mmcblk1p1    /var    ext4   defaults   0  1
```

Note: Use tabs, not spaces!

Reboot the system again.

---

Congrats! Your Eagle Board is now as good as new :D
