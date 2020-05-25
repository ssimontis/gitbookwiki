---
description: How Linux boots...
---

# Boot Process

### Firmware Types

* BIOS
  * Firmware stored in Flash Memory
* UEFI

  * Unified Extensible Firmware Interface
  * Can read partition table and file systems
  * Relies upon boot entries in NVRAM, not MBR, to boot

### System Init

#### BIOS

* Reads first 440 bytes of MBR of first boot device
  * Contains first stage of boot loader
  * First stage contains instructions to load second stage boot loader
* Second stage boot loader read
  * If MBR partition, stored in disk sectors after MBR
  * Partition or partitionless disk Volume Boot Record
  * BIOS boot partition
* Boot loader is launched
* OS is chain-loaded

#### UEFI

* Firmware reads entries in NVRAM to determine EFI application to launch
  * Can simply be a disk
  * Firmware looks for EFI system partition and EFI application at EFI\BOOT\BOOTx64.EFI
* Firmware launches EFI application
  * Boot loader or kernel itself using EFISTUB

#### Linux

* Kernel is loaded
* Kernel unpacks initramfs \(initial RAM filesystem\) archives into empty root filesystem
  * Must contain any modules required to load root operating system
* Kernel executes /init in rootfs as first process
* Real root replaces initial root filesystem
* /sbin/init replaces /init process
* Init calls getty for each virtual terminal
  * VT can ask for username and password or start display manager
* Login program sets environment variables and starts user's shell based on /etc/passwd

