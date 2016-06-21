---
layout: post
title:  "Linux LPIC-1 Study Note(CH6)"
date:   2016-06-01 11:31:00 +0800
categories: [linux, lpic]
---

# Chapter 06 套件與軟體的管理 #

套件可用原始碼編譯安裝, 或用套件管理工具安裝。

RedHat系列的作業系統: RPM套件管理工具  
Debian系列的作業系統: 使用DEB套件管理工具

## Ubuntu發行商的套件說明 ##

DEB是Debian的縮寫, 目前Debian與Ubuntu都是使用deb的套件管理。DEB的基本指令為`dpkg`, 如同`rpm`這指令一般。由於`dpkg`的使用較繁瑣, 且須手動收集所有相關的套件安裝, 因此比較少被使用, 我們常用的DEB管理工具為`apt-get`。

### 認識deb ###

deb是Debian的簡寫, 也就是Debian套件檔案的附檔名。類似於rpm一般, deb也是一個包裏檔, 包含了設定檔、執行檔與暫存檔等。目前使用deb套件的發行商包括Debian與Ubuntu。

deb檔命名格式:

	套件名稱_版本-更新次數_適合的硬體平台.deb

ex: `posfix_2.6.5-3_i386.deb`、`cup_1.4.1-5ubuntu2_i386.deb`、`gcc_4.4_4.4.1-4ubuntu8_i386.deb`。

硬體平台說明:

	| 硬體平台 	| 說明                                                     	|
	|----------	|----------------------------------------------------------	|
	| i386     	| 適用所有x86平台, 所有個人電腦與Intel系列以上的等級都適用 	    |
	| i586     	| 適用Petium I等級以上的電腦                               	|
	| i686     	| 適用Petium II等級以上的電腦                              	|
	| noarch   	| 沒有任何硬體上的限制                                     	|

一台Petium II的主機, 可安裝任何硬體平台的Deb, 但一台Pentium I的主機, 就不適合安裝i686的版本。以Pentium III或是Pentium 4的主機而言, 雖沒有硬體平台的限制, 但能選擇硬體平台等級高的RPM來安裝,則可發揮較好的效能。

### dpkg相關指令的操作 ###

dpkg是Debian PacKaGe的簡稱, 是Debian與Ubuntu上的套件管理指令, 用來安裝、升級、移除軟體。介紹三個指令: dpkg、dpkg-reconfigure與alien。

#### dpkg ####

dpkg [參數或動作][套件名稱或檔案]

1. `-c`,`--contentsarchive`: 列出套件檔內容, 這個參數後面需放套件檔名, ex:

		$ dpkg -c unzip_6.0-4ubuntu2.5_i386.deb
		drwxr-xr-x root/root         0 2015-11-09 15:31 ./
		drwxr-xr-x root/root         0 2015-11-09 15:31 ./usr/
		drwxr-xr-x root/root         0 2015-11-09 15:31 ./usr/share/
		drwxr-xr-x root/root         0 2015-11-09 15:31 ./usr/share/doc/
		drwxr-xr-x root/root         0 2015-11-09 15:31 ./usr/share/doc/unzip/
		-rw-r--r-- root/root     21541 2009-04-20 00:02 ./usr/share/doc/unzip/History.600.gz
		-rw-r--r-- root/root      4074 2015-11-09 15:31 ./usr/share/doc/unzip/copyright
		-rw-r--r-- root/root      9113 2009-04-19 23:10 ./usr/share/doc/unzip/ToDo
		-rw-r--r-- root/root      1509 2015-11-09 15:31 ./usr/share/doc/unzip/changelog.Debian.gz
		-rw-r--r-- root/root      4633 2009-01-02 16:33 ./usr/share/doc/unzip/BUGS
		-xr-x root/root         0 2015-11-09 15:31 ./usr/share/man/
		drwxr-xr-x root/root         0 2015-11-09 15:31 ./usr/share/man/man1/
		-rw-r--r-- root/root      5519 2015-11-09 15:31 ./usr/share/man/man1/unzipsfx.1.gz
		-rw-r--r-- root/root      8894 2015-11-09 15:31 ./usr/share/man/man1/zipinfo.1.gz
		-rw-r--r-- root/root      1986 2015-11-09 15:31 ./usr/share/man/man1/funzip.1.gz
		-rw-r--r-- root/root     18138 2015-11-09 15:31 ./usr/share/man/man1/unzip.1.gz
		-rw-r--r-- root/root      1551 2015-11-09 15:31 ./usr/share/man/man1/zipgrep.1.gz
		drwxr-xr-x root/root         0 2015-11-09 15:31 ./usr/bin/
		-rwxr-xr-x root/root    161428 2015-11-09 15:31 ./usr/bin/zipinfo
		hrwxr-xr-x root/root         0 2015-11-09 15:31 ./usr/bin/unzip link to ./usr/bin/zipinfo
		-rwxr-xr-x root/root     17796 2015-11-09 15:31 ./usr/bin/funzip
		-rwxr-xr-x root/root      2953 2015-11-09 15:31 ./usr/bin/zipgrep
		-rwxr-xr-x root/root     71268 2015-11-09 15:31 ./usr/bin/unzipsfx
		drwxr-xr-x root/root         0 2015-11-09 15:31 ./usr/lib/
		drwxr-xr-x root/root         0 2015-11-09 15:31 ./usr/lib/mime/
		drwxr-xr-x root/root         0 2015-11-09 15:31 ./usr/lib/mime/packages/
		-rw-r--r-- root/root        65 2015-11-09 15:31 ./usr/lib/mime/packages/unzip
		lrwxrwxrwx root/root         0 2015-11-09 15:31 ./usr/share/doc/unzip/changelog.gz -> History.600.gz


2. `-f`,`--fieldarchive[control-field...]`: 列出套件的控制資訊, ex:

		$ dpkg -f ./unzip_6.0-4ubuntu2.5_i386.deb
		Package: unzip
		Version: 6.0-4ubuntu2.5
		Architecture: i386
		Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
		Installed-Size: 375
		Depends: libbz2-1.0, libc6 (>= 2.4)
		Suggests: zip
		Conflicts: unzip-crypt (<< 5.41)
		Replaces: unzip-crypt (<< 5.41)
		Section: utils
		Priority: optional
		Homepage: http://www.info-zip.org/UnZip.html
		Description: De-archiver for .zip files
		 InfoZIP's unzip program. With the exception of multi-volume archives
		 (ie, .ZIP files that are split across several disks using PKZIP's /& option),
		 this can handle any file produced either by PKZIP, or the corresponding
		 InfoZIP zip program.
		 .
		 This version supports encryption.
		Original-Maintainer: Santiago Vila <sanvila@debian.org>

3. `-I`,`--infoarchive[control-file]`: 列出套件的詳細資訊

		$ sudo dpkg -I unzip_6.0-4ubuntu2.5_i386.deb
 		new debian package, version 2.0.
 		size 183074 bytes: control archive=1243 bytes.
 		    720 bytes,    20 lines      control
 		    963 bytes,    16 lines      md5sums
 		    111 bytes,     5 lines   *  postinst             #!/bin/sh
 		     78 bytes,     5 lines   *  postrm               #!/bin/sh
     	Package: unzip
     	Version: 6.0-4ubuntu2.5
     	Architecture: i386
     	Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
     	Installed-Size: 375
     	Depends: libbz2-1.0, libc6 (>= 2.4)
     	Suggests: zip
     	Conflicts: unzip-crypt (<< 5.41)
     	Replaces: unzip-crypt (<< 5.41)
     	Section: utils
     	Priority: optional
     	Homepage: http://www.info-zip.org/UnZip.html
     	Description: De-archiver for .zip files
	 	 InfoZIP's unzip program. With the exception of multi-volume archives
	 	 (ie, .ZIP files that are split across several disks using PKZIP's /& option),
	 	 this can handle any file produced either by PKZIP, or the corresponding
	 	 InfoZIP zip program.
	 	 .
	 	 This version supports encryption.
	 	Original-Maintainer: Santiago Vila <sanvila@debian.org>

4. `-l`,`--list package-name-pattern...`: 列出與關鍵字有關的所有套件, Ex:

		$ dpkg -l vim
		Desired=Unknown/Install/Remove/Purge/Hold
		| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
		|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
		||/ Name           Version      Architecture Description
		+++-==============-============-============-=================================
		ii  vim            2:7.4.052-1u amd64        Vi IMproved - enhanced vi editor


5. `-L`,`--list filespackage-name...`: 列出套件所安裝的檔案

		$ dpkg -L vim
		/.
		/usr
		/usr/bin
		/usr/bin/vim.basic
		/usr/share
		/usr/share/lintian
		/usr/share/lintian/overrides
		/usr/share/lintian/overrides/vim
		/usr/share/bug
		/usr/share/bug/vim
		/usr/share/bug/vim/presubj
		/usr/share/bug/vim/script
		/usr/share/doc
		/usr/share/doc/vim

6. `-i`或是`install`: 安裝指定的套件

		$ sudo dpkg -i unzip_6.0-4ubuntu2.5_amd64.deb
		Selecting previously unselected package unzip.
		(Reading database ... 51150 files and directories currently installed.)
		Preparing to unpack unzip_6.0-4ubuntu2.5_amd64.deb ...
		Unpacking unzip (6.0-4ubuntu2.5) ...
		Setting up unzip (6.0-4ubuntu2.5) ...
		Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
		Processing triggers for mime-support (3.54ubuntu1.1) ...

7. `-r`或是`--remove`或是`deinstall`: 移除指定的套件, 但保留設定檔

		$ sudo dpkg -r unzip

8. `-P`或是`--purge`或是`purge`: 完全移除指定的套件, 不保留設定檔

		$ sudo dpkg -P unzip
		(Reading database ... 51168 files and directories currently installed.)
		Removing unzip (6.0-4ubuntu2.5) ...
		Purging configuration files for unzip (6.0-4ubuntu2.5) ...
		Processing triggers for mime-support (3.54ubuntu1.1) ...
		Processing triggers for man-db (2.6.7.1-1ubuntu1) ...

#### dpkg-reconfigure ####

dpkg-reconfigure是一個讓我們重新設定deb套件安裝的指令,甚至允許我們調整套件內的部分設定

- `-a`或`--all`: 重新設定所有使用debconf安裝的套件
- `-u`或`--unseen-only`: 預設會在畫面上顯示所有問題, 包括已經回答的問題。加上這參數後只會顯示尚未回答的問題
- `--force`: 強迫重新設定套件, 即便該套件不相容或已經毀損
- `--no-reload`: 防止重新載入範本
- -h或--help

#### alien ####

alien這指令是一個Deb與RPM相互轉換的工具:

- `-c`或`--scripts`: 當新增或移除套件時, 使用給定的script去轉換套件
- `-d`或`--to-deb`: 產生debian套件,　這是預設值
- `--fixperms`: 修正檔案的擁有者與權限, 僅對產生DEB檔有效, 預設值關閉
- `-g`或`generate`: 為套件產生一個暫時的目錄, 但不實際新增套件
- `-h`或`--help`
- `-i`或`--install`: 自動安裝產生的套件, 並在安裝後移除該套件
- `-k`或`--keep-version`: 指令執行時預設會增加小版本的編號。如果加上此參數, 則會維持原版本的號碼
- `--patch=patch`: 指定patch的路徑, 只有在建立Deb套件時會生效, 預設路徑為/var/lib/alien
- `--to-rpm`或`-r`: 產生rpm套件
- `--to-tgz`或`-t`: 產生tgz套件
- `--to-slp`: 產生slp套件
- `--to-pkg`或`-p`: 產生Solaris套件
- `-s`或`--single`: 如同`-g`, 但不產生「套件名稱.orig」的目錄, 用於節省空間
- `-T`或`--test`: 測試產生的套件, 僅支援Debian的套件
- `--version=版本號碼`: 指定套件的版本號碼, 這參數只對tgz套件有效。如果沒加參數「=版本號碼」, 就等同於-V。
- `-v`或`--verbose`: 列出詳細的執行過程

### apt-get指令與設定檔 ###

DEB基本指令為dpkg, 但由於dpkg的使用需要一次下載所有需要的套件, 因此較少被使用。

要全部下載顯得十分麻煩, 因此產生apt-get工具, 可自動檢查需安裝或升級的套件, 當確定要安裝, 自動從網路下載並一次裝好。

    $ sudo apt-get install -y alpine
    Reading package lists... Done
    Building dependency tree
    Reading state information... Done
    The following extra packages will be installed:
      alpine-doc mlock
    Suggested packages:
      aspell mail-transport-agent exim4
    The following NEW packages will be installed:
      alpine alpine-doc mlock
    0 upgraded, 3 newly installed, 0 to remove and 82 not upgraded.
    Need to get 3,548 kB of archives.
    After this operation, 8,278 kB of additional disk space will be used.
    Get:1 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty/universe mlock amd64 8:2007f~dfsg-2 [12.2 kB]
    Get:2 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty/universe alpine amd64 2.10+dfsg-1 [3,146 kB]
    Get:3 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty/universe alpine-doc all 2.10+dfsg-1 [389 kB]
    Fetched 3,548 kB in 1s (2,202 kB/s)
    Selecting previously unselected package mlock.
    (Reading database ... 56991 files and directories currently installed.)
    Preparing to unpack .../mlock_8%3a2007f~dfsg-2_amd64.deb ...
    Unpacking mlock (8:2007f~dfsg-2) ...
    Selecting previously unselected package alpine.
    Preparing to unpack .../alpine_2.10+dfsg-1_amd64.deb ...
    Unpacking alpine (2.10+dfsg-1) ...
    Selecting previously unselected package alpine-doc.
    Preparing to unpack .../alpine-doc_2.10+dfsg-1_all.deb ...
    Unpacking alpine-doc (2.10+dfsg-1) ...
    Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
    Setting up mlock (8:2007f~dfsg-2) ...
    Setting up alpine (2.10+dfsg-1) ...
    Setting up alpine-doc (2.10+dfsg-1) ...
    
※APT是Advanced Package Tool的縮寫, 可用來管理DEB檔(副檔名為deb檔案), 檢查相依性, 並可直接從網路下載並安裝。

#### 下載的來源設定 ####

設定在`/etc/apt/sources.list`之中。

	$ cat /etc/apt/sources.list
	## Note, this file is written by cloud-init on first boot of an instance
	## modifications made here will not survive a re-bundle.
	## if you wish to make changes you can:
	## a.) add 'apt_preserve_sources_list: true' to /etc/cloud/cloud.cfg
	##     or do the same in user-data
	## b.) add sources in /etc/apt/sources.list.d
	## c.) make changes to template file /etc/cloud/templates/sources.list.tmpl
	#

	# See http://help.ubuntu.com/community/UpgradeNotes for how to upgrade to
	# newer versions of the distribution.
	deb http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty main restricted
	deb-src http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty main restricted

	## Major bug fix updates produced after the final release of the
	## distribution.
	deb http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty-updates main restricted
	deb-src http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty-updates main restricted

	## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
	## team. Also, please note that software in universe WILL NOT receive any
	## review or updates from the Ubuntu security team.
	deb http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty universe
	deb-src http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty universe
	deb http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty-updates universe
	deb-src http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty-updates universe

	## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
	## team, and may not be under a free licence. Please satisfy yourself as to
	## your rights to use the software. Also, please note that software in
	## multiverse WILL NOT receive any review or updates from the Ubuntu
	## security team.
	deb http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty multiverse
	deb-src http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty multiverse
	deb http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty-updates multiverse
	deb-src http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty-updates multiverse

	## Uncomment the following two lines to add software from the 'backports'
	## repository.
	## N.B. software from this repository may not have been tested as
	## extensively as that contained in the main release, although it includes
	## newer versions of some applications which may provide useful features.
	## Also, please note that software in backports WILL NOT receive any review
	## or updates from the Ubuntu security team.
	deb http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty-backports main restricted universe multiverse
	deb-src http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty-backports main restricted universe multiverse

	## Uncomment the following two lines to add software from Canonical's
	## 'partner' repository.
	## This software is not part of Ubuntu, but is offered by Canonical and the
	## respective vendors as a service to Ubuntu users.
	# deb http://archive.canonical.com/ubuntu trusty partner
	# deb-src http://archive.canonical.com/ubuntu trusty partner

	deb http://security.ubuntu.com/ubuntu trusty-security main
	deb-src http://security.ubuntu.com/ubuntu trusty-security main
	deb http://security.ubuntu.com/ubuntu trusty-security universe
	deb-src http://security.ubuntu.com/ubuntu trusty-security universe
	# deb http://security.ubuntu.com/ubuntu trusty-security multiverse
	# deb-src http://security.ubuntu.com/ubuntu trusty-security multiverse

這設定檔的格式為:

	deb/deb-src 來源 發布類型 [元件1] [元件2]

分述如下:

- deb或deb-src: deb代表一般套件, deb-src代表該套件的原始檔。
- 來源: 來源代表套件的來源, 可能是CD-ROM、http路徑、FTP路徑、目錄、或是ssh。
- 發布類型: 包含穩定的、不穩定的、與測試性的三大類, 通常位於來源的目錄之下。目前常見的穩定發布類型包括trusty、trusty-updates、trusty-security等。
- 元件: 元件可能為main(主要的)、universe(全域通用)、multiverse(複合的)、restricted(受限的)等。

如果有較接近、或連線速度較快的鏡像站台, 可自行定義在這檔案中。

#### apt-get ####

語法: apt-get [參數] [執行功能]

[參數]:

- `-b`: 下載完成後進行編譯
- `-c`: 指定設定檔
- `-d`: 只下載套件, 不進行解壓縮或是安裝
- `-f`: 適當修正斷掉的關聯性
- `-h`: 顯示線上說明
- `-m`: 忽略遺失的套件
- `-q`: 安靜模式
- `-s`: 模擬安裝過程, 不實際動作
- `-y`: 將所有的詢問以y回應, 因此不會出現交談模式
- `-u`: 顯示將被升級的套件
- `-v`: 顯示版本訊息
- `-V`: 顯示需升級的完整套件名稱
- `--auto-remove`: 自動移除不用的相依性套件
- `--diff-only`: 只接受diff的原始檔
- `--dsc-only`: 只接受dsc的原始檔
- `--force-yes`: 強迫所有的詢問都回答yes
- `--ignore-hold`: 忽略存放在本地的套件
- `--list-cleanup`: 自動清除套件列表, 預設值是開啟
- `--no-download`: 不下載套件, 最好能搭配`--ignore-missing`使用
- `--no-remove`: 不移除原有的套件
- `--no-upgrade`: 不要升級現有的套件
- `--only-source`: 只接受原始碼的安裝
- `--purge`: 清除套件, 列出將要移除的套件, 在套件前會顯示*號
- `--reinstall`: 重新安裝已安裝的套件至最新版本
- `--tar-only`: 只接受tar的原始檔

[執行功能]:

- `autoclean`: 自動清除下載的暫存檔
- `autoremove`: 自動移除不需要的套件
- `build-dep`: 安裝或移除時檢驗原始檔的相依性
- `check`: 更新套件的暫存區並檢驗套件的相依性
- `clean`: 清除下載的暫存區
- `dist-upgrade`: 升級並管理新版套件
- `install`: 安裝套件
- `remove`: 移除指定的套件, 若加上-號, 則相同於安裝
- `purge`: 移除套件並清除設定檔
- `source`: 取得原始檔
- `update`: 重新更新套件的指標檔/etc/apt/sources.list
- `upgrade`: 升級已經安裝在系統中的套件, 記錄在/etc/apt/sources.list之中

    $ sudo apt-get install unzip
    Reading package lists... Done
    Building dependency tree
    Reading state information... Done
    Suggested packages:
      zip
    The following NEW packages will be installed:
      unzip
    0 upgraded, 1 newly installed, 0 to remove and 82 not upgraded.
    Need to get 157 kB of archives.
    After this operation, 395 kB of additional disk space will be used.
    Get:1 http://ap-northeast-1.ec2.archive.ubuntu.com/ubuntu/ trusty-updates/main unzip amd64 6.0-9ubuntu1.5 [157 kB]
    Fetched 157 kB in 0s (6,215 kB/s)
    Selecting previously unselected package unzip.
    (Reading database ... 57030 files and directories currently installed.)
    Preparing to unpack .../unzip_6.0-9ubuntu1.5_amd64.deb ...
    Unpacking unzip (6.0-9ubuntu1.5) ...
    Processing triggers for man-db (2.6.7.1-1ubuntu1) ...
    Processing triggers for mime-support (3.54ubuntu1.1) ...
    Setting up unzip (6.0-9ubuntu1.5) ...

移除apache2:

	# /etc/init.d/apache2 stop
	# 
	# apt-get remove apache2

#### apt-cache ####

apt-cache這指令是Deb套件的快取工具, 可列出所有已安裝的套件資訊。參數說明如下:

- `-c`: 讀取指定的設定檔
- `-h`: 顯示線上說明畫面
- `-o`: 指定設定檔選項
- `-v`: 顯示版本資訊

執行功能說明如下:

- `add`: 將套件檔加入原始碼快取中
- `gencaches`: 同時產生套件和原始碼的快取
- `showpkg`: 顯示套件的詳細資訊
- `showsrc`: 顯示原始碼的詳細資訊
- `stats`: 顯示基本的統計資訊
- `dump`: 簡潔的格式列出快取中的所有套件列表
- `unmet`: 列出不滿足相依關係的套件
- `search`: 搜尋套件列表
- `show`: 顯示套件內容
- `depends`: 顯示套件的相依關係
- `rdepends`: 列出所有相依於該套件的所有套件
- `pkgnames`: 列出系統中的所有套件
- `policy`: 顯示該套件可安裝的版本資訊

顯示套件的內容:

	$ sudo apt-cache showpkg vim
	Package: vim
	Versions:
	2:7.4.052-1ubuntu3 (/var/lib/apt/lists/ap-northeast-1.ec2.archive.ubuntu.com_ubuntu_dists_trusty_main_binary-amd64_Packages) (/var/lib/dpkg/status)
	 Description Language:
	                 File: /var/lib/apt/lists/ap-northeast-1.ec2.archive.ubuntu.com_ubuntu_dists_trusty_main_binary-amd64_Packages
	                  MD5: 59e8b8f7757db8b53566d5d119872de8
	 Description Language: en
	                 File: /var/lib/apt/lists/ap-northeast-1.ec2.archive.ubuntu.com_ubuntu_dists_trusty_main_i18n_Translation-en
	                  MD5: 59e8b8f7757db8b53566d5d119872de8


	Reverse Depends:
      vim-syntax-go,vim
      vim-syntax-docker,vim
      mercurial,vim
      exuberant-ctags,vim
      byobu,vim
      vimhelp-de,vim 2:7.5
      vimhelp-de,vim 2:7.4
      vim-vimoutliner,vim
      vim-syntax-go,vim
      vim-syntax-docker,vim
      vim-scripts,vim
      vim-scripts,vim
      vim-rails,vim
      vim-migemo,vim
      vim-latexsuite,vim
      vim-latexsuite,vim
      vim-gocomplete,vim
      vim-addon-manager,vim
      supercollider-vim,vim
      ocaml-tools,vim
      mercurial,vim
      libvi-quickfix-perl,vim
      libtext-vimcolor-perl,vim
      kde-developer-sdk,vim
      halibut,vim
      gworkspace-apps-wrappers,vim
      edubuntu-desktop,vim
      debian-reference-common,vim
      vim-runtime,vim
      vim-dbg,vim 2:7.4.052-1ubuntu3
      vim-common,vim
      exuberant-ctags,vim
      byobu,vim
	Dependencies:
	2:7.4.052-1ubuntu3 - vim-common (5 2:7.4.052-1ubuntu3) vim-runtime (5 2:7.4.052-1ubuntu3) libacl1 (2 2.2.51-8) libc6 (2 2.15) libgpm2 (2 1.20.4) libpython2.7 (2 2.7) libselinux1 (2 1.32) libtinfo5 (0 (null)) ctags (0 (null)) vim-doc (0 (null)) vim-scripts (0 (null))
	Provides:
	2:7.4.052-1ubuntu3 - editor
	Reverse Provides:
	vim-nox 2:7.4.052-1ubuntu3
	vim-gtk 2:7.4.052-1ubuntu3
	vim-athena 2:7.4.052-1ubuntu3
	vim-gnome 2:7.4.052-1ubuntu3

※apt-cahce與dpkg是兩個可以顯示已安裝套件的指令。

## Red Hat與Fedora套件說明 ##

RPM是Linux誕生以來最廣泛被使用的套件管理工具。

### 認識	RPM ###

RPM是RedHat Package Manager的縮寫, 由Red Hat公司所研發的套件管理工具。包含Red Hat、Fedora、Mandriva與SuSE等都用它來作為套件管理工具。此套件工具使用指令為rpm, 管理套件附檔名也是rpm。

※每間發行商所推出的RPM檔通常有所區別, 即使同一間發行商推出的RPM, 也會根據系統版本而有所不同。

#### RPM的優缺點 ####

RPM的優點:

1. 檔案本身是編譯過的可執行檔, 使用者不需編譯, 也不需編譯器(C語言編譯器gcc、make等)
2. 安裝之前, RPM會先檢查磁碟容量、系統版本、套件相依性等, 可避免安裝錯誤
3. 管理的方式使用資料庫的方式記錄RPM的相關參數, 便於升級、移除、查詢與驗證

RPM的缺點:

1. 自制化的空間小, 如無法彈性的新增或移除套件內的模組、無法指定安裝路徑等
2. 必須針對特定的系統或機器掏選適合的RPM, 若是沒有, 則必須尋找原始檔安裝。

#### RPM的檔名涵義 ####

從RPM檔名可了解許多資訊, 而這些資訊是以「-」做區隔, 檔案格式如下:

**套件名稱-版本-更新次數.[發行商].適合的硬體平台.rpm**

ex: sendmail-8.14.2-4.fc9.i386.rpm、 cups-1.3.7-1.fc9.i386.rpm、 kernel-2.6.25-14.fc9.i686.rpm、 gcc-4.3.0-8.i386.rpm

1. 套件名稱
2. 版本
3. 更新次數
4. 發行商: 部分發行商, 會將自身的系統版本加在RPM的資訊之中, 如fc12(Fedora 12)、fc13(Fedora 13)等, 這並非必須存在的名稱。
5. 適合的硬體平台

#### rpm檔案的內容 ####

rpm本身是一個包裏檔, 換言之, 就是一堆檔案打包所產生的一個檔案。這檔案包含執行檔、設定檔、暫存檔等, 我們可透過`rpm2cpio`這指令將RPM的內容解開, 就可看到該rpm檔所包含的內容。

從上面介紹我們得知, rpm檔只是一堆檔案的集合, 透過rpm這指令做正確性與相依性的判斷, 再去安裝或移除我們需要的應用程式。

※ 使用rpm指令安裝RPM除相依性檢驗外, **就只是檔案的複製到正確位置**。

### rpm相關指令的操作 ###

RPM是Red Hat系列上的套件管理指令, 可用來升級、安裝、移除軟體。以下將介紹rpm與rpm2cpio。

#### rpm ####

語法: rpm [參數] [套件名稱]

1. 查詢

	當我們要查詢本機上所安裝的RPM, 可使用-q這個參數, 再搭配其他參數可達到不同效果, 如使用-qa僅會顯示套件名稱; 使用-qi會顯示安裝的詳細資訊; 使用-ql會出現該套件的所有安裝檔案。
	
	1. `-qa`: 顯示所安裝套件
	2. `-qi`: 顯示所安裝的詳細資訊
	3. `-ql`: 顯示所安裝的位置以及檔案

2. 安裝

	1. `-ivh`

		我們可透過`-i`這參數安裝RPM, 但通常我們會加上`-vh`, 也就是使用`-ivh`參數, 以便了解安裝過程, 底下以安裝postfix為例:

			cp /media/Fedora\ ||\ i386\ DVD\Packages/postfix-2.5.6-3.fc12.i586.rpm ./

		接著使用rmp安裝

			rpm -ivh postfix-2.5.6-3.fc12.i586.rpm

3. 升級

	可透過`-U`的參數升級套件, 或是`-F`更新套件, 一般用`-Uvh`或是`-Fvh`來做套件更新的動作
	
	1. `-Uvh`:　Ｕ代表Upgrade, 意思為升級套件
	2. `-Fvh`: F代表Freshen, 意思為更新套件

4. 移除

	1. `-e`: 使用`-e`參數移除RPM, 若不知RPM正確名稱, 可使用上面介紹的查詢功能來確認(`-qa`)

			rpm -e postfix-2.5.6-3

#### rpm2cpio ####

可使用`rpm2cpio`這指令將RPM的內容解開, 以便了解RPM當中有哪些檔案與其中的設定, 語法:

	rpm2cpio <rpm名稱> | cpio -idv

執行結果如下:

	rpm2cpio postfix-2.5.6-3.fc12.i586.rpm | cpio -idv

### yum指令的操作 ###

當我們安裝RPM, 會有套件依賴性問題, 裝A要B, 裝B要C與D。因此手動安裝RPM有其麻煩之處, 也因此產生一個新的套件安裝工具YUM。

YUM是Yellow dog Updater, Modifier的縮寫, 他是一個RPM進化版的更新工具。他會自動計算RPM的相依性, 並告訴我們需要安裝哪些相關的套件, 便於我們安裝具有相依性的套件。底下列出他的特點:

1. 多重資料儲存
2. 簡單的設定檔
3. 正確的相依性檢驗
4. 快速的運算
5. 與rpm一致性
6. 具備群組更新儲庫
7. 簡單的介面

目前沒有圖形介面, 所有操作必須透過指令進行。對於yum有個必備的設定檔:`/etc/yum.conf`。套件的更新來源設定檔位於`/etc/yum.repos.d/`之下命名為`.repo`檔, 如在CentOS下名為`CentOS-Base-repo`

#### yum的指令說明 ####

1. **列出所有可更新的套件清單**

	指令: `yum check-update`

2. **安裝所有更新套件**

	指令: `yum update`

3. **僅安裝指定的套件**

	指令: `yum install 套件名稱`

	如同RPM的安裝一般, 他只會安裝預設的套件, 因此若希望安裝所有相關的套件, 不妨加個`*`。譬如要安裝sendmail, 可使用: `yum install sendmail*`

4. **列出所有可安裝的套件清單**

	指令: `yum list`

	若想知道本機系統上, 有哪些套件需要更新, 可輸入指令: `yum check-update`, 若要自動更新全部, 可輸入指令: `yum update`。

	這指令不建議使用。如果系統上的某些程式需要特定版本的核心、函式庫、編譯器等, 更新全部套件會發生不可預期的損害。可試著加個-y的參數來一項一項詢問是否更新: `yum -y update`

5. **移除相關套件**

	指令: `yum remove 套件名稱`

	安裝時會從網站下載rpm檔, 放置在`/var/cache/yum`之下。如果要清除已經下載的rpm, 可執行:

	`yum clean 套件名稱`

6. 搜尋與套件相關資訊

	套件名稱搜尋: `yum search 套件名稱`

	套件內含檔案搜尋: `yum provides 套件名稱`

	查詢套件資訊: `yum info 套件名稱`

## 原始檔的安裝與管理 ##

原始檔是一個包含許多C語言原始碼與子目錄的目錄, 我們必須透過C的編譯器將它編成執行檔, 並放到我們指定的目錄下。原始檔的好處就是相當的彈性, 可以完全根據我們要的模組或功能來編譯, 不受平台的限制; 缺點就是必須有編譯器, 且過程較為複雜。

原始檔由多檔案與目錄所構成, 因此通常會放置在一個目錄之下並以壓縮檔(tar.gz、tar.Z、zip、tar.bz2)的格式供人下載, 因此下載後必須先解開才能執行後續動作。

- 線上更新紀錄版本相依性驗證: 套件管理工具Yes; 原始檔安裝No
- 安裝個別模組指定安裝位置: 套件管理工具No; 原始檔安裝Yes

※ 除gentoo的Portage可安裝個別模組外, 其餘管理工具都無法變更。

套件管理工具與原始檔安裝互有優缺。原始檔安裝擁有最佳的彈性, 但在整理管理上,就遠不如套件管理工具。此外, 相依性檢驗也是一把雙面刃, 對於一開始的安裝, 確實有便利之處, 但在之後的套件更新上, 將會牽一髮動全身。e.g. 安裝PHP與Apache, 可能因為更新GNOME而必須連帶更新PHP, 並隨之更新Apache,  而導致部分網頁程式無法使用。

※ 對於相依性程度高的套件, 我們可採用原始檔安裝, 避免某些套件要更新時需要一併更新一大群套件。

#### 原始檔安裝 ####

這邊介紹的軟體安裝, 將不透過RPM, 而以直接下載套件原始檔來安裝。

一般所下載的原始檔, 通常附檔名為gz、tgz、bz2壓縮格式, 安裝步驟通常為五步:

1. 下載原始檔
2. 解開壓縮檔並進入解開後的目錄

	為節省磁碟容量與檔案下載時間, 下載的原始檔都是壓縮檔, 如gz、tgz、bz2。

	tar為打包的指令, 會把一群檔案或目錄打包成一個tar檔, 本身不具備壓縮功能, 變成tar後再使用壓縮指令變成壓縮檔, 如tar.gz(或tgz)、tar.bz2、tar.xz。網路上放置的原始檔都放在同目錄之下。

	當拿到壓縮檔, 須根據附檔名挑選適合的解壓縮指令或是參數將壓縮檔解開為目錄。

		附檔名		|	解壓縮指令(解為tar檔)	|	使用的tar參數
		----------------------------------------------------
		tar.gz或tgz	|	gzip或gunzip			|	-z
		tar.bz2		|	bzip2或bunzip2		|	-j
		tar.xz		|	xz					|	-J

	使用解壓縮指令:

		bunzip2 tar -xvf A.tar

	A.tar.bz2 -> A.tar -> A目錄

	---

	使用tar參數, 可一步解開為目錄:

		tar -jxvf A.tar (把參數j加在原有的tar參數之中即可)

	A.tar.bz2 -> A目錄

3. 設定參數並編譯原始檔

	編譯的第一步為設定參數, 通常是執行.configure, 編譯完成後會產生Makefile, 以便後續的編譯動作。

	第二步為執行make, 也就是從原始檔中編譯而產生執行檔、設定檔、紀錄檔、說明檔等相關檔案。

4. 將編譯好的檔案及資料夾放到適當的位置

	我們可執行make install將檔案與資料夾放到適當的位置, 一般而言, 設定檔會放在/etc之下, 執行檔會放在/usr/bin/之下, 記錄檔會放在/var/log/之下。

5. 設定正確的權限

	對於執行所需的相關檔案, 必須給予正確的權限設定

以Apache 2.2.15為例:

1. 下載原始檔並解壓

		# wget http://apache.ntu.edu.tw/httpd/httpd-2.2.15.tar.gz
		# tar zxvf httpd-2.2.15.tar.gz
		# cd httpd-2.2.15
		# ls

2. 設定參數

	我們希望將apache安裝至`/opt/apache`(這目錄預設並不存在, 必須使用`mkdir /opt/apache`去新增一個目錄)之下, 因此在configure之後加上`--prefix`的參數:

		./configure --prefix=/opt/apache

	※ 要編譯原始檔, 必須要安裝編譯器。如果使用Fedora或Red Hat, 可透過「yum install gcc」來安裝, 如果使用Ubuntu, 可透過「apt-get install gcc」來安裝。

3. 編譯原始檔

	執行make

	※ 若系統上沒安裝make, 在Fedora或Red Hat上, 可透過「yum install make」來安裝; 在Ubuntu上, 可透過「apt-get install make」來安裝。

	將原始檔編譯成必要的執行檔、設定檔、紀錄檔以及函式庫等等。

5. 將編譯好的檔案及資料夾放到適當的位置

	執行`make install`, 將編譯後的檔案放到特定的位置之下。

	以上例而言, 編譯後檔案放置在`/opt/apache`之下, 設定檔放在`/opt/apache/conf/`之下, 執行檔放在`/opt/apache/bin/`之下, 記錄檔放在`/opt/apache/logs/`之下。

		# ls -l /opt/apache/

6. 啟動apache

	使用root啟動apache,　則不須額外的權限設定:

		/opt/apache/bin/apachectl start

	如果開啟防火牆, 必須將port 80加入防火牆的允許規則內, 或執行以下指令關閉防火牆:

		iptables -F

	執行後可開啟瀏覽器輸入該機器IP address。

#### 移除原始檔安裝的套件 ####

