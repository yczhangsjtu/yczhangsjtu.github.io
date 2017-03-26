---
title: Advanced Disk Management
---

# Advanced Disk Management

## Parition Disks

* **Master Boot Record (MBR)** is the most common partition table on disks under 2 TB. Suffers limitations and is gradually abandoned. Known as MS-DOS partitions and BIOS paritions.
* **GUID Partition Table (GPT)** overcomes many problems of older MBR and APM, is likely to become increasingly important. Apple adopted it when it adopted Intel CPUs.
* The limitation in size is due to the pointer to sector. MBR uses 32 bits pointer, and the sector is of size 512 KB, so the limit is 2 TB. GPT uses 64 bits pointer, and the limit is 8 ZB.
* The MBR specification provides for just four **primary partitions**. For more partitions, one primary partition could be used as arbitrary number of **logical partitions**, and that primary partition is called **extended partition**. Microsoft Windows must boot from primary partition.
* In GPT, there is no distinction between primary, logical and extended partitions. GPT supports a fixed number of partitions (128 by default), all of which are defined in the partition table.
* GPT supports partition name, MBR does not.
* All partition tables are composed of contiguous set of sectors, so if you want to change parition layout, you may need to move all the data on one or more partitions. This is one limitation that **LVM** is designed to overcome.
