# Btrfs

* Copy on write filesystem
* Only fully supported by GRUB bootloader
  * rEFInd works if compression disabled
  * Syslinux works if compression, encryption, multi-device volumes disabled
* No built-in encryption support
* Writable and RO snapshots
* 16 EIB max size
* Supports subvolumes
  * Separate internal FS roots
* Checksums on data and metadata
* TRIM support
* Efficient incremental backup
* Seed filesystems 
  * RO volumes that act as template for new filesystem volumes



