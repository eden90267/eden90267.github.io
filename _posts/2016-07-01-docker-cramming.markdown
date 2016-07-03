---
layout: post
title:  "Docker cramming"
date:   2016-07-01 09:29:00 +0800
categories: [linux, lpic]
---

# Docker惡補 #

準備參加2016 DevOps summit大會, 對於Docker的理論與實作還不是很熟悉, 所以藉此文章惡補一下, 僅提供個人認為需要補的內容...

原文: [https://www.gitbook.com/book/joshhu/docker_theory_install/details](https://www.gitbook.com/book/joshhu/docker_theory_install/details "最完整的Docker聖經 - Docker原理圖解及全環境安裝")

## 從docCloud平台管理到Docker ##

IDC業者在提供服務時的要點:

- 以Linux平台為主
- 大部分的平台服務的使用均非極端狀況
- VM粒度太大, 造成資源浪費
- VM的Hypervisor必須模擬硬體, 無法使用原生硬體的效能
- DevOps人員無法避免直接接觸Sysadmin的工作
- 客戶要求快速的啟動時間
- 客戶要求更彈性的服務組合
- 客戶要求更快速的服務升級/更新時間


針對PaaS客戶需求設計的Docker, 達成以下目的:

- 不需用到Hypervisor - 直接利用硬體效能
- Linux核心現有功能 - 保持不同distro的Linux相容性
- 放棄大量VM的sysadmin功能, 如HA、FT等, 這些工作交由系統層級實作
	
	可參閱這篇文章: [http://www.ithome.com.tw/node/84956](http://www.ithome.com.tw/node/84956 "vSphere 5.5強化應用容錯與記憶體防護")

- 保留VM的操作概念, 如CPU、記憶體的分配、獨立的網路拓樸 - 符合目前的操作習慣
- 拋棄VM檔案系統的概念, 使用層次化的檔案系統 - 維持Container的輕便
- 使用主從架構, 有Docker Daemon及Docker Client - 便於自訂開發
- 類似應用程式的執行方式 - 用「參數」代表不同的設定值

## 了解Docker的原理 ##

來看一行標準的Docker指令:

	docker run -d --name web -p 8080:80 -v /home/joshhu/html:/html joshhu/webdemo

就這行指令, 可立即建立一個支援Apache/php的網頁服務, 這網頁服務存在本機的/home/joshhu/html目錄下, 並且使用主機的8080埠, 夠簡單吧!

## Docker的最大特色 ##

- 是一個**OS等級虛擬化**的產品
- 使用**Go語言**開發
- 實作了**Linux kernel**功能
- Modules的功能來完成其「虛擬化」的「**長相**」
- 在呼叫Linux的核心提供的虛擬化模組時, 0.9版前還需依賴LXC、libvirt及systemd-nspawn的功能, 0.9版之後, 就預設使用自己開發的`libcontainer`來呼叫(也可向前相容)
- 100%**原生**硬體的效能
- VM等級的**隔離**及**資源分配**
- **應用程式**等級的**輕量**及方便
- 由**唯讀**的**多層次**映像檔作為模板(用來產生Container)
- 產生Container後, 保持**最上層可寫入**(用來提供服務)
- 服務完全**可攜**, 可以讓Docker在不同VM/機器上跑來跑去

在擁有最後三點特色之後, Docker完全擺脫LXC的影子, 讓Container再度進化, 變成又小又輕又可攜, 一下子成為DevOps最愛的好物!

注意-和LXC最大的區別是

相對於LXC, Docker是更輕量的Container, LXC較像VM, Docker較像應用程式。針對同一個應用, LXC仍需要建立Container、下載作業系統、安裝平台軟體、安裝應用程式。但對Docker來說, 一行帶參數的指令就完成上述所有的動作。

## Docker的元件-Linux核心部分 ##

一個能執行Docker的系統分為兩大部分:

- Linux的核心元件
- Docker相關元件

Docker使用的Linux核心模組功能包括下列各項:

- Namespace - 用來隔離不同Container的執行空間
- Cgroup - 用來分配硬體資源
- AUFS(chroot) - 用來建立不同Container的檔案系統
- SELinux - 用來確保Container的網路安全
- Netfilter - 建立Container埠為基礎的網路防火牆封包過濾
- AppArmor - 保護Container的網路及執行安全
- Linux Bridge - 讓不同Container或不同主機上的Container能溝通

## Docker的元件 - Docker核心部分 ##

系統要能執行Docker, 除前述的Linux核心元件外, 就是Docker部份了。

### Docker的執行元件 ###

- Docker client - 呼叫Docker Daemon(本機或其他客戶端)
- Docker daemon - 執行Docker功能並用(本機)
- libcontainer - 和Linux核心溝通的library(本機)
- Docker Image - 建立容器用的映象檔(本機或雲端映像庫)

通常**安裝Docker時, 就是同時安裝Docker客戶端、Docker daemon、libcontainer**, 此時客戶端和daemon是在同台電腦上的(使用unix:///var/unix.sock呼叫)。當然也可讓Docker的客戶端與daemon不再同台電腦使用, 但會有安全上的疑慮。此外你也可利用Docker提供的API來撰寫自己的客戶端, 預設Docker客戶端介面就是我們安裝Docker時的輸入指令。

## 這些元件如何合作建立Docker環境 ##

Docker執行非常簡單, 就是docker client呼叫Docker daemon, 然後daemon透過libcontainer呼叫Linux核心模組來完成建立容器的步驟。

### Docker執行過程 ###

1. 安裝Docker的Linux及硬體
2. 當準備建立Container時, 輸入指令即執行docker client。呼叫Docker daemon, 預設使用的通訊協定是`unix:///var/run/docker.sock`
3. Docker daemon透過`libcontainer`要求Linux核心建立Container
4. 此時Linux核心收到指示, 即啟動核心的`namespace`建立一個獨立空間, 包括`pid、network、IPC、UTS、mount`等namespace, daemon根據client的參數定義分配CPU、記憶體或磁碟IO等。**此時Container中沒有任何東西!**只有空殼沒內容。Container的空殼建立完成, 需要將真正的作業系統及應用程式放入這個空殼中
5. Daemon檢查本機的現有映像檔列表, 看要填入此Container空殼的映象檔之前有沒有下載過
6. 有的話, 直接從本機載入Container中, 此時Container建立完成並啟動
7. 沒有, daemon到預設的Docker Registry, 根據client的參數, 下載適當的映象檔
8. 下載回來即將此映像檔填入Container的空殼, 此時Container即啟動完成

上面流程可得知, Docker Container執行的調整方式就是我們下的參數。事實上, 執行Docker時的參數, 就是決定Linux核心建立Namespace以及設定網路、儲存的方法。

※ 注意: Docker client可在另一台主機, 此時和Daemon的之間連線可用`https`取代`unix://`, 但十分危險, 除非你很確定安全性沒問題才要這麼做。

## 看個實例 ##

1. 確定這個linux環境是可執行Docker。包括安裝Docker, Linux的版本正確, 以及有連上公網。

		$ sudo docker version
		Client:
 		Version:      1.10.3
 		 API version:  1.22
 		 Go version:   go1.5.3
 		 Git commit:   20f81dd
 		 Built:        Thu Mar 10 15:54:52 2016
 		 OS/Arch:      linux/amd64

		Server:
 		 Version:      1.10.3
 		 API version:  1.22
 		 Go version:   go1.5.3
 		 Git commit:   20f81dd
 		 Built:        Thu Mar 10 15:54:52 2016
 		 OS/Arch:      linux/amd64
		$ lsb_release -r
		Release:	14.04
		vagrant@localhost ~ $ ifconfig eth0
		eth0      Link encap:Ethernet  HWaddr 08:00:27:49:0d:cd
		          inet addr:10.0.2.15  Bcast:10.0.2.255  Mask:255.255.255.0
		          inet6 addr: fe80::a00:27ff:fe49:dcd/64 Scope:Link
		          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
		          RX packets:659 errors:0 dropped:0 overruns:0 frame:0
		          TX packets:448 errors:0 dropped:0 overruns:0 carrier:0
		          collisions:0 txqueuelen:1000
		          RX bytes:69696 (69.6 KB)  TX bytes:60008 (60.0 KB)
		
2. 輸入Docker的指令, 即準備呼叫docker daemon: `docker run -d --name web -m 512m -p 8080:80 joshhu/webdemo`。這邊注意的是, 全部以**參數方式表現**, 如**記憶體限制**, **名稱**, **通訊埠對應**, **映像檔名稱**等。

	- `docker run`: docker client command
	- `web`: name of the container
	- `-m 512m`: setting system resource(512m memory)
	- `joshhu/webdemo`: the image to fill up container

3. 按下Enter之後, 即使使用`unix://var/run/unix.sock`呼叫docker daemon。由於joshhu/webdemo這個映像檔已經存在了, 因此就直接使用此映像檔。
4. 可輸入`showmem`看一下記憶體的使用情況

		# showmem
		web 12MB 512MB
		
5. 如果這個`joshhu/webdemo`影像檔沒在本機, 就會先去下載, 下載回來後, 再填入空的Container web中。

※ 注意: Container的ID及名稱

在Docker執行時, 如果你沒使用`--name <名稱>`的參數, Docker會主動幫這個Container取一個好玩的名稱。而不管你有沒有幫這個Container命名, Docker一定會產生一個全世界獨一無二的Container id。

# 全環境Docker的完整安裝 #

## 在Mac及Windows下安裝Docker ##

Mac及Windows無法直接原生使用Docker, 須使用Linux VM才行。使用VM方法分兩種, 一種是自行架設VM(VMWare Workstation/Fusion、VirtualBox), 再自行安裝Linux的VM, 然後再在這個VM中安裝Docker。

另一個使用方式, 則使用官方的boot2docker(現已是Docker toolbox), 雖也適用VM+Linux方式, 但為一次幫你完成上面所有動作, 並使用類似Windows Shell指令及Mac OS的終端視窗操作。

## boot2docker簡介 ##

boot2docker是一個專門在Mac及Windows下使用Docker的套件, 包括：

- 一個VirtualBox程式
- VirtualBox格式的極小Linux VM(24MB在記憶體, 開機只要5秒)
- 位於該VM的Docker程式
- Boot2Docker管理工具

此外, Boot2Docker管理工具本身就是一個輕量級的Linux VM, 專門用來在Mac OS中執行Docker Daemon。

## Mac OS使用boot2docker ##

### 使用Mac OS在VirtualBox網段的IP才行 ###

在正常的Linux Docker中, 可直接存取主機的IP以及對應的埠, 就可看到Apache啟動; 但在Mac OS的boot2docker之下無法看到網頁, 這是因為boot2docker是在一個VirtualBox的VM執行的, 只能先取得Mac的VirtualBox NAT IP。輸入`boot2docker ip`獲得Container執行VM的主機IP。

	$ boot2docker ip
	192.168.59.103
	
此時用VM IP對應埠, 才能看到測試網站的產生。

Boot2docker只是把一個VirtualBox的VM隱藏在記憶體中, 因此無法完全發揮正常Docker的功能, 這裡並不推薦。

## 在Windows下使用boot2docker ##

必須先確定CPU有支援VT-X, 可用CPU-Z來檢查。另外也必須到BIOS中確定VT-X有開啟。(VirtualBox必須)

### 如果無法執行 ###

某些64位元的Windows 7/8/10, boot2docker會一直無法啟動VirtualBox, 此時到VirtualBox的官方重新安裝最新版的VirtualBox即可。

另外如果按下boot2docker start圖示一直跳出記事本或無法進去, 可到`C:\Prodram Files\Boot2Docker for windows\`, 滑鼠右鍵按`Git bash`到bash輸入`./start.sh`即可。