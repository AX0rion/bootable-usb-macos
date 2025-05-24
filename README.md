# 💽 Bootable USB Creation on macOS (no third-party apps required)

This repository provides a simple, generic, and reliable guide to creating a bootable USB drive from **any ISO image** using macOS Terminal.

Whether you're preparing a Linux installer, a Windows setup, or a custom live system, this method works without Boot Camp, without Etcher, and without any GUI tools — just native commands and full control.

---

## 🧰 What You’ll Need

- A USB drive (8 GB minimum recommended)
- A valid `.iso` image (Linux, Windows, etc.)
- Access to the Terminal and admin privileges (for `sudo`)

---

## ⚙️ Step-by-Step Guide

### 1. Insert your USB drive and identify it

Open Terminal and run:

```shell
$ diskutil list

/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *500.3 GB   disk0
   1:             Apple_APFS_ISC Container disk1         524.3 MB   disk0s1
   2:                 Apple_APFS Container disk3         494.4 GB   disk0s2
   3:        Apple_APFS_Recovery Container disk2         5.4 GB     disk0s3

/dev/disk6 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *15.4 GB    disk6
   1:                        EFI EFI                     209.7 MB   disk6s1
   2:       Microsoft Basic Data LINUX                   15.2 GB    disk6s2
```

> [!NOTE]
> This will list all available disks. Look for your USB drive — e.g. `/dev/disk2`, `/dev/disk3`, etc.

> [!CAUTION]
> Double-check the disk identifier. Writing to the wrong disk can erase your macOS system.

In this example, the USB drive is: **`/dev/disk6`**

### 2. Unmount USB drive (don't eject it!)

```shell
$ diskutil unmountDisk /dev/disk6
```

### 3. Use `dd` to write the `.iso` to the USB drive

```shell
$ sudo dd if=/path/to/your.iso of=/dev/disk6 bs=1m status=progress
```
Let’s break down the important options used here:
- `if=` (input file): This specifies the path to the source file, which is the ISO image you want to write to the USB drive.

- `of=` (output file):
This specifies the destination device to write to, in this case your USB drive. It’s important to use the full disk device (not a partition), e.g. `/dev/disk6`

- `bs=1m` (block size): Sets the size of each read/write operation to 1 megabyte (1m). Larger blocks reduce overhead and speed up the process.

- `status=progress`: This option makes dd display ongoing progress information, such as the number of bytes transferred, which is helpful since dd is usually silent.

### 4. Wait for the operation to complete

The process may take a few minutes depending on your USB drive and port (USB 2.0 vs 3.0). At the end, you’ll see something like:
```shell
5242880000 bytes transferred in 220.123456 secs (23823092 bytes/sec)
```

### 5. Eject the USB drive safely
```shell
$ diskutil eject /dev/disk6
```
You can now remove the USB stick and use it to boot on another system.