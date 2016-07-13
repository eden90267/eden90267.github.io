---
layout: post
title:  "Linux LPIC-1 Study Note(CH7)"
date:   2016-06-24 17:33:00 +0800
categories: [linux, lpic]
---

# Chapter 07: 檔案系統與檔案目錄的管理 #

電腦系統上的檔案都必須存放到儲存空間, 而用來當作儲存空間的裝置包括硬碟、記憶卡、隨身碟等。這些裝置必須經過「處理」後才能被使用。所謂「處理」就是Partition(分割)、Format(格式化)與Mount(掛接)。

**檔案**與**目錄**是作業系統的基本的組成結構。

## 新增硬碟與格式化 ##

儲存裝置就像一塊空白的土地, 而資料就像一個物件, 如果沒有對這塊土地進行規劃而隨意將物件丟在上面, 是無法有效使用它。好比土地一般, 儲存裝置需要先分割區域、再將區域內定上座標與註記(格式化)、最後再給予明確的地址(掛接到某目錄下)。

### 切割分割區 ###

所謂分割區, 就是將儲存裝置分割為多個區域, 目的在於管理上的便利性。在Linux中可透過`fdisk`這指令劃分分割區, 而在Linux中可設定的分割區的類型有九十多種, 一般會使用的只有四種, 分述如下:

- (82)Linux swap: 虛擬記憶體, 必需存在的分割區, 建議大小約1GB到8GB之間
- (83)Linux: 實體使用的分割區
- (5)Extended或是(85)Linux extendes: 如果要劃分四個分割區以上, 則必須使用此類型
- (8e)Linux LVM: 使用LVM時須使用此類型的分割區

上面數字表示分割區的代碼(ID), 我們可使用`fdisk`進入磁碟分割區選單中(如`fdisk` `/dev/sda`), 輸入L並按下`Enter`即可顯示所有分割區類型代碼。

	$ sudo fdisk -l

	Disk /dev/xvda: 10.7 GB, 10737418240 bytes
	255 heads, 63 sectors/track, 1305 cylinders, total 20971520 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk identifier: 0x00000000

	    Device Boot      Start         End      Blocks   Id  System
	/dev/xvda1   *       16065    20964824    10474380   83  Linux


	$ sudo fdisk /dev/xvda

	Command (m for help): L

	 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris
	 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-
	 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-
	 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden C:  c6  DRDOS/sec (FAT-
	 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx
	 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data
	 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .
	 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility
	 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt
	 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access
	 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O
	 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor
	 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi eb  BeOS fs
	 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         ee  GPT
	 f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ef  EFI (FAT-12/16/
	10  OPUS            55  EZ-Drive        a7  NeXTSTEP        f0  Linux/PA-RISC b
	11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f1  SpeedStor
	12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f4  SpeedStor
	14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f2  DOS secondary
	16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      fb  VMware VMFS
	17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fc  VMware VMKCORE
	18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fd  Linux raid auto
	1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fe  LANstep
	1c  Hidden W95 FAT3 75  PC/IX           be  Solaris boot    ff  BBT
	1e  Hidden W95 FAT1 80  Old Minix


在Linux必要的分割區有二: 一者為一般資料的根目錄(/,root partition), 一者為SWAP(虛擬記憶體), 如果主機只有一個儲存裝置, 則必需安裝兩個以上的分割區。無論如何, 分割儲存裝置是必要的動作, 即使只需要一個分割區, 也必需執行分割的動作。

※經驗談: 一個儲存裝置上必須要劃了分割區後, 才能進行後續的動作(格式化、掛接等), 一個儲存裝置上至少要劃分一個分割區, 但若我們只有一個儲存裝置,則必須劃分兩個分割區(/與SWAP)以上才能運作。

#### 使用`fdisk`切割四個以內的主分割區 ####

由於主分割區最多只能四個, 因此如果我們的分割區總數在四個以內, 就可以全部使用主分割區

	$ sudo fdisk /dev/xvda1
	Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
	Building a new DOS disklabel with disk identifier 0x324b23d7.
	Changes will remain in memory only, until you decide to write them.
	After that, of course, the previous content won't be recoverable.

	Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

	Command (m for help): p

	Disk /dev/xvda1: 10.7 GB, 10725765120 bytes
	255 heads, 63 sectors/track, 1304 cylinders, total 20948760 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk identifier: 0x324b23d7

	      Device Boot      Start         End      Blocks   Id  System

	Command (m for help): n
	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended
	Select (default p): p
	Partition number (1-4, default 1):
	Using default value 1
	First sector (2048-20948759, default 2048):
	Using default value 2048
	Last sector, +sectors or +size{K,M,G} (2048-20948759, default 20948759): +4G

	Command (m for help): n
	Partition type:
	   p   primary (1 primary, 0 extended, 3 free)
	   e   extended
	Select (default p): p
	Partition number (1-4, default 2):
	Using default value 2
	First sector (8390656-20948759, default 8390656):
	Using default value 8390656
	Last sector, +sectors or +size{K,M,G} (8390656-20948759, default 20948759): +4G

	Command (m for help): P

	Disk /dev/xvda1: 10.7 GB, 10725765120 bytes
	255 heads, 63 sectors/track, 1304 cylinders, total 20948760 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk identifier: 0x324b23d7

	      Device Boot      Start         End      Blocks   Id  System
	/dev/xvda1p1            2048     8390655     4194304   83  Linux
	/dev/xvda1p2         8390656    16779263     4194304   83  Linux

	Command (m for help): w

Command (m for help):

- p: 顯示分割區的狀態
- w: 將變更寫回硬碟並離開

輸入`fdisk -l`的參數即可顯示分割區的狀態

	$ sudo fdisk -l

	Disk /dev/xvda: 10.7 GB, 10737418240 bytes
	255 heads, 63 sectors/track, 1305 cylinders, total 20971520 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk identifier: 0x00000000

	    Device Boot      Start         End      Blocks   Id  System
	/dev/xvda1   *       16065    20964824    10474380   83  Linux

※ First sector 是指一圈圈硬碟磁柱的位置, 藉由第一圈與最後一圈可以算出分割區的大小, 但我們不會去換算這數值, 而會讓系統自動設定First sector, 而在Last sector的部分輸入+數值, 如+4G, 就是由First sector開始增加12GB的空間, 也就是設定該分割區的空間為4G

※ 注意: 在fdisk中的p有兩種意思: 一般的p是print, 顯示列出的意思; 跟隨在新增(n)後面的p, 是設定為primary(主分割區), 要注意區別

#### 在fdisk中將分割區指定為SWAP格式 ####

